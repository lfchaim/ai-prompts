# Prompt
Tenho uma aplicação Spring Boot e preciso integrar com SAP, para envio de Produtos e Estoque, que será atualizado no WMS do SAP.
Identifique as etapas, no SAP para disponibilizar uma API RESTful, com segurança e que atenda o conceito de CRUD para entidade Product.
Identifique as etapas na aplicação Spring Boot, para autenticar na API do SAP e enviar um POST para criação de um novo Product.

# Enhanced Prompt
Aqui está o prompt aprimorado:

"Desenvolva uma integração entre uma aplicação Spring Boot e o SAP para envio de dados de Produtos e Estoque, que serão atualizados no WMS do SAP. Para isso:

No SAP:
a. Identifique as etapas necessárias para criar uma API RESTful segura que atenda ao conceito de CRUD (Create, Read, Update, Delete) para a entidade Product.
b. Especifique as configurações de segurança necessárias para a API, incluindo autenticação e autorização.
c. Detalhe como configurar e implementar a API no SAP, considerando as melhores práticas de segurança e performance.

Na aplicação Spring Boot:
a. Descreva as etapas para autenticar na API RESTful do SAP, utilizando um mecanismo de autenticação adequado (por exemplo, OAuth, Basic Auth, etc.).
b. Implemente um exemplo de código que demonstre como enviar uma requisição POST para a API do SAP para criar um novo Product, incluindo:

Configuração do cliente HTTP (por exemplo, RestTemplate ou WebClient).
Preparação dos dados de Product a serem enviados.
Tratamento de erros e exceções.
Considere:

A API do SAP deve ser configurada para utilizar um protocolo de comunicação seguro (HTTPS).
A aplicação Spring Boot deve lidar com possíveis erros de comunicação, como timeouts e respostas de erro do SAP.
A implementação deve seguir as melhores práticas de segurança e coding standards tanto no SAP quanto na aplicação Spring Boot.
Forneça detalhes técnicos e exemplos de código quando aplicável, e mantenha a explicação clara e concisa."

# Abacus (Claude Sonnet 4.6)
# Integração Spring Boot ↔ SAP (WMS) via API RESTful

---

## 🔷 Parte 1 — SAP: Disponibilizando uma API RESTful para Product

### 1. Pré-requisitos no SAP

- **SAP S/4HANA** ou **SAP ECC** com **SAP Gateway** instalado
- Módulo **WM/EWM** ativo para gestão de estoque/WMS
- Acesso ao **SAP Business Technology Platform (BTP)** (opcional, mas recomendado para APIs modernas)

---

### 2. Criar o OData Service (API RESTful no SAP)

O SAP expõe APIs via **OData** (protocolo REST-like), usando o **SAP Gateway Framework**.

#### Passo a passo no SAP GUI:

**a) Criar o projeto no SAP Gateway**
```
Transação: SEGW (SAP Gateway Service Builder)
→ Criar novo projeto: ZWM_PRODUCT_SRV
→ Definir Entity Type: Product
   - ProductID (Key, String)
   - Description (String)
   - Quantity (Decimal)
   - UnitOfMeasure (String)
   - StorageLocation (String)
→ Definir Entity Set: ProductSet
→ Gerar CRUD: Create, Read, Update, Delete, Query
```

**b) Implementar os métodos CRUD na classe de dados**
```
Transação: SEGW → Reimplementar métodos:
- CREATE_ENTITY  → Cria produto no WMS
- GET_ENTITY     → Busca produto por ID
- GET_ENTITYSET  → Lista todos os produtos
- UPDATE_ENTITY  → Atualiza produto
- DELETE_ENTITY  → Remove produto
```

**c) Ativar e publicar o serviço**
```
Transação: /IWFND/MAINT_SERVICE
→ Add Service → buscar ZWM_PRODUCT_SRV
→ Ativar o serviço
→ URL gerada: https://<sap-host>:<port>/sap/opu/odata/sap/ZWM_PRODUCT_SRV/
```

---

### 3. Segurança na API SAP

#### a) Autenticação — OAuth 2.0 (recomendado)
```
SAP BTP → Security → OAuth 2.0 Configuration
→ Criar OAuth Client:
   - Client ID: spring_wms_client
   - Grant Type: Client Credentials
   - Scope: ZWM_PRODUCT_SRV
→ Token URL: https://<btp-host>/oauth/token
```

#### b) Autenticação — Basic Auth (alternativa simples)
```
Transação: SU01
→ Criar usuário técnico: WMS_API_USER
→ Tipo: System (S)
→ Atribuir perfil: Z_WMS_API_PROFILE
```

#### c) Autorização — Perfis e Roles
```
Transação: PFCG
→ Criar Role: Z_WMS_API_ROLE
→ Atribuir objeto de autorização: S_SERVICE
   - SRV_NAME: ZWM_PRODUCT_SRV
   - SRV_TYPE: HT (HTTP Service)
→ Gerar e atribuir ao usuário técnico
```

