# Prompt
Pense no seguinte cenário.
Criar uma aplicação Spring Boot, com API RESTful, para integração.
Funcionalidades:
Componente de configuração de Layout de arquivos, enviado pelo fornecedor. A configuração deverá contemplar a leitura de um arquivo excel. 1.1. No arquivo excel, na primeira planilha, contém os dados da Invoice do fornecedor. A configuração deve ser feita através da identificação de campos a serem relacionados da camada Core (estou pensando em arquitetura hexagonal). 1.1.1. Para cada campo adicionado na configuração, temos a referência no Excel (localização da célula), o atributo da entidade Core e, se necessário, regra de conversão (exemplo: converter tipo texto em data). Pode ser que tenha campo de destino que deverá ser composto pela concatenação de 2 posições no Excel. 1.2. A segunda planilha do excel, contém os itens da Invoice, então a regra de configuração deve identificar um campo que começa a ser lido numa posição e terminará até que não se tenha valor válido na célula.
Pense na modelagem do Core como sendo uma tabela do tipo BigData, onde a estrutura não é bem definida. Estou pensando em usar campo tipo JSONB do PostgreSQL para atender os campos, conforme a configuração de mapeamento. Faça quantas perguntas forem necessárias, para aprimorar as regras. Se identificar alguma solução que atenda os requisitos acima, listá-los.

# Adapta
Entendi o cenário e os objetivos. Vou propor uma arquitetura e um modelo de configuração que atendem aos requisitos, detalhar componentes técnicos (incluindo leitura do Excel, mapeamento dinâmico, regras de conversão, persistência em JSONB), sugerir endpoints REST, opções de escalabilidade/observabilidade e listar alternativas de implementação. No final, faço um conjunto abrangente de perguntas para fechar lacunas antes de partir para o desenho detalhado ou POC.

## Visão geral da solução
- Aplicação Spring Boot (3.x), API RESTful, arquitetura hexagonal (ports & adapters).
- Configuração de Layout por fornecedor e versão, com mapeamentos dinâmicos entre posições do Excel e “atributos” do Core (armazenados via JSONB no PostgreSQL).
- Suporte a:
  - Leitura da 1ª planilha (cabeçalho/Invoice) por referências a células/intervalos.
  - Leitura da 2ª planilha (itens) por faixa que começa em uma posição e termina quando uma coluna sentinela deixa de ter valor válido.
  - Regras de conversão (tipo texto -> data, numérico com localidade, concatenações, lookup, regex etc.).
  - Campos compostos (concatenação ou expressão).
  - Validações e relatórios de erro.
  - Versionamento de layouts e simulação (“dry-run”).
- Persistência:
  - JSONB para payload flexível (Core “BigData”).
  - Índices GIN/parciais para consultas.
  - Opcionalmente, projeções/visões materializadas se for necessário query operacional com performance.
- Processamento:
  - Idempotência (detecção de duplicados).
  - Lote ou near real time (upload de arquivo, fila, scheduler).
- Observabilidade:
  - Logs estruturados, métricas, traços, DLQ para arquivos malformados.

Se quiser, você pode me enviar um Excel de exemplo. Na plataforma, ative o recurso de Spreadsheets e faça upload de até 5 arquivos .xls/.xlsx/.csv (do mesmo tipo) e os “ative” no chat. Assim consigo validar o desenho com um caso real.

## Arquitetura (Hexagonal)
- Domínio (Core):
  - Serviços de domínio: motor de mapeamento, motor de validação, motor de conversão.
  - Portas (interfaces): LayoutRepositoryPort, IngestionPort (entrada de arquivo/stream), InvoiceRepositoryPort, EventPublisherPort.
- Aplicação (casos de uso):
  - Criar/atualizar layout, validar layout, simular mapeamento, processar Excel, persistir payload, publicar evento.
- Adapters:
  - REST Controller (Spring Web).
  - Excel Reader Adapter (Apache POI com streaming para .xlsx; HSSF para .xls se necessário).
  - Persistence Adapter (Spring Data JPA + PostgreSQL, suporte JSONB; Hibernate Types para mapear JSON).
  - Mensageria (opcional): Kafka/RabbitMQ.
  - Observabilidade (Micrometer + Prometheus/OpenTelemetry).

## Modelo de Configuração de Layout
A configuração é versionada por fornecedor. Recomendo um JSON para expressar as regras. Exemplo simplificado:

