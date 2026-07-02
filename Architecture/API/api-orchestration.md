# API Orchestration
Objetivo: Criar uma solução de orchestração de API, semelhante a BPMN.

# Prompt
Criar um modelo de configuração para um software de orquestração de API RESTful. Pode utilizar como modelo a emissão de Nota Fiscal, sabendo:  
1. A entrada é um JSON completo com dados do Cliente, os Itens, Endereço de Entrega, etc.  
2. O JSON de configuração deve utilizar passos (steps)  
2.1. Step 1: Obter dados do Cliente, invocando uma API /api/v1/customer/{id_customer} onde id_customer está no JSON completo no path /customer/id_customer  
2.2. Step 2: Obter dados dos itens, invocando uma API externa /api/v1/product, através do array /items/id_product e /items/qty_product  
2.3. Step 3: Obter dados de endereço de entrega, através da API externa /api/v1/address através do JSON completo /customer/id_address_delivery  

O processo irá atuando conforme cada configuração de Step, podendo executar métodos de GET, POST, PUT e DELETE, sendo possível configurar Endpoint, Body, Headers, Parameter, etc. Cada step lido, deve gerar uma chave de contexto que pode ser aplicada para converter objetos e efetuar os Steps de atualização.  
Se houver alguma dúvida, pode me perguntar.

# Gemini
Para atender a esse cenário de orquestração de APIs, a melhor abordagem é desenhar uma estrutura de configuração baseada em uma **máquina de estados ou pipeline de execução**. Cada passo (*step*) define sua intenção (verbo HTTP, endpoint), como extrair dados do payload original (usando JSONPath) e como salvar o resultado em um **contexto compartilhado** para os passos seguintes.

Aqui está um modelo robusto e escalável em JSON para o seu orquestrador:

### Exemplo de Payload de Entrada (Input JSON)

Este é o JSON completo que o seu orquestrador receberá inicialmente:

```json
{
  "customer": {
    "id_customer": "cust_98765",
    "id_address_delivery": "addr_12345"
  },
  "items": [
    { "id_product": "prod_001", "qty_product": 2 },
    { "id_product": "prod_002", "qty_product": 5 }
  ]
}

```

---

### JSON de Configuração do Orquestrador (Steps)

Este arquivo de configuração diz ao seu software exatamente o que fazer com o input acima, como mapear as variáveis e como armazenar as respostas no contexto.

```json
{
  "orchestration_name": "Emissao_Nota_Fiscal",
  "version": "1.0.0",
  "steps": [
    {
      "step_number": 1,
      "name": "get_customer_data",
      "description": "Busca dados detalhados do cliente",
      "request": {
        "method": "GET",
        "url": "https://api.interna.com/api/v1/customer/{{input.customer.id_customer}}",
        "headers": {
          "Content-Type": "application/json",
          "Authorization": "Bearer {{env.TOKEN_CLIENTE}}"
        }
      },
      "output": {
        "context_key": "customer_details"
      }
    },
    {
      "step_number": 2,
      "name": "get_products_data",
      "description": "Busca detalhes dos produtos em lote ou iterando o array",
      "request": {
        "method": "POST",
        "url": "https://api.externa.com/api/v1/product/bulk",
        "headers": {
          "Content-Type": "application/json"
        },
        "body": {
          "products": "{{input.items}}"
        }
      },
      "output": {
        "context_key": "products_details"
      }
    },
    {
      "step_number": 3,
      "name": "get_delivery_address",
      "description": "Busca o endereço de entrega do cliente",
      "request": {
        "method": "GET",
        "url": "https://api.externa.com/api/v1/address/{{input.customer.id_address_delivery}}",
        "headers": {
          "Content-Type": "application/json"
        }
      },
      "output": {
        "context_key": "delivery_address_details"
      }
    },
    {
      "step_number": 4,
      "name": "emit_invoice",
      "description": "Passo final (Exemplo): Une os dados do contexto e emite a NF",
      "request": {
        "method": "POST",
        "url": "https://api.governo.com/api/v1/invoice",
        "headers": {
          "Content-Type": "application/json"
        },
        "body": {
          "faturamento_para": "{{context.customer_details.razao_social}}",
          "documento": "{{context.customer_details.cnpj}}",
          "entrega": "{{context.delivery_address_details}}",
          "itens_nota": "{{context.products_details.lista}}"
        }
      },
      "output": {
        "context_key": "invoice_result"
      }
    }
  ]
}

```