#### d) HTTPS / SSL
```
Transação: STRUST
→ Importar certificado SSL
→ Ativar HTTPS no ICM (Internet Communication Manager)
Transação: SMICM → HTTPS ativo na porta 443
```

---

### 4. Testar a API no SAP
```
Transação: /IWFND/GW_CLIENT
→ GET /ProductSet          → lista produtos
→ GET /ProductSet('P001')  → busca por ID
→ POST /ProductSet         → cria produto
→ PUT /ProductSet('P001')  → atualiza
→ DELETE /ProductSet('P001') → remove
```

---

## 🔶 Parte 2 — Spring Boot: Autenticar e enviar POST para SAP

### 1. Dependências — `pom.xml`

```xml
<dependencies>
    <!-- HTTP Client -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-webflux</artifactId>
    </dependency>
    <!-- OAuth2 Client -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-oauth2-client</artifactId>
    </dependency>
    <!-- Lombok -->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>
</dependencies>
```

---

### 2. Configuração — `application.yml`

```yaml
sap:
  base-url: https://<sap-host>/sap/opu/odata/sap/ZWM_PRODUCT_SRV
  oauth:
    token-url: https://<btp-host>/oauth/token
    client-id: spring_wms_client
    client-secret: your-secret
    scope: ZWM_PRODUCT_SRV
  # Alternativa Basic Auth:
  basic:
    username: WMS_API_USER
    password: your-password
```

---

### 3. Model — `ProductRequest.java`

```java
@Data
@Builder
public class ProductRequest {
    private String productID;
    private String description;
    private BigDecimal quantity;
    private String unitOfMeasure;
    private String storageLocation;
}
```

---

### 4. Serviço OAuth2 — `SapAuthService.java`

```java
@Service
@RequiredArgsConstructor
public class SapAuthService {

    @Value("${sap.oauth.token-url}")
    private String tokenUrl;

    @Value("${sap.oauth.client-id}")
    private String clientId;

    @Value("${sap.oauth.client-secret}")
    private String clientSecret;

    @Value("${sap.oauth.scope}")
    private String scope;

    private final WebClient webClient = WebClient.create();

    public String getAccessToken() {
        return webClient.post()
            .uri(tokenUrl)
            .contentType(MediaType.APPLICATION_FORM_URLENCODED)
            .body(BodyInserters.fromFormData("grant_type", "client_credentials")
                .with("client_id", clientId)
                .with("client_secret", clientSecret)
                .with("scope", scope))
            .retrieve()
            .bodyToMono(JsonNode.class)
            .map(json -> json.get("access_token").asText())
            .block();
    }
}
```

---

### 5. Serviço de Integração — `SapProductService.java`

```java
@Service
@RequiredArgsConstructor
@Slf4j
public class SapProductService {

    @Value("${sap.base-url}")
    private String sapBaseUrl;

    private final SapAuthService authService;
    private final WebClient.Builder webClientBuilder;

    public void createProduct(ProductRequest product) {
        String token = authService.getAccessToken();

        WebClient client = webClientBuilder
            .baseUrl(sapBaseUrl)
            .defaultHeader(HttpHeaders.AUTHORIZATION, "Bearer " + token)
            .defaultHeader(HttpHeaders.CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE)
            .defaultHeader("Accept", "application/json")
            .build();

        // OData requer X-CSRF-Token — buscar antes do POST
        String csrfToken = fetchCsrfToken(client);

        client.post()
            .uri("/ProductSet")
            .header("X-CSRF-Token", csrfToken)
            .bodyValue(product)
            .retrieve()
            .onStatus(HttpStatusCode::isError, response ->
                response.bodyToMono(String.class)
                    .flatMap(body -> Mono.error(
                        new RuntimeException("SAP API Error: " + body))))
            .bodyToMono(Void.class)
            .doOnSuccess(v -> log.info("Product {} created in SAP WMS", product.getProductID()))
            .block();
    }

    // OData exige CSRF Token para operações de escrita (POST/PUT/DELETE)
    private String fetchCsrfToken(WebClient client) {
        return client.get()
            .uri("/ProductSet?$top=1")
            .header("X-CSRF-Token", "Fetch")
            .retrieve()
            .toBodilessEntity()
            .map(response -> response.getHeaders()
                .getFirst("X-CSRF-Token"))
            .block();
    }
}
```

---

### 6. Controller — `ProductController.java`

```java
@RestController
@RequestMapping("/api/products")
@RequiredArgsConstructor
public class ProductController {

    private final SapProductService sapProductService;

    @PostMapping
    public ResponseEntity<String> createProduct(@RequestBody @Valid ProductRequest request) {
        sapProductService.createProduct(request);
        return ResponseEntity.status(HttpStatus.CREATED)
            .body("Product sent to SAP WMS successfully");
    }
}
```

---

## 🔑 Pontos de Atenção

