# Prompt
Estou com uma ideia para implementar uma api, chamada query-builder. O objetivo é configurar uma query complexa, envolvendo várias tabelas e joins, com configuração de sub-queries sendo executadas com Virtual Thread. A query principal é executada primeiro, careega um Map e os valores e chaves do Map podem ser relacionados na sub-query. Com relação às expressões, na configuração penso em ter uma chave "filter", podendo ter várias chaves e que permita controlar os parâmetros de cada grupo de filters. Exemplo, se um grupo filters informar que um parâmetro é obrigatório, e este não foi passado, deve-se eliminar todo o grupo desse filter.
Pode estruturar um prompt que detalhe esses pontos, de maneira estruturada e organizada?
Se tiver alguma sugestão ou melhoria, pode me perguntar.

# Claude
Ótimo! Aqui está o prompt estruturado e detalhado:

---

# Prompt — API `query-builder`

---

## 🎯 Objetivo

Implemente uma API chamada **`query-builder`** em **Java com Spring Boot**, com suporte a **programação reativa (Project Reactor / WebFlux)** e **Virtual Threads (Java 21+)**. O objetivo é permitir a configuração declarativa de queries complexas via um **DSL em JSON**, suportando múltiplas tabelas, joins, sub-queries paralelas e grupos de filtros com regras de obrigatoriedade.

---

## 1. Estrutura do DSL (JSON de configuração)

O DSL deve descrever completamente a query de forma declarativa. Estrutura esperada:

```json
{
  "query": {
    "name": "orders-report",
    "mainQuery": {
      "from": "orders o",
      "joins": [
        { "type": "INNER", "table": "customers c", "on": "c.id = o.customer_id" },
        { "type": "LEFT",  "table": "products p",  "on": "p.id = o.product_id"  }
      ],
      "select": ["o.id", "o.total", "c.name", "p.description"],
      "filters": [
        {
          "groupId": "dateFilter",
          "required": ["startDate", "endDate"],
          "optional": ["status"],
          "conditions": [
            { "column": "o.created_at", "op": ">=", "param": "startDate" },
            { "column": "o.created_at", "op": "<=", "param": "endDate"   },
            { "column": "o.status",     "op": "=",  "param": "status"    }
          ]
        },
        {
          "groupId": "customerFilter",
          "required": [],
          "optional": ["customerId"],
          "conditions": [
            { "column": "c.id", "op": "=", "param": "customerId" }
          ]
        }
      ]
    },
    "subQueries": [
      {
        "name": "orderItems",
        "bindKey": "orderId",
        "mainResultKey": "o.id",
        "from": "order_items oi",
        "joins": [
          { "type": "INNER", "table": "products p", "on": "p.id = oi.product_id" }
        ],
        "select": ["oi.quantity", "oi.price", "p.name"],
        "filters": [
          {
            "groupId": "itemFilter",
            "required": ["orderId"],
            "optional": [],
            "conditions": [
              { "column": "oi.order_id", "op": "=", "param": "orderId" }
            ]
          }
        ],
        "mergeKey": "items"
      }
    ]
  }
}
```

### Campos obrigatórios do DSL

| Campo | Descrição |
|---|---|
| `mainQuery.from` | Tabela principal com alias |
| `mainQuery.joins` | Lista de joins (INNER, LEFT, RIGHT, FULL) |
| `mainQuery.select` | Colunas a retornar |
| `mainQuery.filters` | Grupos de filtros (ver seção 3) |
| `subQueries[].bindKey` | Parâmetro que a sub-query recebe |
| `subQueries[].mainResultKey` | Chave do Map da query principal usada como valor do `bindKey` |
| `subQueries[].mergeKey` | Chave inserida em cada entrada do Map principal com o resultado da sub-query |

---

## 2. Comportamento dos Virtual Threads + Reativo

### Fluxo de execução

```
DSL JSON
   │
   ▼
QueryBuilderService (reactive)
   │
   ├──► mainQuery → executa → retorna Flux<Map<String, Object>>
   │
   └──► Para cada registro do Flux:
           │
           ├─ Coleta os valores de mainResultKey
           │
           └─ Dispara sub-queries em paralelo via Virtual Threads
                  │
                  └─ Faz merge do resultado em cada Map (mergeKey)
```

### Regras de implementação

- A **query principal** deve ser executada de forma **reativa** (`Flux<Map<String, Object>>`), utilizando Spring WebFlux + R2DBC (ou JOOQ reativo).
- Cada **sub-query** deve ser executada em uma **Virtual Thread** separada (`Thread.ofVirtual()`), disparadas em paralelo para todos os registros do resultado principal.
- O merge deve ser **não-bloqueante**: usar `Flux.flatMap` com `Schedulers.boundedElastic()` ou um executor de Virtual Threads customizado.
- As sub-queries são **independentes entre si**, mas todas dependem do resultado da query principal.

### Exemplo de configuração do executor