```json
{
  "supplierCode": "ACME",
  "version": "2024-12",
  "description": "Layout ACME v2024-12",
  "sheets": {
    "header": {
      "name": "Sheet1",
      "mappings": [
        {
          "targetPath": "invoice.number",
          "source": { "type": "cell", "ref": "B2" },
          "required": true,
          "converters": [ { "type": "trim" } ],
          "validations": [ { "type": "notBlank" } ]
        },
        {
          "targetPath": "invoice.date",
          "source": { "type": "cell", "ref": "B3" },
          "converters": [
            { "type": "date", "pattern": "dd/MM/yyyy", "timezone": "UTC" }
          ]
        },
        {
          "targetPath": "invoice.customer.name",
          "source": {
            "type": "expression",
            "expr": "concat(cell('B5'), ' ', cell('C5'))"
          },
          "converters": [ { "type": "uppercase", "locale": "pt-BR" } ]
        },
        {
          "targetPath": "invoice.currency",
          "source": { "type": "constant", "value": "BRL" }
        }
      ]
    },
    "items": {
      "name": "Sheet2",
      "start": { "row": 5, "col": "A" },
      "termination": {
        "type": "firstEmptyInColumn",
        "col": "A",
        "validPredicate": "notBlank"
      },
      "rowFilter": {
        "enabled": true,
        "predicate": "notBlank(cell('A'))"
      },
      "itemPath": "invoice.items[]",
      "columns": [
        {
          "targetPath": "sku",
          "source": { "type": "col", "col": "A" },
          "converters": [ { "type": "trim" } ],
          "validations": [ { "type": "notBlank" } ]
        },
        {
          "targetPath": "description",
          "source": { "type": "col", "col": "B" }
        },
        {
          "targetPath": "qty",
          "source": { "type": "col", "col": "C" },
          "converters": [ { "type": "number", "locale": "pt-BR" } ],
          "validations": [ { "type": "min", "value": 0.0001 } ]
        },
        {
          "targetPath": "unitPrice",
          "source": { "type": "col", "col": "D" },
          "converters": [ { "type": "number", "locale": "pt-BR" } ]
        },
        {
          "targetPath": "total",
          "source": {
            "type": "expression",
            "expr": "cell('C') * cell('D')"
          }
        }
      ]
    }
  },
  "postProcessing": [
    {
      "type": "validateTotals",
      "params": {
        "sumPath": "invoice.items[].total",
        "targetPath": "invoice.totalAmount",
        "tolerance": 0.01
      }
    }
  ],
  "idempotency": {
    "naturalKey": [
      { "path": "invoice.number" },
      { "path": "invoice.date" },
      { "path": "invoice.supplierCode", "constant": "ACME" }
    ]
  }
}
```

Notas:
- targetPath usa notação de caminho (dot notation) para o JSON final.
- source pode ser cell (referência A1), col (coluna da linha atual nos itens), constant ou expression.
- expression: podemos usar uma DSL simples (concat, substring, regex, math) ou SpEL/JEXL. Prefiro uma DSL controlada para segurança e previsibilidade.
- termination define onde parar a leitura dos itens.
- itemPath indica onde criar o array.
- validations e converters são compostáveis (pipeline).

## Leitura do Excel
- Biblioteca: Apache POI.
  - .xlsx: XSSF + streaming (SXSSF) para memória controlada.
  - .xls: HSSF (sem streaming; avaliar limites de memória).
- Endereçamento:
  - Referências A1 (ex.: “B3”) para cabeçalho.
  - Para itens, mapeio por coluna (A, B, C...) a partir de start.row.
- Terminação dos itens:
  - Regra padrão: primeira célula vazia em uma coluna sentinela (ex.: A).
  - Regras alternativas: N linhas fixas, até encontrar “TOTAL”, até duas linhas vazias consecutivas, predicate customizado etc.
- Conversões nativas do POI:
  - Datas: se a célula é do tipo date, ler como LocalDate/LocalDateTime; se for texto, aplicar pattern.
  - Números: considerar formatação local (decimal separador).
- Tamanho/volume:
  - Para planilhas grandes, leitura linha a linha (streaming), commit por lotes.

## Persistência (PostgreSQL + JSONB)
Tabelas sugeridas (mínimo):
- invoices
  - id (UUID)
  - supplier_code (text)
  - layout_version (text)
  - payload (jsonb) — contém invoice + items
  - status (text) — RECEIVED, VALIDATED, PROCESSED, ERROR
  - errors (jsonb) — se houver
  - checksum/natural_key (text) — para idempotência
  - created_at, updated_at (timestamp)