| Tópico | Detalhe |
|---|---|
| **X-CSRF-Token** | Obrigatório em toda escrita OData SAP — sempre buscar antes do POST |
| **OAuth vs Basic** | OAuth 2.0 é recomendado para produção; Basic Auth apenas para dev/teste |
| **OData vs REST puro** | SAP Gateway usa OData; para REST puro considere **SAP Integration Suite** |
| **SAP BTP** | Ideal para expor APIs modernas com segurança gerenciada |
| **Certificado SSL** | Em produção, configurar `SSLContext` no `WebClient` com o certificado SAP |
| **Retry/Circuit Breaker** | Adicionar **Resilience4j** para tolerância a falhas na integração |

# Adapta
## Visão geral

Abaixo está um guia técnico completo para integrar uma aplicação Spring Boot com o SAP para envio de dados de Produto (e, por extensão, Estoque), visando atualização no WMS do SAP. O foco recai em:

- Expor no SAP uma API RESTful (tipicamente OData) segura, com CRUD para Product.
- Consumir essa API a partir do Spring Boot com boas práticas de segurança, robustez e performance.
- Exemplos práticos de configuração e código.

Observação importante: quando possível, avalie usar uma API padrão do SAP (ex.: Product Master) em vez de construir uma API customizada. Se não houver API padrão que atenda, use RAP (ABAP RESTful Application Programming Model) para criar uma API customizada OData V4. Os passos abaixo contemplam ambos os cenários.

---

## 1) No SAP

### 1.a) Etapas para criar uma API RESTful CRUD (entidade Product)

Existem duas abordagens principais:

1) Usar API padrão (recomendado quando existir)
- Exemplo: S/4HANA tem a API “Product Master” (serviço OData v2), com entidade A_Product, que já provê leitura e, em alguns cenários, criação/atualização dependendo da edição do S/4HANA e autorização.
- Passos:
  - Ativar/checar o serviço no SAP Gateway (on‑prem: transação /IWFND/MAINT_SERVICE; cloud: Communication Arrangement).
  - Publicar o endpoint e garantir que o canal HTTPS esteja ativo.
  - Criar usuários de comunicação (Cloud) ou usuários técnicos (on‑prem), papéis e perfis autorizativos.
  - Configurar autenticação (OAuth 2.0 Client Credentials em Cloud; Basic/OAuth em on‑prem) e escopos.
  - Testar via SAP API Hub ou via o próprio endpoint OData.

2) Criar API customizada com ABAP RAP (OData V4)
- Passos no ADT (ABAP Development Tools no Eclipse):
  1. Modelagem de Dados (CDS):
     - Criar CDS View Entity Z_I_Product com chaves e campos de negócio (ProductId, SKU, Name, Description, Unit, Price, WarehouseId etc.).
     - Definir anotação @AccessControl para controle de acesso (se usar DCL).
  2. Comportamento (Behavior Definition/Implementation):
     - Criar Behavior Definition (BDEF) para habilitar create, update, delete e read.
     - Implementar Behavior Implementation (classe ABAP) para regras de negócio, validações e persistência.
  3. Service Definition:
     - Definir a Service Definition incluindo a entidade Product (ex.: expose Z_I_Product as Products).
  4. Service Binding:
     - Criar Service Binding (OData V4 UI/Service). Publicar o serviço e capturar o caminho do endpoint.
  5. Ativação e Testes:
     - Ativar objetos, usar o preview do ADT para inspecionar a $metadata e testar operações CRUD.
  6. Segurança e Autorização:
     - Definir objetos de autorização (PFCG) e perfis.
     - Amarrar escopos/roles à exposição do serviço (IAM em Cloud; PFCG/roles em on‑prem).
  7. Gateway/ICF:
     - Garantir que o serviço está acessível via ICF (SICF) e que HTTPS está ativo (STRUST/ICM).

> Dica: Se o WMS estiver em SAP EWM e você realmente precisa atualizar estoques, verifique APIs padrão de estoque/warehouse tasks. Caso não cubram o fluxo, crie também uma entidade de “Inventory/Stock” via RAP ou componha via API Management.

### 1.b) Configurações de segurança (autenticação e autorização)

- Protocolo:
  - Somente HTTPS (TLS 1.2+). Desabilite cifras fracas. Em on‑prem, configure em STRUST/ICM. Em Cloud, por padrão é HTTPS.
- Autenticação:
  - SAP S/4HANA Cloud e BTP: preferencialmente OAuth 2.0 Client Credentials (máquina‑para‑máquina).
  - On‑prem (Gateway): pode-se usar Basic Auth sobre HTTPS ou OAuth 2.0 via SAP Identity Authentication/IdP.
- Autorização:
  - Definir roles e escopos: “Product.Read”, “Product.Write”, “Product.Delete” etc.
  - Atribuir papéis a usuários técnicos (on‑prem) ou a “Communication Users/Arrangements” (Cloud).
- CSRF:
  - Para operações de escrita (POST/PUT/PATCH/DELETE) em OData, use X-CSRF-Token.
  - Fluxo: primeiro requisitar token com header X-CSRF-Token: Fetch; depois enviar a mutação com header X-CSRF-Token: <token> e o cookie de sessão retornado.
