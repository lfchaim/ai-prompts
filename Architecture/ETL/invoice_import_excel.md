# Prompt
Pense no seguinte cenário.  
Criar uma aplicação Spring Boot, com API RESTful, para integração.  
Funcionalidades:  
1. Componente de configuração de Layout de arquivos, enviado pelo fornecedor. A configuração deverá contemplar a leitura de um arquivo excel.
  1.1. No arquivo excel, na primeira planilha, contém os dados da Invoice do fornecedor. A configuração deve ser feita através da identificação de campos a serem relacionados da camada Core (estou pensando em arquitetura hexagonal).
  1.1.1. Para cada campo adicionado na configuração, temos a referência no Excel (localização da célula), o atributo da entidade Core e, se necessário, regra de conversão (exemplo: converter tipo texto em data). Pode ser que tenha campo de destino que deverá ser composto pela concatenação de 2 posições no Excel. 
1.2. A segunda planilha do excel, contém os itens da Invoice, então a regra de configuração deve identificar um campo que começa a ser lido numa posição e terminará até que não se tenha valor válido na célula.  
Pense na modelagem do Core como sendo uma tabela do tipo BigData, onde a estrutura não é bem definida. Estou pensando em usar campo tipo JSONB do PostgreSQL para atender os campos, conforme a configuração de mapeamento. Faça quantas perguntas forem necessárias, para aprimorar as regras. Se identificar alguma solução que atenda os requisitos acima, listá-los.  
Pense fora da caixa!  

# Abacus
### Leituras iniciais (o que eu entendi)
Você quer uma API Spring Boot (REST) para integrar invoices de fornecedores via **Excel**, onde existe um **componente de configuração de layout** (por fornecedor/versão) que define como ler:

- **Planilha 1 (Header/Invoice)**: campos em células específicas (ex.: `B4`) mapeados para atributos do “Core” (arquitetura hexagonal), com **conversões** e possivelmente **campos compostos** (concatenação / regra).
- **Planilha 2 (Itens)**: lista tabular que começa em uma posição e vai até uma condição de parada (quando não houver mais valor “válido”).
- O Core tem estrutura “não bem definida” (BigData-like) e você está pensando em persistir o payload conforme o mapeamento em **PostgreSQL JSONB**.

Abaixo eu faço perguntas para fechar regras e, em seguida, listo soluções/modelagens possíveis (incluindo “fora da caixa”).

## Perguntas (para fechar regras e evitar retrabalho)

### 1) Escopo e versionamento do layout
1. A configuração é **por fornecedor**? Pode haver **múltiplas versões** ativas (ex.: fornecedor mudou o Excel e manda dos dois)?
2. Como você identifica o fornecedor/layout na entrada?
   - `supplierId` informado na API?
   - Ou detecta por heurística (nome de aba, “célula assinatura”, template id)?
3. Precisa de **workflow de aprovação** do layout (rascunho → aprovado → ativo)?

### 2) Excel: variações e dificuldades reais
4. Os arquivos vêm sempre como `.xlsx`? Pode vir `.xls`, protegido por senha, com fórmulas, merged cells?
5. As células de referência são sempre fixas (ex.: `B4`), ou às vezes precisam ser encontradas por **label** (ex.: procurar “Invoice Date” e pegar a célula ao lado)?
6. Os valores podem estar:
   - como texto com máscara (ex.: “14/01/2026”)
   - como número serial Excel (data)
   - como fórmula?
7. Há necessidade de **locale** (pt-BR vs en-US) para decimal/data/moeda?

### 3) Header (planilha 1): mapeamento e composição
8. Campo composto: a composição é sempre concatenação simples ou precisa de:
   - template (ex.: `"${A1}-${B1}"`)
   - join com separador
   - regex/substring
   - condicionais (`if blank então ...`)?
9. Existem campos “default” ou “constantes” por fornecedor (ex.: `sourceSystem = "X"`)?
10. Precisa validar obrigatórios e tipos antes de persistir? Se falhar, como retorna (400 com lista de erros) ou aceita e marca “incompleto”?