- Opcional: invoice_items (normalizada) se precisar consulta/relatório de alta performance por item (SKU, etc.). Alternativa: gerar materialized views extraindo campos do JSONB.
- Índices:
  - GIN em payload com jsonb_path_ops.
  - Índices parciais por fornecedor ou por período.
  - Índices por campos chave: (supplier_code, layout_version), (checksum).
- Particionamento:
  - Por mês/ano em invoices (nativo do Postgres).
- Consultas:
  - Operadores ->, ->>, @>, jsonb_path_query para filtros específicos.
- Prós: flexibilidade total do Core, evolução sem migrações pesadas.
- Contras: consultas analíticas podem exigir projeções/visões auxiliares.

## Endpoints REST propostos
- Layouts
  - POST /layouts — cria layout (supplierCode + version + JSON de config).
  - GET /layouts/{supplierCode}/{version} — obtém layout.
  - PUT /layouts/{supplierCode}/{version} — atualiza layout (gera nova subversão ou controla status).
  - POST /layouts/{supplierCode}/{version}/validate — valida a configuração (schema + regras).
  - POST /layouts/{supplierCode}/{version}/dry-run — envia Excel e retorna payload mapeado + relatório, sem persistir.
- Ingestão
  - POST /ingestions/{supplierCode}/{version} — upload do Excel, processa, persiste e retorna id do registro + resumo.
  - POST /ingestions/{supplierCode}/{version}/async — inicia ingestão assíncrona e retorna jobId; resultados via polling/webhook.
- Consultas/Core
  - GET /invoices/{id} — retorna JSONB completo.
  - GET /invoices — filtros por período, fornecedor, número de invoice (via campos do JSONB).
  - GET /invoices/{id}/status — status e erros.
- Admin/Operação
  - GET /health, /metrics, /info
  - GET /dead-letter — lista falhas recentes
  - POST /reprocess/{id} — reprocessa.

## Fluxo de processamento
1. Receber Excel (upload REST ou apontar para storage).
2. Identificar layout pelo fornecedor + versão.
3. Ler cabeçalho (planilha 1) e aplicar mapeamentos (células e expressões).
4. Ler itens (planilha 2), iterando linhas até a condição de término; aplicar colunas mapeadas, filtros e conversões.
5. Montar payload JSON final.
6. Validar (schema leve, obrigatoriedade, consistência de totais, formatos).
7. Calcular checksum/idempotency key com base em campos naturais (ex.: supplier + invoiceNumber + date).
8. Persistir no Postgres (com conflito ignorado/UPSERT se duplicado, conforme política).
9. Publicar evento (opcional) em Kafka/RabbitMQ para downstream.
10. Retornar resumo (quantidade de itens, total, warnings).

## Regras de conversão e validação
Conversores comuns:
- trim, uppercase/lowercase (locale-aware)
- date (pattern, timezone), datetime
- number (locale, separadores), currency normalization
- boolean (mapeia “Sim/Não”, “Y/N”)
- regexExtract, regexReplace
- concat, substring
- mapLookup (ex.: mapear códigos do fornecedor para códigos internos)
- defaultIfBlank, nullIfEquals
Validações:
- notBlank, min/max, pattern
- isDate, isNumber
- set membership (ex.: currency ∈ {BRL, USD, EUR})
- regras cross-field (ex.: soma dos itens ≈ total do cabeçalho)

Implementação:
- Pipeline de conversores + validações com curto-circuito e acúmulo de warnings.
- Estratégia de erro configurável: FAIL_FAST, COLLECT_AND_CONTINUE, SKIP_ROW.

## Idempotência e deduplicação
- Definição de chave natural no layout (ex.: invoice.number + date + supplier).
- Geração de hash/assinatura (ex.: SHA-256) para UPSERT.
- Política configurável: REJECT_DUPLICATE, REPLACE, KEEP_FIRST.

## Observabilidade e operação
- Logs estruturados (JSON) com traceId por ingestão.
- Métricas: tempo de parsing, contagem de linhas processadas, falhas por conversor/validação, taxa de duplicados.
- Dashboards (Prometheus/Grafana).
- Dead-letter store para Excel inválido/configuração incorreta.

## Segurança e multi-tenant
- Autenticação/autorização (Spring Security + JWT/OAuth2).
- Multi-tenant: por fornecedor (tenantId), com escopo nos endpoints e dados.
- Proteção de PII (mascaramento em logs, criptografia em repouso se preciso).