- Proteções adicionais:
  - Rate limiting e quotas via SAP API Management (se expor pela camada de API).
  - IP allowlist.
  - Logging/auditoria e mascaramento de dados sensíveis.
  - CORS conforme necessário (se front-ends externos consumirem a API).
  - Conteúdo: aceite e responda application/json; rejeite formatos inesperados.

### 1.c) Configurar e implementar a API no SAP com segurança e performance

- Performance (RAP/OData):
  - Use $select para reduzir colunas devolvidas.
  - Paginação ($top/$skip).
  - ETags e If-Match para controle concorrente e caching.
  - $batch para operações em lote quando precisar criar/atualizar muitos registros.
  - Pushdown no banco: CDS, AMDP quando necessário, índices nas tabelas.
  - GZIP habilitado no ICM (compressão HTTP).
- Hardening:
  - Validar payloads no Behavior Implementation (tamanhos máximos, formatos, ranges).
  - Sanitizar entradas (evitar injeções em consultas dinâmicas).
  - Separar usuários de leitura e escrita (princípio do menor privilégio).
- Observabilidade:
  - Ativar logs de acesso/erros no ICM/Gateway.
  - Correlação: suportar cabeçalho X-Correlation-Id (propagar a partir do cliente).
- Publicação do endpoint:
  - Exemplo genérico de endpoint OData V4 (RAP):
    - GET/POST: https://<host>/sap/opu/odata4/<namespace>/<service>/<version>/Products
    - PATCH/DELETE/GET por chave: https://<host>/.../Products('<ProductId>')
  - OData V2 (API padrão, ex. Product Master):
    - GET/POST: https://<host>/sap/opu/odata/sap/API_PRODUCT_SRV/A_Product

Links úteis:
- ABAP RAP Overview {target="_blank"}: https://help.sap.com/docs/abap-cloud/abap-rap
- SAP API Business Hub {target="_blank"}: https://api.sap.com/

---

## 2) Na aplicação Spring Boot

### 2.a) Etapas para autenticar na API do SAP

Escolha 1: OAuth 2.0 Client Credentials (recomendado em Cloud/BTP/S/4HANA Cloud ou via API Management)
- Fluxo:
  - Obter access_token no token endpoint (client_id, client_secret).
  - Incluir Authorization: Bearer <token> em todas as chamadas à API.
- Benefícios: rotação de segredos, escopos, revogação centralizada.

Escolha 2: Basic Auth (possível em on‑prem)
- Fluxo:
  - Enviar Authorization: Basic <base64(user:password)> sobre HTTPS.
- Notas: Menos flexível; use contas técnicas com privilégios mínimos, segredo guardado em cofre.

CSRF (independente do método de autenticação):
- Para POST/PUT/PATCH/DELETE em OData, requisitar token:
  - Enviar uma chamada preliminar (ex.: GET em $metadata ou HEAD) com X-CSRF-Token: Fetch.
  - Reutilizar o token X-CSRF-Token e o cookie de sessão (ex.: SAP_SESSIONID) na chamada de escrita.

TLS:
- Importe o certificado do servidor SAP em um truststore (JKS/P12).
- Configure o WebClient/HTTP client para confiar apenas no truststore (não desative validações!).

Timeouts, retries e resiliência:
- Timeouts de conexão, leitura e escrita.
- Retry com backoff para 429/503/504 (não retentar em 4xx de validação).
- Circuit breaker e bulkhead se necessário.

### 2.b) Exemplo de código (Spring Boot) – POST para criar Product

A seguir, um exemplo com:
- WebClient (preferido) com timeouts.
- OAuth2 Client Credentials.
- Fluxo de CSRF para OData.
- Tratamento de erros com parsing do erro OData.
- Alternativa com Basic Auth (comentada).

Estrutura minimalista:
- application.yml
- DTOs: ProductRequest, SapODataError
- Configuração: WebClientConfig (inclui SSL, OAuth, timeouts)
- Cliente: SapProductClient (getCsrfToken + createProduct)
- Exemplo de uso no Service/Controller

#### application.yml (exemplo)

```yaml
server:
  port: 8080

sap:
  base-url: "https://sap.example.com/sap/opu/odata4/zns/product/0001"
  product-path: "/Products"
  # Para OData v2 (API padrão), algo como:
  # base-url: "https://sap.example.com/sap/opu/odata/sap/API_PRODUCT_SRV"
  # product-path: "/A_Product"

security:
  oauth2:
    client:
      sap:
        token-uri: "https://sap-iam.example.com/oauth/token"
        client-id: "${SAP_CLIENT_ID}"
        client-secret: "${SAP_CLIENT_SECRET}"
        scope: "Product.Write"

http:
  connect-timeout-ms: 5000
  read-timeout-ms: 10000
  write-timeout-ms: 10000

ssl:
  enabled: true
  truststore:
    path: "classpath:truststore/sap-truststore.p12"
    password: "${TRUSTSTORE_PASSWORD}"
    type: "PKCS12"
```

Coloque os segredos (client-secret, senhas) em variáveis de ambiente/cofre.

#### DTOs