### 4) Itens (planilha 2): regra de repetição e parada
11. O “começa a ser lido numa posição” é:
   - linha inicial fixa (ex.: linha 12)?
   - ou encontra o cabeçalho (“Item”, “Qty”, “Price”) e começa abaixo?
12. A condição de parada é:
   - primeira célula-chave vazia (ex.: coluna A vazia)?
   - ou “linha toda vazia”?
   - ou “valor inválido” (qual coluna define validade)?
13. Existem linhas de subtotal/observação no meio que devem ser ignoradas?
14. Há necessidade de suportar **itens em múltiplas seções** ou múltiplas planilhas?

### 5) Core/BigData: consulta, auditoria e rastreabilidade
15. Você precisa **consultar** isso depois por campos do JSON (ex.: buscar por `invoiceNumber`, `cnpj`, `issueDate`)? Quais são os filtros mais comuns?
16. Precisa manter:
   - o **arquivo original** (blob em S3/minio ou no banco)?
   - o **payload bruto extraído** + o **payload transformado**?
   - trilha de auditoria (quem configurou, quando processou, idempotência)?
17. Volume esperado (arquivos/dia; itens/arquivo)? Isso influencia parsing (Apache POI streaming) e indexes.

### 6) Integração e idempotência
18. Como evitar duplicidade? Existe chave natural (supplier + invoiceNumber + issueDate)?
19. A integração é síncrona (API responde com resultado completo) ou assíncrona (aceita → processa → consulta status)?

Se você responder essas perguntas (mesmo que parcialmente), dá para fechar um desenho bem robusto.

## Uma solução que atende seus requisitos (com arquitetura hexagonal)

### A) Conceito central: “Layout Definition” versionado + “Mapping Engine”
- **LayoutDefinition** (por fornecedor/versão) descreve:
  - workbook/abas
  - regras de extração (célula fixa, busca por label, range/tabular)
  - regras de transformação (conversores, composição, validação)
  - regras de parada/ignorar linhas
- Um **Mapping Engine** aplica o LayoutDefinition a um Excel e gera um **documento canônico** (JSON) com:
  - `header` (invoice)
  - `items[]`
  - `metadata` (layoutVersion, hash do arquivo, warnings, etc.)

### B) Persistência “BigData-like” com JSONB + colunas canônicas
Mesmo usando JSONB, eu recomendo fortemente um híbrido:
- Colunas “fixas” para filtros/joins/garantia (ex.: `supplier_id`, `invoice_number`, `issue_date`, `status`, `layout_version`, `ingestion_id`)
- Um `payload jsonb` com o resto (flexível)
- Índice `GIN` no JSONB para queries eventuais + índices B-tree nas colunas canônicas

Isso mantém flexibilidade sem matar performance/observabilidade.

### C) Regras de transformação: conversores + expressões
- Registry de conversores (ex.: `StringToLocalDate`, `MoneyToBigDecimal`, `Trim`, `Upper`, `RegexExtract`)
- Para “fora da caixa”: permitir **expressões** em vez de só concatenação:
  - Spring Expression Language (SpEL) ou Apache JEXL
  - Ex.: `concat(cell("B4"), "-", cell("C4"))`
  - Ex.: `toDate(cell("B6"), "dd/MM/yyyy")`
Isso reduz a explosão de “tipos de regra” no seu modelo.

### D) Itens: extração tabular robusta
Em vez de “até célula inválida” apenas:
- Defina um **rowStartStrategy**:
  - `FIXED_ROW(12)` ou `FIND_HEADER(["Item","SKU","Qty"])`
- Defina um **rowEndStrategy**:
  - `UNTIL_BLANK(column="A")`
  - `UNTIL_CONSECUTIVE_BLANKS(column="A", n=2)`
  - `UNTIL_ROW_EMPTY()`  
- Defina um **rowFilter**:
  - ignore subtotais (`if cell("A") matches /total/i then skip`)

## Modelagem sugerida (alto nível)

### Domínio (Core)
- `LayoutDefinition` (agregado)
  - `layoutId`, `supplierId`, `version`, `status`
  - `sheetMappings[]` (header, items)