## Escalabilidade
- Processamento assíncrono com fila (Kafka/RabbitMQ) para alto volume.
- Execução paralela por arquivo/linha (cuidado com ordenação e memória).
- Tunning do pool JDBC, batch inserts.
- Particionamento de tabela e retenção por janelas.

## Testes e qualidade
- Testes de contrato do layout (validação do JSON de configuração).
- Testes com Excel de amostra (unit + integração).
- Conjuntos de regressão por versão de layout.
- Linters para expressões/DSL.

## Alternativas que atendem (ou complementam) os requisitos
- Spring Batch:
  - Excelente para leitura em lote de planilhas grandes, com chunk processing, skip/retry, listeners e reprocessamento. Adapta-se bem ao seu item 1.2 de término “até célula inválida”.
- Apache Camel:
  - Rotas de integração, componentes para arquivos, timers, mensageria; poderia orquestrar ingestão e conversões, mantendo o motor de mapeamento como bean de domínio.
- Jolt (transformação JSON):
  - Estratégia: Excel -> JSON intermediário “canônico” -> Jolt spec -> JSON final. Facilita manutenção de mapeamentos complexos e versionamento.
- Apache NiFi:
  - Pipeline low-code para ingestão/transformação; pode ser exagero se precisa ser produto Spring Boot, mas ajuda em prototipagem e observabilidade.
- Mapear com JsonPath/JsonPointer e SpEL/JEXL:
  - Permite flexibilidade para compor campos (concatenações, condicionais); atenção a segurança das expressões.
- Persistência híbrida:
  - JSONB como fonte de verdade + tabelas de projeção para relatórios/consultas frequentes. Pode ser automatizado com triggers/cron jobs ou Debezium + sink.

Tudo acima é compatível com os requisitos, mas minha recomendação primária é: Spring Boot + POI + JSONB + motor de mapeamento/validação em DSL própria + Spring Batch (se volume alto).

## Esboço de classes-chave (conceitual)

Interface de conversor:
```java
public interface ValueConverter {
  Object convert(Object input, Map<String, Object> ctx);
  String name();
}
```

Interface de validador:
```java
public interface ValueValidator {
  void validate(Object value, Map<String, Object> ctx, List<String> errors, List<String> warnings);
  String name();
}
```

Aplicação do mapeamento (simplificado):
```java
public class MappingEngine {

  private final ExpressionEvaluator evaluator; // DSL/SpEL/JEXL
  private final ConverterRegistry converters;
  private final ValidatorRegistry validators;

  public ObjectNode mapHeader(Sheet sheet, HeaderMapping cfg) {
    ObjectNode target = JsonNodeFactory.instance.objectNode();
    for (FieldMapping fm : cfg.getMappings()) {
      Object raw = resolveSource(sheet, fm.getSource(), null);
      Object val = applyConverters(raw, fm.getConverters());
      List<String> errors = applyValidators(val, fm.getValidations());
      if (!errors.isEmpty() && fm.isRequired()) throw new ValidationException(errors);
      JsonUtil.setByPath(target, fm.getTargetPath(), val);
    }
    return target;
  }

  public ArrayNode mapItems(Sheet sheet, ItemsMapping cfg) {
    ArrayNode arr = JsonNodeFactory.instance.arrayNode();
    int row = cfg.getStartRow();
    while (true) {
      Row r = sheet.getRow(row);
      if (shouldTerminate(r, cfg)) break;
      if (shouldSkip(r, cfg)) { row++; continue; }
      ObjectNode item = JsonNodeFactory.instance.objectNode();
      for (ColumnMapping cm : cfg.getColumns()) {
        Object raw = resolveSource(sheet, cm.getSource(), r);
        Object val = applyConverters(raw, cm.getConverters());
        applyValidators(val, cm.getValidations()); // acumular warnings/erros
        JsonUtil.setByPath(item, cm.getTargetPath(), val);
      }
      arr.add(item);
      row++;
    }
    return arr;
  }
}
```

Persistência com JSONB (JPA + Hibernate Types):
```java
@Entity
@Table(name = "invoices")
public class InvoiceEntity {
  @Id
  private UUID id;

  private String supplierCode;
  private String layoutVersion;

  @Type(JsonType.class)
  @Column(columnDefinition = "jsonb")
  private JsonNode payload;

  private String checksum;
  private String status;

  @Type(JsonType.class)
  @Column(columnDefinition = "jsonb")
  private JsonNode errors;

  @CreationTimestamp
  private Instant createdAt;
}
```