```java
// Produto enviado para criação no SAP (ajuste campos aos do serviço SAP real)
public record ProductRequest(
    String ProductId,
    String SKU,
    String Name,
    String Description,
    String Unit,
    String WarehouseId,
    java.math.BigDecimal Price,
    java.time.OffsetDateTime LastUpdated
) {}

// Erro OData típico (simplificado)
public class SapODataError {
  public Error error;
  public static class Error {
    public String code;
    public Message message;
  }
  public static class Message {
    public String lang;
    public String value;
  }
}
```

Observação: No serviço padrão API_PRODUCT_SRV (OData v2), o payload e campos são diferentes (ex.: Product, ProductType, BaseUnit, ProductGroup etc.). Adeque o DTO aos metadados ($metadata) do seu endpoint.

#### Configurando WebClient com OAuth2, SSL e timeouts

```java
import io.netty.handler.ssl.SslContextBuilder;
import io.netty.handler.ssl.util.InsecureTrustManagerFactory;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.client.reactive.ReactorClientHttpConnector;
import org.springframework.util.StringUtils;
import org.springframework.web.reactive.function.client.ExchangeFilterFunction;
import org.springframework.web.reactive.function.client.WebClient;
import reactor.netty.http.client.HttpClient;
import reactor.netty.tcp.TcpClient;

import javax.net.ssl.KeyManagerFactory;
import javax.net.ssl.TrustManagerFactory;
import java.io.InputStream;
import java.security.KeyStore;
import java.time.Duration;
import java.util.Map;

@Configuration
public class WebClientConfig {

  @Value("${sap.base-url}")
  private String sapBaseUrl;

  @Value("${http.connect-timeout-ms:5000}")
  private int connectTimeoutMs;

  @Value("${http.read-timeout-ms:10000}")
  private int readTimeoutMs;

  @Value("${http.write-timeout-ms:10000}")
  private int writeTimeoutMs;

  @Value("${ssl.enabled:true}")
  private boolean sslEnabled;

  @Value("${ssl.truststore.path:}")
  private String truststorePath;

  @Value("${ssl.truststore.password:}")
  private String truststorePassword;

  @Value("${ssl.truststore.type:PKCS12}")
  private String truststoreType;

  @Bean
  public WebClient sapWebClient(WebClient.Builder builder) throws Exception {
    HttpClient httpClient = HttpClient.create()
        .responseTimeout(Duration.ofMillis(readTimeoutMs));

    if (sslEnabled) {
      httpClient = httpClient.secure(sslContextSpec -> {
        try {
          if (StringUtils.hasText(truststorePath)) {
            KeyStore ts = KeyStore.getInstance(truststoreType);
            try (InputStream is = getClass().getResourceAsStream(truststorePath.startsWith("classpath:") ?
                truststorePath.replace("classpath:", "/") : truststorePath)) {
              ts.load(is, truststorePassword.toCharArray());
            }
            TrustManagerFactory tmf = TrustManagerFactory.getInstance(TrustManagerFactory.getDefaultAlgorithm());
            tmf.init(ts);
            sslContextSpec.sslContext(SslContextBuilder.forClient().trustManager(tmf).build());
          } else {
            // Evite isto em produção; aqui só como fallback ilustrativo
            sslContextSpec.sslContext(SslContextBuilder.forClient()
                .trustManager(InsecureTrustManagerFactory.INSTANCE).build());
          }
        } catch (Exception e) {
          throw new RuntimeException("Erro configurando SSL", e);
        }
      });
    }

    return builder
        .baseUrl(sapBaseUrl)
        .clientConnector(new ReactorClientHttpConnector(httpClient))
        .defaultHeader("Accept", "application/json")
        .filter(loggingFilter())
        .build();
  }

  private ExchangeFilterFunction loggingFilter() {
    return ExchangeFilterFunction.ofRequestProcessor(clientRequest -> {
      // Log enxuto, sem segredos
      System.out.println("SAP Request: " + clientRequest.method() + " " + clientRequest.url());
      return reactor.core.publisher.Mono.just(clientRequest);
    });
  }
}
```

Autenticação OAuth2: podemos obter o token com um pequeno TokenClient e aplicar um ExchangeFilterFunction para adicionar o header Authorization.