```java
ExecutorService virtualThreadExecutor = Executors.newVirtualThreadPerTaskExecutor();
Scheduler virtualScheduler = Schedulers.fromExecutorService(virtualThreadExecutor);
```

---

## 3. Regras de Filter Groups

Cada grupo de filtros (`filters[]`) possui comportamento controlado pelos campos `required` e `optional`.

### Regras

| Situação | Comportamento |
|---|---|
| Todos os `required` foram passados | Grupo é incluído na query |
| Ao menos um `required` está ausente | **Todo o grupo é eliminado** da query (nenhuma condition do grupo é aplicada) |
| Parâmetro `optional` ausente | Apenas a condition desse parâmetro é removida; o grupo permanece |
| Grupo completamente vazio após remoções | Grupo é descartado silenciosamente |

### Exemplo prático

Dado o grupo `dateFilter` com `required: ["startDate", "endDate"]`:
- Chamada com `startDate` e `endDate` → grupo incluído ✅
- Chamada sem `endDate` → grupo inteiro removido, nenhuma condition de data é aplicada ❌
- Chamada com `startDate`, `endDate` e `status` → grupo incluído com todas as conditions ✅
- Chamada com `startDate`, `endDate` mas sem `status` → grupo incluído, apenas a condition de `status` é omitida ✅

---

## 4. Exemplos de Uso

### 4.1 — Endpoint de execução

```http
POST /query-builder/execute
Content-Type: application/json

{
  "queryName": "orders-report",
  "params": {
    "startDate": "2024-01-01",
    "endDate": "2024-12-31",
    "customerId": "42"
  }
}
```

### 4.2 — Resposta esperada (com merge das sub-queries)

```json
[
  {
    "o.id": 1001,
    "o.total": 350.00,
    "c.name": "João Silva",
    "p.description": "Notebook",
    "items": [
      { "oi.quantity": 1, "oi.price": 350.00, "p.name": "Notebook Pro" }
    ]
  }
]
```

### 4.3 — Estrutura de serviço reativo sugerida

```java
public Flux<Map<String, Object>> execute(QueryConfig config, Map<String, Object> params) {
    return mainQueryExecutor.execute(config.mainQuery(), params)
        .flatMap(row ->
            Flux.fromIterable(config.subQueries())
                .flatMap(sub -> executeSubQuery(sub, row, params)
                    .subscribeOn(virtualScheduler)
                    .map(result -> mergeIntoRow(row, sub.mergeKey(), result))
                )
                .then(Mono.just(row))
        );
}
```

---

## 5. Restrições e Observações Técnicas

- O DSL deve ser **validado na inicialização** ou no momento da requisição, lançando erros descritivos para configurações inválidas.
- `bindKey` e `mainResultKey` são a **ponte entre a query principal e a sub-query** — devem ser validados antes da execução.
- A API deve suportar **múltiplas sub-queries por query principal**, todas executadas em paralelo.
- O operador `op` nas conditions deve suportar: `=`, `!=`, `>`, `<`, `>=`, `<=`, `LIKE`, `IN`, `IS NULL`, `IS NOT NULL`.
- Considerar cache do DSL parseado para evitar re-parsing a cada requisição.

---

Se quiser, posso detalhar alguma seção específica, gerar os modelos Java (records/classes) para o DSL, ou montar o esqueleto do projeto Spring WebFlux completo.

# Prompt
Perfeito. O projeto montado, tem a implementação dos itens apresentados acima?
Se não gerar o código-fonte, quais ferramentas de IA podem ser usadas para essa tarefa?

# Claude
Não, o prompt que montei é apenas uma **especificação detalhada** — ele não gera código por si só. Ele serve como instrução para você (ou uma IA) implementar o projeto.

---

## Ferramentas de IA para gerar o código-fonte

### 🥇 Mais indicadas para projetos complexos como esse

| Ferramenta | Pontos fortes para esse caso |
|---|---|
| **Claude** (aqui mesmo) | Excelente para gerar código Java estruturado, entende contexto longo, ótimo para Spring WebFlux + arquitetura em camadas |
| **Cursor** | IDE com IA integrada, ideal para gerar e iterar o projeto inteiro em arquivo por arquivo |
| **GitHub Copilot** (no IntelliJ/VSCode) | Bom para completar implementações enquanto você já escreve o esqueleto |
| **Aider** | Ferramenta CLI que aplica código diretamente nos arquivos do projeto via terminal |

### ⚠️ Limitação importante

Nenhuma ferramenta vai gerar o projeto **completo e funcional de uma vez** para algo dessa complexidade. A abordagem mais eficiente é:

1. **Gerar por camadas** — modelos DSL → parser → executor da query principal → executor das sub-queries → merge reativo
2. **Iterar com a IA** — gerar, testar, ajustar, expandir
3. **Usar o prompt que montamos** como contexto fixo em cada conversa

---

## Posso gerar aqui agora

Se quiser, consigo produzir o código diretamente nessa conversa, por partes:Ordene acima e já começo a gerar camada por camada!