- `Ingestion` / `InvoiceDocument`
  - `supplierId`, `layoutVersion`, `sourceFileHash`, `status`
  - `payload jsonb` (header + items + metadata)
  - `errors[]/warnings[]` (também pode ser JSONB)

### Portas e adaptadores (hexagonal)
- **Inbound**
  - REST: cadastrar layout, validar layout com arquivo exemplo, processar arquivo
- **Application service**
  - `ProcessInvoiceUseCase(layoutId, file)` → `InvoiceDocument`
- **Outbound**
  - `LayoutRepository` (Postgres)
  - `InvoiceRepository` (Postgres)
  - `FileStore` (S3/MinIO) opcional
  - `EventPublisher` (Kafka/Rabbit) opcional

## Endpoints REST possíveis
- `POST /layouts` (criar layout)
- `PUT /layouts/{id}` (editar)
- `POST /layouts/{id}/validate` (upload de Excel de exemplo → retorna preview + erros)
- `POST /ingestions` (upload excel + layoutId → cria ingestion; sync/async)
- `GET /ingestions/{id}` (status + erros + payload resumido)

## “Fora da caixa”: alternativas/ideias que podem simplificar muito

### 1) Layout como “DSL JSON” + UI de configuração com preview
Você guarda a configuração como um JSONB (DSL) e oferece:
- “apontar célula” via UI (preview do Excel)
- testar conversões em tempo real
- versionar e aprovar
Mesmo que você não faça UI agora, desenhar a DSL desde já evita refactor.

### 2) Extração baseada em “âncoras” (label-driven), não em célula fixa
Para Excel que muda de posição:
- Procura texto “Invoice Number” e pega célula à direita/abaixo
- Para itens, acha linha do cabeçalho automaticamente
Isso costuma ser mais resiliente do que depender de `B4`.

### 3) Normalização em duas camadas: “raw” e “canonical”
- `raw_extracted`: exatamente o que veio das células
- `canonical`: após conversões/expressões
Ajuda muito em auditoria, debugging e ajuste de layout (você consegue explicar “de onde veio cada campo”).

### 4) “Data lineage” por campo (proveniência)
Para cada campo mapeado, guardar metadados:
- origem (sheet, cell/range, rowIndex)
- regra aplicada
- valor antes/depois
Isso vira um diferencial enorme para suporte.

### 5) Motor de validação declarativa
Acoplar validações no layout:
- required, regex, min/max, enum, consistência (ex.: soma itens = total)
E retornar relatório completo de qualidade do arquivo.

## Soluções/tecnologias que atendem o que você descreveu (lista)
1. **Spring Boot + Apache POI (XSSF/SXSSF)** para leitura Excel, com engine de mapeamento próprio e config em JSONB.
2. **Spring Boot + POI + SpEL/JEXL** para expressões (com sandbox) → composição e conversões flexíveis.
3. **Persistência Postgres JSONB híbrida** (colunas canônicas + payload flexível) com índices GIN e versionamento de layout.
4. **Arquitetura orientada a eventos** (opcional): ingestão assíncrona + publish de `InvoiceParsed` em Kafka/Rabbit.
5. **Abordagem anchor/label-driven** (fora da caixa) para tolerar mudanças de template sem quebrar.

## Próximo passo
Responda (do jeito mais curto possível) estas 6 perguntas-chave e eu devolvo um desenho fechado do modelo de configuração (um “schema” de layout em JSON), incluindo exemplos de regra para:
- campo simples (`B4 → header.invoiceNumber`)
- campo com conversão (`B6 texto → LocalDate`)
- campo composto (concat/expressão)
- itens com detecção de início/fim

Perguntas-chave:
1) Como identificar o fornecedor/layout na chamada?  
2) Planilha 2: a parada é por qual coluna (e qual critério de “válido”)?  
3) O Excel muda posição com frequência ou é template fixo?  
4) Quais 3 campos você mais precisa consultar depois (para eu sugerir colunas canônicas)?  
5) Processamento síncrono ou assíncrono?  
6) Precisa guardar o arquivo original (sim/não)?