```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.http.MediaType;
import org.springframework.stereotype.Component;
import org.springframework.web.reactive.function.client.WebClient;

import java.time.Instant;
import java.util.Map;

@Component
public class OAuthTokenClient {

  private final WebClient http;
  private final String tokenUri;
  private final String clientId;
  private final String clientSecret;
  private final String scope;

  private volatile String cachedToken;
  private volatile Instant expiresAt = Instant.EPOCH;

  public OAuthTokenClient(
      WebClient.Builder builder,
      @Value("${security.oauth2.client.sap.token-uri}") String tokenUri,
      @Value("${security.oauth2.client.sap.client-id}") String clientId,
      @Value("${security.oauth2.client.sap.client-secret}") String clientSecret,
      @Value("${security.oauth2.client.sap.scope:}") String scope
  ) {
    this.http = builder.baseUrl(tokenUri).build();
    this.tokenUri = tokenUri;
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.scope = scope;
  }

  public synchronized String getAccessToken() {
    if (cachedToken != null && Instant.now().isBefore(expiresAt.minusSeconds(30))) {
      return cachedToken;
    }
    var bodySpec = http.post()
        .contentType(MediaType.APPLICATION_FORM_URLENCODED)
        .bodyValue("grant_type=client_credentials&client_id=" + clientId +
            "&client_secret=" + clientSecret +
            (scope != null && !scope.isBlank() ? "&scope=" + scope : ""));

    Map<?, ?> token = bodySpec.retrieve()
        .bodyToMono(Map.class)
        .block();

    String accessToken = (String) token.get("access_token");
    Number expiresIn = (Number) token.get("expires_in");
    cachedToken = accessToken;
    expiresAt = Instant.now().plusSeconds(expiresIn != null ? expiresIn.longValue() : 300);
    return cachedToken;
  }
}
```

Filter para aplicar o Bearer Token:

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.reactive.function.client.ExchangeFilterFunction;

@Configuration
public class OAuthFilterConfig {

  @Bean
  public ExchangeFilterFunction oauthBearerFilter(OAuthTokenClient tokenClient) {
    return ExchangeFilterFunction.ofRequestProcessor(req -> {
      return reactor.core.publisher.Mono.just(
          ClientRequest.from(req)
              .headers(h -> h.setBearerAuth(tokenClient.getAccessToken()))
              .build()
      );
    });
  }
}
```

E injete esse filtro no WebClient (no bean sapWebClient), por exemplo:

```java
// dentro de sapWebClient(...)
return builder
    .baseUrl(sapBaseUrl)
    .clientConnector(new ReactorClientHttpConnector(httpClient))
    .defaultHeader("Accept", "application/json")
    .filter(oauthBearerFilter) // injete esse bean aqui
    .filter(loggingFilter())
    .build();
```

Alternativa Basic Auth (se optar):
- Em vez do filter acima, use `.defaultHeaders(h -> h.setBasicAuth(user, password))` no WebClient.

#### Cliente para SAP: obtendo CSRF e criando Product

```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseCookie;
import org.springframework.stereotype.Component;
import org.springframework.web.reactive.function.BodyInserters;
import org.springframework.web.reactive.function.client.ClientResponse;
import org.springframework.web.reactive.function.client.WebClient;
import reactor.core.publisher.Mono;

import java.net.HttpCookie;
import java.util.List;
import java.util.Map;
import java.util.Optional;

@Component
public class SapProductClient {

  private final WebClient sapWebClient;
  private final String productPath;

  public SapProductClient(
      WebClient sapWebClient,
      @Value("${sap.product-path}") String productPath
  ) {
    this.sapWebClient = sapWebClient;
    this.productPath = productPath;
  }

  // 1) Obter X-CSRF-Token e cookies de sessão
  public CsrfSession fetchCsrf() {
    ClientResponse resp = sapWebClient.get()
        .uri(uriBuilder -> uriBuilder
            // Para OData V4: chamar $metadata ou a entidade com $top=1
            .path("/$metadata")
            .build()
        )
        .header("X-CSRF-Token", "Fetch")
        .exchangeToMono(Mono::just)
        .block();

    if (resp == null) {
      throw new RuntimeException("Sem resposta do SAP ao requisitar CSRF");
    }

    String token = resp.headers().asHttpHeaders().getFirst("X-CSRF-Token");
    List<String> setCookies = resp.headers().asHttpHeaders().getOrDefault(HttpHeaders.SET_COOKIE, List.of());
    if (token == null || setCookies.isEmpty()) {
      // Alguns endpoints retornam o token apenas em mutação; em OData geralmente funciona no $metadata
      throw new RuntimeException("Não foi possível obter CSRF Token e/ou cookies de sessão do SAP");
    }

    // Coletar cookies para reenviar
    String cookieHeader = String.join("; ",
        setCookies.stream()
            .map(ResponseCookie::fromClientResponse)
            .map(Object::toString)
            .toList()
    );

    return new CsrfSession(token, cookieHeader);
  }

  // 2) Criar Product (POST)
  public Map<?, ?> createProduct(ProductRequest product) {
    CsrfSession csrf = fetchCsrf();

    ClientResponse resp = sapWebClient.post()
        .uri(productPath) // ex.: "/Products" ou "/A_Product"
        .contentType(MediaType.APPLICATION_JSON)
        .header("X-CSRF-Token", csrf.token())
        .header(HttpHeaders.COOKIE, csrf.cookieHeader())
        .header("X-Correlation-Id", Correlation.id()) // se você usa correlação
        .body(BodyInserters.fromValue(product))
        .exchangeToMono(Mono::just)
        .block();

    if (resp == null) {
      throw new RuntimeException("Sem resposta do SAP ao criar Product");
    }

    if (resp.statusCode().is2xxSuccessful()) {
      return resp.bodyToMono(Map.class).block();
    }

    // Tratamento de erro OData
    String raw = resp.bodyToMono(String.class).block();
    SapODataError err = null;
    try {
      err = JsonUtils.fromJson(raw, SapODataError.class); // implemente um util com Jackson
    } catch (Exception ignore) {}

    String msg = Optional.ofNullable(err)
        .map(e -> e.error != null && e.error.message != null ? e.error.message.value : null)
        .orElse(raw);

    if (resp.statusCode().value() == 409) {
      throw new ConflictException("Conflito ao criar Product: " + msg);
    } else if (resp.statusCode().is4xxClientError()) {
      throw new ClientErrorException("Erro 4xx do SAP: " + msg);
    } else if (resp.statusCode().is5xxServerError()) {
      throw new ServerErrorException("Erro 5xx do SAP: " + msg);
    } else {
      throw new RuntimeException("Erro inesperado do SAP (" + resp.statusCode().value() + "): " + msg);
    }
  }