## Perguntas para refinar as regras
Sobre o Excel e layouts:
1. Você consegue compartilhar um Excel de exemplo por fornecedor? Se sim, suba via Spreadsheets (até 5 arquivos .xls/.xlsx do mesmo tipo) e ative no chat.
2. O Excel é sempre .xlsx ou há .xls também?
3. A 1ª planilha tem posições fixas (ex.: número em B2, data em B3) ou pode variar por fornecedor/versão?
4. Para itens, qual é exatamente a coluna sentinela que define o término? Sempre a coluna A? Pode haver linhas em branco entre itens?
5. Existe possibilidade de múltiplas seções de itens na planilha (ex.: itens, descontos, fretes)?
6. Precisaremos ler fórmulas (células com formula) e avaliar o resultado calculado?

Regras de conversão e validação:
7. Quais formatos de data e numérico são esperados por fornecedor (ex.: dd/MM/yyyy, separador de milhar/ponto)?
8. Precisamos de normalização de moeda, casas decimais, arredondamento?
9. Precisamos de lookups (ex.: SKU do fornecedor -> SKU interno)? Onde reside essa tabela (DB, serviço externo)?
10. Há regras condicionais (ex.: “se coluna X = 'Y', então formata Z”)?
11. Quais são os campos obrigatórios do lado do Core? Algum relacionamento (ex.: invoice tem que pertencer a um fornecedor cadastrado previamente)?
12. Como lidar com erros: rejeitar o arquivo inteiro, ou processar parcialmente (itens válidos entram, inválidos viram erro por item)?

Modelagem e consultas:
13. Você pretende consultar frequentemente por quais campos? Número da invoice, data, CNPJ do fornecedor, SKU, totais?
14. Armazenar os itens dentro do JSONB é suficiente, ou você precisa tabelas normalizadas de itens para relatórios/BI?
15. Qual volume esperado (tamanho de arquivo, número de linhas por planilha, ingestões por dia)?

Idempotência e fluxo:
16. Qual a chave natural de deduplicação? Número de invoice + data + fornecedor é suficiente?
17. Deve sobrescrever o registro se chegar uma versão “corrigida” da mesma invoice?
18. O processamento será síncrono (upload e resposta) ou assíncrono (fila, callback)?

Segurança e operação:
19. Há requisitos de autenticação/autorização específicos (ex.: OAuth2 com Keycloak, Azure AD)?
20. Necessidade de multi-tenant (um tenant por fornecedor) com segregação física/lógica?
21. Prazos de retenção de dados? LGPD/PII presente?

DevOps:
22. Necessidade de rodar em Kubernetes? Observabilidade padrão (Prometheus/Grafana/ELK)?
23. Precisamos de mecanismo de versionamento de layouts com aprovação (workflow) e rollback?
24. Você deseja UI para manutenção de layout (formulário/low-code) ou começamos 100% via API/JSON?

Extensibilidade:
25. Precisaremos suportar outros formatos além de Excel (CSV, PDF tabelar)?
26. Alguma integração downstream imediata (ERP, faturamento, BI) — via API, fila ou dump?

## Próximos passos sugeridos
- Se puder, envie um Excel de exemplo pelo recurso de Spreadsheets e descreva onde estão 3–5 campos do cabeçalho e 3–5 colunas dos itens (incluindo a coluna sentinela de término). Posso montar um layout JSON e rodar um “dry-run” de mapeamento conceitual aqui.
- Definimos a DSL de expressões mínima (concat, substring, math, regex) e a lista de conversores/validações.
- Escolhemos a estratégia de idempotência e de erro (fail fast vs. collect).
- Decidimos se Spring Batch entra na primeira versão (recomendo se tiver >10k linhas por planilha ou alta concorrência).
- Validamos índices e partições no Postgres com base nos volumes.

## Resumo
- A arquitetura com Spring Boot + POI + JSONB + mapeamento configurável por JSON atende aos requisitos descritos (planilha 1 para cabeçalho por endereço de célula; planilha 2 para itens por varredura até célula inválida; campos compostos; conversões; hexagonal).
- Alternativas como Spring Batch e Apache Camel fortalecem a robustez de ingestão e escabilidade.
- O uso de JSONB dá a flexibilidade do “Core BigData”, com possibilidade de projeções para consultas críticas.
- Precisamos fechar detalhes de layouts, regras de término, conversões e volume para definir os defaults corretos.

Fico no aguardo das respostas às perguntas e, se possível, do Excel de exemplo (use o recurso de Spreadsheets da plataforma e ative o arquivo no chat). Com isso, já te devolvo um layout.json inicial e os endpoints prontos para a primeira POC. 🚀