# Prompt
Respostas:
1. Configuração por Fornecedor, mas podemos ter a mesma configuração para vários fornecedores. Só pode haver uma versão ativa para o Fornecedor.
2. Sim, usaremos supplierId na API
3. Não precisa
4. Atender formatos xls e xlsx, sem merged, sem senha e sem fórmulas (somente dados)
5. Sempre fixas
6. Pode ocorrer de ter máscara, o objetivo é ter DSL simples para conversão. Exemplo: converter type: date, format: "yyyy-MM-dd HH:mm:ss.SSS"
7. Sim
8. Pode haver necessidade de quebrar (com split por exemplo) então acho viável usar DSL
9. Não
10. Sim. Perfeito utilizar lista de erros
11. Estou pensando em ter um fluxo de leitura para lista. Por exemplo: definir ponto de partida, ler o label (tipo header) até que não encontre coluna com conteúdo (no blank), após a leitura das colunas, ler as linhas (para cada coluna identificada) e adicionar em array no JSON.
12. Sim, até que a coluna seja vazia (para o header), a partir de um ponto de partida (célula). Até que encontre uma linha vazia, para os itens da lista.
13. Não
14. Não
15. O objetivo é fazer a leitura e gravar no JSON conforme a identificação da chave no arquivo de mapeamento.
16. Não é necessário apagar o arquivo de leitura
17. 300 arquivos por dia
18. Somente supplier + invoiceNumber
19. Assíncrona. Pode-se ter uma tabela de resultado

# Abacus
Com base nas suas respostas, o cenário está bem delimitado. O volume de 300 arquivos/dia é baixo para o Spring Boot, o que permite focar em **flexibilidade e robustez da DSL** sem preocupações extremas com performance de streaming (embora o Apache POI trate bem ambos os formatos).

Abaixo, apresento uma proposta de **DSL de Configuração**, a **Modelagem do Banco** e a **Estratégia de Processamento**.

### 1. DSL de Configuração (O "Coração" do Componente)
Sugiro uma estrutura JSON para o Layout que será armazenada no banco. Ela separa o mapeamento em `header` (células fixas) e `items` (leitura dinâmica).

```json
{
  "layoutName": "Padrao Fornecedor A",
  "version": 1,
  "locale": "pt-BR",
  "sheets": {
    "invoice": {
      "index": 0,
      "fields": [
        {
          "target": "invoiceNumber",
          "source": { "cell": "B4" },
          "type": "string",
          "required": true
        },
        {
          "target": "issueDate",
          "source": { "cell": "B5" },
          "type": "date",
          "format": "dd/MM/yyyy",
          "required": true
        },
        {
          "target": "fullCode",
          "source": { "expression": "concat(cell('A1'), '-', cell('A2'))" },
          "type": "string"
        }
      ]
    },
    "items": {
      "index": 1,
      "startAt": "A2", 
      "stopCondition": "FIRST_CELL_BLANK",
      "columns": [
        { "index": 0, "target": "sku", "type": "string" },
        { "index": 1, "target": "description", "type": "string" },
        { "index": 2, "target": "quantity", "type": "decimal" },
        { "index": 3, "target": "unitPrice", "type": "decimal" }
      ]
    }
  }
}
```

### 2. Modelagem de Dados (PostgreSQL + JSONB)

Para atender ao requisito de "BigData" e flexibilidade, usaremos uma tabela de **Ingestion** com colunas canônicas para busca e o `JSONB` para o conteúdo variável.

#### Tabela: `layout_config`
- `id`: UUID
- `supplier_id`: String (Indexado)
- `active`: Boolean
- `definition`: JSONB (A DSL acima)

#### Tabela: `invoice_ingestion`
- `id`: UUID
- `supplier_id`: String
- `invoice_number`: String (Extraído para busca rápida)
- `status`: String (PENDING, PROCESSED, ERROR)
- `raw_payload`: JSONB (O resultado da extração conforme a DSL)
- `error_details`: JSONB (Lista de erros de validação/conversão)
- `created_at`: Timestamp