  public record CsrfSession(String token, String cookieHeader) {}
}
```

Utilitário simples para JSON (Jackson):

```java
import com.fasterxml.jackson.databind.ObjectMapper;

public class JsonUtils {
  private static final ObjectMapper MAPPER = new ObjectMapper();
  public static <T> T fromJson(String json, Class<T> type) {
    try {
      return MAPPER.readValue(json, type);
    } catch (Exception e) {
      throw new RuntimeException("Falha ao parsear JSON", e);
    }
  }
}
```

Correlation ID (opcional):

```java
import java.util.UUID;

public class Correlation {
  private static final ThreadLocal<String> ID = new ThreadLocal<>();

  public static String id() {
    String v = ID.get();
    if (v == null) {
      v = UUID.randomUUID().toString();
      ID.set(v);
    }
    return v;
  }

  public static void clear() {
    ID.remove();
  }
}
```

Exemplo de uso em um Service/Controller:

```java
import org.springframework.stereotype.Service;

@Service
public class ProductService {

  private final SapProductClient sapProductClient;

  public ProductService(SapProductClient sapProductClient) {
    this.sapProductClient = sapProductClient;
  }

  public Map<?, ?> createProduct(ProductRequest req) {
    // Validações locais
    if (req.ProductId() == null || req.ProductId().isBlank()) {
      throw new IllegalArgumentException("ProductId é obrigatório");
    }
    // Idempotência (opcional): consultar antes; ou usar chave natural como ProductId
    return sapProductClient.createProduct(req);
  }
}
```

Tratamento de timeouts e retries:
- Você pode envolver as chamadas com Resilience4j:

```xml
<!-- pom.xml -->
<dependency>
  <groupId>io.github.resilience4j</groupId>
  <artifactId>resilience4j-spring-boot3</artifactId>
  <version>2.2.0</version>
</dependency>
```

```java
// Exemplo: retry apenas para 503/504/429
import io.github.resilience4j.retry.annotation.Retry;
import org.springframework.web.reactive.function.client.WebClientResponseException;

@Retry(name = "sapCreateProduct")
public Map<?, ?> createProductWithRetry(ProductRequest req) {
  try {
    return sapProductClient.createProduct(req);
  } catch (WebClientResponseException.TooManyRequests |
           WebClientResponseException.ServiceUnavailable |
           WebClientResponseException.GatewayTimeout e) {
    throw e; // Retentável
  } catch (Exception e) {
    throw e; // Não retentar por padrão
  }
}
```

application.yml (Resilience4j):

```yaml
resilience4j:
  retry:
    instances:
      sapCreateProduct:
        max-attempts: 3
        wait-duration: 1s
        retry-exceptions:
          - org.springframework.web.reactive.function.client.WebClientResponseException$TooManyRequests
          - org.springframework.web.reactive.function.client.WebClientResponseException$ServiceUnavailable
          - org.springframework.web.reactive.function.client.WebClientResponseException$GatewayTimeout
```

Observações sobre OData e CSRF:
- Alguns proxies/API Management podem gerenciar CSRF; caso o endpoint finalize a necessidade do token, remova a etapa de fetch. Em geral, serviços OData no SAP exigem X-CSRF-Token para métodos mutáveis.
- Para OData v2, o caminho do metadata é /$metadata sob o serviço; para v4 (RAP) também.

Alternativa com RestTemplate (se preferir, embora seja legado):

```java
RestTemplate rest = new RestTemplateBuilder()
    .setConnectTimeout(Duration.ofMillis(5000))
    .setReadTimeout(Duration.ofMillis(10000))
    .build();
