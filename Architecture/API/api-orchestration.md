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

# Prompt
Pode criar uma classe Java que processe essa estrutura e chamadas?  
Utilize Map<String,Object> para manipulação dos dados.  
O método deve receber dois JSON:  
1. JSON com a configuração do orquestrador  
2. JSON com o input de dados  

# Gemini
Aqui está um exemplo de implementação em Java utilizando `Map<String, Object>` para representar o contexto, os inputs e as configurações.

Para manter o código limpo, focado na lógica de orquestração e independente de bibliotecas externas pesadas, utilizei a API nativa do Java (`HttpURLConnection` e `HttpClient` a partir do Java 11) e abstraí o parse inicial de JSON para `Map` (você pode usar Jackson ou Gson no seu projeto para converter as Strings JSON em `Map`).

### Classe Java do Orquestrador

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.util.*;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class ApiOrchestrator {

    private final HttpClient httpClient;

    public ApiOrchestrator() {
        this.httpClient = HttpClient.newHttpClient();
    }

    /**
     * Método principal que processa a orquestração.
     * @param config O JSON de configuração convertido em Map
     * @param input O JSON de input com os dados iniciais convertido em Map
     */
    public Map<String, Object> execute(Map<String, Object> config, Map<String, Object> input) {
        // Inicializa o contexto compartilhado
        Map<String, Object> context = new HashMap<>();
        
        List<Map<String, Object>> steps = (List<Map<String, Object>>) config.get("steps");
        if (steps == null) return context;

        // Ordena os passos pelo step_number para garantir a sequência correta
        steps.sort(Comparator.comparingInt(s -> (int) s.get("step_number")));

        for (Map<String, Object> step : steps) {
            String stepName = (String) step.get("name");
            System.out.println("Executando Step: " + stepName);

            Map<String, Object> requestConfig = (Map<String, Object>) step.get("request");
            Map<String, Object> outputConfig = (Map<String, Object>) step.get("output");
            String contextKey = (String) outputConfig.get("context_key");

            // Verifica se o passo atual possui uma estrutura de Loop
            if (step.containsKey("loop")) {
                Map<String, Object> loopConfig = (Map<String, Object>) step.get("loop");
                String iterablePath = (String) loopConfig.get("iterable");
                String itemAlias = (String) loopConfig.get("item_alias");

                // Resolve o array que deve ser iterado a partir do input/contexto
                List<Map<String, Object>> itemsToIterate = (List<Map<String, Object>>) resolveTemplateString(iterablePath, input, context, null);
                List<Map<String, Object>> aggregatedResults = new ArrayList<>();

                for (Map<String, Object> currentItem : itemsToIterate) {
                    // Executa a requisição passando o item atual do loop no escopo local
                    Map<String, Object> response = executeHttpRequest(requestConfig, input, context, currentItem, itemAlias);
                    aggregatedResults.add(response);
                }

                // Salva o array de resultados agregados no contexto
                context.put(contextKey, aggregatedResults);
            } else {
                // Execução simples (Sem Loop)
                Map<String, Object> response = executeHttpRequest(requestConfig, input, context, null, null);
                context.put(contextKey, response);
            }
        }

        return context;
    }

    /**
     * Monta e executa a requisição HTTP substituindo as variáveis dinâmicas.
     */
    private Map<String, Object> executeHttpRequest(Map<String, Object> requestConfig, 
                                                   Map<String, Object> input, 
                                                   Map<String, Object> context, 
                                                   Map<String, Object> currentLoopItem, 
                                                   String itemAlias) {
        
        String method = (String) requestConfig.get("method");
        String rawUrl = (String) requestConfig.get("url");
        
        // 1. Resolve a URL substituindo osplaceholders {{...}}
        String resolvedUrl = (String) resolveTemplateString(rawUrl, input, context, currentLoopItem != null ? Map.of(itemAlias, currentLoopItem) : null);

        // Adiciona Query Params na URL se existirem
        if (requestConfig.containsKey("query_params")) {
            Map<String, Object> queryParams = (Map<String, Object>) requestConfig.get("query_params");
            StringJoiner sj = new StringJoiner("&", "?", "");
            queryParams.forEach((k, v) -> {
                Object resolvedVal = resolveTemplateString(v.toString(), input, context, currentLoopItem != null ? Map.of(itemAlias, currentLoopItem) : null);
                sj.add(k + "=" + resolvedVal.toString());
            });
            resolvedUrl += sj.toString();
        }

        try {
            HttpRequest.Builder requestBuilder = HttpRequest.newBuilder().uri(URI.create(resolvedUrl));

            // 2. Resolve e aplica os Headers
            if (requestConfig.containsKey("headers")) {
                Map<String, String> headers = (Map<String, String>) requestConfig.get("headers");
                headers.forEach((k, v) -> {
                    String resolvedHeader = (String) resolveTemplateString(v, input, context, null);
                    requestBuilder.header(k, resolvedHeader);
                });
            }

            // 3. Resolve e aplica o Body (se aplicável)
            if (Arrays.asList("POST", "PUT", "PATCH").contains(method.toUpperCase()) && requestConfig.containsKey("body")) {
                Object bodyTemplate = requestConfig.get("body");
                Object resolvedBody = bodyTemplate;
                
                // Se for uma String pura com template, resolve. Se for um mapa interno, precisaria de recursão (simplificado aqui)
                if (bodyTemplate instanceof String) {
                    resolvedBody = resolveTemplateString((String) bodyTemplate, input, context, currentLoopItem != null ? Map.of(itemAlias, currentLoopItem) : null);
                } else if (bodyTemplate instanceof Map) {
                    // Se passarmos o objeto direto ex: {"products": "{{input.items}}"}
                    resolvedBody = resolveMapTemplates((Map<String, Object>) bodyTemplate, input, context, currentLoopItem != null ? Map.of(itemAlias, currentLoopItem) : null);
                }
                
                // Aqui você converteria o 'resolvedBody' (Map ou List) para String JSON Real usando Jackson/Gson
                String jsonBodyString = resolvedBody.toString(); 
                requestBuilder.method(method, HttpRequest.BodyPublishers.ofString(jsonBodyString));
            } else {
                requestBuilder.method(method, HttpRequest.BodyPublishers.noBody());
            }

            // 4. Dispara a chamada HTTP
            HttpResponse<String> response = httpClient.send(requestBuilder.build(), HttpResponse.BodyHandlers.ofString());

            // Mock de conversão da resposta para o mapa do contexto
            Map<String, Object> responseMap = new HashMap<>();
            responseMap.put("status_code", response.statusCode());
            // Nota: Em produção, faça o parse do response.body() de String para Map/List aqui
            responseMap.put("body", response.body()); 

            return responseMap;

        } catch (Exception e) {
            throw new RuntimeException("Falha ao executar step HTTP para a URL: " + resolvedUrl, e);
        }
    }

    /**
     * Mecanismo simples de Regex para varrer e substituir as tags {{input.xx}}, {{context.xx}} ou {{loop.xx}}
     */
    private Object resolveTemplateString(String template, Map<String, Object> input, Map<String, Object> context, Map<String, Object> loopScope) {
        if (template == null) return null;
        
        // Atalho caso a string seja EXATAMENTE uma referência de objeto/array completo (ex: "{{input.items}}")
        if (template.startsWith("{{") && template.endsWith("}}") && template.indexOf("}}") == template.length() - 2) {
            String path = template.substring(2, template.length() - 2).trim();
            return getValueByPath(path, input, context, loopScope);
        }

        // Caso seja um texto misto com variáveis na URL (ex: "/api/v1/customer/{{input.customer.id_customer}}")
        Pattern pattern = Pattern.compile("\\{\\{([^}]+)\\}\\}");
        Matcher matcher = pattern.matcher(template);
        StringBuilder sb = new StringBuilder();

        while (matcher.find()) {
            String path = matcher.group(1).trim();
            Object value = getValueByPath(path, input, context, loopScope);
            matcher.appendReplacement(sb, value != null ? value.toString() : "");
        }
        matcher.appendTail(sb);
        return sb.toString();
    }

    private Map<String, Object> resolveMapTemplates(Map<String, Object> sourceMap, Map<String, Object> input, Map<String, Object> context, Map<String, Object> loopScope) {
        Map<String, Object> resolved = new HashMap<>();
        for (Map.Entry<String, Object> entry : sourceMap.entrySet()) {
            if (entry.getValue() instanceof String) {
                resolved.put(entry.getKey(), resolveTemplateString((String) entry.getValue(), input, context, loopScope));
            } else {
                resolved.put(entry.getKey(), entry.getValue());
            }
        }
        return resolved;
    }

    /**
     * Navega recursivamente nos Maps baseando-se no path separado por pontos (Ex: input.customer.id_customer)
     */
    private Object getValueByPath(String path, Map<String, Object> input, Map<String, Object> context, Map<String, Object> loopScope) {
        String[] parts = path.split("\\.");
        Map<String, Object> currentScope;

        // Identifica a raiz do escopo
        if ("input".equals(parts[0])) currentScope = input;
        else if ("context".equals(parts[0])) currentScope = context;
        else if ("loop".equals(parts[0]) && loopScope != null) currentScope = loopScope;
        else return null;

        Object currentObj = currentScope;
        for (int i = 1; i < parts.length; i++) {
            if (currentObj instanceof Map) {
                currentObj = ((Map<String, Object>) currentObj).get(parts[i]);
            } else {
                return null; // Caminho quebrado ou índice de array não tratado nesta versão simplificada
            }
        }
        return currentObj;
    }
}

```

---

### Principais Destaques da Implementação:

1. **Separação de Escopo**: O método `getValueByPath` quebra strings como `input.customer.id_customer` e vai navegando de mapa em mapa (`map.get("customer")` -> `map.get("id_customer")`).
2. **Suporte nativo ao Loop**: Se a tag `"loop"` for detectada na configuração do passo, ele captura o array do input, cria uma subiteração isolada e injeta o objeto atual sob o rótulo configurado (`item_alias: "current_item"`), permitindo que expressões do tipo `loop.current_item.id_product` funcionem em tempo de execução.
3. **Estratégia de Agregação automática**: Caso o passo use loops, o retorno gerado não sobrepõe a chave do contexto; em vez disso, o motor cria um `ArrayList<Map<String, Object>>` acumulando as respostas de cada ciclo do loop para que fiquem disponíveis nos próximos passos.