### 3. Arquitetura do Componente de Leitura (Fora da Caixa)

Para implementar a DSL de forma elegante no Spring Boot:

#### A. O Engine de Expressão (SpEL)
Em vez de criar dezenas de tipos de "concatenação" ou "split", use o **Spring Expression Language (SpEL)**. 
- Você expõe um objeto `Helper` para a expressão que tem acesso ao Workbook.
- Exemplo na DSL: `"expression": "cell('A1').substring(0,3) + cell('B1')"`
- O Spring resolve isso nativamente, dando um poder imenso de transformação sem você programar cada caso.

#### B. Estratégia de Leitura de Itens (Dynamic Header)
Conforme sua ideia de ler o header até o "no blank":
1. O motor vai até a célula `startAt` (ex: A2).
2. Ele lê a linha horizontalmente. Para cada célula não vazia, ele tenta dar match com a configuração de `columns`.
3. Uma vez mapeado o índice de cada coluna, ele desce verticalmente processando as linhas até encontrar a `stopCondition`.

#### C. Conversores Genéricos
Crie uma interface `ValueConverter<T>`:
- `DateConverter`: usa o `format` da DSL.
- `NumberConverter`: trata vírgula vs ponto baseado no `locale`.
- `SplitConverter`: usa um parâmetro `delimiter` e `index` da DSL.

### 4. Fluxo Assíncrono

1. **API (POST /ingest):** Recebe `supplierId` + `MultipartFile`.
2. **Validação Inicial:** Verifica se existe layout ativo para o fornecedor.
3. **Persistência Inicial:** Salva o registro em `invoice_ingestion` como `PENDING` e retorna o ID para o cliente.
4. **Processamento (@Async ou Queue):**
   - O Engine lê o Excel usando Apache POI.
   - Aplica as regras da DSL.
   - Se houver erro de conversão (ex: data inválida), adiciona na lista de erros.
   - Se `invoiceNumber` + `supplierId` já existir, marca como erro de duplicidade.
5. **Finalização:** Atualiza o registro com o `raw_payload` (JSONB) e status final.

### 5. Sugestões "Fora da Caixa"

1. **Virtual File System para Testes:** Como você terá muitos layouts, crie um endpoint de "Simulação". O usuário envia o Excel e a DSL (sem salvar) e a API retorna exatamente como o JSON ficaria e quais erros ocorreriam. Isso facilita muito a criação de novos layouts pelos analistas.
2. **JSONB Indexing (GIN):** No Postgres, crie um índice GIN no campo `raw_payload`. Isso permitirá que, mesmo sendo "BigData", você consiga fazer queries do tipo: 
   `SELECT * FROM invoice_ingestion WHERE raw_payload @> '{"header": {"status": "PAID"}}'`.
3. **Auto-detecção de Layout:** Se o `supplierId` não for suficiente (ex: o mesmo fornecedor tem 3 tipos de arquivos), adicione um campo `fingerprint` na DSL. O sistema lê a célula `A1` e, se contiver "Relatório de Vendas X", ele sabe qual layout usar automaticamente.
4. **Resiliência de Tipagem:** No JSONB, salve os valores sempre em dois formatos se necessário: o valor original (string) e o valor convertido. Isso ajuda a recuperar dados se a regra de conversão estiver errada na DSL, sem precisar reprocessar o arquivo.

### Perguntas para Refinamento Técnico:
1. **Tratamento de Erros:** Se um item da lista (Planilha 2) falhar na conversão, você deve invalidar a Invoice inteira ou importar a Invoice com um "warning" e pular o item?
2. **Segurança de Expressão:** Se usarmos SpEL para as fórmulas de concatenação/split, você se preocupa com a segurança de quem edita a DSL (risco de execução de código malicioso) ou o ambiente de configuração é restrito a administradores?
3. **Notificação:** Como o processo é assíncrono, o cliente fará *polling* no ID da ingestão ou você pretende disparar um Webhook/Evento quando terminar?