// Obtenha CSRF via exchange() e reenvie cookies + header nas mutações
```

### 3) Considerações e melhores práticas

- HTTPS obrigatório:
  - Verifique o handshake TLS e a cadeia de certificação em homologação antes da produção.
- Padrão de payloads e tipos:
  - Use application/json; verifique se o serviço aceita e retorna JSON (OData v2/v4 normalmente suportam).
- Tratamento de erro do SAP:
  - OData retorna um envelope com “error.code” e “error.message.value”.
  - Mapeie 400/404/409, 5xx. Não retentar 4xx de validação.
- Idempotência:
  - Para criação, considere:
    - Usar chave natural (ProductId) e tratar 409 (conflito) como já criado.
    - Ou usar um header de idempotência (se exposto via API Management) com deduplicação no SAP.
- Concorrência:
  - Utilize ETag/If-Match em updates (PATCH) para evitar sobregravações.
- Performance no cliente:
  - Conexões persistentes (HTTP/1.1 keep-alive ou HTTP/2, se suportado).
  - GZIP habilitado.
  - Batch ($batch) para grandes volumes.
- Observabilidade:
  - Logue o X-Correlation-Id em ambos os lados para rastrear fluxos.
  - Métricas e alertas (latência, taxa de erro, timeouts).
- Segurança de segredos:
  - Nunca comitar segredos. Use Vault/Secrets Manager/KMS.
  - Rotacione credenciais periodicamente.
- Padrões de código:
  - DTOs imutáveis, validações com Bean Validation.
  - Camadas separadas (Controller/Service/Client).
  - Testes integrados contra sandbox/API Hub quando disponível.

---

## Exemplos de chamadas CRUD (genéricos)

Supondo RAP OData V4, base-url: https://sap.example.com/sap/opu/odata4/zns/product/0001

- Create (POST):
  - POST /Products
  - Headers: Authorization: Bearer <token>, X-CSRF-Token: <token>, Cookie: <sessão>, Content-Type: application/json
  - Body (exemplo adaptado ao seu metadado):
    {
      "ProductId": "PRD-001",
      "SKU": "SKU-001",
      "Name": "Produto X",
      "Description": "Descrição",
      "Unit": "EA",
      "WarehouseId": "WH01",
      "Price": 99.90,
      "LastUpdated": "2026-02-20T10:00:00Z"
    }

- Read (GET):
  - GET /Products('PRD-001')?$select=ProductId,Name,SKU
  - GET /Products?$filter=WarehouseId eq 'WH01'&$top=50&$skip=0

- Update (PATCH):
  - PATCH /Products('PRD-001')
  - Headers: X-CSRF-Token, If-Match: <ETag>
  - Body:
    { "Price": 109.90 }

- Delete (DELETE):
  - DELETE /Products('PRD-001')
  - Headers: X-CSRF-Token, If-Match: <ETag>

Para OData V2 (API_PRODUCT_SRV), adapte endpoints e nomes de campos conforme a $metadata.

---

## Estoque (WMS) – Nota rápida

- Se você também precisa atualizar o estoque, verifique se há API padrão do EWM (por exemplo, para Quantidades, Warehouse Tasks, Stock Change). Caso contrário, crie um serviço RAP para uma entidade Inventory/Stock (chaves como ProductId + WarehouseId + StorageBin etc.), com as mesmas diretrizes de segurança e performance.
- Consistência entre Produto e Estoque:
  - Se forem dois endpoints, trate a consistência via “saga” (compensações) ou use $batch transacional se o serviço suportar.
  - Idempotência é crítica para eventos de estoque (evitar contagens duplicadas).

---

## Checklist rápido

- SAP
  - [ ] Usar API padrão (ex.: Product Master) OU publicar RAP OData V4 para Product/Inventory.
  - [ ] Habilitar HTTPS com TLS 1.2+ e cifras fortes.
  - [ ] Configurar autenticação (OAuth 2.0 Client Credentials preferencial).
  - [ ] Definir roles/escopos e autorização por operação.
  - [ ] Habilitar/usar X-CSRF-Token para métodos mutáveis.
  - [ ] Performance: $select/$filter/paginação/$batch/GZIP, ETags.
  - [ ] Logs/auditoria, correlacionar requisições.

- Spring Boot
  - [ ] WebClient com timeouts, TLS truststore configurado.
  - [ ] OAuth2 Token Client ou Basic Auth.
  - [ ] Fluxo CSRF: fetch + reuso de token e cookies.
  - [ ] Tratamento de erros OData (parse de “error.message.value”).
  - [ ] Retries com backoff para 429/503/504; não retentar 4xx de validação.
  - [ ] Observabilidade, correlação, segurança de segredos.

---

## Conclusão

- No SAP, prefira APIs padrão. Se não houver, crie um serviço OData V4 com RAP, com CRUD completo para Product, segurança via OAuth 2.0, HTTPS, CSRF, roles/escopos, e boas práticas de performance (ETags, $select, paginação).
- No Spring Boot, use WebClient com OAuth2 Client Credentials, configure SSL com truststore, implemente o fluxo de CSRF, trate erros OData adequadamente, aplique timeouts/retries com backoff e mantenha logs e correlações.
- Para Estoque/WMS, avalie APIs padrão do EWM; na ausência, modele e exponha uma entidade Inventory/Stock com os mesmos padrões.

Se quiser, posso:
- Validar a $metadata do seu endpoint para ajustar exatamente o DTO e os campos.
- Fornecer um exemplo usando a API padrão API_PRODUCT_SRV com campos reais.
- Montar um fluxo $batch para criação em massa com idempotência.