---

### Como a Engine do seu Orquestrador deve funcionar:

1. **Sintaxe de Template (`{{ ... }}`)**: O orquestrador deve possuir um motor de renderização (como Handlebars, Liquid ou regex própria) para substituir os valores antes de disparar a requisição.
2. **Escopos do Contexto**:
* `{{input.path}}`: Dados vindos do JSON original.
* `{{context.step_name.path}}`: Dados consolidados a partir das respostas dos passos anteriores.
* `{{env.VAR}}`: Variáveis de ambiente globais (como tokens de autenticação).


3. **Tratamento de Arrays (Step 2)**: No modelo acima, assumi que a API de produtos aceita um *Bulk* (o array inteiro de itens). Se a sua API externa de produtos exigir uma chamada **para cada** item do array, o Step 2 precisaria de uma propriedade extra de controle de fluxo, por exemplo: `"loop": "{{input.items}}"`.

O modelo atende bem à flexibilidade que você precisa (suportando qualquer verbo HTTP, headers e corpos dinâmicos)? Se precisar que eu detalhe a estrutura de loops para APIs que não aceitam lote, é só avisar!

# Prompt
Detalhar a estrutura de loops.  

# Gemini
Para tratar cenários onde a API externa não aceita um lote (*bulk*) e exige uma chamada para cada item cadastrado, precisamos introduzir uma estrutura de controle de fluxo no JSON de configuração.

Geralmente, em engines de orquestração, adicionamos um bloco de `loop` ou `iterator`. Isso instrui o software a varrer o array do JSON de entrada, injetar o item atual em um contexto temporário (`item`) e executar a requisição repetidas vezes, agrupando os resultados.

Aqui está como fica a estrutura do **Step 2** modificada para realizar esse loop:

### JSON de Configuração com Estrutura de Loop (Step 2)

```json
{
  "step_number": 2,
  "name": "get_products_data",
  "description": "Busca detalhes de cada produto individualmente iterando o array de itens",
  "loop": {
    "iterable": "{{input.items}}",
    "item_alias": "current_item"
  },
  "request": {
    "method": "GET",
    "url": "https://api.externa.com/api/v1/product/{{loop.current_item.id_product}}",
    "headers": {
      "Content-Type": "application/json",
      "Authorization": "Bearer {{env.TOKEN_PRODUTOS}}"
    },
    "query_params": {
      "quantity": "{{loop.current_item.qty_product}}"
    }
  },
  "output": {
    "context_key": "products_details",
    "strategy": "AGGREGATE"
  }
}

```

---

### O que mudou e como a Engine deve processar:

* **O Bloco `"loop"**`:
* `iterable`: Aponta para o array no JSON original (`{{input.items}}`) que ditará quantas vezes o passo será executado.
* `item_alias`: Define o nome da variável de escopo local (`current_item`). Durante a execução, o orquestrador substitui isso pelas propriedades do item da vez.


* **Acesso Dinâmico**: Note que na URL e nos parâmetros usamos `{{loop.current_item.id_product}}`. A engine saberá que essa informação vem da iteração atual, e não do contexto global.
* **Estratégia de Saída (`strategy: "AGGREGATE"`)**:
* Como este passo roda múltiplas vezes, a engine não pode simplesmente sobrescrever a chave `products_details`.
* Com a estratégia `AGGREGATE`, o orquestrador cria automaticamente um **array de respostas** dentro da chave de contexto.



---

### Como o Contexto Compartilhado se parecerá após o Step 2?

Quando o Step 2 terminar, o objeto de contexto que será passado para os próximos passos (como o passo de emissão da NF) terá esta estrutura interna:

```json
{
  "context": {
    "customer_details": { "nome": "Empresa ACME", "cnpj": "12.345..." },
    
    "products_details": [
      {
        "status_code": 200,
        "body": { "id": "prod_001", "nome": "Parafuso Alen", "preco": 1.50 }
      },
      {
        "status_code": 200,
        "body": { "id": "prod_002", "nome": "Chave de Fenda", "preco": 15.00 }
      }
    ]
  }
}

```

Dessa forma, no passo final de envio da Nota Fiscal, você poderá acessar os dados agregados usando caminhos como `{{context.products_details[0].body.nome}}` ou passar o array completo para a API de faturamento mapear.

Faz sentido essa estrutura de mapeamento local para o loop da sua engine?

