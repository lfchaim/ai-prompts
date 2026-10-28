# Prompt
Atuar como um Software Engineer sênior com mais de 25 anos de experiência, especializado em desenvolvimento de aplicações Java com Spring Boot.

Sua missão é criar uma documentação de orientações técnicas completa e organizada para desenvolvimento de aplicações, cobrindo, no mínimo:

1. Spring Boot
2. API RESTful
3. Lean Code
4. SOLID
5. Qualidade de Software
6. Testes Automatizados
7. Boas práticas de arquitetura e manutenção de código
8. Armadilhas e problemas comuns de implementação

Inclua também orientações práticas para evitar problemas frequentes, como:
- uso do Lombok
- problemas de performance com ORM
- N+1
- Lazy Loading
- Eager Loading

Estruture o conteúdo de forma clara, didática e técnica, com seções, subtópicos e explicações objetivas. Priorize recomendações acionáveis, exemplos quando úteis, alertas sobre riscos e boas práticas que melhorem legibilidade, manutenibilidade, desempenho e testabilidade.

Antes de redigir a documentação completa, se considerar útil, comece montando uma lista de tópicos e sub-tópicos para validação do escopo, a fim de verificar se está completo ou se falta algo.

# Adapta (ONE Smart)
Montei o escopo como proposta de estrutura antes de redigir. Segue o índice completo com seções e subtópicos.

## Bloco 0 — Convenções e stack de referência
- Versões de referência: Java 21 LTS (records, pattern matching, virtual threads) e Spring Boot 3.4+; notas de migração para Spring Boot 4.1.x / Java 25
- Estrutura de projeto em Clean Architecture: `domain` / `application` / `infrastructure` / `shared`
- Regra de dependência: `domain` e `application` não importam nada de `infrastructure`
- Organização por feature vs por camada

## Bloco 1 — Spring Boot essencial
1. Injeção de dependência exclusivamente por construtor
2. `@ConfigurationProperties` tipadas e validadas
3. Profiles e externalização de configuração
4. Auto-configuração: entender antes de sobrescrever
5. Ciclo de vida do contexto, escopos de bean, proxies
6. Actuator, health/readiness, graceful shutdown
7. Anti-padrões: field injection, `@Component` em excesso, `ApplicationContext` como service locator

## Bloco 2 — API RESTful
1. Modelagem de recursos, hierarquia e uso correto dos verbos
2. Status codes e semântica (201 + Location, 202, 204, 409, 422)
3. Versionamento e política de deprecação
4. Paginação, filtros, ordenação e contrato de listagem
5. Bean Validation, grupos e separação entre validação de forma e de negócio
6. Tratamento de erros com `ProblemDetail` (RFC 9457), catálogo de códigos e correlation ID
7. Idempotência: PUT vs PATCH vs POST, `Idempotency-Key`, retries
8. OpenAPI: contrato primeiro vs código primeiro
9. CORS, autenticação vs autorização, rate limiting
10. Hipermedia (HATEOAS): quando agrega e quando é peso morto

## Bloco 3 — Lean Code
1. YAGNI e custo do código especulativo
2. Records, imutabilidade e expressividade
3. Guard clauses, early return e redução de aninhamento
4. Métodos pequenos e nomes que revelam intenção
5. Remoção de cerimônia: mapeamentos redundantes, camadas vazias, DTO de DTO
6. DRY vs duplicação acidental (regra de três)
7. Quando **não** abstrair — o custo da indireção
8. Código morto e dívida técnica

## Bloco 4 — SOLID aplicado ao ecossistema Spring
1. SRP e granularidade de use cases — o "service Deus"
2. OCP com Strategy e portas, eliminando cadeias de `if/else` sobre enum
3. LSP em hierarquias de entidades e exceções
4. ISP: repositórios específicos por caso de uso vs genéricos
5. DIP: portas/adaptadores e domínio puro
6. Detecção automática de violações com ArchUnit

## Bloco 5 — Arquitetura e manutenibilidade
1. Limites de camada e direção das dependências
2. Fronteiras transacionais: onde a transação começa e termina
3. Monólito modular, Spring Modulith e extração de serviços
4. Eventos de domínio e acoplamento
5. ADRs e documentação viva
6. Estratégia de evolução: strangler, deprecação, refactoring seguro

## Bloco 6 — Qualidade de software
1. Análise estática no build: Checkstyle, SpotBugs, PMD, ErrorProne, SonarQube
2. Métricas que importam (complexidade, acoplamento) e o que cobertura **não** mede
3. Logs estruturados, níveis, correlation ID e mascaramento de dados sensíveis
4. Observabilidade: Micrometer, OpenTelemetry, tracing, métricas de negócio
5. Code review: checklist objetivo e tamanho de PR
6. Convenções e formatação automatizada

## Bloco 7 — Testes automatizados
1. Estratégia: pirâmide vs troféu, o que testar em cada camada
2. Unitário: JUnit 5, AssertJ, Mockito, Given-When-Then, nomes por comportamento
3. Quando (não) mockar — mocks que escondem bugs
4. Fatias: `@WebMvcTest`, `@DataJpaTest`, `@JsonTest`
5. Integração com Testcontainers e banco real + migrations
6. Testes de contrato (consumidor/provedor)
7. ArchUnit e teste de mutação (PIT)
8. Dados de teste: builders, fixtures, determinismo de tempo e random
9. Anti-padrões: `@SpringBootTest` para tudo, cobertura como meta, testes frágeis

## Bloco 8 — Persistência: JPA/Hibernate e performance
1. **N+1**: diagnóstico, `join fetch`, `@EntityGraph`, batch fetching, projections
2. **Lazy vs Eager**: padrão correto, `LazyInitializationException`, `open-in-view`
3. Paginação com fetch join e `Specifications`
4. `@Transactional`: propagação, self-invocation, `readOnly`
5. Dirty checking, flush e bulk updates
6. Locking otimista vs pessimista e concorrência
7. Migrations com Flyway/Liquibase e por que `ddl-auto` é proibido em produção
8. HikariCP: dimensionamento de pool e timeouts
9. Modelagem: identidade, `equals/hashCode`, valores monetários, datas e timezone
10. Quando sair do ORM: JDBC, projeções, modelo de leitura dedicado

## Bloco 9 — Lombok: uso, riscos e alternativas
1. `@Data` em entidades — `equals/hashCode` e proxies do Hibernate
2. `@Builder`, valores default e interação com Jackson
3. `@EqualsAndHashCode` e `@ToString` com relacionamentos cíclicos
4. `@RequiredArgsConstructor` vs construtor explícito
5. Annotation processing, IDE e depuração comprometida
6. Alternativa recomendada: records + código explícito
7. Regra prática: onde pode, onde nunca

## Bloco 10 — Segurança e configuração
1. Spring Security: filter chain e method security
2. JWT/OAuth2: validação de token e claims
3. Mass assignment, validação de entrada, SSRF e injection
4. Gestão de secrets
5. Headers de segurança, CSRF, CORS

## Bloco 11 — Operação e resiliência
1. Timeouts, retry e circuit breaker (Resilience4j)
2. Async e virtual threads: quando usar e quando evitar
3. Caching (Caffeine/Redis): invalidação e cache stampede
4. Empacotamento e JVM tuning básico
5. Graceful shutdown e readiness probes

## Bloco 12 — Catálogo de armadilhas
Controller com regra de negócio · domínio anêmico · exceções genéricas e `catch` vazio · log de PII · `ddl-auto=update` em produção · `double` para dinheiro · `Optional` como campo · N+1 silencioso · `@Transactional` sem efeito · configuração hardcoded · testes que nunca falham · dependências com CVE.

## Bloco 13 — Checklists operacionais
PR · review · go-live.# Documentação de Orientações Técnicas — Aplicações Java com Spring Boot

**Base de referência:** Spring Boot 4.1 (Spring Framework 7) · Java 25 LTS
**Escopo:** arquitetura, API, código, qualidade, testes, persistência e armadilhas recorrentes
**Natureza:** documento normativo — onde houver divergência com hábitos do time, o documento prevalece até ser revisado

> Nota de calibração: recomendações marcadas com ⚠️ dependem de detalhes de release específicos da linha 4.x. Valide contra as release notes da versão exata antes de adotar em produção.

---

## 1. Convenções e stack de referência

### 1.1 Recursos de linguagem que você deve usar (Java 25)

| Recurso | Onde aplicar | Status |
|---|---|---|
| `record` | DTOs, Value Objects, projeções, `@ConfigurationProperties` | Use por padrão |
| Sealed interfaces + pattern matching em `switch` | Modelagem de estados, resultados, eventos | Use quando o conjunto de casos é fechado |
| Virtual threads | Cargas de I/O bloqueante e alta concorrência | Use com critério (ver 12.2) |
| Scoped Values | Propagação de contexto (tenant, correlation ID) | Use em vez de `ThreadLocal` |
| Unnamed patterns `_` | Lambdas e patterns onde a variável não importa | Use livremente |
| `Optional` como retorno | Buscas que podem não encontrar | Nunca como campo ou parâmetro |

Cuidado com material legado: **String Templates foi retirado** da linguagem. Não use `STR."..."`. Para interpolação, use `String.format`, `formatted()` ou builders.

### 1.2 Estrutura de projeto (Clean Architecture)

```
src/main/java/br/com/empresa/app/
├── domain/                 # núcleo — zero dependência de framework
│   ├── model/              # entidades de domínio, Value Objects (records)
│   ├── event/              # eventos de domínio
│   ├── exception/          # exceções de negócio (sem HTTP, sem Spring)
│   └── port/
│       ├── in/             # interfaces de casos de uso
│       └── out/            # interfaces de repositório/serviços externos
├── application/            # orquestração — depende só de domain
│   ├── usecase/            # implementações dos casos de uso
│   ├── dto/                # records de entrada/saída
│   └── mapper/             # conversão domínio <-> DTO
├── infrastructure/         # adaptadores — depende de tudo
│   ├── web/                # controllers, filters, exception handlers
│   ├── persistence/        # JPA entities, repositories, adapters
│   ├── client/             # integrações HTTP externas
│   └── config/             # beans, security, properties
└── shared/                 # utilitários técnicos e sem regra de negócio
```

### 1.3 A regra que sustenta tudo

> **`domain` e `application` não importam nada de `infrastructure`, nem do Spring.**

Se você precisa de `@Service`, `@Transactional` ou `@Entity` dentro de `domain`, o modelo está vazando. Exceção pragmática aceitável: `domain` pode usar `jakarta.validation` em Value Objects — nada além disso.

**Como fiscalizar mecanicamente:** ArchUnit no build (ver 5.6). Regra não automatizada é regra que será violada no terceiro sprint.

### 1.4 Por feature vs por camada

Camadas no topo do pacote (`domain/`, `application/`) e **features dentro delas** funcionam bem até ~30 casos de uso. Acima disso, considere inverter: `feature/pedido/{domain,application,infrastructure}`. Decida uma vez e não misture os dois estilos — mistura produz pacotes órfãos e busca quebrada.

---

## 2. Spring Boot essencial

### 2.1 Injeção de dependência

Injeção por construtor, sempre. Um único construtor dispensa `@Autowired`.

```java
@Service
public class CriarPedidoUseCase implements CriarPedido {

    private final PedidoRepository pedidoRepository;
    private final EstoqueGateway estoqueGateway;
    private final Clock clock;

    public CriarPedidoUseCase(PedidoRepository pedidoRepository,
                              EstoqueGateway estoqueGateway,
                              Clock clock) {
        this.pedidoRepository = pedidoRepository;
        this.estoqueGateway = estoqueGateway;
        this.clock = clock;
    }

    @Override
    public PedidoOutput executar(CriarPedidoInput input) {
        // ...
    }
}
```

**Proibido:** `@Autowired` em campo, `@Autowired` em setter, `ApplicationContext` como service locator, `new` para classes gerenciadas.

Por que: dependência de campo esconde complexidade, impede `final`, quebra testes sem reflexão, permite ciclo de dependência silencioso e ainda engessa o design.

### 2.2 Configuração tipada

Nada de `@Value` espalhado. Use `@ConfigurationProperties` com record + validação.

```java
@ConfigurationProperties(prefix = "app.pagamento")
@Validated
public record PagamentoProperties(
        @NotBlank String baseUrl,
        @NotNull Duration timeout,
        @Min(1) @Max(10) int tentativas
) {}
```

```java
@SpringBootApplication
@ConfigurationPropertiesScan
public class Application { }
```

**Regra prática:** toda chave lida pelo código tem uma propriedade tipada, documentada e com default explícito. `@Value` sobrevive apenas em código de bootstrap.

### 2.3 Profiles e configuração externa

- `application.yml` contém o comum; `application-{profile}.yml` contém desvios.
- Profile selecionado por variável de ambiente em runtime, nunca por valor no artefato.
- **Secrets vêm de variável de ambiente ou secret manager.** Nunca em `application.yml`, nunca no repositório — mesmo em profile de dev.
- `spring.config.import` para composição de fontes (Vault, Config Server, filesystem).

### 2.4 Auto-configuração: entenda antes de sobrescrever

Sintoma de alerta: `@SpringBootApplication(exclude = {...})` com cinco exclusões. Isso indica uma dependência que não deveria estar no classpath.

Ordem de intervenção, da mais barata à mais cara:
1. Propriedade de configuração (a maioria dos comportamentos é configurável).
2. Bean próprio do mesmo tipo (o seu vence o auto-configurado por `@ConditionalOnMissingBean`).
3. `exclude` no auto-config.
4. Escrever um auto-config próprio (último recurso).

### 2.5 Ciclo de vida e escopos

- Bean `singleton` precisa ser **stateless**. Um único campo mutável por request transforma seu serviço em armadilha de concorrência.
- `@Scope("prototype")` em componente web quase sempre é sinal de design errado, não de requisito.
- `request`/`session` exigem proxy; usá-los em serviço injetado em singleton gera erro sutil de resolução. Prefira passar o dado como parâmetro.
- `@PostConstruct` não deve fazer I/O pesado — atrase para health check ou warm-up explícito.

### 2.6 Actuator e operação

```yaml
management:
  endpoints.web.exposure.include: health,info,metrics,prometheus
  endpoint.health.probes.enabled: true
  endpoint.health.show-details: when_authorized
server.shutdown: graceful
spring.lifecycle.timeout-per-shutdown-phase: 30s
```

Readiness deve refletir **capacidade de atender tráfego** (pool de conexão disponível, migrations aplicadas), não apenas "processo vivo". Liveness mal configurado causa restart em cascata durante picos.

### 2.7 Anti-padrões desta seção

- Campos `static` mutáveis como estado compartilhado.
- `@Component` em tudo — inclusive em classes de domínio.
- Construtor com 8 dependências: o problema não é o construtor, é a classe.
- Lógica no `@PostConstruct` que impede o app de subir.
- `@Value("${...}")` com expressão SpEL complexa escondida no meio da regra de negócio.

---

## 3. API RESTful

### 3.1 Recursos e verbos

Recurso é **substantivo no plural**: `/pedidos`, `/pedidos/{id}/itens`. Ação é verbo HTTP, não URL.

| Intenção | Verbo | URL | Sucesso |
|---|---|---|---|
| Listar | GET | `/pedidos` | 200 |
| Obter | GET | `/pedidos/{id}` | 200 / 404 |
| Criar | POST | `/pedidos` | 201 + `Location` |
| Substituir | PUT | `/pedidos/{id}` | 200 / 204 |
| Alterar parcialmente | PATCH | `/pedidos/{id}` | 200 / 204 |
| Remover | DELETE | `/pedidos/{id}` | 204 |

**Proibido:** `POST /pedidos/criar`, `GET /pedidos/delete/{id}`, `POST /listarPedidos`.

### 3.2 Status codes — semântica, não decoração

- `201` + `Location` quando há recurso criado.
- `202` para processamento assíncrono real (com URL de acompanhamento).
- `400` para payload malformado; `422` para payload válido com regra de negócio violada.
- `409` para conflito de estado (duplicidade, versão desatualizada).
- `404` quando o recurso não existe **ou** não é visível ao usuário — não vaze existência.
- `401` vs `403`: não autenticado vs autenticado sem permissão.
- `429` com `Retry-After` quando há rate limiting.

Nunca retorne `200` com `{"erro": "..."}` no corpo. Isso quebra clientes, cache, monitoramento e retry automático.

### 3.3 Erros padronizados

Use `ProblemDetail` (RFC 9457), centralizado em `@RestControllerAdvice`.

```java
@RestControllerAdvice
public class ApiExceptionHandler {

    @ExceptionHandler(RecursoNaoEncontradoException.class)
    ProblemDetail naoEncontrado(RecursoNaoEncontradoException ex) {
        var pd = ProblemDetail.forStatusAndDetail(HttpStatus.NOT_FOUND, ex.getMessage());
        pd.setType(URI.create("https://api.exemplo.com/problems/recurso-nao-encontrado"));
        pd.setTitle("Recurso não encontrado");
        pd.setProperty("codigo", "PEDIDO_NAO_ENCONTRADO");
        return pd;
    }
}
```

Regras:
- **Nunca** exponha stack trace, SQL, nome de tabela ou classe interna.
- Toda exceção de negócio tem tradução explícita; `Exception` genérica cai em `500` com mensagem neutra.
- Corpo de erro carrega `correlationId` para rastreio — ver 7.3.
- Erros de validação (`MethodArgumentNotValidException`) viram `400` com lista campo → mensagem.

### 3.4 Versionamento

Prefira versionamento na URL (`/v1/pedidos`) pela clareza de contrato e facilidade de roteamento. Spring Framework 7 traz versionamento declarativo em mappings, o que reduz boilerplate — mas **a política importa mais que o mecanismo**:

- Versão nova só nasce com breaking change.
- Versão antiga tem prazo de vida anunciado e telemetria de uso por cliente.
- Campos novos são sempre opcionais; remoção exige versão nova.

### 3.5 Listagens: contrato único para toda a API

```java
public record PageResponse<T>(
        List<T> conteudo,
        int pagina,
        int tamanho,
        long total,
        int totalPaginas
) {
    public static <T> PageResponse<T> de(Page<T> page) {
        return new PageResponse<>(page.getContent(), page.getNumber(),
                page.getSize(), page.getTotalElements(), page.getTotalPages());
    }
}
```

- Padronize nomes de query params: `page`, `size`, `sort` (ex.: `sort=valor,desc`).
- Limite `size` no servidor (máx. 100). Sem isso, um cliente derruba o banco com `size=100000`.
- **Nunca** exponha `Page` do Spring direto: o JSON muda entre versões e vaza detalhes internos.

### 3.6 Validação em duas fronteiras

1. **Forma** (`jakarta.validation`): campos obrigatórios, formato, tamanho, faixa.
2. **Negócio** (use case / domínio): unicidade, transição de estado válida, regra que depende de dados.

Erro clássico: colocar regra de negócio em anotação customizada no DTO. A anotação passa a consultar banco, o controller ganha transação, e o teste vira integração sem necessidade.

```java
public record CriarPedidoRequest(
        @NotNull @Positive Long clienteId,
        @NotEmpty @Size(max = 50) List<@Valid ItemRequest> itens,
        @NotNull @PositiveOrZero BigDecimal desconto
) {}
```

### 3.7 Idempotência

POST que cria recurso com efeito financeiro/externo precisa de proteção:

- Cliente envia `Idempotency-Key` (UUID).
- Servidor persiste chave + resposta por janela de tempo; requisição repetida devolve a mesma resposta com o mesmo status.
- Sem isso, um retry de timeout gera pedido duplicado. Todo cliente HTTP decente faz retry — assuma que vai acontecer.

### 3.8 OpenAPI

- Contrato gerado a partir do código com anotações, ou escrito à mão em contract-first. Escolha uma direção.
- A doc precisa rodar no CI: se a geração quebra, o build quebra.
- Nunca publique Swagger em produção sem autenticação.

### 3.9 CORS, autenticação e limites

```java
@Bean
CorsConfigurationSource corsConfigurationSource() {
    var config = new CorsConfiguration();
    config.setAllowedOrigins(List.of("https://app.exemplo.com")); // nunca "*" com credenciais
    config.setAllowedMethods(List.of("GET", "POST", "PUT", "PATCH", "DELETE"));
    config.setAllowedHeaders(List.of("Authorization", "Content-Type", "Idempotency-Key"));
    config.setMaxAge(3600L);
    var source = new UrlBasedCorsConfigurationSource();
    source.registerCorsConfiguration("/**", config);
    return source;
}
```

`*` em `allowedOrigins` com `allowCredentials=true` é rejeitado pelo navegador e, quando contornado, é falha de segurança. Rate limiting por cliente na borda (gateway), não no controller.

---

## 4. Lean Code

Lean aqui não é "menos linhas". É **menos código especulativo**.

### 4.1 YAGNI com consequência financeira

Cada abstração tem custo permanente: leitura, teste, migração, documentação. Antes de criar uma interface, responda: *quantas implementações existem hoje?* Se a resposta é uma, a interface só se justifica em fronteira arquitetural (porta de saída para serviço externo ou repositório), nunca para "facilitar um mock futuro".

### 4.2 Records e imutabilidade

```java
public record Dinheiro(BigDecimal valor, Moeda moeda) {
    public Dinheiro {
        Objects.requireNonNull(valor, "valor");
        Objects.requireNonNull(moeda, "moeda");
        if (valor.scale() > 2) throw new IllegalArgumentException("máximo 2 casas");
    }

    public Dinheiro somar(Dinheiro outro) {
        if (moeda != outro.moeda) throw new IllegalArgumentException("moedas divergentes");
        return new Dinheiro(valor.add(outro.valor), moeda);
    }
}
```

Record não é só "getter curto": é contrato de imutabilidade com validação no construtor compacto. Use em DTOs, Value Objects, projeções e configurações.

**Quando NÃO usar record:** entidade JPA (precisa de construtor no-args e estado mutável), objeto com identidade própria mutável, classe com herança.

### 4.3 Guard clauses e profundidade

```java
// Antes
public void aprovar(Pedido pedido) {
    if (pedido != null) {
        if (pedido.getStatus() == Status.PENDENTE) {
            if (pedido.getTotal().valor().compareTo(BigDecimal.ZERO) > 0) {
                pedido.aprovar();
            } else { throw new RegraNegocioException("total inválido"); }
        } else { throw new RegraNegocioException("status inválido"); }
    } else { throw new IllegalArgumentException("pedido nulo"); }
}

// Depois
public void aprovar(Pedido pedido) {
    Objects.requireNonNull(pedido, "pedido");
    if (pedido.status() != Status.PENDENTE) throw new RegraNegocioException("status inválido");
    if (pedido.total().valor().signum() <= 0) throw new RegraNegocioException("total inválido");
    pedido.aprovar();
}
```

**Métrica objetiva:** nenhum método com mais de 2 níveis de aninhamento. Três níveis é dívida técnica declarada.

### 4.4 Removendo cerimônia

Código que parece "arquitetura" mas não entrega nada:
- `PedidoDTO` → `PedidoModel` → `PedidoEntity` com mapeamento 1:1 e zero transformação.
- Interface + `Impl` para classe sem segunda implementação.
- `Service` que só repassa para o repository.
- `Mapper` manual de 40 linhas para copiar campos de mesmo nome.
- Camada `facade` que agrega um único service.

Se o mapeamento é campo-a-campo, use MapStruct ou um construtor no record. Se não há transformação de dado nem de conceito, não há DTO.

### 4.5 DRY com triagem

Duplicação é dívida **quando a mudança propaga**. Antes de extrair:
1. As duas ocorrências mudam pelo mesmo motivo? Se não, deixe duplicado.
2. A abstração resultante tem nome honesto? Se o nome é `UtilHelperBaseService`, não extraia.
3. Você tem três ocorrências ou duas? Regra de três: a partir da terceira, extraia.

Duplicação acidental é mais barata que acoplamento errado.

### 4.6 Quando não abstrair

- Variação que pode nunca existir (YAGNI).
- Abstração que exige `instanceof` para funcionar — polimorfismo não resolvido.
- Hierarquia com `super.metodo()` em todos os filhos.
- Configuração que virou linguagem própria (DSL interna com 300 linhas de parsing).

### 4.7 Código morto

`@Deprecated` sem data de remoção é código morto com boas maneiras. Política: toda deprecação tem issue, responsável e prazo. Métodos privados sem uso devem ser removidos no mesmo PR — compilador e IDE apontam; ignore a tentação de "deixar para depois".

---

## 5. SOLID aplicado ao ecossistema Spring

### 5.1 SRP — o "Service Deus"

Sintoma: `PedidoService` com 1.200 linhas, 25 dependências e métodos `criar`, `aprovar`, `cancelar`, `gerarRelatorio`, `enviarEmail`, `sincronizarErp`. **Não é problema de SOLID, é problema de deploy:** qualquer mudança pode quebrar qualquer coisa.

Quebre por **caso de uso**, não por entidade:

```
application/usecase/pedido/
├── CriarPedido.java        (porta)
├── CriarPedidoUseCase.java
├── AprovarPedido.java
├── AprovarPedidoUseCase.java
├── CancelarPedido.java
└── ...
```

Um use case tem um método público, uma transação, um motivo para mudar.

### 5.2 OCP — Strategy em vez de `if/else` sobre tipo

```java
public interface CalculadoraFrete {
    Modalidade modalidade();
    BigDecimal calcular(Pedido pedido);
}

@Component
class FreteExpresso implements CalculadoraFrete {
    public Modalidade modalidade() { return Modalidade.EXPRESSO; }
    public BigDecimal calcular(Pedido pedido) { /* ... */ }
}

@Service
public class FreteService {
    private final Map<Modalidade, CalculadoraFrete> calculadoras;

    public FreteService(List<CalculadoraFrete> implementacoes) {
        this.calculadoras = implementacoes.stream()
                .collect(Collectors.toMap(CalculadoraFrete::modalidade, Function.identity()));
    }

    public BigDecimal calcular(Modalidade modalidade, Pedido pedido) {
        var calc = calculadoras.get(modalidade);
        if (calc == null) throw new RegraNegocioException("modalidade não suportada: " + modalidade);
        return calc.calcular(pedido);
    }
}
```

Novo modal = novo arquivo, zero alteração no serviço. Se `switch` sobre enum aparece em 3+ lugares, é hora de Strategy ou de método no próprio enum.

### 5.3 LSP — herança é contrato

- Subclasse não pode enfraquecer pré-condição nem reforçar pós-condição.
- `UnsupportedOperationException` em método herdado é violação de LSP, não "implementação parcial".
- Em exceções: nunca herde de `RuntimeException` para "reusar" semântica diferente. `PagamentoRecusadoException` e `EstoqueIndisponivelException` não são irmãs.

Prefira composição. Herança em código de aplicação (fora de framework) deve ser rara e justificada.

### 5.4 ISP — interfaces específicas

```java
// Ruim: força implementações a ter métodos que não usam
public interface PedidoRepository extends JpaRepository<Pedido, Long> {
    List<Pedido> findByCliente(Long clienteId);
    List<Pedido> findPendentesDesde(LocalDate data);
    BigDecimal somarTotalPorPeriodo(LocalDate inicio, LocalDate fim);
    // ... 20 métodos herdados que 80% dos consumidores nunca usam
}

// Bom: portas por necessidade
public interface PedidoRepository {
    Pedido salvar(Pedido pedido);
    Optional<Pedido> porId(PedidoId id);
}

public interface RelatorioPedidoQuery {
    BigDecimal totalPorPeriodo(LocalDate inicio, LocalDate fim);
}
```

Fronteira prática: **nunca** estenda `JpaRepository` na porta de domínio. Ele traz 20 métodos e acopla seu domínio ao Spring Data.

### 5.5 DIP — a inversão que dá nome à arquitetura

```
domain/port/out/PedidoRepository      (interface — domínio define)
infrastructure/persistence/JpaPedidoRepository  (implementa — infraestrutura obedece)
```

O domínio dita o contrato; a infraestrutura se adapta. Se a interface fala em `Page`, `Specification` ou `EntityManager`, a inversão não aconteceu — o domínio está acoplado ao Spring Data.

### 5.6 ArchUnit — fiscalização automatizada

```java
@AnalyzeClasses(packages = "br.com.empresa.app")
class ArchitectureTest {

    @ArchTest
    static final ArchRule domainNaoDependeDeInfra =
        noClasses().that().resideInAPackage("..domain..")
            .should().dependOnClassesThat().resideInAnyPackage("..infrastructure..", "org.springframework..");

    @ArchTest
    static final ArchRule useCasesDevemImplementarPorta =
        classes().that().haveSimpleNameEndingWith("UseCase")
            .should().implement(resideInAPackage("..domain.port.in.."));

    @ArchTest
    static final ArchRule controllersNaoAcessamRepositorios =
        noClasses().that().resideInAPackage("..infrastructure.web..")
            .should().dependOnClassesThat().resideInAPackage("..infrastructure.persistence..");
}
```

Essas regras custam 30 linhas e salvam meses de erosão arquitetural.

---

## 6. Arquitetura e manutenibilidade

### 6.1 Fronteiras transacionais

- A transação pertence ao **caso de uso**, não ao controller nem ao repository.
- `@Transactional` em método público de um bean (proxies ignoram chamadas privadas e self-invocation).
- Chamada HTTP externa **nunca** dentro de transação: você segura conexão de banco por todo o timeout do parceiro. Faça: transação curta → commit → chamada externa → transação curta de compensação.
- `readOnly = true` em consultas: economiza dirty checking e orienta o pool.

```java
@Transactional
public PedidoOutput executar(CriarPedidoInput input) {
    var pedido = Pedido.criar(input.clienteId(), input.itens());
    return mapper.toOutput(pedidoRepository.salvar(pedido));
}
```

### 6.2 Monólito modular

Comece com um monólito modular: um processo, fronteiras internas explícitas, deploy único. Extrair serviços é caro e irreversível na prática.

Use Spring Modulith para tornar módulos **verificáveis**: dependências permitidas declaradas e testadas; eventos entre módulos com persistência e reentrega. Só considere decompor em serviços quando houver necessidade real de escala independente, isolamento de falha ou autonomia de time — não por estética.

### 6.3 Eventos de domínio

```java
public sealed interface PedidoEvento {
    record Criado(PedidoId id, LocalDateTime em) implements PedidoEvento {}
    record Aprovado(PedidoId id, LocalDateTime em) implements PedidoEvento {}
}
```

- Publicação via `ApplicationEventPublisher` para efeitos internos.
- Para efeitos críticos (e-mail, ERP, faturamento), use transactional outbox — não `@EventListener` síncrono. Se a transação faz rollback, o e-mail já saiu. Esse bug aparece em produção, não em teste.
- Consumidor deve ser idempotente: eventos chegam duplicados.

### 6.4 ADRs

Decisões que merecem registro (um arquivo `.md` curto por decisão, versionado no repositório):
- Escolha de banco, mensageria, cache, autenticação.
- Estrutura de módulos e por que essa e não outra.
- Estratégia de versionamento de API.
- Uso de biblioteca transversal (Lombok, MapStruct, etc.).

Formato: contexto → decisão → alternativas consideradas → consequências (inclusive negativas). ADR sem consequência negativa é propaganda, não registro.

### 6.5 Evolução segura

- **Strangler fig**: rota nova convive com legada, tráfego migra gradualmente com métrica de comparação.
- **Deprecação**: anunciar, instrumentar uso, comunicar, desligar. Nunca desligar sem telemetria.
- **Refactoring**: sempre coberto por teste antes de começar. Refatorar sem rede de segurança é reescrever com outro nome.

---

## 7. Qualidade de software

### 7.1 Análise estática no build

```xml
<plugin>
  <groupId>com.diffplug.spotless</groupId>
  <artifactId>spotless-maven-plugin</artifactId>
</plugin>
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-checkstyle-plugin</artifactId>
</plugin>
```

Pipeline mínimo: formatação (Spotless/Spotless Gradle), null-safety e bugs óbvios (ErrorProne ou SpotBugs), regras arquiteturais (ArchUnit) e cobertura (JaCoCo). SonarQube como quality gate no PR, não como relatório mensal que ninguém lê.

**Regra:** análise estática que não bloqueia merge não é análise estática, é decoração.

### 7.2 Métricas que importam

| Métrica | Limite prático | O que revela |
|---|---|---|
| Complexidade ciclomática por método | ≤ 10 | Dificuldade de teste e leitura |
| Profundidade de aninhamento | ≤ 2 | Fluxo de controle embaralhado |
| Parâmetros por método | ≤ 4 | Método com responsabilidades demais |
| Linhas por classe | ≤ 300 | SRP em risco |
| Dependências injetadas | ≤ 5 | Classe orquestrando demais |
| Fan-out por pacote | monitorar tendência | Acoplamento crescendo |

**O que cobertura NÃO mede:** qualidade da asserção. Você pode ter 95% de cobertura com testes que só verificam que o método não lança exceção. Cobertura é indicador de lacuna, nunca de qualidade.

### 7.3 Logs

```java
log.info("pedido aprovado pedidoId={} clienteId={} valor={}",
        pedido.id(), pedido.clienteId(), pedido.total());
log.error("falha ao sincronizar pedido pedidoId={} tentativa={}",
        pedidoId, tentativa, ex);
```

- **Structured logging** (JSON em produção) com `correlationId`/`traceId`, `tenantId`, `userId`.
- Parâmetros nomeados, nunca concatenação — logging condicional não deve custar CPU.
- **Nunca logue PII**: CPF, e-mail, cartão, senha, token. Se precisa rastrear, mascare (`***.***.***-**`).
- `log.error` com exceção: passe o `Throwable` como último argumento, sem `ex.getMessage()` no lugar do objeto.
- Log de entrada e saída de método público em produção é ruído. Logue decisões e transições de estado.
- `System.out.println` não existe em código profissional.

### 7.4 Observabilidade

- Micrometer + Prometheus: latência p95/p99, taxa de erro, throughput, saturação de pool.
- OpenTelemetry: trace ponta a ponta, com propagação de contexto para chamadas externas.
- **Métricas de negócio** importam mais que CPU: pedidos por minuto, taxa de recusa de pagamento, fila de compensação. Alerta de negócio detecta incidente antes do alerta de infraestrutura.
- Correlação obrigatória entre log, métrica e trace via `traceId`.

### 7.5 Code review

Checklist objetivo, sem opinião:
- A mudança tem teste que falharia sem ela?
- Respeita as fronteiras de camada? (ArchUnit pega, mas revise)
- Introduz dependência nova? Está justificada?
- Trata erro de I/O externo com timeout?
- Altera contrato de API? Está versionado?
- Existe query nova? Tem índice e não é N+1?
- Log de dado sensível?
- Config nova está tipada e documentada?

PR com mais de ~400 linhas úteis tem qualidade de review próxima de zero. Quebre.

---

## 8. Testes automatizados

### 8.1 Estratégia

Nem pirâmide nem troféu por dogma. A distribuição que funciona:

- **Muitos** testes unitários de domínio e caso de uso (rápidos, sem Spring).
- **Alguns** testes de fatia (`@WebMvcTest`, `@DataJpaTest`) para contrato HTTP e mapeamento ORM.
- **Poucos** testes de integração completos (`@SpringBootTest`) para fluxos críticos de ponta a ponta.
- **Contrato** onde há consumidor externo.

Regra: se o teste demora mais de 1s, ele pertence à camada de integração — e a camada de integração deve ser pequena.

### 8.2 Testes unitários

```java
@ExtendWith(MockitoExtension.class)
class AprovarPedidoUseCaseTest {

    @Mock PedidoRepository pedidoRepository;
    @Mock EventoPublisher eventoPublisher;
    @InjectMocks AprovarPedidoUseCase useCase;

    @Test
    void deveAprovarPedidoPendenteComTotalPositivo() {
        // given
        var pedido = PedidoFixture.pendenteComTotal(new BigDecimal("150.00"));
        given(pedidoRepository.porId(pedido.id())).willReturn(Optional.of(pedido));

        // when
        useCase.executar(new AprovarPedidoInput(pedido.id()));

        // then
        assertThat(pedido.status()).isEqualTo(Status.APROVADO);
        then(eventoPublisher).should().publicar(any(PedidoEvento.Aprovado.class));
    }

    @Test
    void deveLancarExcecaoQuandoPedidoJaAprovado() {
        var pedido = PedidoFixture.aprovado();
        given(pedidoRepository.porId(pedido.id())).willReturn(Optional.of(pedido));

        assertThatThrownBy(() -> useCase.executar(new AprovarPedidoInput(pedido.id())))
                .isInstanceOf(RegraNegocioException.class)
                .hasMessageContaining("status");
    }
}
```

Convenções:
- Nome descreve comportamento: `deveXQuandoY`.
- **Given-When-Then** com comentários separando os blocos.
- AssertJ para asserções fluentes e mensagens legíveis.
- Uma asserção conceitual por teste (várias asserções sobre o mesmo comportamento são aceitáveis).
- Fixtures em classe dedicada (`PedidoFixture`), nunca builder gigante replicado em cada teste.

### 8.3 Quando não mockar

Mock em excesso testa a implementação, não o comportamento. Sinais de alerta:
- Mock de `String`, `List`, `BigDecimal` — use valor real.
- Mock de entidade de domínio — use instância real.
- Mock que retorna mock que retorna mock — o teste está acoplado à estrutura interna.
- `verify()` em cada linha — você está testando a ordem das chamadas, não o resultado.

**Mock apenas em fronteira de I/O:** repositório, cliente HTTP, fila, relógio (`Clock`), gerador de ID. Todo o resto deve ser real.

### 8.4 Testes de fatia

```java
@WebMvcTest(PedidoController.class)
class PedidoControllerTest {

    @Autowired MockMvc mvc;
    @MockitoBean CriarPedido criarPedido;

    @Test
    void deveRetornar201ComLocationQuandoPedidoCriado() throws Exception {
        given(criarPedido.executar(any())).willReturn(new PedidoOutput(42L, "PENDENTE"));

        mvc.perform(post("/v1/pedidos")
                .contentType(MediaType.APPLICATION_JSON)
                .content("""
                        {"clienteId": 1, "itens": [{"sku": "A1", "quantidade": 2}]}
                        """))
           .andExpect(status().isCreated())
           .andExpect(header().string("Location", containsString("/v1/pedidos/42")));
    }
}
```

`@DataJpaTest` para validar queries, mapeamentos, `@EntityGraph` e constraints — com Testcontainers, não H2. H2 tem dialeto e comportamento diferentes; teste que passa em H2 e falha em PostgreSQL é falsa segurança.

### 8.5 Testcontainers

```java
@SpringBootTest
@Testcontainers
class PedidoRepositoryIntegrationTest {

    @Container
    @ServiceConnection
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:16-alpine");

    @Autowired PedidoRepository repository;

    @Test
    void devePaginarComFetchJoinSemN1() {
        // ...
    }
}
```

Regras:
- Reutilize containers entre classes (`testcontainers.reuse.enable=true` em dev) para não pagar 8s por classe.
- Imagem fixada por versão, não `latest`.
- Migrations Flyway rodam no teste: o schema é o mesmo de produção. É a única forma de descobrir que a migration quebra.

### 8.6 Contrato

Produtor valida que mantém o contrato; consumidor valida que a expectativa está atualizada. Ferramentas: Spring Cloud Contract ou Pact. Essencial quando há múltiplos consumidores externos ou times distintos.

### 8.7 Teste de mutação

PIT Mutation Testing mostra onde o teste passa mas não pega o bug (ex.: remove uma condição e nada falha). Não rode em todo build — rode em módulos críticos (cálculo, pagamento, autorização) e use como diagnóstico de qualidade de asserção.

### 8.8 Dados de teste e determinismo

```java
@Bean
@Profile("test")
Clock fixedClock() {
    return Clock.fixed(Instant.parse("2026-01-15T10:00:00Z"), ZoneOffset.UTC);
}
```

- **Sempre** injete `Clock` em vez de chamar `LocalDateTime.now()`. Sem isso, teste de regra temporal é frágil ou impossível.
- UUIDs e geradores de ID, quando afetam regra, também devem ser injetáveis.
- Seed fixa em dados aleatórios (`new Random(42)`), se precisar de aleatoriedade.
- Teste não depende de ordem de execução nem de estado deixado por outro teste.
- Nunca `Thread.sleep` como sincronização. Use Awaitility.

### 8.9 Anti-padrões de teste

- `@SpringBootTest` para testar cálculo de frete (contexto de 8s para testar `a + b`).
- Teste que nunca falha porque não tem asserção real.
- Cobertura como meta de sprint — gera teste inútil em massa.
- Teste acoplado a string de mensagem de erro exata, quebrando a cada ajuste de texto.
- `@DirtiesContext` como solução padrão para isolamento (indica estado compartilhado mal desenhado).
- Teste que mocka o próprio objeto sob teste.

---

## 9. Persistência: JPA/Hibernate e performance

Esta é a seção onde 80% dos incidentes de performance nascem.

### 9.1 N+1 — o problema e as quatro soluções

**Sintoma:** 1 query para listar 100 pedidos + 100 queries para buscar os clientes = 101 queries onde deveria haver 1. Latência explode de 20ms para 2s.

**Diagnóstico:** ative `spring.jpa.show-sql` e `hibernate.generate_statistics` em dev e **conte queries por requisição**. Se o número cresce com o volume da lista, é N+1. Em produção, use o trace: cada query é um span.

**Solução 1 — `join fetch` pontual:**

```java
@Query("select p from PedidoJpa p join fetch p.cliente where p.status = :status")
List<PedidoJpa> buscarComCliente(@Param("status") Status status);
```

**Solução 2 — `@EntityGraph` (mais declarativo, ideal para paginação):**

```java
@EntityGraph(attributePaths = {"cliente", "itens"})
List<PedidoJpa> findByStatus(Status status);
```

**Solução 3 — projeção (melhor para leitura):**

```java
public interface PedidoResumo {
    Long getId();
    String getClienteNome();
    BigDecimal getTotal();
}

@Query("""
    select p.id as id, c.nome as clienteNome, p.total as total
    from PedidoJpa p join p.cliente c
    where p.status = :status
    """)
List<PedidoResumo> resumirPorStatus(@Param("status") Status status);
```

**Solução 4 — `@BatchSize` / `hibernate.default_batch_fetch_size`:**

```yaml
spring.jpa.properties.hibernate.default_batch_fetch_size: 25
```

Transforma 100 queries em 4 (`IN (...)` com 25 ids). É mitigação global, não substitui modelagem correta — mas é a rede de segurança mais barata que existe.

**Regra:** toda listagem exposta por API tem teste que conta queries. Sem isso, o N+1 volta na primeira refatoração.

### 9.2 Lazy vs Eager

**Padrão correto:** tudo `LAZY` em `@ManyToOne` e `@OneToOne` (o default do JPA é `EAGER` nesses dois — **anule explicitamente**). `@OneToMany` e `@ManyToMany` já são `LAZY`.

```java
@Entity
class PedidoJpa {
    @ManyToOne(fetch = FetchType.LAZY, optional = false)
    @JoinColumn(name = "cliente_id", nullable = false)
    private ClienteJpa cliente;

    @OneToMany(mappedBy = "pedido", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<ItemPedidoJpa> itens = new ArrayList<>();
}
```

**Problemas de `EAGER`:**
- Você não escolhe quando carregar; toda consulta paga o custo.
- `EAGER` em relacionamento de coleção + `EAGER` em outro = produto cartesiano e `MultipleBagFetchException`.
- Não é sobrescrevível para pior: você não consegue voltar a `LAZY` em runtime.

**`LazyInitializationException`:** ocorre quando se lê uma associação lazy fora da sessão. As três causas e correções:
1. Entidade vazou para fora da transação (controller recebendo entidade) → **correto:** converta para DTO dentro do caso de uso.
2. Mapeamento necessário não foi carregado → use `join fetch`/`@EntityGraph` na query que precisa.
3. `open-in-view` mascarando o problema → desligue.

```yaml
spring.jpa.open-in-view: false
```

`open-in-view: true` (default) mantém a sessão aberta durante a renderização, o que esconde N+1 e vazamento de camada, além de segurar conexão do pool. **Desligue desde o primeiro dia.** Ligar depois, em sistema grande, é projeto de semanas.

### 9.3 Paginação com fetch join

`join fetch` em coleção + `Pageable` faz o Hibernate paginar **em memória** — ele carrega tudo e corta a lista. Você não vê erro, vê memória e latência.

Soluções:
- Pagine a **raiz** e carregue as coleções com `@EntityGraph` (Hibernate faz query secundária) ou `@BatchSize`.
- Use duas queries: uma paginada com IDs, outra com `join fetch` restrita aos IDs.
- Para relatório, use projeção — não pagina entidade com coleção.

### 9.4 `@Transactional` — onde costuma falhar

| Armadilha | Consequência |
|---|---|
| Self-invocation (`this.outroMetodo()`) | Proxy não intercepta; sem transação |
| Método `private`/`final` | Proxy não intercepta |
| `try/catch` engolindo exceção | Rollback não acontece |
| Exceção checked sem `rollbackFor` | Commit parcial |
| Chamada HTTP externa dentro da transação | Conexão presa por todo o timeout |
| `@Transactional` em controller | Transação aberta durante serialização |
| `readOnly=false` em consulta | Dirty checking desnecessário, flush a cada iteração |

### 9.5 Dirty checking, flush e bulk

- Toda entidade carregada em modo gerenciado é comparada no flush. Em loop sobre 10.000 registros, isso é O(n) de comparação e memória.
- Atualizações em massa: use `@Modifying @Query` com `clearAutomatically = true, flushAutomatically = true`, ou JDBC puro. Lembre que bulk update **não** atualiza o contexto de persistência.
- Faça `flush()` explícito só quando a ordem importa. Caso contrário, deixe o framework gerenciar.
- `saveAll` de 10k registros sem `batch_size` gera 10k inserts. Configure:

```yaml
spring.jpa.properties.hibernate.jdbc.batch_size: 50
spring.jpa.properties.hibernate.order_inserts: true
spring.jpa.properties.hibernate.order_updates: true
```

### 9.6 Concorrência

- **Optimistic locking** (`@Version`) para conflito raro e leitura predominante. Detecta e devolve `409`.
- **Pessimistic locking** (`PESSIMISTIC_WRITE`) para seção crítica curta com contenção real. Custa throughput e risco de deadlock — use o mínimo possível, com timeout.
- **Nunca** use lock sem timeout. Lock esperando indefinidamente derruba o pool.

### 9.7 Migrations

- **`spring.jpa.hibernate.ddl-auto: validate` em todo ambiente não-local.** `update` em produção é proibido: ele não remove coluna, não renomeia, não migra dados e pode corromper schema sem aviso.
- Flyway ou Liquibase, migrations versionadas no repositório, imutáveis após aplicadas.
- Migration em produção precisa ser compatível para trás (expand/contract): adicionar coluna nullable → backfill → começar a usar → remover coluna antiga em release posterior. Nunca `DROP COLUMN` no mesmo deploy que remove o código.
- `CREATE INDEX` em tabela grande: use `CONCURRENTLY` (PostgreSQL) fora de transação, ou aceite janela de manutenção.
- Toda migration é testada — o teste de integração roda com Testcontainers e aplica o schema real.

### 9.8 Connection pool

```yaml
spring.datasource.hikari:
  maximum-pool-size: 20        # ≈ (núcleos × 2) + discos efetivos
  minimum-idle: 10
  connection-timeout: 3000     # falhe rápido, não espere 30s
  max-lifetime: 1800000        # menor que o timeout do banco/proxy
  leak-detection-threshold: 20000
```

- Pool grande não aumenta throughput — aumenta contenção no banco. `maximum-pool-size=100` em 4 instâncias = 400 conexões, e o banco sufoca.
- `connection-timeout` de 30s (default em alguns stacks) transforma indisponibilidade momentânea em timeout em cascata. 3s é mais saudável.
- Hikari em modo `fixed size` (min = max) reduz latência por evitar abertura de conexão.

### 9.9 Modelagem

- **Dinheiro:** `BigDecimal` com escala definida, nunca `double`/`float`. Coluna `NUMERIC(19,2)`. Moeda é parte do valor — use Value Object.
- **Datas:** `Instant`/`OffsetDateTime` para instantes; `LocalDate` para data civil. `TIMESTAMP WITH TIME ZONE` no banco. Timezone default do servidor é fonte de bug: sempre explicite.
- **`equals`/`hashCode`:** baseados no ID de negócio, não no ID gerado. Compare classes com `instanceof` para suportar proxies do Hibernate.
- **`toString`:** nunca inclua coleções lazy (dispara carga) nem dados sensíveis, e cuidado com recursão circular.
- **Identidade:** use UUID v7 (ordenável) ou chave natural quando fizer sentido; `IDENTITY` em bulk insert é ruim (impede batching).
- **Enums:** `@Enumerated(EnumType.STRING)`, sempre. `ORDINAL` quebra ao reordenar o enum.
- **Não use `@Data` do Lombok em entidade** (ver seção 10).

### 9.10 Quando sair do ORM

Use JDBC/`JdbcClient` quando:
- Relatório com agregação sobre dezenas de milhares de linhas.
- Bulk insert/update de grande volume.
- Query com window functions, CTE complexa, `ON CONFLICT`.
- Modelo de leitura dedicado (CQRS light) onde a entidade não ajuda.

Regra: ORM para **escrita transacional de agregados**; SQL para **leitura e volume**. Forçar uma coisa a fazer o papel da outra é a origem dos problemas.

---

## 10. Lombok: uso, riscos e alternativas

**Política recomendada neste documento:** não use Lombok em código novo. Use `record` para DTOs/Value Objects e construtores explícitos para o resto. Em código legado que já usa, siga as regras de contenção abaixo.

### 10.1 Por que evitar

**Diagnóstico de IDE e depuração:** o código que existe no arquivo não é o código que roda. Debug aponta para linha gerada; o stack trace mostra `Pedido$Builder.build()` que você nunca escreveu. Em produção, isso custa tempo.

**Fragilidade de ordem de evolução do modelo:** gerar construtor/builder para classe que muda de campo constante gera recompilação em cascata e diffs ruidosos.

**Annotation processing** acopla o build a versões específicas do JDK; novas versões frequentemente exigem atualização do Lombok antes de atualizar a JDK — já aconteceu e vai acontecer de novo.

**O que você perde de graça:** records dão `equals`/`hashCode`/`toString`/acessores imutáveis, com semântica correta e zero processamento de anotação.

### 10.2 Os riscos específicos, um por um

**`@Data` em entidade JPA — o pior cenário:**

```java
@Data          // NUNCA em @Entity
@Entity
public class Pedido {
    @Id private Long id;
    @OneToMany(mappedBy = "pedido")
    private List<Item> itens = new ArrayList<>();
}
```

Três problemas simultâneos:
1. `equals`/`hashCode` gerados incluem `itens` → comparação de coleção lazy dispara carga (performance) e muda conforme o estado da coleção (contrato quebrado em `Set`).
2. `hashCode` muda quando o ID é gerado → entidade em `HashSet` "desaparece" depois do `save`. Bug silencioso e intermitente.
3. `toString` inclui relacionamento → recursão infinita (`Pedido.toString()` → `Cliente.toString()` → `Pedido.toString()`) ou carga de coleção lazy.

**`@Builder` + valores default:** `@Builder.Default` é necessário, e esquecê-lo deixa `null` ou `0` silenciosamente:

```java
@Builder.Default private Status status = Status.PENDENTE; // obrigatório
```

Sem isso, `Pedido.builder().cliente(c).build()` produz `status == null`, e o erro aparece no banco como violação de constraint — longe da causa.

**`@Builder` + Jackson:** record ou construtor normal funciona nativamente. Com `@Builder` em classe, Jackson precisa de `@Jacksonized` ou construtor compatível; sem isso, desserialização falha em runtime, não em teste.

**`@EqualsAndHashCode`/`@ToString` com relacionamento cíclico:** `@EqualsAndHashCode.Exclude` e `@ToString.Exclude` são obrigatórios — e quem esquece descobre em produção.

**`@RequiredArgsConstructor`:** conveniente em injeção por construtor, mas se anexado a classe com `@Value`/`final` em entidade, gera construtor que conflita com o exigido pelo JPA.

**`@SneakyThrows`:** engole checked exception sem declarar. Remove a única vantagem de checked exceptions (visibilidade no contrato). Proibido.

**`@Value` do Lombok:** cria classe `final` com campos privados e getters — não é record, não tem acesso por componente, não é interoperável com pattern matching. Use record.

### 10.3 Regras de contenção (se o legado usa Lombok)

Permitido:
- `@Getter` em entidade JPA (nunca `@Data`).
- `@RequiredArgsConstructor` em classes com dependência `final`.
- `@Slf4j` para o logger.
- `@Builder` em classes de domínio simples, sempre com `@Builder.Default` nos defaults.

Proibido:
- `@Data` em entidade JPA.
- `@SneakyThrows`.
- `@EqualsAndHashCode`/`@ToString` sem `callSuper` e `exclude` avaliados.
- `@Value` do Lombok em código novo (use `record`).
- Lombok em DTO — record resolve.

### 10.4 A alternativa concreta

```java
// Lombok
@Data @Builder
public class CriarPedidoInput {
    private Long clienteId;
    private List<ItemInput> itens;
}

// Java 25
public record CriarPedidoInput(Long clienteId, List<ItemInput> itens) {
    public CriarPedidoInput {
        Objects.requireNonNull(clienteId, "clienteId");
        itens = List.copyOf(Objects.requireNonNull(itens, "itens"));
    }
}
```

Menos código, sem processamento de anotação, imutável, validado, `equals`/`hashCode` corretos, sem mágica de IDE.

---

## 11. Segurança e configuração

### 11.1 Spring Security

- `SecurityFilterChain` declarada como bean, com regras explícitas e `denyAll` como default.
- Method security (`@PreAuthorize`) na camada de aplicação para regras de negócio de autorização.
- Senha: `BCrypt` ou `Argon2` com custo calibrado. Nunca MD5/SHA puro.
- CSRF desabilitado apenas para API stateless com token; habilitado para app com sessão e formulário.
- Headers: HSTS, `X-Content-Type-Options: nosniff`, `Content-Security-Policy` quando há UI.

### 11.2 JWT e OAuth2

- Valide assinatura, `iss`, `aud` e `exp`. Token sem validação de audiência aceita token de outro serviço do mesmo provedor.
- Nunca aceite `alg: none` nem algoritmo escolhido pelo cliente.
- Não coloque dado sensível no payload — é apenas base64, não criptografia.
- `refresh token` rotativo e revogável; access token curto (5-15 min).

### 11.3 Fronteiras de entrada

- **Mass assignment:** nunca `BeanUtils.copyProperties(request, entity)`. DTO explícito com campo permitido, mapeamento explícito.
- **SQL injection:** sempre query parametrizada. `@Query` com concatenação de string é falha grave.
- **SSRF:** URL fornecida pelo usuário em chamada externa precisa de allowlist de host, não blocklist.
- **Deserialization:** não habilite polimorfismo Jackson aberto em endpoint público.
- Validação de upload: tipo real (magic bytes), tamanho, nome sanitizado, armazenamento fora do webroot.

### 11.4 Secrets

- Variável de ambiente ou secret manager (Vault, AWS Secrets Manager, etc.).
- Rotação com sobreposição: aceite a chave antiga e a nova durante a janela de troca.
- **Nunca** logue, nunca serialize em DTO de configuração exposto, nunca versione.

---

## 12. Operação e resiliência

### 12.1 Timeouts, retry e circuit breaker

```java
@Bean
HttpClient httpClient() {
    return HttpClient.newBuilder()
            .connectTimeout(Duration.ofSeconds(2))
            .build();
}
```

Regras:
- **Todo cliente externo tem timeout de conexão e de leitura.** Timeout infinito é a causa raiz mais comum de thread esgotada.
- Retry apenas em operação idempotente e em erro transitório (timeout, 5xx, 429). Nunca em `4xx` de negócio.
- Backoff exponencial com jitter. Retry imediato em cascata gera tempestade.
- Circuit breaker (Resilience4j) com fallback definido — fallback que retorna `null` silencioso é pior que falhar.

### 12.2 Virtual threads e async

```yaml
spring.threads.virtual.enabled: true
```

Virtual threads brilham em I/O bloqueante (HTTP, banco) com alta concorrência. Cuidados:
- **`synchronized` pode pinçar** a virtual thread em versões anteriores; prefira `ReentrantLock` em seção crítica com I/O.
- Não são ganho para CPU-bound.
- **Não combine com pool limitado** esperando ganho: se o pool de banco tem 20 conexões, 10.000 virtual threads só enfileiram.
- Cancele o `ExecutorService` no shutdown; `@Async` sem executor controlado segue sendo fonte de thread leak.

### 12.3 Cache

- Cache só onde há leitura repetida de dado estável. Cache de dado volátil gera bug de consistência que ninguém reproduz.
- Defina **TTL** e **invalidação explícita** na escrita. Um sem o outro é dívida.
- **Cache stampede:** ao expirar uma chave quente, N requisições vão ao banco. Use probabilístico (`XFetch`) ou lock de recomputação.
- **Nunca** cacheie entidade JPA gerenciada. Cacheie DTO imutável.
- Cache local (Caffeine) para dado por instância; Redis para dado compartilhado. Misturar sem estratégia gera inconsistência entre nós.

### 12.4 Empacotamento e JVM

- Fat jar com camadas (`layertools`) para aproveitar cache de imagem Docker.
- Imagem base multi-stage, usuário não-root, `-XX:MaxRAMPercentage=75` em vez de `-Xmx` fixo.
- JVM: prefira G1 (default) para a maioria; ZGC para heaps grandes com requisito de pausa baixa.
- Habilite `-XX:+HeapDumpOnOutOfMemoryError` com path persistente.

---

## 13. Catálogo de armadilhas

Cada item abaixo é um incidente real recorrente. Trate como checklist negativo.

**Arquitetura e camadas**
- Controller com regra de negócio: impossível testar sem HTTP.
- Domínio anêmico: entidade só com getters, regra espalhada em services.
- Entidade JPA vazando para o JSON de resposta: acopla contrato ao banco.
- `Service` chamando outro `Service` que chama outro `Service`: transações aninhadas e ciclo.

**Persistência**
- N+1 silencioso em listagem.
- `open-in-view: true` escondendo N+1.
- `EAGER` em `@ManyToOne` por default não anulado.
- `ddl-auto: update` em produção.
- `join fetch` + `Pageable` em coleção: paginação em memória.
- `@Transactional` em self-invocation sem efeito.
- `equals`/`hashCode` de entidade baseado em ID gerado.
- `double` para dinheiro.
- `@Enumerated(ORDINAL)`.

**Código**
- `catch (Exception e) {}` vazio.
- `Optional.get()` sem `isPresent`.
- `null` como contrato de "não encontrado" em método público.
- `static` mutável como cache improvisado.
- `System.out.println` em produção.
- Configuração hardcoded (`if (env.equals("prod"))` no meio da regra).
- `Thread.sleep` como sincronização.

**API**
- Erro `200` com corpo de falha.
- `500` para erro de validação.
- Stack trace no corpo de erro.
- Paginação sem limite máximo de `size`.
- POST sem idempotência em operação financeira.
- CORS com `*` e credenciais.

**Testes**
- Teste sem asserção real.
- `@SpringBootTest` para lógica pura.
- Mock de tudo, incluindo objeto sob teste.
- Cobertura como meta, não como diagnóstico.
- `LocalDateTime.now()` dentro da regra (impossível testar).

**Operação**
- Cliente HTTP sem timeout.
- Retry sem backoff.
- Log com PII.
- Health check que só verifica processo vivo.
- Pool de conexão superdimensionado.
- Migration destrutiva no mesmo deploy que remove o código.

---

## 14. Checklists operacionais

### 14.1 Antes de abrir PR

- Compila, `mvn verify` verde, testes novos cobrem o comportamento novo.
- Sem Lombok novo, sem `@Autowired` em campo, sem `System.out`.
- Fronteiras de camada respeitadas (ArchUnit verde).
- Query nova: tem índice? não é N+1? tem teste que conta queries se for listagem?
- Contrato de API alterado: versionado e documentado no OpenAPI.
- Config nova: tipada, validada, documentada, sem secret no repositório.
- Log novo: sem PII, com `correlationId`.
- Migration nova: compatível para trás, testada com Testcontainers.
- Tratamento de erro de I/O externo: timeout, retry, fallback.

### 14.2 Antes de ir para produção

- `ddl-auto: validate`, `open-in-view: false`.
- Actuator com readiness real e graceful shutdown configurado.
- Pool dimensionado, timeouts definidos em todos os clientes.
- Métricas de negócio e trace coletando; alertas configurados para latência p95, taxa de erro e saturação de pool.
- Plano de rollback testado (migration reversível ou expand/contract).
- Carga testada com volume realista (pelo menos 2× o pico conhecido).
- Secrets via env/secret manager, rotação definida.
- Documentação de API publicada e protegida.

---

## Resumindo

- **A regra que sustenta a arquitetura:** `domain` e `application` não conhecem `infrastructure` nem Spring. Fiscalize com ArchUnit, não com boa vontade.
- **Três padrões que eliminam a maior parte dos incidentes:** injeção por construtor, `record` no lugar de DTO/Lombok, `LAZY` com `open-in-view: false` desde o dia 1.
- **Persistência é onde o risco se concentra:** N+1, `EAGER` acidental, `join fetch` com paginação e `@Transactional` sem efeito cobrem a maior parte — e todos são detectáveis com teste que conta queries.
- **Teste sem asserção real e cobertura como meta dão falsa segurança.** Invista em teste de comportamento e em teste de mutação nos módulos críticos.
- **Lombok não é proibido por gosto:** `@Data` em entidade quebra contrato de `hashCode` e dispara lazy loading. Record resolve com menos código.

## Se quiser, posso

- Converter este documento em `.docx` ou `.pdf` pronto para o repositório de padrões da equipe.
- Gerar o `pom.xml`/`build.gradle` com Spotless, Checkstyle, JaCoCo, ArchUnit e Testcontainers já configurados para Java 25.
- Produzir um template de projeto Spring Boot 4.1 completo com a estrutura de pacotes, `ProblemDetail`, `@ConfigurationProperties` e um caso de uso de exemplo com teste.
- Criar as regras ArchUnit específicas do seu domínio para bloquear violações no CI.

# Itens 2,3 e 4
Consigo. E o mais útil aqui não é justapor os três blocos — é mostrar que eles **não são temas paralelos**, e sim três disciplinas que atuam sobre o mesmo código em momentos diferentes:

- **Lean** decide **o que existe** (e o que não deve existir).
- **SOLID** decide **onde cada coisa mora**.
- **RESTful** é a fronteira onde os dois são testados sob pressão, porque é a única camada que o cliente externo vê.

Aplicar SOLID antes de Lean produz abstração inflada. Aplicar Lean antes de SOLID produz classe-deus enxuta. Os três blocos, juntos, formam um único critério de decisão. Segue o capítulo integrado.

---

# Parte II — API RESTful sob Lean Code e SOLID

**Base:** Spring Boot 4.1 · Java 25
**Exemplo condutor:** um único fluxo (`POST /v1/pedidos`) que evolui ao longo do capítulo, para você ver as mesmas decisões se cruzando.

---

## 1. A tese: por que os três juntos

Um controller mal escrito viola os três blocos simultaneamente, e é por isso que estudá-los separados não resolve o problema:

| Sintoma no controller | Bloco violado | Por quê |
|---|---|---|
| `if (pedido.getTotal() > limite)` na borda | SOLID (SRP) | Regra de negócio em camada de transporte |
| `PedidoEntity` retornada no JSON | REST (fronteira) | Contrato acoplado ao schema do banco |
| `PedidoDTO` → `PedidoModel` → `PedidoEntity` 1:1 | Lean | Três classes para zero transformação |
| `Service` com 15 métodos | SOLID (SRP) + Lean | Classe-deus, e cada método é código que precisa existir |
| `switch` sobre tipo de frete em 4 endpoints | SOLID (OCP) | Cada novo tipo edita N arquivos |
| Tratamento de erro espalhado em `try/catch` | Lean + OCP | Duplicação que cresce a cada exceção nova |

O critério unificado que vou usar como fio condutor:

> **Toda classe precisa responder a duas perguntas ao mesmo tempo: "por que eu mudaria?" (SOLID) e "por que eu existo?" (Lean). Se a resposta para a segunda for "porque a camada de cima precisa passar dado para a de baixo", a classe não deve existir.**

---

## 2. A anatomia de uma requisição bem desenhada

Fluxo completo do `POST /v1/pedidos`, camada por camada, com o princípio que justifica cada peça.

```
HTTP POST /v1/pedidos
   │
   ├─ PedidoController            [adaptador] — traduz HTTP ↔ aplicação. Zero regra.
   │     ↓ CriarPedido (porta de entrada — interface, 1 método)
   ├─ CriarPedidoUseCase          [aplicação] — 1 transação, 1 motivo para mudar
   │     ↓ PedidoRepository (porta de saída)
   ├─ Pedido (domínio)            [domínio] — invariantes, zero framework
   │     ↓
   └─ JpaPedidoRepository         [adaptador] — implementa a porta, conhece JPA
```

Cinco artefatos. Nenhum deles existe por cerimônia: cada um tem um motivo de mudança distinto e nenhum é mapeamento 1:1 de outro.

### 2.1 A porta de entrada — ISP aplicado

```java
// domain/port/in/CriarPedido.java
public interface CriarPedido {
    PedidoOutput executar(CriarPedidoInput input);
}
```

Uma interface, um método, um caso de uso. Isso é **ISP** (ninguém é forçado a implementar o que não usa) e **SRP** ao mesmo tempo — e é o que impede o nascimento do `PedidoService`.

**Alerta Lean:** a interface só se justifica aqui porque é **fronteira arquitetural** (o adaptador web depende da abstração, não da implementação). Se você criar `PedidoMapper` como interface com uma implementação, está pagando DIP sem necessidade — isso é cerimônia, não Lean.

### 2.2 O controller — a superfície deve ser um sumário

```java
// infrastructure/web/PedidoController.java
@RestController
@RequestMapping("/v1/pedidos")
public class PedidoController {

    private final CriarPedido criarPedido;
    private final ConsultarPedido consultarPedido;   // porta separada, não "PedidoService"

    public PedidoController(CriarPedido criarPedido, ConsultarPedido consultarPedido) {
        this.criarPedido = criarPedido;
        this.consultarPedido = consultarPedido;
    }

    @PostMapping
    public ResponseEntity<PedidoOutput> criar(@Valid @RequestBody CriarPedidoInput input) {
        var output = criarPedido.executar(input);
        return ResponseEntity
                .created(URI.create("/v1/pedidos/" + output.id()))
                .body(output);
    }

    @GetMapping("/{id}")
    public PedidoOutput porId(@PathVariable Long id) {
        return consultarPedido.executar(id);
    }
}
```

Leia o controller como índice do caso de uso. Se você precisa de `if`, `for`, `try`, `BigDecimal` ou `LocalDate` dentro dele, a regra vazou.

**Três proibições concretas:**

- `@Transactional` no controller — a transação vira do transporte.
- `try/catch` no controller — o tratamento é responsabilidade do `@ControllerAdvice` (OCP).
- `PedidoEntity` na assinatura — a entidade JPA vira contrato de API.

### 2.3 O caso de uso — o único lugar com transação

```java
// application/usecase/pedido/CriarPedidoUseCase.java
@Service
public class CriarPedidoUseCase implements CriarPedido {

    private final PedidoRepository pedidoRepository;
    private final ClienteGateway clienteGateway;
    private final Clock clock;

    // construtor omitido

    @Override
    @Transactional
    public PedidoOutput executar(CriarPedidoInput input) {
        var cliente = clienteGateway.porId(input.clienteId())
                .orElseThrow(() -> new RecursoNaoEncontradoException("cliente", input.clienteId()));

        var pedido = Pedido.criar(cliente.id(), input.itens(), clock.instant());
        var salvo = pedidoRepository.salvar(pedido);

        return PedidoOutput.de(salvo);
    }
}
```

Quatro decisões embutidas, cada uma resolvendo um dos blocos:

- **SRP:** o método só orquestra. Cálculo está em `Pedido.criar`, persistência no adapter.
- **DIP:** depende de `PedidoRepository` (porta), não de `JpaRepository`.
- **Lean:** sem DTO intermediário — `CriarPedidoInput` entra, `PedidoOutput` sai, e `PedidoOutput.de()` é fábrica estática em vez de uma classe `Mapper` com um método.
- **Transação:** começa e termina aqui. A chamada externa (`clienteGateway`) está antes, curta, e não há I/O HTTP dentro de transação longa.

**Atenção à ordem:** carregar o cliente antes de abrir a escrita reduz a janela transacional. Se a chamada externa fosse dentro da transação depois do `salvar`, você seguraria conexão de banco durante o timeout do parceiro.

---

## 3. Lean Code na fronteira: o catálogo do excesso

A camada web é onde a cerimônia se multiplica, porque cada nova camada parece "mais profissional". Estes são os padrões que **devem ser recusados em code review**:

### 3.1 O triângulo de cerimônia

```
CriarPedidoRequest  ──1:1──►  CriarPedidoInput  ──1:1──►  PedidoEntity
      (web)                      (aplicação)                    (JPA)
```

Três records/classe com campos idênticos e um `Mapper` de 40 linhas que copia campo por campo. **Zero valor entregue, três lugares para manter.**

**Critério de decisão:** se os três têm os mesmos campos e nenhuma anotação diferente, use **um** record na camada de aplicação e deixe o controller e o adapter tiparem por ele. Camadas se justificam por **transformação de conceito**, não por posição no diagrama.

**Quando o desdobramento se justifica:**

- O DTO web tem anotação de validação de forma que não pertence ao caso de uso (`@Size`, `@Pattern`).
- O caso de uso é chamado por mais de um adaptador (HTTP, fila, CLI) e cada um tem formato próprio.
- A entidade JPA tem campos que não devem existir em nenhuma saída (`senhaHash`, `deletedAt`).

Se nenhum dos três se aplica, **colapse**.

### 3.2 A interface com uma implementação

```java
public interface PedidoService { PedidoOutput criar(CriarPedidoInput input); }

@Service
public class PedidoServiceImpl implements PedidoService { /* único impl */ }
```

Isso é DIP mal aplicado: pagou-se a abstração sem a fronteira que a justifica. **Regra:** interface só em `domain/port/` (fronteira arquitetural) ou quando há 2+ implementações reais **hoje**.

### 3.3 O mapper manual e o mapper desnecessário

```java
// 40 linhas para copiar campos de mesmo nome
public static PedidoOutput toOutput(Pedido pedido) {
    return new PedidoOutput(pedido.getId(), pedido.getClienteId(), /* ... */);
}

// versus: fábrica no próprio record de saída
public record PedidoOutput(Long id, Long clienteId, Status status, BigDecimal total) {
    public static PedidoOutput de(Pedido pedido) {
        return new PedidoOutput(pedido.id(), pedido.clienteId(), pedido.status(), pedido.total().valor());
    }
}
```

A fábrica estática mora junto do contrato que ela produz. Um `Mapper` como classe separada só se justifica quando a conversão é **não trivial** e **reutilizada** por vários casos de uso.

### 3.4 O `@Component` em tudo

`@Component` em classe de domínio, em DTO, em enum utilitário. Anotação de framework em código que deveria ser puro torna o teste dependente de contexto. Se a classe não precisa ser **injetada**, não precisa de anotação.

### 3.5 Paginação: o contrato mínimo

```java
public record PageResponse<T>(List<T> conteudo, int pagina, int tamanho, long total, int totalPaginas) {
    public static <T> PageResponse<T> de(Page<T> page) {
        return new PageResponse<>(page.getContent(), page.getNumber(),
                page.getSize(), page.getTotalElements(), page.getTotalPages());
    }
}
```

Record de 5 campos, uma fábrica estática. Não crie `PageResponseFactory`, `PaginationHelper` e `PageMapper` — isso é Lean invertido.

E o limite de `size` pertence a este ponto: sem teto no servidor, `size=100000` é um DoS acidental.

---

## 4. SOLID na fronteira: os cinco aplicados de verdade

### 4.1 SRP — a medida que funciona na prática

Não use "uma classe, uma responsabilidade" — é abstrato demais. Use a medida operacional:

> **Uma classe tem uma responsabilidade quando você consegue nomear o motivo de mudança em uma frase que não tem "e".**

`PedidoService`: "cria pedido **e** aprova **e** cancela **e** gera relatório" → quatro motivos, quatro classes.
`CriarPedidoUseCase`: "cria pedido a partir de uma solicitação" → um.

**Como detectar antes do desastre:** se o PR toca o mesmo arquivo por motivos não relacionados em duas semanas consecutivas, o arquivo tem mais de uma responsabilidade.

Fronteira prática: quebre por **caso de uso**, não por entidade. `PedidoService` com 25 métodos é o resultado de organizar por substantivo.

### 4.2 OCP — Strategy sem cerimônia

O `if/else` sobre enum é o cheiro de OCP mais comum em API.

```java
// OCP violado: cada novo tipo edita este método
public BigDecimal calcularFrete(TipoFrete tipo, Pedido pedido) {
    if (tipo == TipoFrete.EXPRESSO) return /* ... */;
    else if (tipo == TipoFrete.PADRAO) return /* ... */;
    else if (tipo == TipoFrete.RETIRADA) return BigDecimal.ZERO;
    throw new IllegalArgumentException();
}
```

Versão extensível, registrada automaticamente pelo Spring:

```java
public interface CalculadoraFrete {
    TipoFrete tipo();                                              // ISP: interface mínima
    BigDecimal calcular(Pedido pedido);
}

@Component
class FreteExpresso implements CalculadoraFrete {
    public TipoFrete tipo() { return TipoFrete.EXPRESSO; }
    public BigDecimal calcular(Pedido pedido) { /* ... */ }
}

@Service
public class FreteService {
    private final Map<TipoFrete, CalculadoraFrete> calculadoras;

    public FreteService(List<CalculadoraFrete> implementacoes) {
        this.calculadoras = implementacoes.stream()
                .collect(Collectors.toMap(CalculadoraFrete::tipo, Function.identity()));
    }

    public BigDecimal calcular(TipoFrete tipo, Pedido pedido) {
        var calc = calculadoras.get(tipo);
        if (calc == null) throw new RegraNegocioException("tipo de frete não suportado: " + tipo);
        return calc.calcular(pedido);
    }
}
```

**Mas o Lean limita o OCP.** Três condições para criar Strategy:

1. Existem **3+ variantes** hoje, ou 2 com crescimento já comprometido em roadmap.
2. A regra varia **por tipo**, não por combinação de fatores (se varia por 3 dimensões, o mapa não resolve — reveja a modelagem).
3. Cada variante tem **complexidade própria** (5+ linhas). Duas fórmulas de uma linha não justificam uma interface.

Se as condições não se aplicam, mantenha o `switch` — mas centralizado em **um único lugar**, não replicado em quatro endpoints. Um `switch` em um lugar é Lean; o mesmo `switch` em quatro endpoints é dívida.

**Variante de Java 25 que dispensa o mapa:** se as variantes são puras e sem dependência injetada, `sealed interface` + pattern matching com exaustividade verificada pelo compilador:

```java
public sealed interface Frete {
    record Expresso(BigDecimal multiplicador) implements Frete {}
    record Padrao() implements Frete {}
    record Retirada() implements Frete {}
}

public BigDecimal calcular(Frete frete, Pedido pedido) {
    return switch (frete) {
        case Frete.Expresso e -> pedido.subtotal().multiplicar(e.multiplicador());
        case Frete.Padrao p   -> TAXA_PADRAO;
        case Frete.Retirada r -> BigDecimal.ZERO;
    };   // compilador garante exaustividade — novo caso quebra o build
}
```

Esse é o OCP mais barato que Java oferece hoje: **o compilador é o mecanismo de extensão**. Adicionar variante quebra o build em todo lugar que precisa de decisão. Só use Strategy com injeção quando a variante tem dependência externa.

### 4.3 LSP — o contrato que filhos não podem quebrar

Aplicado a API, LSP aparece em três lugares:

- **Exceções:** `PagamentoRecusadoException` e `EstoqueIndisponivelException` não são irmãs de `RegraNegocioException` por "reuso de código". Herança de exceção deve refletir **semântica de tratamento**, não conveniência.
- **`equals`/`hashCode`:** sobrescrever de forma inconsistente entre hierarquia quebra o contrato de `Object`.
- **Implementações de porta:** se `JpaPedidoRepository` lança exceção onde `PedidoRepository` promete `Optional.empty()`, o adaptador quebrou LSP e o contrato virou mentira.

O último é o mais perigoso porque não aparece em teste unitário. **Escreva o contrato no Javadoc da porta e teste o adapter contra ele.**

### 4.4 ISP — a porta não herda 20 métodos

```java
// ERRADO: porta acoplada ao Spring Data
public interface PedidoRepository extends JpaRepository<PedidoEntity, Long> { }

// CORRETO: porta por necessidade
public interface PedidoRepository {
    Pedido salvar(Pedido pedido);
    Optional<Pedido> porId(PedidoId id);
}

// Leitura pesada em porta separada
public interface RelatorioPedidoQuery {
    BigDecimal totalPorPeriodo(LocalDate inicio, LocalDate fim);
}
```

`JpaRepository` traz `flush`, `deleteAllInBatch`, `getReferenceById` e mais 15 métodos que 80% dos consumidores nunca usam — e que acoplam o domínio ao ecossistema Spring Data. **Nunca estenda `JpaRepository` na porta de domínio.** O adapter pode usar; a porta não.

### 4.5 DIP — a inversão que o nome da arquitetura promete

```
domain/port/out/PedidoRepository          ← o domínio define
infrastructure/persistence/JpaPedidoRepository  ← a infraestrutura obedece
```

O teste de validade é simples:

> **Se a interface fala em `Page`, `Specification`, `EntityManager` ou `@Query`, a inversão não aconteceu.** O domínio está delegando ao framework, não o contrário.

---

## 5. Tratamento de erro — onde Lean e OCP se encontram

Este é o ponto do capítulo onde os três blocos realmente convergem. Erro mal tratado é simultaneamente duplicado (Lean), espalhado por N camadas (SRP) e fechado para extensão (OCP).

### 5.1 O antipadrão

```java
// Em cada endpoint
try {
    return service.criar(input);
} catch (RegraNegocioException e) {
    return ResponseEntity.badRequest().body(Map.of("erro", e.getMessage()));
} catch (Exception e) {
    log.error("erro", e);
    return ResponseEntity.status(500).body(Map.of("erro", "Erro interno"));
}
```

Quatro falhas em cinco linhas: duplicação em todo controller, `200`/`400` arbitrário, sem contrato de erro, e a mensagem de stack trace vaza para o cliente.

### 5.2 A versão extensível e padronizada

```java
@RestControllerAdvice
public class ApiExceptionHandler {

    private static final Logger log = LoggerFactory.getLogger(ApiExceptionHandler.class);

    @ExceptionHandler(RecursoNaoEncontradoException.class)
    ProblemDetail naoEncontrado(RecursoNaoEncontradoException ex) {
        var pd = ProblemDetail.forStatusAndDetail(HttpStatus.NOT_FOUND, ex.getMessage());
        pd.setType(URI.create("https://api.exemplo.com/problems/recurso-nao-encontrado"));
        pd.setTitle("Recurso não encontrado");
        pd.setProperty("codigo", "RECURSO_NAO_ENCONTRADO");
        pd.setProperty("correlationId", MDC.get("correlationId"));
        return pd;
    }

    @ExceptionHandler(RegraNegocioException.class)
    ProblemDetail regraNegocio(RegraNegocioException ex) {
        var pd = ProblemDetail.forStatusAndDetail(HttpStatus.UNPROCESSABLE_ENTITY, ex.getMessage());
        pd.setType(URI.create("https://api.exemplo.com/problems/regra-negocio"));
        pd.setTitle("Regra de negócio violada");
        pd.setProperty("codigo", ex.codigo());
        pd.setProperty("correlationId", MDC.get("correlationId"));
        return pd;
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    ProblemDetail validacao(MethodArgumentNotValidException ex) {
        var pd = ProblemDetail.forStatusAndDetail(HttpStatus.BAD_REQUEST, "Falha de validação");
        pd.setType(URI.create("https://api.exemplo.com/problems/validacao"));
        pd.setProperty("campos", ex.getBindingResult().getFieldErrors().stream()
                .map(f -> Map.of("campo", f.getField(), "mensagem", f.getDefaultMessage()))
                .toList());
        return pd;
    }

    @ExceptionHandler(Exception.class)
    ProblemDetail inesperado(Exception ex) {
        log.error("erro inesperado correlationId={}", MDC.get("correlationId"), ex);
        var pd = ProblemDetail.forStatusAndDetail(HttpStatus.INTERNAL_SERVER_ERROR, "Erro interno");
        pd.setType(URI.create("https://api.exemplo.com/problems/erro-interno"));
        pd.setProperty("codigo", "ERRO_INTERNO");
        pd.setProperty("correlationId", MDC.get("correlationId"));
        return pd;
    }
}
```

Por que esta versão resolve os três blocos de uma vez:

- **Lean:** o `try/catch` desaparece de todos os controllers. Um lugar, não N.
- **SOLID (OCP):** nova exceção de negócio = novo método `@ExceptionHandler`. Nenhum controller é editado. O handler cresce por adição, não por modificação.
- **REST:** `ProblemDetail` (RFC 9457) é contrato padronizado, com `codigo` estável consumível por cliente, e `correlationId` para rastreio.

**Regra dura:** a exceção genérica **sempre** tem handler, sempre loga com correlation ID, e **nunca** devolve `getMessage()` ou stack trace ao cliente. Sem esse catch-all, uma `NullPointerException` devolve a página de erro padrão do Spring com detalhes internos.

### 5.3 O contrato de erro como parte da API

```json
{
  "type": "https://api.exemplo.com/problems/regra-negocio",
  "title": "Regra de negócio violada",
  "status": 422,
  "detail": "Pedido não pode ser aprovado com status CANCELADO",
  "codigo": "PEDIDO_STATUS_INVALIDO",
  "correlationId": "b1f3c2a4-8e7d-4f1a-9b6c-2d5e8a7f0c93"
}
```

O campo `codigo` é o que permite ao cliente programar contra o erro. `detail` é para humano, `codigo` é para máquina. **Nunca** faça o cliente comparar string de mensagem — é o erro que quebra na primeira revisão de texto.

---

## 6. Validação — SRP na fronteira

A divisão de responsabilidades aqui é o exemplo mais nítido de SRP bem aplicado em API.

### 6.1 Duas responsabilidades, dois mecanismos

| Tipo | Onde | Mecanismo | Exemplo |
|---|---|---|---|
| **Forma** | DTO de entrada | `jakarta.validation` | `@NotNull`, `@Positive`, `@Size(max = 50)` |
| **Negócio** | Domínio / caso de uso | Código explícito | Unicidade, transição de estado, saldo disponível |

```java
public record CriarPedidoInput(
        @NotNull @Positive Long clienteId,
        @NotEmpty @Size(max = 50) List<@Valid ItemInput> itens,
        @NotNull @PositiveOrZero BigDecimal desconto
) {}
```

### 6.2 O antipadrão: validador que consulta banco

```java
@Documented
@Constraint(validatedBy = SkuUnicoValidator.class)   // ← validador que acessa repository
@Target(ElementType.FIELD)
public @interface SkuUnico { /* ... */ }
```

Três consequências, todas ruins:

- A validação de forma agora depende do banco → teste unitário do DTO vira integração.
- A ordem de execução deixa de ser previsível (o validador pode rodar antes do objeto estar completo).
- A camada web ganha transação e acesso a repositório — violação direta de SRP e de fronteira.

**Regra:** anotação de validação verifica **o dado**, nunca **o estado do sistema**. Unicidade de SKU pertence ao caso de uso, com a constraint de banco como rede de segurança final.

### 6.3 As três camadas de proteção que não se sobrepõem

1. **DTO** (`@Valid`) — payload malformado vira `400` sem chamar o caso de uso.
2. **Domínio** — invariante de agregado lança `RegraNegocioException` → `422`.
3. **Banco** — `UNIQUE`, `CHECK`, `NOT NULL` → `409`/`422` após tradução do handler.

Sobreposição intencional, papéis distintos. Remover a camada de banco "porque o código já valida" é o caminho para dado inconsistente via migration ou script manual.

---

## 7. O que o Lean proíbe na fronteira (e por quê)

Catálogo de artefatos que parecem arquitetura e **devem ser recusados**:

- **`Mapper` quando os nomes são idênticos** — use fábrica estática no record de saída.
- **`Converter` de um campo** — coloque no construtor compacto do record.
- **`Facade` que delega para um service** — a camada extra não agrega nada; o controller pode chamar a porta.
- **`BaseController` com métodos utilitários** — herança em controller é quase sempre o caminho para acoplamento horizontal. Se você precisa dela, a lógica está no lugar errado (deve estar em `@ControllerAdvice` ou em mixin do Jackson).
- **`Service` que só repassa** — `return repository.findById(id)` em um método é um caso de uso legítimo **se** houver regra (validação de visibilidade, DTO). Se não houver, o controller chama a porta de consulta.
- **`DTO` de resposta com todos os campos da entidade** — se o cliente não usa 40% dos campos, você está pagando serialização e acoplando o contrato ao schema.

### 7.1 Quando a abstração se justifica

| Artefato | Justificativa válida | Justificativa inválida |
|---|---|---|
| Interface de porta | Fronteira arquitetural ou 2+ impl reais | "Facilita mock futuro" |
| DTO separado da entidade | Campo sensível, anotação de forma, adaptador múltiplo | "É boa prática" |
| Strategy | 3+ variantes com dependência injetada | "Pode crescer" |
| Mapper dedicado | Conversão não trivial e reutilizada | "Separar responsabilidades" |
| Classe base | Nunca em controller | Conveniência |

---

## 8. Matriz de diagnóstico: sintoma → princípio → correção

A tabela que você leva para o code review.

| Sintoma observado | Princípio violado | Correção Lean-compatível |
|---|---|---|
| Controller com `if`/`for`/`try` | SRP | Mover para caso de uso |
| Controller com `@Transactional` | SRP | Mover para caso de uso |
| `PedidoEntity` na assinatura HTTP | Fronteira REST + DIP | Record de saída + fábrica estática |
| `Page` do Spring no JSON | Fronteira REST | `PageResponse` record |
| `try/catch` repetido em endpoints | Lean + OCP | `@RestControllerAdvice` |
| String de erro comparada no cliente | Contrato REST | Campo `codigo` estável |
| `switch` sobre enum em 3+ arquivos | OCP | Strategy ou sealed + switch exaustivo |
| `XxxServiceImpl` único | Lean + DIP mal aplicado | Remover interface |
| DTO → Model → Entity 1:1 | Lean | Colapsar em um record |
| `JpaRepository` na porta | ISP + DIP | Porta mínima própria |
| Service com 15 métodos | SRP | Quebrar por caso de uso |
| Validador que acessa repository | SRP | Mover para caso de uso |
| Erro de negócio retornando `400` | REST | `422` para regra, `400` para forma |
| `200` com corpo `{"erro": ...}` | REST | Status HTTP correto |

---

## 9. Checklists

### 9.1 Code review — fronteira web

- O controller tem apenas anotação, delegação e `ResponseEntity`?
- A assinatura usa record, nunca entidade JPA?
- Status e `Location` corretos para criação?
- `size` de paginação tem teto no servidor?
- Erro novo tem handler dedicado, sem editar controller existente?
- Corpo de erro tem `codigo` estável e `correlationId`?
- Nenhum campo sensível no DTO de saída?
- `@Valid` no `@RequestBody`?

### 9.2 Code review — aplicação e domínio

- O caso de uso tem **um** método público e **uma** transação?
- Há I/O externo dentro de transação?
- A porta de saída fala em termos de domínio (não `Page`, `Specification`, `@Query`)?
- A interface tem 2+ implementações hoje ou é fronteira arquitetural?
- Há `switch` sobre tipo em mais de um arquivo?
- Método tem mais de 2 níveis de aninhamento?
- Classe tem mais de 5 dependências injetadas?

### 9.3 Antes do merge

- ArchUnit verde (domínio sem Spring, controller sem repository)?
- OpenAPI atualizado se o contrato mudou?
- Teste de fatia (`@WebMvcTest`) cobre os status codes novos?
- Teste do caso de uso cobre o caminho de erro, não só o de sucesso?

---

## Resumindo

- **Os três blocos se resolvem por uma pergunta única:** existe motivo de mudança próprio? Se não, a classe não deve existir (Lean) — e o controller é onde a pergunta é mais ignorada.
- **Strategy não é padrão obrigatório:** para variantes puras sem injeção, `sealed interface` + `switch` exaustivo com Java 25 dá OCP **verificado pelo compilador** com uma fração do código.
- **`@RestControllerAdvice` é o ponto onde Lean e OCP se encontram:** elimina duplicação de `try/catch` e cresce por adição quando surge exceção nova.
- **Validação de forma no DTO, de negócio no domínio:** misturar produz validador que consulta banco, DTO acoplado a transação e teste unitário impossível.
- **A matriz sintoma → princípio → correção é a ferramenta prática** — use em review. Sem ela, "SOLID" e "Lean" viram opinião na reunião.
- **Fronteira arquitetural é o único critério que justifica interface:** "facilita mock futuro" e "pode crescer" são justificativas de cerimônia, não de design.

Se quiser, posso:

- Escrever a Parte III integrando os Blocos 5, 6 e 7 (arquitetura, qualidade e testes) com o mesmo fio condutor — como o teste revela a violação de SOLID antes do code review.
- Gerar as regras ArchUnit exatas para as proibições deste capítulo, prontas para o CI.
- Criar um projeto de referência executável com esse fluxo completo (`PedidoController` → `CriarPedidoUseCase` → adapter JPA) incluindo `@WebMvcTest`, `@DataJpaTest` com Testcontainers e teste de N+1.

# Parte III
# Parte III — Arquitetura, Qualidade e Testes como um único sistema de verificação

**Base:** Spring Boot 4.1 · Java 25
**Fio condutor:** o teste é o primeiro code review do seu design — e o único que não depende de opinião.

---

## 1. A tese: teste difícil não é obstáculo, é diagnóstico

Na Parte II o critério foi: *toda classe precisa responder "por que eu mudaria?" e "por que eu existo?"*. Naquela fronteira, o code review aplicava o critério.

Aqui o critério é diferente e mais duro, porque é verificável por máquina:

> **Se escrever o teste é difícil, o design está errado. A dificuldade não é um problema a ser contornado com mock, `ReflectionTestUtils` ou `@SpringBootTest` — ela é a informação mais precisa sobre o seu acoplamento que você vai receber em todo o ciclo.**

Isso muda a posição do teste no processo. Ele deixa de ser atividade de verificação no fim e passa a ser **instrumento de medição de arquitetura** no início.

### 1.1 O que o code review vê e o que o teste vê

Um revisor experiente lê 400 linhas e identifica violações óbvias. Mas há uma classe inteira de defeitos que ele **não consegue** detectar por leitura:

| Defeito | Code review detecta? | Teste detecta? |
|---|---|---|
| `@Transactional` em self-invocation sem efeito | Não (parece correto) | Sim (o teste falha ao não ver rollback) |
| N+1 silencioso | Não | Sim (contagem de queries) |
| `hashCode` de entidade mudando após `save` | Não | Sim (`HashSet` corrompido) |
| Cliente HTTP sem timeout | Não (parece correto) | Sim (teste de resiliência trava) |
| `open-in-view` mascarando vazamento | Não | Sim (falha ao desligar) |
| Metric/log sem correlation ID | Não | Sim (asserção no `MDC`) |
| Fronteira de camada rompida | Parcialmente | Sim (ArchUnit) |
| Cálculo com arredondamento errado em 3 casas | Talvez | Sim |

Leitura humana encontra **estilo e estrutura**. O teste encontra **comportamento sob condição**. Você precisa dos dois, mas só o segundo é executável em cada commit.

### 1.2 Tabela de leitura de design

Esta é a tabela de trabalho do capítulo. Ela converte "o teste está chato de escrever" em "esta é a violação".

| Sintoma ao escrever o teste | Defeito de design | Bloco violado |
|---|---|---|
| Setup com 6+ mocks | Classe orquestra demais | SRP (classe-deus) |
| Precisa subir o contexto Spring para testar cálculo | Regra de negócio acoplada ao framework | DIP / fronteira de camada |
| Precisa de banco para testar transição de estado | Regra mora dentro da entidade JPA | SRP / fronteira de persistência |
| `ReflectionTestUtils.setField` para injetar dependência | Field injection | SRP (contrato implícito) |
| `Thread.sleep` como sincronização | Tempo/concorrência não injetados | Testabilidade / desenho de dependência |
| `@DirtiesContext` em toda classe | Estado estático mutável | SRP / escopo de bean |
| `verify(mock, times(3))` na ordem exata | Teste acoplado à implementação | ISP / contrato mal definido |
| 40 linhas de construção de dado | Modelo não expressivo | Modelagem de domínio |
| Não consegue nomear o teste em uma frase | Método com múltiplos comportamentos | SRP (método) |
| `@MockBean` de repositório para testar controller | Controller acessa dados diretamente | Fronteira de camada |
| Precisa mockar o próprio objeto testado | Self-invocation / proxy quebrado | SRP / transação |
| Teste quebra ao reordenar campos do DTO | Contrato baseado em identidade total | Modelagem de igualdade |

Cada linha dessa tabela é uma decisão de arquitetura saindo do campo da opinião. Em vez de discutir se a classe tem "responsabilidade única", você diz: *este teste precisa de sete mocks — a classe tem sete motivos de mudança.*

---

## 2. Os três ciclos de feedback e o custo de errar tarde

Arquitetura, qualidade e teste não são fases; são **ciclos com tempos de retorno radicalmente diferentes**. O papel do pipeline é empurrar a descoberta para o ciclo mais curto possível.

| Ciclo | Tempo de retorno | Ferramenta | Custo de descobrir o defeito aqui | Custo de descobrir em produção |
|---|---|---|---|---|
| Teste unitário | milissegundos | JUnit, AssertJ | minutos | dias |
| Análise estática | segundos | SpotBugs, ArchUnit, ErrorProne | minutos | dias |
| Teste de fatia | segundos | `@WebMvcTest`, `@DataJpaTest` | minutos | dias |
| Integração | dezenas de segundos | Testcontainers | horas | dias |
| Review humano | horas | pessoa | horas | dias |
| Observabilidade | minutos a dias | Micrometer, trace | — | **é a descoberta** |

A conclusão prática é desconfortável: **tudo que só o revisor consegue ver está pagando o ciclo mais caro da lista.** Por isso cada regra arquitetural que pode virar teste deve virar teste — ArchUnit não é luxo de time grande, é a única forma de fiscalizar fronteira sem depender de disciplina.

E a última linha da tabela é a mais importante: **quando o defeito escapa de tudo isso, quem descobre é o monitoramento.** Isso significa que observabilidade não é o "Bloco 6" isolado — é o último teste da cadeia, rodando em produção, com dados reais. Um sistema com excelentes testes e sem trace é um sistema que você descobre quebrado pelo cliente.

---

## 3. As leituras de design, uma por uma

### 3.1 Muitos mocks = classe com muitos motivos de mudança

```java
// Sinal de alarme: o teste conta a história do problema
@ExtendWith(MockitoExtension.class)
class ProcessarPagamentoUseCaseTest {

    @Mock PagamentoGateway gateway;
    @Mock NotaFiscalService notaFiscal;
    @Mock EstoqueService estoque;
    @Mock EmailService email;
    @Mock AuditoriaService auditoria;
    @Mock FraudeService fraude;
    @Mock PedidoRepository pedidoRepository;
    @Mock ClienteRepository clienteRepository;

    @InjectMocks ProcessarPagamentoUseCase useCase;  // 8 dependências
}
```

O `@InjectMocks` está funcionando. O desenho é que está errado: esse caso de uso tem oito motivos para mudar. Ele é um **orquestrador de efeitos colaterais**, e cada um desses efeitos pertence a um fluxo próprio.

**Correção de design, não de teste:** extraia os efeitos para eventos e deixe o caso de uso decidir *o que aconteceu*, não *o que fazer sobre isso*.

```java
@Transactional
public PagamentoOutput executar(ProcessarPagamentoInput input) {
    var pedido = pedidoRepository.porId(input.pedidoId()).orElseThrow(...);
    var autorizacao = gateway.autorizar(pedido);          // única dependência de I/O no fluxo crítico
    if (!autorizacao.aprovada()) {
        pedido.registrarRecusa(autorizacao.motivo());
        return PagamentoOutput.recusado(autorizacao.motivo());
    }
    pedido.confirmarPagamento(autorizacao.id(), clock.instant());
    pedidoRepository.salvar(pedido);                      // evento de domínio é publicado aqui
    return PagamentoOutput.aprovado(pedido.id());
}
```

Nota fiscal, estoque, e-mail e auditoria passam a reagir ao evento `PagamentoConfirmado`. O teste do caso de uso cai de oito mocks para dois — e o teste do `NotaFiscalListener` existe separado, com uma responsabilidade.

**Regra operacional:** se o construtor do teste tem mais de 4 `@Mock`, o problema não é o teste. É a classe.

### 3.2 Precisa subir o Spring para testar cálculo = regra acoplada ao framework

```java
// Isto é um recuo de design disfarçado de teste
@SpringBootTest
class CalculadoraDescontoTest {
    @Autowired CalculadoraDesconto calculadora;

    @Test
    void deveAplicarDescontoProgressivo() { /* ... */ }
}
```

Se `CalculadoraDesconto` precisa do contexto Spring para funcionar, ela depende de algo que não é o cálculo — provavelmente repositório, propriedade injetada ou bean auxiliar. O cálculo **não pode** ser dependente disso.

**Correção:** a regra vira função pura em `domain`, testada em microssegundos.

```java
public record FaixaDesconto(BigDecimal limite, BigDecimal percentual) {}

public final class PoliticaDesconto {

    private final List<FaixaDesconto> faixas;   // injetado uma vez, imutável

    public PoliticaDesconto(List<FaixaDesconto> faixas) {
        this.faixas = List.copyOf(faixas);
    }

    public BigDecimal calcular(BigDecimal subtotal) {
        return faixas.stream()
                .filter(f -> subtotal.compareTo(f.limite()) >= 0)
                .max(Comparator.comparing(FaixaDesconto::limite))
                .map(FaixaDesconto::percentual)
                .orElse(BigDecimal.ZERO);
    }
}
```

```java
class PoliticaDescontoTest {
    private final PoliticaDesconto politica = new PoliticaDesconto(List.of(
            new FaixaDesconto(new BigDecimal("100.00"), new BigDecimal("0.05")),
            new FaixaDesconto(new BigDecimal("500.00"), new BigDecimal("0.10"))));

    @Test
    void deveAplicarDezPorCentoAcimaDeQuinhentos() {
        assertThat(politica.calcular(new BigDecimal("600.00")))
                .isEqualByComparingTo("0.10");
    }

    @Test
    void deveAplicarZeroAbaixoDaPrimeiraFaixa() {
        assertThat(politica.calcular(new BigDecimal("50.00")))
                .isEqualByComparingTo("0.00");
    }
}
```

Zero contexto, zero mock, dois testes em 3ms. **O teste ficou fácil porque o design ficou correto** — não o contrário.

### 3.3 Precisa de banco para validar transição de estado = regra mora no ORM

Se o teste da regra "pedido cancelado não pode ser aprovado" exige `@DataJpaTest`, a regra está dentro da entidade JPA ou, pior, dentro de uma query. Isso significa que a regra não existe como código de domínio — ela existe como efeito de persistência.

**Correção:** o agregado de domínio é um record/objeto puro, e a entidade JPA é um **registro de persistência** que dele deriva.

```java
// domain/model/Pedido.java — puro, sem anotação
public record Pedido(PedidoId id, Status status, Dinheiro total) {

    public Pedido {
        Objects.requireNonNull(id, "id");
        Objects.requireNonNull(status, "status");
        Objects.requireNonNull(total, "total");
    }

    public Pedido aprovar() {
        if (status == Status.CANCELADO) {
            throw new RegraNegocioException("PEDIDO_STATUS_INVALIDO",
                    "Pedido cancelado não pode ser aprovado");
        }
        if (total.zero()) {
            throw new RegraNegocioException("PEDIDO_TOTAL_INVALIDO", "Total deve ser positivo");
        }
        return new Pedido(id, Status.APROVADO, total);
    }
}
```

O teste fica uma linha por caso, sem banco, sem Spring, sem fixture:

```java
@Test
void naoDeveAprovarPedidoCancelado() {
    var cancelado = new Pedido(new PedidoId(1L), Status.CANCELADO, Dinheiro.de("100.00"));

    assertThatThrownBy(cancelado::aprovar)
            .isInstanceOf(RegraNegocioException.class)
            .extracting("codigo").isEqualTo("PEDIDO_STATUS_INVALIDO");
}
```

E o adapter JPA passa a ser um tradutor testado isoladamente (`@DataJpaTest`), sem carregar regra.

### 3.4 `Thread.sleep` = tempo não injetado

```java
// Teste que dorme é teste que mente
@Test
void deveExpirarReservaApos30Minutos() {
    service.reservar(pedido);
    Thread.sleep(30 * 60 * 1000);   // inviável, e ainda assim instável
}
```

Toda regra temporal precisa de `Clock` injetado. Isso é testabilidade **e** é arquitetura: **tempo é uma dependência externa**, exatamente como banco ou HTTP.

```java
@Service
public class ReservaService {
    private final Clock clock;

    public Reserva reservar(PedidoId pedido) {
        return new Reserva(pedido, Instant.now(clock).plus(Duration.ofMinutes(30)));
    }
}

@Test
void deveCalcularExpiracaoComBaseNoRelogioInjetado() {
    var fixo = Clock.fixed(Instant.parse("2026-01-15T10:00:00Z"), ZoneOffset.UTC);
    var service = new ReservaService(fixo);

    var reserva = service.reservar(new PedidoId(1L));

    assertThat(reserva.expiraEm()).isEqualTo(Instant.parse("2026-01-15T10:30:00Z"));
}
```

Determine também: se existe `Thread.sleep` no código de produção para esperar consistência eventual, o problema é maior — falta evento ou falta retry controlado. Nesse caso a correção é de arquitetura, não de teste.

### 3.5 `ReflectionTestUtils` = contrato implícito

Usar reflexão para injetar dependência em teste é aceitar que a classe tem um contrato que não declara. Isso é **field injection** com outro nome. O segundo sintoma da mesma doença aparece em produção: dependência nula sem erro de inicialização, ou ciclo de dependência que o Spring resolveu por sorte.

Não existe correção de teste aqui. A correção é construtor.

### 3.6 `verify` na ordem = contrato mal definido

```java
// Testa implementação, não comportamento
then(gateway).should().autorizar(any());
then(gateway).should().capturar(any());
then(repository).should().salvar(any());
```

Este teste quebra em qualquer refatoração que preserve o comportamento. Ele não protege nada além da ordem interna que você escolheu hoje.

**Critério:** `verify` se justifica quando **o efeito é o contrato** — "o e-mail foi enviado", "o evento foi publicado". Quando o efeito é interno — "o repositório foi chamado" — você está testando a implementação.

Um caso legítimo:

```java
then(eventoPublisher).should().publicar(argThat(e -> e instanceof PedidoAprovado));
```

Um caso ilegítimo:

```java
then(pedidoRepository).should().salvar(argThat(p -> p.getStatus() == APROVADO));
```

O segundo testa que o método chamou o repositório com certo argumento — quando o comportamento observável (retorno do caso de uso) já cobriria isso.

### 3.7 Teste impossível de nomear = método com múltiplos comportamentos

Tentativa de nome: `deveProcessarPedidoQuandoClienteAtivoEEstoqueDisponivelEPagamentoAprovadoENotaFiscalEmitida`. Isso não é um teste; é um roteiro de integração.

**Método com mais de 3 caminhos de saída (retornos + exceções) não pode ser testado com nomes honestos.** Ou você fragmenta o método, ou o teste vira uma tabela de casos sem significado.

Correção estrutural: extraia as decisões em unidades nomeáveis — cada uma vira um teste com nome de uma linha. O método orquestrador testa apenas o encadeamento.

---

## 4. Testes de fatia como sonda de acoplamento

As fatias de teste (`@WebMvcTest`, `@DataJpaTest`) são apresentadas como otimização de velocidade. Elas são mais que isso: são **medidores de acoplamento**.

`@WebMvcTest` carrega **apenas** a camada web. Se o teste do seu controller não sobe porque falta um bean de repositório, isso não é um problema de configuração de teste — é a prova de que o controller alcança a persistência. A falha do teste é o achado.

```java
@WebMvcTest(PedidoController.class)
class PedidoControllerTest {

    @Autowired MockMvc mvc;
    @MockitoBean CriarPedido criarPedido;      // única dependência legítima do controller

    @Test
    void deveRetornar422QuandoRegraDeNegocioViolada() throws Exception {
        given(criarPedido.executar(any()))
                .willThrow(new RegraNegocioException("PEDIDO_STATUS_INVALIDO", "status inválido"));

        mvc.perform(post("/v1/pedidos")
                .contentType(MediaType.APPLICATION_JSON)
                .content("""
                        {"clienteId": 1, "itens": [{"sku": "A1", "quantidade": 1}]}
                        """))
           .andExpect(status().isUnprocessableEntity())
           .andExpect(jsonPath("$.codigo").value("PEDIDO_STATUS_INVALIDO"))
           .andExpect(jsonPath("$.correlationId").exists());
    }
}
```

Se este teste exigir `@SpringBootTest` completo, o controller está **carregando dados** — uma violação de fronteira que passaria despercebida em review.

Aplicando a mesma lógica a `@DataJpaTest`: ele carrega apenas persistência. Se o teste do repositório não sobe porque falta um bean de serviço, o repositório está chamando **regra de negócio**. Também é achado, não incômodo.

**Métrica prática:** para cada classe de teste, o tempo de inicialização do contexto diz quanto a classe está acoplada. `@WebMvcTest` em 400ms é saudável. `@SpringBootTest` em 12s para testar um cálculo é um problema de desenho com sintoma de lentidão.

### 4.1 A regra do teste que não compila = fronteira respeitada

Um teste de arquitetura mental que funciona bem: **tente escrever o teste de cada camada importando só o que aquela camada deveria conhecer.** Se o teste de `domain` precisa importar `org.springframework.data.domain.Page` para compilar, a fronteira já vazou.

Isso é exatamente o que o ArchUnit automatiza (seção 6).

---

## 5. Qualidade: onde a métrica estática encontra o teste

O Bloco 6 tinha métricas — complexidade ciclomática, profundidade de aninhamento, dependências injetadas. Todos esses números têm uma tradução direta em esforço de teste.

| Métrica | Relação com teste | Ação |
|---|---|---|
| Complexidade ciclomática = N | N caminhos independentes para testar | Se N > 10, o método precisa ser quebrado antes de ser testado |
| Aninhamento > 2 | Combinação de caminhos cresce multiplicativamente | Guard clauses primeiro, teste depois |
| Dependências injetadas > 5 | Mocks no teste | Extrair casos de uso ou usar evento |
| Fan-out de pacote alto | Testes de integração em cascata | Verificar fronteira com ArchUnit |

**A conclusão que economiza semanas:** complexidade alta não é um número feio no Sonar, é a previsão do custo do teste. Refatorar antes de testar é investimento; refatorar depois de testar é retrabalho duplo.

### 5.1 Cobertura como lacuna, não como meta

```
Cobertura de linha responde: "que código nunca executo?"
Cobertura de mutação responde: "que código eu executo mas não verifico?"
```

A primeira é fácil de manipular (teste sem asserção move o número). A segunda é a que mede qualidade de teste de fato — e é caríssima. O uso profissional é cirúrgico:

- Rode PIT nos módulos de **cálculo, autorização e pagamento**, não no projeto inteiro.
- Use como diagnóstico de sprint, não como gate de PR.
- Investigue apenas mutantes sobreviventes em código com regra de negócio relevante.

```xml
<plugin>
  <groupId>org.pitest</groupId>
  <artifactId>pitest-maven</artifactId>
  <configuration>
    <targetClasses>
      <param>br.com.empresa.app.domain.pagamento.*</param>
    </targetClasses>
    <mutationThreshold>75</mutationThreshold>
  </configuration>
</plugin>
```

Se um mutante sobrevive — o código muda e nenhum teste falha — você tem um teste que existe sem proteger nada. Esse é o diagnóstico mais preciso que existe sobre qualidade de asserção.

### 5.2 Observabilidade também é testável

O Bloco 6 exige `correlationId` em logs e erros, e métricas de negócio. Ambos são verificáveis por teste — e devem ser, porque são requisitos de produção que ninguém valida manualmente.

```java
@Test
void deveRegistrarMetricaDePedidosCriados() {
    var registry = new SimpleMeterRegistry();

    new MetricasDePedido(registry).registrarCriacao();

    assertThat(registry.get("pedidos.criados").counter().count()).isEqualTo(1.0);
}
```

```java
@Test
void deveIncluirCorrelationIdNaRespostaDeErro() throws Exception {
    try (var mdc = new MDCCloseable("correlationId", "abc-123")) {
        mvc.perform(get("/v1/pedidos/999"))
           .andExpect(jsonPath("$.correlationId").value("abc-123"));
    }
}
```

Isso transforma "convenção de observabilidade" em requisito executável. Uma vez que o revisor não consegue checar em cada PR se alguém esqueceu o correlation ID, o teste precisa checar.

**Alerta de escopo:** não teste o *conteúdo textual* de todo log — isso produz suíte frágil. Teste apenas log de auditoria e ausência de PII em campos críticos.

---

## 6. ArchUnit: arquitetura como teste, não como documento

O Bloco 5 produziu regras ("`domain` não importa Spring"). O Bloco 7 é onde elas vivem. Uma regra que mora em documento morre no terceiro sprint; uma regra que mora no build sobrevive a toda rotatividade de time.

```java
@AnalyzeClasses(packages = "br.com.empresa.app")
class ArquiteturaTest {

    // Fronteira central
    @ArchTest
    static final ArchRule domainNaoConheceFramework =
            noClasses().that().resideInAPackage("..domain..")
                    .should().dependOnClassesThat()
                    .resideInAnyPackage("org.springframework..", "jakarta.persistence..", "..infrastructure..");

    // Camada web não acessa persistência
    @ArchTest
    static final ArchRule controllersNaoAcessamRepositorios =
            noClasses().that().resideInAPackage("..infrastructure.web..")
                    .should().dependOnClassesThat().resideInAPackage("..infrastructure.persistence..");

    // Nenhuma entidade JPA vaza para a web
    @ArchTest
    static final ArchRule controllersNaoUsamEntidadesJpa =
            noClasses().that().resideInAPackage("..infrastructure.web..")
                    .should().dependOnClassesThat().areAnnotatedWith(jakarta.persistence.Entity.class);

    // Casos de uso implementam uma porta e têm um único método público
    @ArchTest
    static final ArchRule casosDeUsoDevemImplementarPorta =
            classes().that().haveSimpleNameEndingWith("UseCase")
                    .should().implement(resideInAPackage("..domain.port.in.."));

    // Proibições herdadas da Parte II
    @ArchTest
    static final ArchRule semFieldInjection =
            noFields().should().beAnnotatedWith(org.springframework.beans.factory.annotation.Autowired.class);

    @ArchTest
    static final ArchRule semLombokEmCodigoNovo =
            noClasses().that().resideInAPackage("..domain..")
                    .should().dependOnClassesThat().resideInAPackage("lombok..");

    // Portas de domínio não estendem Spring Data
    @ArchTest
    static final ArchRule portasNaoEstendemJpaRepository =
            noClasses().that().resideInAPackage("..domain.port..")
                    .should().dependOnClassesThat()
                    .haveFullyQualifiedName("org.springframework.data.jpa.repository.JpaRepository");
}
```

Cinco detalhes que fazem a diferença entre ArchUnit útil e ArchUnit ignorado:

- **Rode no build normal** (`mvn verify`), não em job separado — regra que não bloqueia merge não existe.
- **Mensagem de erro acionável:** `because("o domínio deve permanecer independente de framework — ver ADR-007")`. Quem quebrar a regra precisa saber por quê sem perguntar no chat.
- **Poucas regras, bem escolhidas.** Trinta regras produzem uma cesta de exclusões. Oito regras produzem arquitetura.
- **`freeze()` com prazo.** Se o legado já viola, `FreezingArchRule` permite congelar as violações atuais e proibir novas — com issue aberta para descongelar.
- **Não fiscalize estilo** (nomes, ordem de método). Isso é Spotless/Checkstyle. ArchUnit é para fronteira.

### 6.1 Contrato de porta: o teste que pega LSP em adapter

Esta é a técnica mais subutilizada da lista. O Bloco 5 disse que o adapter não pode quebrar o contrato da porta (LSP). O teste que garante isso é o **teste de contrato por herança**.

```java
abstract class PedidoRepositoryContract {

    protected abstract PedidoRepository repositorio();

    @Test
    void deveRetornarVazioQuandoIdNaoExiste() {
        assertThat(repositorio().porId(new PedidoId(-1L))).isEmpty();
    }

    @Test
    void devePersistirEDevolverMesmoId() {
        var pedido = PedidoFixture.pendente();

        var salvo = repositorio().salvar(pedido);

        assertThat(repositorio().porId(salvo.id())).isPresent();
    }

    @Test
    void naoDeveLancarExcecaoAoSalvarDuasVezes() {
        var pedido = PedidoFixture.pendente();
        var salvo = repositorio().salvar(pedido);

        assertThatCode(() -> repositorio().salvar(salvo)).doesNotThrowAnyException();
    }
}

class JpaPedidoRepositoryTest extends PedidoRepositoryContract {
    // @DataJpaTest + Testcontainers
}

class InMemoryPedidoRepositoryTest extends PedidoRepositoryContract {
    // fake em memória usado nos testes unitários
}
```

O mesmo contrato testado contra duas implementações. Se o adapter JPA lançar exceção onde o fake devolve `Optional.empty()`, o teste falha e o contrato está violado. **Isso pega a divergência semântica entre fake e real — a origem mais comum de teste unitário verde e produção quebrada.**

---

## 7. Matriz de decisão: o que testar em cada camada

A pergunta errada é "quanto testar". A pergunta certa é "o que pertence a esta camada".

| Camada | Ferramenta | Deve garantir | Não deve testar aqui |
|---|---|---|---|
| `domain` | JUnit + AssertJ, sem Spring | Invariantes, cálculos, transições de estado | Persistência, HTTP, transação |
| `application` (use case) | JUnit + Mockito | Orquestração, ordem de decisão, tradução de exceção | Regra de domínio (já testada), SQL |
| Adapter web | `@WebMvcTest` | Status, contrato JSON, headers, validação de forma | Regra de negócio, query |
| Adapter persistência | `@DataJpaTest` + Testcontainers | Mapeamento, query, `EntityGraph`, N+1, constraints | Regra de negócio |
| Integração | `@SpringBootTest` seletivo | Fluxo crítico de ponta a ponta, transação real | Caminhos alternativos (caro) |
| Arquitetura | ArchUnit | Fronteiras, proibições | Comportamento |
| Contrato (externo) | Pact / Spring Cloud Contract | Compatibilidade de contrato | Implementação |

### 7.1 A regra dos caminhos alternativos

Regra prática que economiza horas de CI: **teste todos os caminhos no nível mais barato; teste um caminho completo no nível mais caro.**

Os dez caminhos de erro da regra de aprovação de pedido testam no domínio, em milissegundos. O `@SpringBootTest` roda apenas o caminho felizes ponta a ponta — uma vez, como prova de que a cola funciona.

Se você tem 40 testes de integração, 30 deles estão testando o que o domínio já testava mais rápido.

---

## 8. Guardrails: testes que protegem a evolução

Nem todo teste verifica comportamento de hoje. Alguns existem para impedir **regressões específicas de arquitetura** que a refatoração futura reintroduz.

### 8.1 Guardrail de N+1

O Bloco 9 definiu que toda listagem deve ter teste que conta queries. Eis a implementação:

```java
@SpringBootTest
@Testcontainers
class SemConsultasN1Test {

    @Container
    @ServiceConnection
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:16-alpine");

    @Autowired EntityManagerFactory emf;
    @Autowired RelatorioPedidoQuery query;

    @Test
    void mustListarPedidosSemN1() {
        var stats = emf.unwrap(SessionFactory.class).getStatistics();
        stats.setStatisticsEnabled(true);
        stats.clear();

        var resultado = query.listarPorStatus(Status.PENDENTE);

        assertThat(resultado).isNotEmpty();
        assertThat(stats.getPrepareStatementCount())
                .as("uma listagem não pode gerar query por item")
                .isLessThanOrEqualTo(2);
    }
}
```

O limite de 2 (query principal + batch) é o contrato. Se alguém adicionar um `@ManyToOne` sem `fetch join`, o teste quebra em 3 queries — antes do deploy.

### 8.2 Guardrail de migration

```java
@SpringBootTest
@Testcontainers
class MigrationTest {

    @Container
    @ServiceConnection
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:16-alpine");

    @Test
    void deveAplicarTodasAsMigrationsEmBancoVazio() {
        // Flyway roda na inicialização; falha aqui = migration quebrada
        assertThat(true).isTrue();
    }
}
```

Simples e subestimado: garante que a migration roda em banco vazio e que o schema aplicado é coerente com o esperado pela aplicação (via `ddl-auto: validate`).

### 8.3 Guardrail de resiliência

```java
@Test
void deveFalharRapidoQuandoServicoExternoNaoResponde() {
    // WireMock com delay acima do timeout configurado
    stubFor(post("/autorizacao").willReturn(aResponse().withFixedDelay(5000)));

    var inicio = Instant.now();
    assertThatThrownBy(() -> gateway.autorizar(pedido))
            .isInstanceOf(TimeoutException.class);
    assertThat(Duration.between(inicio, Instant.now())).isLessThan(Duration.ofSeconds(3));
}
```

Esse teste protege a configuração de timeout — o item mais esquecido e mais fatal da lista de armadilhas.

### 8.4 Guardrail de contrato externo

Quando há consumidor externo, teste de contrato substitui o teste de integração caro:

```java
@SpringBootTest(webEnvironment = WebEnvironment.MOCK)
@AutoConfigureMockMvc
class ContratoPedidoTest {

    @Autowired MockMvc mvc;

    @Test
    void deveManterContratoDoPedido() throws Exception {
        mvc.perform(get("/v1/pedidos/1"))
           .andExpect(status().isOk())
           .andExpect(jsonPath("$.id").isNumber())
           .andExpect(jsonPath("$.status").isString())
           .andExpect(jsonPath("$.total").isNumber());
    }
}
```

Regra do Bloco 3 levada ao teste: **campo só sai do contrato com versão nova.** Um teste que falha na remoção é a diferença entre deprecação planejada e quebra de cliente.

---

## 9. Anti-padrões que destroem o sinal

O maior risco não é ter pouco teste. É ter muito teste que **mente**.

| Anti-padrão | Por que destrói o sinal |
|---|---|
| Cobertura como meta de sprint | Produz teste sem asserção em massa; número sobe, proteção não |
| `@SpringBootTest` como default | Suíte lenta é suíte que o time para de rodar localmente |
| Mock de entidade, `String`, `List` | Testa o mock, não o comportamento |
| Asserção em string de mensagem | Quebra em toda revisão de texto; time relaxa a asserção |
| `@DirtiesContext` generalizado | Esconde estado compartilhado que também afeta produção |
| Teste que depende de ordem | Vermelho aleatório treina o time a ignorar falha |
| `verify` em toda chamada | Acopla à implementação; refatoração segura fica impossível |
| Fixture copiada em cada classe | 40 linhas de setup escondem o dado relevante do caso |
| Teste que passa por acidente | Sem asserção real; mutação revelaria |
| `Thread.sleep` como sincronização | Instável por construção |

**O sintoma agregado:** quando o time começa a dizer "esse teste sempre falha, roda de novo", a suíte morreu. A confiança é o ativo — e ela se perde com poucos testes frágeis.

**Como recuperar:** meça a taxa de falha intermitente, exclua ou conserte o teste frágil na hora, e nunca aceite `@Disabled` sem issue e prazo.

---

## 10. Teste como ferramenta de design (sem dogma de TDD)

Não se trata de escrever todo teste antes de todo código. Trata-se de usar a **dificuldade de escrever o teste** como sinal de projeto — o que pode acontecer antes, durante ou depois da implementação.

O ciclo que produz os melhores resultados na prática:

1. **Desenhe o contrato** — a porta de entrada do caso de uso, em uma assinatura de uma linha.
2. **Escreva o teste do caminho de sucesso** — se ele exige 6 mocks, o desenho está errado; volte ao passo 1.
3. **Implemente o mínimo** para o teste passar.
4. **Adicione os caminhos de erro** no nível mais barato possível.
5. **Refatore com a rede de segurança instalada** — e aqui a refatoração fica segura, porque o comportamento está fixado.

O passo 2 é o que dá o retorno: ele detecta o desenho ruim **antes** de existir código para defender. É muito mais barato mudar uma interface do que mudar uma implementação e 12 chamadores.

### 10.1 O que refatorar com confiança

Com testes de comportamento no lugar, você pode mudar livremente: estrutura de classes, nomes de método privado, extração de colaborador, mudança de query. O que os testes de comportamento **não** cobrem é justamente o que não deveria ser protegido — a forma interna.

Se refatorar exige reescrever o teste, o teste estava acoplado à implementação — outro achado de design.

---

## 11. Pipeline: onde cada gate entra

A distribuição que funciona em CI real, equilibrando tempo e cobertura:

| Etapa | Tempo alvo | O que roda | Bloqueia? |
|---|---|---|---|
| Pre-commit | < 5s | Spotless, formatação | Sim (local) |
| PR — estático | < 60s | SpotBugs, ErrorProne, ArchUnit | Sim |
| PR — unitário | < 90s | `domain` + `application` | Sim |
| PR — fatias | < 3min | `@WebMvcTest`, `@DataJpaTest` | Sim |
| PR — integração | < 8min | Testcontainers, fluxos críticos | Sim |
| Main — contrato | < 5min | Pact / contract tests | Sim em main |
| Nightly — mutação | 20-60min | PIT em módulos críticos | Não (relatório) |
| Nightly — carga | 30min | k6/Gatling, 2× pico | Não (alerta) |

Três regras que sustentam esse desenho:

- **O que roda no PR precisa ser rápido o suficiente para o desenvolvedor esperar.** Se passa de 10 minutos, o time começa a fazer merge sem olhar.
- **ArchUnit vai no estático**, não no integrado — é barato e deve falhar primeiro.
- **Mutação e carga vão para nightly.** São diagnósticos, não gates.

---

## 12. Checklists

### 12.1 Design (verificável pelo teste)

- O teste do caso de uso tem no máximo 4 mocks?
- A regra de negócio é testável sem contexto Spring?
- A regra temporal é testável sem `Thread.sleep` (`Clock` injetado)?
- Nenhum teste usa `ReflectionTestUtils` para injetar dependência?
- Nenhum teste usa `@DirtiesContext` como solução padrão?
- Todo teste tem nome que descreve um único comportamento?
- O controller é testável com `@WebMvcTest` sem carregar repositório?

### 12.2 Qualidade

- ArchUnit verde com as regras de fronteira declaradas?
- Análise estática bloqueia merge?
- Métricas de negócio e correlation ID têm teste?
- Nenhum log de PII nos caminhos testados?
- Nenhuma migration sem teste em banco real?

### 12.3 Teste

- Existe teste de contagem de queries para cada listagem nova?
- O adapter implementa o contrato da porta testado por herança?
- Timeouts de clientes externos têm teste de resiliência?
- Contrato de API alterado tem teste que falha na remoção de campo?
- Nenhum teste com `Thread.sleep`, ordem implícita ou asserção em texto de mensagem?
- A suíte completa roda em menos de 10 minutos no PR?

---

## Resumindo

- **O teste é o code review mais objetivo que existe:** a dificuldade de escrevê-lo mede acoplamento com precisão que leitura humana não alcança — N+1, `@Transactional` sem efeito e hash quebrado são invisíveis em review e óbvios em teste.
- **Cada sintoma de teste tem nome de defeito:** muitos mocks = classe-deus, necessidade de Spring = regra acoplada, necessidade de banco = regra no ORM, `Thread.sleep` = tempo não injetado. A tabela de leitura converte desconforto em diagnóstico.
- **Fatias de teste são sondas de acoplamento:** se `@WebMvcTest` não sobe sem repositório, o controller acessa dados — o erro de configuração é o achado.
- **ArchUnit é o único fiscal de arquitetura que sobrevive à rotatividade do time.** Oito regras com mensagem acionável valem mais que trinta com exclusões.
- **Teste de contrato por herança é a técnica mais subutilizada:** o mesmo contrato rodando contra fake e contra adapter JPA pega a divergência semântica que produz teste verde e produção quebrada.
- **Complexidade alta é a previsão do custo do teste.** Refatorar antes de testar é investimento; depois, é retrabalho duplo.
- **Cobertura mede lacuna; mutação mede qualidade de asserção.** Só a segunda responde se o teste protege algo.
- **O último teste da cadeia roda em produção:** sem trace e métrica de negócio, o defeito que escapou de tudo é descoberto pelo cliente.

Se quiser, posso:

- Escrever a Parte IV integrando os Blocos 8 e 9 (persistência/JPA e Lombok) com o mesmo fio condutor — como os dois concentram os defeitos que só o teste de contagem de queries e o teste de identidade revelam.
- Gerar o `pom.xml` completo com ArchUnit, PIT, JaCoCo, Spotless, SpotBugs, Testcontainers e as regras de exemplo prontas para rodar em Java 25.
- Criar o projeto de referência executável com a estrutura de camadas, o contrato de porta por herança, o guardrail de N+1 e o `@WebMvcTest` funcionando de ponta a ponta.

# Parte IV
# Parte IV — Persistência e Lombok: onde a classe que você lê não é a classe que roda

**Base:** Spring Boot 4.1.0 (lançado em 11/06/2026, gerencia Hibernate 7.4.1.Final e Jakarta Persistence 3.2) · Java 25
**Fio condutor:** Blocos 8 e 9 são o mesmo problema em dois formatos — e é exatamente por isso que vieram juntos.

---

## 1. A tese: dois mecanismos, um único tipo de defeito

O Bloco 8 (JPA/Hibernate) e o Bloco 9 (Lombok) parecem não ter relação. Eles têm a mesma natureza de risco:

| | Lombok | Hibernate |
|---|---|---|
| O que você escreve | Classe com 4 campos | Entidade com `@ManyToOne` |
| O que executa | Classe com construtor, getters, `equals`, `hashCode`, `toString`, builder gerados | Grafo de proxies, dirty checking, flush, batch, cache de primeiro nível |
| Distância entre os dois | Gerada em tempo de compilação | Gerada em tempo de execução |
| Quem revisa vê | O código-fonte | O código-fonte |
| Quem descobre o defeito | O runtime, tarde | O runtime, tarde |

Em ambos, a superfície legível é **menor** que o comportamento real. E o Bloco 7 (Parte III) já entregou a ferramenta que fecha essa lacuna: o teste observa o comportamento, não a forma.

A consequência prática é desconfortável: **nenhum dos dois blocos pode ser validado por leitura de código.** Ler uma entidade com `@Data` não revela `hashCode` mutável. Ler um `@ManyToOne` sem `fetch` não revela que ele é `EAGER`. Você precisa de teste, de estatística de Hibernate, ou de produção.

### 1.1 Onde os dois blocos se cruzam: `equals`/`hashCode` em entidade

Aqui está o único ponto em que Lombok e JPA produzem **o mesmo bug por caminhos diferentes** — e é o ponto de partida porque ele explica tudo o mais.

```java
@Data                    // ← Lombok gera equals/hashCode sobre TODOS os campos
@Entity
public class PedidoJpa {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String numero;

    @OneToMany(mappedBy = "pedido", cascade = CascadeType.ALL)
    private List<ItemPedidoJpa> itens = new ArrayList<>();
}
```

Três defeitos simultâneos, e nenhum aparece em teste de caminho felizes:

**Defeito 1 — `hashCode` muda depois de `save`.** Antes da persistência `id` é `null`; depois, é `1L`. O `hashCode` gerado inclui `id`, logo ele muda. Uma entidade inserida em `HashSet` ou usada como chave de `HashMap` **fica inacessível** após o `save`. Sem exceção, sem log — o elemento simplesmente desaparece da busca.

```java
var set = new HashSet<PedidoJpa>();
var pedido = new PedidoJpa();
set.add(pedido);              // hashCode calculado com id == null
repository.save(pedido);      // id passa a ser 1L → hashCode novo
set.contains(pedido);          // false
```

**Defeito 2 — `equals` dispara carregamento preguiçoso.** Comparar duas entidades com `@Data` acessa `itens`, que é lazy. Comparação vira query. Em um `list.contains(x)` dentro de loop, você tem N+1 nascendo de uma comparação — o defeito do Bloco 8 causado por uma anotação do Bloco 9.

**Defeito 3 — `toString` gera recursão infinita.** `Pedido.toString()` inclui `itens`; `ItemPedidoJpa.toString()` inclui `pedido`; de volta. `StackOverflowError` em produção, geralmente dentro de um log de erro — o momento em que você menos pode perder o contexto.

**A correção não é `@EqualsAndHashCode.Exclude`.** É reconhecer que entidade JPA **não tem identidade de valor**: ela tem identidade de banco. A regra correta é a do JPA:

```java
@Entity
@Getter                                    // nunca @Data em entidade
@EqualsAndHashCode(onlyExplicitlyIncluded = true)
public class PedidoJpa {

    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    @EqualsAndHashCode.Include
    private Long id;

    @ReadOnlyProperty                       // Lombok não inclui no toString
    @OneToMany(mappedBy = "pedido", cascade = CascadeType.ALL)
    private List<ItemPedidoJpa> itens = new ArrayList<>();
}
```

Ou, preferencialmente, sem Lombok: `equals`/`hashCode` explícitos declarando a decisão.

**Alerta que economiza um incidente:** `equals`/`hashCode` por `id` tem problema próprio — duas entidades transientes têm `id == null` e seriam "iguais". Se você precisa de identidade antes da persistência, use atribuição de ID antecipada (UUID gerado na criação) ou chave de negócio imutável. Não deixe isso implícito.

---

## 2. N+1: não é bug, é o default

O título é literal. O Hibernate não tem um defeito chamado N+1 — ele tem um comportamento padrão (carregar associação lazy quando tocada) que, aplicado a uma lista, produz N+1. Entender isso muda a correção: você não está consertando um erro do framework, você está **escolhendo o padrão de carga para cada consulta**.

### 2.1 Como nasce — as três portas de entrada

**Porta 1: acesso dentro de loop.**

```java
var pedidos = pedidoRepository.findByStatus(PENDENTE);       // 1 query
for (var p : pedidos) {
    p.getCliente().getNome();                                 // N queries
}
```

**Porta 2: mapeamento para DTO fora da query.**

```java
return pedidos.stream().map(PedidoOutput::de).toList();       // cada "de" toca o cliente
```

Esta é a perigosa, porque o `map` parece inofensivo e está longe da query.

**Porta 3: serialização.** Retornar entidade no `@RestController` e deixar o Jackson percorrer o grafo. O serializer toca cada associação — e se você combinou com `open-in-view: true`, funciona (lentamente). Com `open-in-view: false`, quebra com `LazyInitializationException`. As duas saídas são sintoma do mesmo erro: entidade como contrato de API.

### 2.2 Diagnóstico: medir, não adivinhar

```yaml
# application-dev.yml
spring:
  jpa:
    show-sql: true
    properties:
      hibernate:
        generate_statistics: true
logging:
  level:
    org.hibernate.stat: debug
```

A saída que importa é a linha `Session Metrics` — `prepareStatementCount` é o número de queries da requisição. Cresce com o volume da lista? É N+1.

Em produção, o equivalente é o trace: se uma única requisição HTTP abre 200 spans de banco, você tem a resposta sem ligar nada.

**Regra operacional:** toda listagem exposta por API tem um número de queries conhecido e testado. Sem isso, o N+1 volta na primeira refatoração de mapper.

### 2.3 As quatro correções e quando cada uma se aplica

**`join fetch` — quando a associação é obrigatória para a regra.**

```java
@Query("""
    select p from PedidoJpa p
    join fetch p.cliente
    where p.status = :status
    """)
List<PedidoJpa> buscarComCliente(@Param("status") Status status);
```

Uma query, um resultado. **Cuidado:** `join fetch` em **coleção** multiplica linhas e não pode ser combinado com `Pageable` (seção 4).

**`@EntityGraph` — mais declarativo, seguro com paginação e com mais de uma associação.**

```java
@EntityGraph(attributePaths = {"cliente"})
List<PedidoJpa> findByStatus(Status status);
```

O Spring Data transforma em join ou em query secundária conforme o tipo da associação. É a escolha para `findBy` derivado.

**Projeção — quando você só precisa de leitura.**

```java
public record PedidoResumo(Long id, String clienteNome, BigDecimal total) {}

@Query("""
    select new br.com.empresa.infrastructure.persistence.PedidoResumo(
        p.id, c.nome, p.total)
    from PedidoJpa p join p.cliente c
    where p.status = :status
    """)
List<PedidoResumo> resumirPorStatus(@Param("status") Status status);
```

Nenhuma entidade carregada, nenhum proxy, nenhum dirty checking. **É a solução arquiteturalmente correta para 80% das listagens de API** — porque listagem de API não precisa de entidade, precisa de dado.

**Batch fetching — mitigação global, uma linha.**

```yaml
spring.jpa.properties.hibernate.default_batch_fetch_size: 50
```

Por padrão, o Hibernate só aplica batch em associações anotadas com `@BatchSize` ([FetchSettings – Hibernate Javadocs](https://docs.hibernate.org/orm/7.0/javadocs/org/hibernate/cfg/FetchSettings.html)). Definir esse default transforma N queries em N/50 — 401 em 9, no relato de um artigo técnico que documenta exatamente esse caso com Hibernate 7.4 ([Medium, set/2026](https://medium.com/@alxkm/n-1-in-hibernate-is-not-a-bug-its-a-default-17d3ef0cb047)).

**Este é o ponto mais importante da seção:** batch fetching não substitui modelagem correta, mas é a rede de segurança mais barata que existe. Ligue desde o primeiro dia. Ele não muda a query principal, não quebra paginação e não exige alterar serviço nenhum — logo, não será removido na primeira refatoração.

### 2.4 Guardrail: o teste que conta queries

O Bloco 7 definiu que lisagem precisa de teste de contagem. A implementação em Spring Boot 4.1 / Hibernate 7.4:

```java
@SpringBootTest
@Testcontainers
class SemN1Test {

    @Container
    @ServiceConnection
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:16-alpine");

    @Autowired EntityManagerFactory emf;
    @Autowired ConsultarPedidosPorStatus consulta;

    @Test
    void deveListarSemConsultasN1() {
        var stats = emf.unwrap(SessionFactory.class).getStatistics();
        stats.setStatisticsEnabled(true);
        stats.clear();

        var resultado = consulta.executar(Status.PENDENTE);

        assertThat(resultado).isNotEmpty();
        assertThat(stats.getPrepareStatementCount())
                .as("listagem não pode gerar query por item")
                .isLessThanOrEqualTo(2);   // principal + batch
    }
}
```

Sem esse teste, a correção de hoje é o bug de amanhã.

---

## 3. Lazy vs Eager: o default que engana

### 3.1 O mapa real dos padrões

| Associação | Default do Jakarta Persistence | O que você deve usar |
|---|---|---|
| `@ManyToOne` | **`EAGER`** | `LAZY` explícito |
| `@OneToOne` | **`EAGER`** | `LAZY` explícito |
| `@OneToMany` | `LAZY` | `LAZY` (nada a fazer) |
| `@ManyToMany` | `LAZY` | `LAZY` e repensar se é necessário |

Os dois defaults que enganam são justamente as associações mais comuns. `@ManyToOne` sem `fetch` é `EAGER` — e `EAGER` é irreversível para o bem:

```java
@ManyToOne(fetch = FetchType.LAZY, optional = false)
@JoinColumn(name = "cliente_id", nullable = false)
private ClienteJpa cliente;
```

Três motivos para nunca usar `EAGER`:

- Ele **anula o batch fetching**. O batch existe para carregar coleções e associações sob demanda; `EAGER` já as carrega sempre.
- Ele **quebra a paginação**: `EAGER` em coleção + `Pageable` = paginação em memória.
- Dois `EAGER` de coleção na mesma entidade produzem `MultipleBagFetchException`.

### 3.2 `open-in-view`: a configuração que esconde o problema

```yaml
spring.jpa.open-in-view: false
```

O default é `true`, e ele mantém a `EntityManager` (e a conexão) aberta durante toda a renderização da resposta. Consequência: `LazyInitializationException` não acontece, N+1 fica invisível, e o pool é consumido por toda a serialização.

**É a configuração mais custosa de mudar depois.** Ligada, o app "funciona" e você não sabe quanto custa. Desligue no primeiro commit do projeto. Em sistema grande, desligar depois exige revisar todos os controllers — semanas de trabalho.

### 3.3 `LazyInitializationException`: três causas, três correções certas

A exceção nunca é o problema; é o sintoma. E não existe "uma correção" — existem três, para três causas distintas.

**Causa 1 — entidade vazou para fora da transação.** O controller recebeu entidade e a serialização toca uma associação lazy. Correção: converter para DTO **dentro** do caso de uso. Não é workaround; é a fronteira que faltava.

**Causa 2 — a associação precisa estar carregada e não foi.** O caso de uso realmente necessita do dado. Correção: `join fetch` ou `@EntityGraph` **na consulta que precisa**. Explícito e local.

**Causa 3 — alguém "resolveu" com `Hibernate.initialize()` espalhado.** Isso não corrige nada: só troca uma exceção por uma query igualmente implícita. Trate como dívida técnica declarada.

**A causa raiz das três é a mesma:** entidade JPA usada como modelo de leitura. Entidade existe para escrita transacional de agregado. Consulta existe para leitura de dado. Quando as duas coisas usam o mesmo objeto, o lazy loading se torna uma decisão de runtime que ninguém tomou.

---

## 4. Paginação com `join fetch`: o bug que não aparece no log

Este merece seção própria porque não gera exceção, não gera log e não gera erro — gera memória.

```java
// PARECE correto, está errado
@Query("select p from PedidoJpa p join fetch p.itens where p.status = :status")
Page<PedidoJpa> buscar(Status status, Pageable pageable);
```

`join fetch` em coleção multiplica linhas: um pedido com 50 itens vira 50 linhas. O Hibernate **não consegue** aplicar `LIMIT` corretamente nesse resultado, então carrega tudo e corta a lista **em memória**. Com 200 mil pedidos, você carrega 200 mil pedidos para devolver 20.

Sintoma em produção: `OutOfMemoryError`, GC saturado e latência que cresce com o volume da tabela — nunca com o tamanho da página. E o `EXPLAIN` da query parece ótimo.

**Três correções válidas:**

```java
// 1. Pagine a raiz, carregue a coleção em query secundária
@EntityGraph(attributePaths = {"itens"})   // Hibernate usa query secundária para a coleção
Page<PedidoJpa> findByStatus(Status status, Pageable pageable);

// 2. Duas etapas: IDs paginados, depois o grafo
@Query("select p.id from PedidoJpa p where p.status = :status")
Page<Long> idsPorStatus(Status status, Pageable pageable);

@Query("select distinct p from PedidoJpa p join fetch p.itens where p.id in :ids")
List<PedidoJpa> comItens(@Param("ids") List<Long> ids);

// 3. Não pagine entidade: projete
Page<PedidoResumo> resumirPorStatus(Status status, Pageable pageable);
```

**Regra prática:** `join fetch` em coleção + `Pageable` no mesmo método é proibido. Se o code review encontrar essa combinação, é bloqueio de merge — não observação.

---

## 5. A correção na origem: agregado não é tabela

As quatro soluções de N+1 e o bug de paginação têm uma causa comum: **o modelo de persistência foi desenhado como espelho do banco, e o modelo de leitura foi desenhado como espelho da entidade.**

A pergunta que resolve na origem não é "qual fetch usar". É: **quais dados este caso de uso realmente precisa?**

Se a resposta é "os pedidos pendentes com nome do cliente e total", o modelo correto é um record de 3 campos — não uma entidade com 5 associações que você vai carregar com 3 configurações de fetch diferentes.

```java
// Modelo de leitura — não é entidade, não tem proxy, não tem lazy
public record PedidoListagem(Long id, String clienteNome, BigDecimal total, LocalDate data) {}
```

**Consequência arquitetural:** você pode ter um agregado de escrita (`Pedido` com `itens`, consistência transacional) e vários modelos de leitura (`PedidoListagem`, `PedidoResumo`, `PedidoDetalhe`). Isso não é CQRS completo — é apenas não usar a mesma estrutura para finalidades diferentes.

Quem paga o custo de não fazer isso: o pool de conexão, o GC e o tempo de resposta em produção.

---

## 6. `@Transactional`: as sete armadilhas verificáveis

| Armadilha | Por que falha | Como o teste revela |
|---|---|---|
| Self-invocation (`this.metodo()`) | Proxy não intercepta; não há transação | Rollback não acontece no teste |
| Método `private`/`final` | Proxies CGLIB não interceptam | Idem |
| `catch` que engole exceção | Sem exceção, sem rollback | Teste de erro não vê estado revertido |
| `RuntimeException` vs checked | Checked sem `rollbackFor` não faz rollback | Dado parcial permanece |
| I/O externo dentro da transação | Conexão presa por todo o timeout | Teste de resiliência trava |
| `@Transactional` no controller | Transação durante serialização | `@WebMvcTest` acusa acoplamento |
| `readOnly` ausente em consulta | Dirty checking desnecessário | Estatística de entidade carregada |

O caso mais frequente e menos visível é a self-invocation:

```java
@Service
public class PedidoService {
    public void aprovar(Long id) {
        this.persistirAprovacao(id);      // ← proxy ignorado, sem transação
    }

    @Transactional
    public void persistirAprovacao(Long id) { /* ... */ }
}
```

Parece correto. Funciona em teste unitário (o objeto é real). Falha em produção porque a transação nunca abriu — e o sintoma é dado inconsistente, não exceção. A correção é extrair para outro bean, não adicionar `@Transactional` no método público (que abriria transação onde não deve).

---

## 7. Dirty checking, batch e bulk

- **Dirty checking custa O(n).** Toda entidade carregada em modo gerenciado é comparada no flush. Loop sobre 50 mil registros em transação única = 50 mil comparações + memória.
- **Batch insert:** sem configuração, 10 mil inserts são 10 mil round-trips.

```yaml
spring.jpa.properties.hibernate.jdbc.batch_size: 50
spring.jpa.properties.hibernate.order_inserts: true
spring.jpa.properties.hibernate.order_updates: true
```

`GenerationType.IDENTITY` **impede batching** — o banco precisa gerar o ID a cada insert. Se volume de escrita importa, use `SEQUENCE` com `allocationSize`.

- **Bulk update** (`@Modifying`) não atualiza o contexto de persistência:

```java
@Modifying(clearAutomatically = true, flushAutomatically = true)
@Query("update PedidoJpa p set p.status = :novo where p.status = :atual")
int atualizarStatusEmLote(@Param("atual") Status atual, @Param("novo") Status novo);
```

Sem `clearAutomatically`, você lê entidade desatualizada do cache de primeiro nível depois do update. Bug de leitura silencioso, típico de relatório.

---

## 8. Concorrência: opte por padrão, não por acidente

```java
@Version
private Long versao;                    // optimistic locking
```

- **Optimistic** para conflito raro: detecta e devolve `409`. Custo zero em leitura, e o erro é honesto.
- **Pessimistic** (`PESSIMISTIC_WRITE`) para seção crítica curta e real contenção. Sempre com timeout:

```java
@Entity
public class EstoqueJpa {
    @Lock(LockModeType.PESSIMISTIC_WRITE)
    @QueryHints(@QueryHint(name = "jakarta.persistence.lock.timeout", value = "3000"))
    Optional<EstoqueJpa> findById(Long id);
}
```

Lock sem timeout é a forma mais eficiente de derrubar o pool inteiro. Um único fluxo travado segura a conexão até o cliente desistir.

---

## 9. Migrations: o único código que não tem rollback fácil

- **`ddl-auto: validate` em todo ambiente não-local.** Nunca `update` em produção: ele não remove coluna, não renomeia, não migra dado — e altera schema sem registro.
- **Compatibilidade para trás (expand/contract):** adicionar coluna nullable → backfill → usar → remover a antiga em release posterior. `DROP COLUMN` no mesmo deploy que remove o código impede rollback.
- **`CREATE INDEX` em tabela grande:** `CONCURRENTLY` (PostgreSQL) ou janela de manutenção.
- **Teste em banco real:** migration que só roda em produção é migration que nunca foi testada.

---

## 10. Pool: menos é mais

```yaml
spring.datasource.hikari:
  maximum-pool-size: 20          # ~ (núcleos × 2) + discos efetivos
  minimum-idle: 20               # pool fixo reduz latência de abertura
  connection-timeout: 3000       # falhe rápido
  max-lifetime: 1800000          # abaixo do timeout do banco/proxy
  leak-detection-threshold: 20000
```

`maximum-pool-size: 100` em 4 instâncias = 400 conexões concorrentes. O banco sufoca e o throughput **cai**. O pool é para controlar pressão, não para aumentar paralelismo.

Nota de versão: Spring Boot 4.1 adicionou `spring.datasource.connection-fetch` (`eager` / `lazy`) para controlar a inicialização de conexões do pool ([release notes](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-4.1-Release-Notes)). Útil quando você quer subir a aplicação antes de o banco estar disponível.

---

## 11. Modelagem: quatro decisões que geram incidente

| Tema | Errado | Certo |
|---|---|---|
| Dinheiro | `double` / `float` | `BigDecimal` + `NUMERIC(19,2)` + Value Object com moeda |
| Datas | `LocalDateTime` para instante | `Instant`/`OffsetDateTime` + `TIMESTAMP WITH TIME ZONE` |
| Enums | `@Enumerated(ORDINAL)` | `@Enumerated(EnumType.STRING)` |
| Igualdade | `@Data` ou `id` gerado | `id` explicitamente + estratégia de identidade declarada |

`ORDINAL` grava a posição do enum. Reordenar as constantes — o que parece inofensivo em review — troca silenciosamente o significado dos dados históricos. Esse bug corrompe base de dados e não tem correção retroativa fácil.

`double` para dinheiro produz erro de arredondamento acumulado: `0.1 + 0.2 != 0.3`. Em cálculo de imposto sobre milhões de registros, a diferença aparece em relatório financeiro.

---

## 12. Quando sair do ORM

Use SQL direto (`JdbcClient`, `@Query` nativa) quando:

- Relatório com agregação sobre volume alto.
- Bulk insert/update de grande escala.
- Window functions, CTE, `ON CONFLICT`, `RETURNING`.
- Modelo de leitura dedicado onde entidade não agrega.

O critério não é performance bruta — é **o ORM estar no caminho**. Se você carrega 5 entidades para somar um campo, o ORM é custo sem benefício.

E o inverso também é verdade: escrever SQL manual para CRUD transacional de agregado com invariantes é abdicar de dirty checking, cascata e controle de versão para reescrever o que o framework já faz.

---

## 13. Lombok: o que quebra, mecanicamente

### 13.1 O problema de base mudou em Java 23+

Este é um fato de versão que muita documentação interna ainda ignora: **a partir do JDK 23, o `javac` não executa mais processadores de anotação por padrão.** Sem configuração explícita, o código compila mas os métodos gerados pelo Lombok **simplesmente não existem** — e o erro é `cannot find symbol` no getter/setter, não uma mensagem sobre Lombok.

Foi exatamente o que aconteceu no relato registrado na issue [#3949 do Lombok](https://github.com/projectlombok/lombok/issues/3949): com JDK 25 e Lombok 1.18.42, `setCode` não era encontrado — a causa era annotation processing desabilitado.

| JDK | Versão mínima de Lombok |
|---|---|
| 25 LTS | 1.18.40 |
| 26 | 1.18.46 |
| 27 | 1.18.48 |

Fonte: [matriz de compatibilidade Lombok × JDK](https://www.marmo.dev/lombok-java).

Em Maven, a configuração mínima para JDK 23+:

```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-compiler-plugin</artifactId>
  <configuration>
    <release>25</release>
    <annotationProcessorPaths>
      <path>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.40</version>
      </path>
    </annotationProcessorPaths>
  </configuration>
</plugin>
```

**Consequência para a política:** atualizar o JDK agora exige, na mesma ordem, verificar a versão do Lombok e a configuração de annotation processing. Isso é um acoplamento permanente de toolchain com uma biblioteca de terceiro — e é um dos argumentos mais concretos contra adotá-lo em código novo.

### 13.2 Os riscos, um por um

**`@Data` em entidade JPA** — já demonstrado na seção 1.1. Três defeitos simultâneos. **Proibido, sem exceção.**

**`@Builder` sem `@Builder.Default`** — silenciosamente produz `null`/`0`:

```java
@Builder.Default
private Status status = Status.PENDENTE;   // sem isso, builder().build() → null
```

O erro aparece como violação de constraint no banco, longe da causa, e com stack trace que aponta para o SQL.

**`@Builder` + Jackson** — desserialização de request falha em runtime, não em teste, a menos que você adicione `@Jacksonized` ou construtor compatível. Em DTO de entrada, esse é um clássico de "funciona no meu teste com `new`".

**`@ToString` recursivo** — `StackOverflowError` em `log.debug("pedido={}", pedido)` com relacionamento bidirecional. E o log de erro é justamente onde você precisa do contexto.

**`@SneakyThrows`** — remove do contrato a única informação que checked exceptions carregam. Quem chama não sabe que pode falhar. Proibido.

**`@Value` do Lombok** — cria classe `final` com getters, não um record. Não tem acesso por componente, não participa de pattern matching, não é interoperável com desestruturação. Em Java 25 isso é escolher a versão pior de um recurso nativo.

### 13.3 Política de contenção

| Contexto | Permitido | Proibido |
|---|---|---|
| Entidade JPA | `@Getter`, `@NoArgsConstructor(access = PROTECTED)` | `@Data`, `@Builder`, `@EqualsAndHashCode`/`@ToString` sem análise |
| DTO / Value Object | `record` nativo | Lombok de qualquer tipo |
| Serviço / use case | `@RequiredArgsConstructor`, `@Slf4j` | `@Autowired` em campo |
| Qualquer lugar | — | `@SneakyThrows`, `@Value` do Lombok |

Conversão direta, que é a maior parte do uso real:

```java
// Lombok
@Data @Builder
public class CriarPedidoInput {
    private Long clienteId;
    private List<ItemInput> itens;
}

// Java 25
public record CriarPedidoInput(Long clienteId, List<ItemInput> itens) {
    public CriarPedidoInput {
        Objects.requireNonNull(clienteId, "clienteId");
        itens = List.copyOf(Objects.requireNonNull(itens, "itens"));
    }
}
```

Menos código, imutável, validado no construtor compacto, `equals`/`hashCode` corretos, sem annotation processing e legível no debug.

---

## 14. Matriz sintoma → causa → correção

| Sintoma | Causa raiz | Correção |
|---|---|---|
| 200 queries numa listagem | N+1 por toque em associação | Projeção ou `@EntityGraph` + batch fetch global |
| `OutOfMemoryError` em listagem paginada | `join fetch` em coleção + `Pageable` | Paginar raiz ou projetar |
| `LazyInitializationException` | Entidade vazando para a borda | DTO dentro do caso de uso |
| App "funciona" mas lento e sem trace de query | `open-in-view: true` | Desligar e corrigir a causa |
| `HashSet.contains` retorna `false` após save | `@Data` em entidade | `equals` por id explícito |
| `StackOverflowError` no log | `toString` bidirecional | `@ToString.Exclude` ou log explícito |
| Rollback não acontece | Self-invocation ou `catch` | Extrair para outro bean |
| Dado desatualizado após bulk update | Contexto de persistência não limpo | `clearAutomatically = true` |
| Constraint violation sem causa clara | `@Builder.Default` ausente | Default explícito |
| Compilação falha com "cannot find symbol" após upgrade de JDK | Annotation processing desabilitado (JDK 23+) | `annotationProcessorPaths` + Lombok compatível |
| Migração trava tabela grande | `CREATE INDEX` sem `CONCURRENTLY` | Índice concorrente |
| Pool esgotado sem tráfego alto | Timeout infinito em cliente externo ou lock | Timeouts + `leak-detection-threshold` |

---

## 15. Checklists

**Antes de abrir PR — persistência**

- Todo `@ManyToOne`/`@OneToOne` declara `fetch = LAZY`?
- `open-in-view` está `false`?
- Nenhum `join fetch` em coleção combinado com `Pageable`?
- Listagem nova tem teste de contagem de queries?
- Toda consulta nova tem índice que a suporte?
- `ddl-auto` está em `validate` fora de dev?
- Migration é compatível para trás (expand/contract)?
- Nenhum `double`/`float` em valor monetário?
- Enums persistidos como `STRING`?
- `@Version` presente em agregado com concorrência concorrente?

**Antes de abrir PR — Lombok**

- Nenhum `@Data` em `@Entity`?
- `equals`/`hashCode` de entidade explicitam a estratégia de identidade?
- `@ToString` exclui relacionamentos e dados sensíveis?
- Todo `@Builder` tem `@Builder.Default` nos campos com valor inicial?
- DTO de entrada é `record`, não classe com `@Builder`?
- Versão de Lombok ≥ mínima para o JDK em uso?
- Annotation processing configurado explicitamente no build?
- Nenhum `@SneakyThrows`?

---

## Resumindo

- **Blocos 8 e 9 produzem o mesmo tipo de defeito:** a classe que se lê não é a classe que roda. Nenhum dos dois pode ser validado por leitura — só por teste, por estatística de Hibernate ou por produção.
- **O ponto de cruzamento é `equals`/`hashCode` em entidade:** `@Data` gera hash mutável (entidade some do `HashSet` após `save`), `equals` que dispara lazy loading e `toString` que causa `StackOverflowError`. Entidade tem identidade de banco, não identidade de valor.
- **N+1 não é bug, é o default:** a correção não é "consertar o Hibernate", é escolher o padrão de carga por consulta — projeção para leitura, `@EntityGraph` para associação obrigatória, `default_batch_fetch_size` como rede de segurança permanente.
- **`join fetch` + `Pageable` em coleção é proibido:** pagina em memória, sem erro e sem log. Deve bloquear merge.
- **`open-in-view: false` desde o primeiro commit:** ligado, ele esconde N+1 e vaza conexão. Habilitar depois em sistema grande custa semanas.
- **A correção real de persistência é modelagem:** agregado para escrita, record de leitura para consulta. O resto é configuração compensando design.
- **Java 23+ quebrou o Lombok de um jeito silencioso:** annotation processing não roda mais por padrão e o erro é `cannot find symbol`. JDK 25 exige Lombok ≥ 1.18.40 **e** `annotationProcessorPaths` explícito — acoplamento permanente de toolchain.

---

## Encerramento do conjunto

A documentação ficou em quatro partes, cada uma com um fio condutor distinto e complementar:

| Parte | Blocos | Pergunta que ela responde |
|---|---|---|
| I — Orientações técnicas | 0 a 13 | O que fazer e o que evitar, em cada tema |
| II — API sob Lean e SOLID | 2, 3, 4 | O que deve existir e onde cada coisa mora |
| III — Arquitetura, qualidade e testes | 5, 6, 7 | Como verificar isso sem depender de opinião |
| IV — Persistência e Lombok | 8, 9 | Onde o runtime esconde o defeito que a leitura não vê |

O que conecta as quatro: **a Parte I define o padrão, a II decide a forma, a III instala o mecanismo de verificação e a IV cobre os dois pontos cegos do mecanismo.**

Se o time só puder adotar dez itens deste conjunto, a ordem de retorno seria: `open-in-view: false` e `default_batch_fetch_size` (uma linha cada), injeção por construtor, records em vez de Lombok, `@RestControllerAdvice` com `ProblemDetail`, ArchUnit no build, teste que conta queries em toda listagem, `ddl-auto: validate`, `Clock` injetado, e timeout em todo cliente externo.

Se quiser, posso:
- Consolidar as quatro partes em um único documento `.docx` ou `.pdf` navegável, com sumário e numeração contínua de seções.
- Gerar o `pom.xml` completo para Java 25 / Spring Boot 4.1 com Spotless, Checkstyle, SpotBugs, JaCoCo, ArchUnit, PIT, Testcontainers e configuração de annotation processing já correta.
- Criar o projeto de referência executável com camadas, contrato de porta por herança, guardrail de N+1 e teste de configuração de fetch validando que nenhum `@ManyToOne` ficou `EAGER`.

# Projeto
Vou entregar os dois últimos itens como um projeto único e coerente: o `pom.xml` com o pipeline completo e o código de referência que os testes realmente exercitam.

Três decisões de versão que validei antes de escrever, porque afetam compilação direta: no Spring Boot 4 o starter web passou a se chamar `spring-boot-starter-webmvc` e cada starter tem um `-test` correspondente (`spring-boot-starter-webmvc-test`, `spring-boot-starter-data-jpa-test`) ([Modularizing Spring Boot](https://spring.io/blog/2025/10/28/modularizing-spring-boot/), [Build Systems](https://docs.spring.io/spring-boot/reference/using/build-systems.html)); Flyway agora exige o starter próprio `spring-boot-starter-flyway`; e `@MockBean` foi removido em favor de `@MockitoBean` ([migration guide](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-4.0-Migration-Guide)).

---

## 1. Estrutura do projeto

```
pedidos-service/
├── pom.xml
├── config/checkstyle/checkstyle.xml
└── src
    ├── main
    │   ├── java/br/com/empresa/pedidos
    │   │   ├── PedidosApplication.java
    │   │   ├── domain
    │   │   │   ├── model          Pedido, PedidoId, ItemPedido, Dinheiro, Status, Pagina
    │   │   │   ├── exception      RegraNegocioException, RecursoNaoEncontradoException
    │   │   │   └── port
    │   │   │       ├── in         CriarPedido, AprovarPedido, ConsultarPedidosPorStatus
    │   │   │       │              CriarPedidoInput, ItemInput, PedidoOutput, PedidoResumo
    │   │   │       └── out        PedidoRepository, PedidoQuery
    │   │   ├── application
    │   │   │   └── usecase        CriarPedidoUseCase, AprovarPedidoUseCase, ConsultarPedidosPorStatusUseCase
    │   │   ├── infrastructure
    │   │   │   ├── config         ClockConfig
    │   │   │   ├── web            PedidoController, ApiExceptionHandler, CorrelationIdFilter
    │   │   │   └── persistence    PedidoJpa, ItemPedidoJpa, ClienteJpa, PedidoJpaRepository,
    │   │   │                      PedidoJpaMapper, PedidoPersistenceAdapter, PedidoQueryAdapter
    │   │   └── shared
    │   └── resources
    │       ├── application.yml
    │       └── db/migration/V1__schema.sql
    └── test/java/br/com/empresa/pedidos
        ├── architecture             ArquiteturaTest
        ├── domain/model             PedidoTest, DinheiroTest
        ├── application/usecase      CriarPedidoUseCaseTest, AprovarPedidoUseCaseTest
        ├── infrastructure/web       PedidoControllerTest
        ├── infrastructure/persistence
        │                            PedidoRepositoryContract, JpaPedidoRepositoryTest,
        │                            InMemoryPedidoRepository, InMemoryPedidoRepositoryTest,
        │                            SemN1Test, FetchConfigurationTest
        └── support                  PedidoFixture
```

---

## 2. Build

### `pom.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>4.1.0</version>
    <relativePath/>
  </parent>

  <groupId>br.com.empresa</groupId>
  <artifactId>pedidos-service</artifactId>
  <version>1.0.0-SNAPSHOT</version>
  <name>pedidos-service</name>

  <properties>
    <java.version>25</java.version>
    <archunit.version>1.4.0</archunit.version>
    <pitest.version>1.17.4</pitest.version>
    <spotless.version>2.44.2</spotless.version>
    <jacoco.version>0.8.14</jacoco.version>
    <spotbugs.version>4.8.6.6</spotbugs.version>
    <checkstyle.plugin.version>3.6.0</checkstyle.plugin.version>
  </properties>

  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-webmvc</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-flyway</artifactId>
    </dependency>
    <dependency>
      <groupId>org.flywaydb</groupId>
      <artifactId>flyway-database-postgresql</artifactId>
    </dependency>
    <dependency>
      <groupId>org.postgresql</groupId>
      <artifactId>postgresql</artifactId>
      <scope>runtime</scope>
    </dependency>

    <!-- Testes -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-webmvc-test</artifactId>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-jpa-test</artifactId>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-testcontainers</artifactId>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.testcontainers</groupId>
      <artifactId>junit-jupiter</artifactId>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.testcontainers</groupId>
      <artifactId>postgresql</artifactId>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>com.tngtech.archunit</groupId>
      <artifactId>archunit-junit5</artifactId>
      <version>${archunit.version}</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>

      <!-- Formatação: falha o build se o código não estiver formatado -->
      <plugin>
        <groupId>com.diffplug.spotless</groupId>
        <artifactId>spotless-maven-plugin</artifactId>
        <version>${spotless.version}</version>
        <configuration>
          <java>
            <includes>
              <include>src/main/java/**/*.java</include>
              <include>src/test/java/**/*.java</include>
            </includes>
            <palantirJavaFormat/>
            <removeUnusedImports/>
            <importOrder/>
            <endWithNewline/>
          </java>
        </configuration>
        <executions>
          <execution>
            <id>spotless-check</id>
            <phase>validate</phase>
            <goals><goal>check</goal></goals>
          </execution>
        </executions>
      </plugin>

      <!-- Checkstyle: regras mínimas, sem ruído de estilo -->
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-checkstyle-plugin</artifactId>
        <version>${checkstyle.plugin.version}</version>
        <configuration>
          <configLocation>config/checkstyle/checkstyle.xml</configLocation>
          <consoleOutput>true</consoleOutput>
          <failOnViolation>true</failOnViolation>
          <violationSeverity>warning</violationSeverity>
          <includeTestSourceDirectory>true</includeTestSourceDirectory>
        </configuration>
        <executions>
          <execution>
            <id>checkstyle</id>
            <phase>verify</phase>
            <goals><goal>check</goal></goals>
          </execution>
        </executions>
      </plugin>

      <!-- Cobertura: indicador de lacuna, não meta -->
      <plugin>
        <groupId>org.jacoco</groupId>
        <artifactId>jacoco-maven-plugin</artifactId>
        <version>${jacoco.version}</version>
        <executions>
          <execution>
            <id>prepare-agent</id>
            <goals><goal>prepare-agent</goal></goals>
          </execution>
          <execution>
            <id>report</id>
            <phase>verify</phase>
            <goals><goal>report</goal></goals>
          </execution>
          <execution>
            <id>gate-camadas-centrais</id>
            <phase>verify</phase>
            <goals><goal>check</goal></goals>
            <configuration>
              <rules>
                <rule>
                  <element>BUNDLE</element>
                  <limits>
                    <limit>
                      <counter>LINE</counter>
                      <value>COVEREDRATIO</value>
                      <minimum>0.85</minimum>
                    </limit>
                  </limits>
                </rule>
              </rules>
            </configuration>
          </execution>
        </executions>
      </plugin>

      <!-- Bugs: defaults do SpotBugs, sem exclude file -->
      <plugin>
        <groupId>com.github.spotbugs</groupId>
        <artifactId>spotbugs-maven-plugin</artifactId>
        <version>${spotbugs.version}</version>
        <configuration>
          <effort>Max</effort>
          <threshold>Medium</threshold>
          <excludeFilterFile>config/spotbugs/spotbugs-exclude.xml</excludeFilterFile>
        </configuration>
        <executions>
          <execution>
            <id>spotbugs-check</id>
            <phase>verify</phase>
            <goals><goal>check</goal></goals>
          </execution>
        </executions>
      </plugin>

      <!-- Mutação: rodar sob demanda, não em todo build -->
      <plugin>
        <groupId>org.pitest</groupId>
        <artifactId>pitest-maven</artifactId>
        <version>${pitest.version}</version>
        <configuration>
          <targetClasses>
            <param>br.com.empresa.pedidos.domain.model.*</param>
          </targetClasses>
          <targetTests>
            <param>br.com.empresa.pedidos.domain.model.*</param>
          </targetTests>
          <mutationThreshold>75</mutationThreshold>
          <outputFormats>
            <outputFormat>HTML</outputFormat>
          </outputFormats>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

### `config/checkstyle/checkstyle.xml`

```xml
<?xml version="1.0"?>
<!DOCTYPE module PUBLIC
    "-//Checkstyle//DTD Checkstyle Configuration 1.3//EN"
    "https://checkstyle.org/dtds/configuration_1_3.dtd">
<module name="Checker">
  <property name="severity" value="warning"/>
  <module name="TreeWalker">
    <module name="UnusedImports"/>
    <module name="RedundantImport"/>
    <module name="EqualsHashCode"/>
    <module name="MissingSwitchDefault"/>
    <module name="SimplifyBooleanExpression"/>
    <module name="EmptyBlock">
      <property name="option" value="text"/>
    </module>
    <module name="IllegalCatch">
      <property name="illegalClassNames" value="java.lang.Exception, java.lang.Throwable"/>
    </module>
    <module name="IllegalThrows">
      <property name="illegalClassNames" value="java.lang.Throwable, java.lang.Error"/>
    </module>
    <module name="ParameterNumber">
      <property name="max" value="5"/>
    </module>
    <module name="MethodLength">
      <property name="max" value="60"/>
    </module>
  </module>
  <module name="LineLength">
    <property name="max" value="140"/>
  </module>
</module>
```

> Duas configurações de suporte que o pom referencia e que você cria no repositório: `config/spotbugs/spotbugs-exclude.xml` com um `<FindBugsFilter/>` vazio, e as versões de plugins mantidas no seu repositório interno. Espelhe os números acima apenas se não houver versão corporativa homologada.

---

## 3. Configuração da aplicação

### `application.yml`

```yaml
spring:
  application:
    name: pedidos-service
  jpa:
    open-in-view: false
    hibernate:
      ddl-auto: validate
    properties:
      hibernate:
        default_batch_fetch_size: 50
        jdbc:
          batch_size: 50
        order_inserts: true
        order_updates: true
  datasource:
    hikari:
      maximum-pool-size: 20
      minimum-idle: 20
      connection-timeout: 3000
      max-lifetime: 1800000

management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics,prometheus
  endpoint:
    health:
      probes:
        enabled: true
      show-details: when_authorized

server:
  shutdown: graceful

logging:
  pattern:
    level: "%5p [%X{correlationId:-}]"
```

### `db/migration/V1__schema.sql`

```sql
create sequence pedido_seq start with 1 increment by 50;
create sequence item_pedido_seq start with 1 increment by 50;

create table cliente (
    id   bigint       primary key,
    nome varchar(120) not null
);

create table pedido (
    id         bigint       primary key,
    cliente_id bigint       not null references cliente (id),
    status     varchar(20)  not null,
    criado_em  timestamptz  not null
);

create index idx_pedido_status_criado_em on pedido (status, criado_em desc);

create table item_pedido (
    id             bigint         primary key,
    pedido_id      bigint         not null references pedido (id) on delete cascade,
    sku            varchar(40)    not null,
    quantidade     integer        not null check (quantidade > 0),
    preco_unitario numeric(19, 2) not null check (preco_unitario > 0)
);

create index idx_item_pedido_pedido on item_pedido (pedido_id);
```

---

## 4. Domínio — Java puro, zero framework

### `Pedido.java`

```java
package br.com.empresa.pedidos.domain.model;

import br.com.empresa.pedidos.domain.exception.RegraNegocioException;
import java.time.Instant;
import java.util.List;
import java.util.Objects;

public record Pedido(PedidoId id, Long clienteId, Status status, List<ItemPedido> itens, Instant criadoEm) {

    public Pedido {
        Objects.requireNonNull(clienteId, "clienteId");
        Objects.requireNonNull(status, "status");
        Objects.requireNonNull(criadoEm, "criadoEm");
        itens = List.copyOf(Objects.requireNonNull(itens, "itens"));
    }

    public static Pedido criar(Long clienteId, List<ItemPedido> itens, Instant agora) {
        if (itens == null || itens.isEmpty()) {
            throw new RegraNegocioException("PEDIDO_SEM_ITENS", "Pedido precisa de ao menos um item");
        }
        if (itens.size() > 50) {
            throw new RegraNegocioException("PEDIDO_ITENS_EXCEDIDOS", "Pedido aceita no máximo 50 itens");
        }
        return new Pedido(null, clienteId, Status.PENDENTE, itens, agora);
    }

    public Dinheiro total() {
        return itens.stream().map(ItemPedido::subtotal).reduce(Dinheiro.zero(), Dinheiro::somar);
    }

    public Pedido aprovar() {
        if (status == Status.CANCELADO) {
            throw new RegraNegocioException("PEDIDO_STATUS_INVALIDO", "Pedido cancelado não pode ser aprovado");
        }
        if (status == Status.APROVADO) {
            throw new RegraNegocioException("PEDIDO_STATUS_INVALIDO", "Pedido já está aprovado");
        }
        if (total().naoPositivo()) {
            throw new RegraNegocioException("PEDIDO_TOTAL_INVALIDO", "Total do pedido deve ser positivo");
        }
        return new Pedido(id, clienteId, Status.APROVADO, itens, criadoEm);
    }

    public Pedido cancelar() {
        if (status == Status.CANCELADO) {
            throw new RegraNegocioException("PEDIDO_STATUS_INVALIDO", "Pedido já está cancelado");
        }
        return new Pedido(id, clienteId, Status.CANCELADO, itens, criadoEm);
    }

    public Pedido comId(PedidoId novoId) {
        return new Pedido(novoId, clienteId, status, itens, criadoEm);
    }
}
```

### `PedidoId.java`, `Status.java`, `ItemPedido.java`, `Dinheiro.java`, `Pagina.java`

```java
package br.com.empresa.pedidos.domain.model;

import java.util.Objects;

public record PedidoId(Long valor) {
    public PedidoId {
        Objects.requireNonNull(valor, "valor");
    }
}
```

```java
package br.com.empresa.pedidos.domain.model;

public enum Status {
    PENDENTE,
    APROVADO,
    CANCELADO
}
```

```java
package br.com.empresa.pedidos.domain.model;

import java.util.Objects;

public record ItemPedido(String sku, int quantidade, Dinheiro precoUnitario) {

    public ItemPedido {
        Objects.requireNonNull(sku, "sku");
        Objects.requireNonNull(precoUnitario, "precoUnitario");
        if (quantidade <= 0) {
            throw new IllegalArgumentException("quantidade deve ser positiva");
        }
    }

    public Dinheiro subtotal() {
        return precoUnitario.multiplicar(quantidade);
    }
}
```

```java
package br.com.empresa.pedidos.domain.model;

import java.math.BigDecimal;
import java.math.RoundingMode;
import java.util.Objects;

public record Dinheiro(BigDecimal valor) {

    public Dinheiro {
        Objects.requireNonNull(valor, "valor");
        valor = valor.setScale(2, RoundingMode.HALF_EVEN);
    }

    public static Dinheiro de(String valor) {
        return new Dinheiro(new BigDecimal(valor));
    }

    public static Dinheiro zero() {
        return new Dinheiro(BigDecimal.ZERO);
    }

    public Dinheiro somar(Dinheiro outro) {
        return new Dinheiro(valor.add(outro.valor));
    }

    public Dinheiro multiplicar(int quantidade) {
        return new Dinheiro(valor.multiply(BigDecimal.valueOf(quantidade)));
    }

    public boolean naoPositivo() {
        return valor.signum() <= 0;
    }
}
```

```java
package br.com.empresa.pedidos.domain.model;

import java.util.List;

public record Pagina<T>(List<T> conteudo, int pagina, int tamanho, long total, int totalPaginas) {

    public Pagina {
        conteudo = List.copyOf(conteudo);
    }

    public static <T> Pagina<T> de(List<T> conteudo, int pagina, int tamanho, long total) {
        int paginas = tamanho == 0 ? 0 : (int) Math.ceil((double) total / tamanho);
        return new Pagina<>(conteudo, pagina, tamanho, total, paginas);
    }
}
```

### Exceções de domínio

```java
package br.com.empresa.pedidos.domain.exception;

public class RegraNegocioException extends RuntimeException {

    private final String codigo;

    public RegraNegocioException(String codigo, String mensagem) {
        super(mensagem);
        this.codigo = codigo;
    }

    public String codigo() {
        return codigo;
    }
}
```

```java
package br.com.empresa.pedidos.domain.exception;

public class RecursoNaoEncontradoException extends RuntimeException {

    private final String recurso;
    private final Object identificador;

    public RecursoNaoEncontradoException(String recurso, Object identificador) {
        super(recurso + " não encontrado: " + identificador);
        this.recurso = recurso;
        this.identificador = identificador;
    }

    public String recurso() {
        return recurso;
    }

    public Object identificador() {
        return identificador;
    }
}
```

---

## 5. Portas e contratos

### `domain/port/out/PedidoRepository.java` e `PedidoQuery.java`

```java
package br.com.empresa.pedidos.domain.port.out;

import br.com.empresa.pedidos.domain.model.Pedido;
import br.com.empresa.pedidos.domain.model.PedidoId;
import br.com.empresa.pedidos.domain.model.Status;
import java.util.List;
import java.util.Optional;

/**
 * Porta de escrita: opera sobre o agregado completo.
 * Contrato: {@code porId} devolve {@code Optional.empty()} quando o pedido não existe;
 * {@code salvar} devolve o agregado com id atribuído.
 */
public interface PedidoRepository {

    Pedido salvar(Pedido pedido);

    Optional<Pedido> porId(PedidoId id);

    List<Pedido> listarPorStatus(Status status);
}
```

```java
package br.com.empresa.pedidos.domain.port.out;

import br.com.empresa.pedidos.domain.model.Pagina;
import br.com.empresa.pedidos.domain.model.Status;
import br.com.empresa.pedidos.domain.port.in.PedidoResumo;

/** Porta de leitura: devolve dado, nunca entidade. Não conhece Spring Data. */
public interface PedidoQuery {

    Pagina<PedidoResumo> resumirPorStatus(Status status, int pagina, int tamanho);
}
```

### Portas de entrada e seus contratos

```java
package br.com.empresa.pedidos.domain.port.in;

public interface CriarPedido {
    PedidoOutput executar(CriarPedidoInput input);
}
```

```java
package br.com.empresa.pedidos.domain.port.in;

public interface AprovarPedido {
    PedidoOutput executar(PedidoId id);
}
```

```java
package br.com.empresa.pedidos.domain.port.in;

import br.com.empresa.pedidos.domain.model.Pagina;
import br.com.empresa.pedidos.domain.model.Status;

public interface ConsultarPedidosPorStatus {
    Pagina<PedidoResumo> executar(Status status, int pagina, int tamanho);
}
```

```java
package br.com.empresa.pedidos.domain.port.in;

import jakarta.validation.Valid;
import jakarta.validation.constraints.NotEmpty;
import jakarta.validation.constraints.NotNull;
import jakarta.validation.constraints.Positive;
import jakarta.validation.constraints.Size;
import java.util.List;

public record CriarPedidoInput(
        @NotNull @Positive Long clienteId, @NotEmpty @Size(max = 50) List<@Valid ItemInput> itens) {}
```

```java
package br.com.empresa.pedidos.domain.port.in;

import br.com.empresa.pedidos.domain.model.Dinheiro;
import br.com.empresa.pedidos.domain.model.ItemPedido;
import jakarta.validation.constraints.DecimalMin;
import jakarta.validation.constraints.Max;
import jakarta.validation.constraints.Min;
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.NotNull;
import jakarta.validation.constraints.Size;
import java.math.BigDecimal;

public record ItemInput(
        @NotBlank @Size(max = 40) String sku,
        @Min(1) @Max(999) int quantidade,
        @NotNull @DecimalMin("0.01") BigDecimal precoUnitario) {

    public ItemPedido paraDominio() {
        return new ItemPedido(sku, quantidade, new Dinheiro(precoUnitario));
    }
}
```

```java
package br.com.empresa.pedidos.domain.port.in;

import br.com.empresa.pedidos.domain.model.Pedido;
import java.math.BigDecimal;
import java.time.Instant;

public record PedidoOutput(
        Long id, Long clienteId, String status, BigDecimal total, Instant criadoEm) {

    public static PedidoOutput de(Pedido pedido) {
        return new PedidoOutput(
                pedido.id() == null ? null : pedido.id().valor(),
                pedido.clienteId(),
                pedido.status().name(),
                pedido.total().valor(),
                pedido.criadoEm());
    }
}
```

```java
package br.com.empresa.pedidos.domain.port.in;

import java.math.BigDecimal;
import java.time.Instant;

public record PedidoResumo(Long id, String clienteNome, Instant criadoEm, BigDecimal total) {}
```

---

## 6. Aplicação — orquestração com uma transação por caso de uso

```java
package br.com.empresa.pedidos.application.usecase;

import br.com.empresa.pedidos.domain.model.Pedido;
import br.com.empresa.pedidos.domain.port.in.CriarPedido;
import br.com.empresa.pedidos.domain.port.in.CriarPedidoInput;
import br.com.empresa.pedidos.domain.port.in.PedidoOutput;
import br.com.empresa.pedidos.domain.port.out.PedidoRepository;
import java.time.Clock;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class CriarPedidoUseCase implements CriarPedido {

    private final PedidoRepository pedidoRepository;
    private final Clock clock;

    public CriarPedidoUseCase(PedidoRepository pedidoRepository, Clock clock) {
        this.pedidoRepository = pedidoRepository;
        this.clock = clock;
    }

    @Override
    @Transactional
    public PedidoOutput executar(CriarPedidoInput input) {
        var itens = input.itens().stream().map(ItemInputMapper::paraDominio).toList();
        var pedido = Pedido.criar(input.clienteId(), itens, clock.instant());
        return PedidoOutput.de(pedidoRepository.salvar(pedido));
    }
}
```

> Substitua `ItemInputMapper::paraDominio` por `ItemInput::paraDominio` — o compilador aceita a referência de método de instância direto. Mantive o tipo explícito apenas para deixar o mapeamento visível na leitura.

```java
package br.com.empresa.pedidos.application.usecase;

import br.com.empresa.pedidos.domain.exception.RecursoNaoEncontradoException;
import br.com.empresa.pedidos.domain.model.PedidoId;
import br.com.empresa.pedidos.domain.port.in.AprovarPedido;
import br.com.empresa.pedidos.domain.port.in.PedidoOutput;
import br.com.empresa.pedidos.domain.port.out.PedidoRepository;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class AprovarPedidoUseCase implements AprovarPedido {

    private final PedidoRepository pedidoRepository;

    public AprovarPedidoUseCase(PedidoRepository pedidoRepository) {
        this.pedidoRepository = pedidoRepository;
    }

    @Override
    @Transactional
    public PedidoOutput executar(PedidoId id) {
        var pedido = pedidoRepository
                .porId(id)
                .orElseThrow(() -> new RecursoNaoEncontradoException("pedido", id.valor()));
        return PedidoOutput.de(pedidoRepository.salvar(pedido.aprovar()));
    }
}
```

```java
package br.com.empresa.pedidos.application.usecase;

import br.com.empresa.pedidos.domain.model.Pagina;
import br.com.empresa.pedidos.domain.model.Status;
import br.com.empresa.pedidos.domain.port.in.ConsultarPedidosPorStatus;
import br.com.empresa.pedidos.domain.port.in.PedidoResumo;
import br.com.empresa.pedidos.domain.port.out.PedidoQuery;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
@Transactional(readOnly = true)
public class ConsultarPedidosPorStatusUseCase implements ConsultarPedidosPorStatus {

    private static final int TAMANHO_MAXIMO = 100;

    private final PedidoQuery pedidoQuery;

    public ConsultarPedidosPorStatusUseCase(PedidoQuery pedidoQuery) {
        this.pedidoQuery = pedidoQuery;
    }

    @Override
    public Pagina<PedidoResumo> executar(Status status, int pagina, int tamanho) {
        int paginaNormalizada = Math.max(pagina, 0);
        int tamanhoNormalizado = Math.min(Math.max(tamanho, 1), TAMANHO_MAXIMO);
        return pedidoQuery.resumirPorStatus(status, paginaNormalizada, tamanhoNormalizado);
    }
}
```

---

## 7. Infraestrutura web

### `PedidoController.java`

```java
package br.com.empresa.pedidos.infrastructure.web;

import br.com.empresa.pedidos.domain.model.Pagina;
import br.com.empresa.pedidos.domain.model.PedidoId;
import br.com.empresa.pedidos.domain.model.Status;
import br.com.empresa.pedidos.domain.port.in.AprovarPedido;
import br.com.empresa.pedidos.domain.port.in.ConsultarPedidosPorStatus;
import br.com.empresa.pedidos.domain.port.in.CriarPedido;
import br.com.empresa.pedidos.domain.port.in.CriarPedidoInput;
import br.com.empresa.pedidos.domain.port.in.PedidoOutput;
import br.com.empresa.pedidos.domain.port.in.PedidoResumo;
import jakarta.validation.Valid;
import java.net.URI;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/v1/pedidos")
public class PedidoController {

    private final CriarPedido criarPedido;
    private final AprovarPedido aprovarPedido;
    private final ConsultarPedidosPorStatus consultarPedidos;

    public PedidoController(
            CriarPedido criarPedido,
            AprovarPedido aprovarPedido,
            ConsultarPedidosPorStatus consultarPedidos) {
        this.criarPedido = criarPedido;
        this.aprovarPedido = aprovarPedido;
        this.consultarPedidos = consultarPedidos;
    }

    @PostMapping
    public ResponseEntity<PedidoOutput> criar(@Valid @RequestBody CriarPedidoInput input) {
        var output = criarPedido.executar(input);
        return ResponseEntity.created(URI.create("/v1/pedidos/" + output.id())).body(output);
    }

    @PostMapping("/{id}/aprovacao")
    public PedidoOutput aprovar(@PathVariable Long id) {
        return aprovarPedido.executar(new PedidoId(id));
    }

    @GetMapping
    public Pagina<PedidoResumo> listar(
            @RequestParam(defaultValue = "PENDENTE") Status status,
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "20") int size) {
        return consultarPedidos.executar(status, page, size);
    }
}
```

### `ApiExceptionHandler.java`

```java
package br.com.empresa.pedidos.infrastructure.web;

import br.com.empresa.pedidos.domain.exception.RecursoNaoEncontradoException;
import br.com.empresa.pedidos.domain.exception.RegraNegocioException;
import java.net.URI;
import java.util.List;
import java.util.Map;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.slf4j.MDC;
import org.springframework.http.HttpStatus;
import org.springframework.http.ProblemDetail;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice
public class ApiExceptionHandler {

    private static final Logger log = LoggerFactory.getLogger(ApiExceptionHandler.class);

    @ExceptionHandler(RecursoNaoEncontradoException.class)
    ProblemDetail naoEncontrado(RecursoNaoEncontradoException ex) {
        var problema = ProblemDetail.forStatusAndDetail(HttpStatus.NOT_FOUND, ex.getMessage());
        problema.setType(URI.create("https://api.exemplo.com/problems/recurso-nao-encontrado"));
        problema.setTitle("Recurso não encontrado");
        problema.setProperty("codigo", "RECURSO_NAO_ENCONTRADO");
        problema.setProperty("correlationId", correlationId());
        return problema;
    }

    @ExceptionHandler(RegraNegocioException.class)
    ProblemDetail regraDeNegocio(RegraNegocioException ex) {
        var problema = ProblemDetail.forStatusAndDetail(HttpStatus.UNPROCESSABLE_ENTITY, ex.getMessage());
        problema.setType(URI.create("https://api.exemplo.com/problems/regra-negocio"));
        problema.setTitle("Regra de negócio violada");
        problema.setProperty("codigo", ex.codigo());
        problema.setProperty("correlationId", correlationId());
        return problema;
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    ProblemDetail validacao(MethodArgumentNotValidException ex) {
        List<Map<String, String>> campos = ex.getBindingResult().getFieldErrors().stream()
                .map(erro -> Map.of("campo", erro.getField(), "mensagem", String.valueOf(erro.getDefaultMessage())))
                .toList();
        var problema = ProblemDetail.forStatusAndDetail(HttpStatus.BAD_REQUEST, "Falha de validação");
        problema.setType(URI.create("https://api.exemplo.com/problems/validacao"));
        problema.setTitle("Requisição inválida");
        problema.setProperty("codigo", "VALIDACAO");
        problema.setProperty("campos", campos);
        problema.setProperty("correlationId", correlationId());
        return problema;
    }

    @ExceptionHandler(Exception.class)
    ProblemDetail inesperado(Exception ex) {
        log.error("erro inesperado correlationId={}", correlationId(), ex);
        var problema = ProblemDetail.forStatusAndDetail(HttpStatus.INTERNAL_SERVER_ERROR, "Erro interno");
        problema.setType(URI.create("https://api.exemplo.com/problems/erro-interno"));
        problema.setTitle("Erro interno");
        problema.setProperty("codigo", "ERRO_INTERNO");
        problema.setProperty("correlationId", correlationId());
        return problema;
    }

    private String correlationId() {
        return MDC.get("correlationId");
    }
}
```

### `CorrelationIdFilter.java`

```java
package br.com.empresa.pedidos.infrastructure.web;

import jakarta.servlet.FilterChain;
import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Optional;
import java.util.UUID;
import org.slf4j.MDC;
import org.springframework.stereotype.Component;
import org.springframework.web.filter.OncePerRequestFilter;

@Component
public class CorrelationIdFilter extends OncePerRequestFilter {

    public static final String HEADER = "X-Correlation-Id";

    @Override
    protected void doFilterInternal(
            HttpServletRequest request, HttpServletResponse response, FilterChain chain)
            throws ServletException, IOException {
        var id = Optional.ofNullable(request.getHeader(HEADER))
                .filter(valor -> !valor.isBlank())
                .orElseGet(() -> UUID.randomUUID().toString());
        MDC.put("correlationId", id);
        response.setHeader(HEADER, id);
        try {
            chain.doFilter(request, response);
        } finally {
            MDC.remove("correlationId");
        }
    }
}
```

---

## 8. Infraestrutura de persistência

### Entidades

```java
package br.com.empresa.pedidos.infrastructure.persistence;

import jakarta.persistence.Entity;
import jakarta.persistence.Id;
import jakarta.persistence.Table;

@Entity
@Table(name = "cliente")
public class ClienteJpa {

    @Id
    private Long id;

    private String nome;

    protected ClienteJpa() {}

    public ClienteJpa(Long id, String nome) {
        this.id = id;
        this.nome = nome;
    }

    public Long getId() {
        return id;
    }

    public String getNome() {
        return nome;
    }
}
```

```java
package br.com.empresa.pedidos.infrastructure.persistence;

import br.com.empresa.pedidos.domain.model.Status;
import jakarta.persistence.CascadeType;
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.FetchType;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.OneToMany;
import jakarta.persistence.SequenceGenerator;
import jakarta.persistence.Table;
import java.time.Instant;
import java.util.ArrayList;
import java.util.List;

@Entity
@Table(name = "pedido")
public class PedidoJpa {

    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "pedido_seq")
    @SequenceGenerator(name = "pedido_seq", sequenceName = "pedido_seq", allocationSize = 50)
    private Long id;

    @Column(name = "cliente_id", nullable = false)
    private Long clienteId;

    /**
     * Associação somente leitura: existe para permitir join na consulta.
     * A escrita usa o escalar clienteId acima.
     */
    @ManyToOne(fetch = FetchType.LAZY, optional = false)
    @JoinColumn(name = "cliente_id", insertable = false, updatable = false)
    private ClienteJpa cliente;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false, length = 20)
    private Status status;

    @Column(name = "criado_em", nullable = false)
    private Instant criadoEm;

    @OneToMany(mappedBy = "pedido", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<ItemPedidoJpa> itens = new ArrayList<>();

    protected PedidoJpa() {}

    public Long getId() {
        return id;
    }

    public Long getClienteId() {
        return clienteId;
    }

    public void setClienteId(Long clienteId) {
        this.clienteId = clienteId;
    }

    public Status getStatus() {
        return status;
    }

    public void setStatus(Status status) {
        this.status = status;
    }

    public Instant getCriadoEm() {
        return criadoEm;
    }

    public void setCriadoEm(Instant criadoEm) {
        this.criadoEm = criadoEm;
    }

    public List<ItemPedidoJpa> getItens() {
        return List.copyOf(itens);
    }

    public void adicionarItem(ItemPedidoJpa item) {
        item.vincularA(this);
        this.itens.add(item);
    }
}
```

```java
package br.com.empresa.pedidos.infrastructure.persistence;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.FetchType;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.SequenceGenerator;
import jakarta.persistence.Table;
import java.math.BigDecimal;

@Entity
@Table(name = "item_pedido")
public class ItemPedidoJpa {

    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "item_pedido_seq")
    @SequenceGenerator(name = "item_pedido_seq", sequenceName = "item_pedido_seq", allocationSize = 50)
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY, optional = false)
    @JoinColumn(name = "pedido_id", nullable = false)
    private PedidoJpa pedido;

    @Column(nullable = false, length = 40)
    private String sku;

    @Column(nullable = false)
    private int quantidade;

    @Column(name = "preco_unitario", nullable = false, precision = 19, scale = 2)
    private BigDecimal precoUnitario;

    protected ItemPedidoJpa() {}

    public ItemPedidoJpa(String sku, int quantidade, BigDecimal precoUnitario) {
        this.sku = sku;
        this.quantidade = quantidade;
        this.precoUnitario = precoUnitario;
    }

    void vincularA(PedidoJpa pedido) {
        this.pedido = pedido;
    }

    public String getSku() {
        return sku;
    }

    public int getQuantidade() {
        return quantidade;
    }

    public BigDecimal getPrecoUnitario() {
        return precoUnitario;
    }
}
```

### Repositório, mapper e adapters

```java
package br.com.empresa.pedidos.infrastructure.persistence;

import br.com.empresa.pedidos.domain.model.Status;
import br.com.empresa.pedidos.domain.port.in.PedidoResumo;
import java.util.List;
import java.util.Optional;
import org.springframework.data.domain.Pageable;
import org.springframework.data.jpa.repository.EntityGraph;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;

interface PedidoJpaRepository extends JpaRepository<PedidoJpa, Long> {

    @Override
    @EntityGraph(attributePaths = "itens")
    Optional<PedidoJpa> findById(Long id);

    long countByStatus(Status status);

    @Query(
            """
            select new br.com.empresa.pedidos.domain.port.in.PedidoResumo(
                p.id,
                c.nome,
                p.criadoEm,
                (select sum(i.precoUnitario * i.quantidade)
                   from ItemPedidoJpa i
                  where i.pedido = p))
            from PedidoJpa p
              join p.cliente c
            where p.status = :status
            order by p.criadoEm desc
            """)
    List<PedidoResumo> resumirPorStatus(@Param("status") Status status, Pageable pageable);
}
```

```java
package br.com.empresa.pedidos.infrastructure.persistence;

import br.com.empresa.pedidos.domain.model.Dinheiro;
import br.com.empresa.pedidos.domain.model.ItemPedido;
import br.com.empresa.pedidos.domain.model.Pedido;
import br.com.empresa.pedidos.domain.model.PedidoId;

/** Conversão estrutural; mora junto do adapter, não é bean. */
final class PedidoJpaMapper {

    private PedidoJpaMapper() {}

    static Pedido paraDominio(PedidoJpa entidade) {
        var itens = entidade.getItens().stream()
                .map(item -> new ItemPedido(
                        item.getSku(), item.getQuantidade(), new Dinheiro(item.getPrecoUnitario())))
                .toList();
        return new Pedido(
                new PedidoId(entidade.getId()),
                entidade.getClienteId(),
                entidade.getStatus(),
                itens,
                entidade.getCriadoEm());
    }

    static PedidoJpa novo(Pedido pedido) {
        var entidade = new PedidoJpa();
        entidade.setClienteId(pedido.clienteId());
        entidade.setStatus(pedido.status());
        entidade.setCriadoEm(pedido.criadoEm());
        pedido.itens()
                .forEach(item -> entidade.adicionarItem(
                        new ItemPedidoJpa(item.sku(), item.quantidade(), item.precoUnitario().valor())));
        return entidade;
    }
}
```

```java
package br.com.empresa.pedidos.infrastructure.persistence;

import br.com.empresa.pedidos.domain.exception.RecursoNaoEncontradoException;
import br.com.empresa.pedidos.domain.model.Pedido;
import br.com.empresa.pedidos.domain.model.PedidoId;
import br.com.empresa.pedidos.domain.model.Status;
import br.com.empresa.pedidos.domain.port.out.PedidoRepository;
import java.util.List;
import java.util.Optional;
import org.springframework.stereotype.Repository;

@Repository
public class PedidoPersistenceAdapter implements PedidoRepository {

    private final PedidoJpaRepository jpa;

    public PedidoPersistenceAdapter(PedidoJpaRepository jpa) {
        this.jpa = jpa;
    }

    @Override
    public Pedido salvar(Pedido pedido) {
        var entidade = pedido.id() == null ? PedidoJpaMapper.novo(pedido) : existente(pedido);
        return PedidoJpaMapper.paraDominio(jpa.save(entidade));
    }

    @Override
    public Optional<Pedido> porId(PedidoId id) {
        return jpa.findById(id.valor()).map(PedidoJpaMapper::paraDominio);
    }

    @Override
    public List<Pedido> listarPorStatus(Status status) {
        return jpa.findAll().stream().filter(p -> p.getStatus() == status).map(PedidoJpaMapper::paraDominio).toList();
    }

    private PedidoJpa existente(Pedido pedido) {
        var entidade = jpa.findById(pedido.id().valor())
                .orElseThrow(() -> new RecursoNaoEncontradoException("pedido", pedido.id().valor()));
        entidade.setStatus(pedido.status());
        return entidade;
    }
}
```

> `listarPorStatus` acima é deliberadamente ingênuo e não é usado por nenhum fluxo de leitura — ele existe apenas para completar o contrato da porta. Se você o expuser, ele vira N+1 na primeira chamada, e é exatamente o tipo de método que o teste da seção 9.5 pega.

```java
package br.com.empresa.pedidos.infrastructure.persistence;

import br.com.empresa.pedidos.domain.model.Pagina;
import br.com.empresa.pedidos.domain.model.Status;
import br.com.empresa.pedidos.domain.port.in.PedidoResumo;
import br.com.empresa.pedidos.domain.port.out.PedidoQuery;
import org.springframework.data.domain.PageRequest;
import org.springframework.stereotype.Repository;

@Repository
public class PedidoQueryAdapter implements PedidoQuery {

    private final PedidoJpaRepository jpa;

    public PedidoQueryAdapter(PedidoJpaRepository jpa) {
        this.jpa = jpa;
    }

    @Override
    public Pagina<PedidoResumo> resumirPorStatus(Status status, int pagina, int tamanho) {
        var conteudo = jpa.resumirPorStatus(status, PageRequest.of(pagina, tamanho));
        var total = jpa.countByStatus(status);
        return Pagina.de(conteudo, pagina, tamanho, total);
    }
}
```

### Aplicação e configuração

```java
package br.com.empresa.pedidos;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class PedidosApplication {

    public static void main(String[] args) {
        SpringApplication.run(PedidosApplication.class, args);
    }
}
```

```java
package br.com.empresa.pedidos.infrastructure.config;

import java.time.Clock;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class ClockConfig {

    @Bean
    Clock clock() {
        return Clock.systemUTC();
    }
}
```

---

## 9. Testes

### 9.1 Arquitetura (ArchUnit)

```java
package br.com.empresa.pedidos.architecture;

import static com.tngtech.archunit.lang.syntax.ArchRuleDefinition.classes;
import static com.tngtech.archunit.lang.syntax.ArchRuleDefinition.noClasses;
import static com.tngtech.archunit.lang.syntax.ArchRuleDefinition.noFields;

import com.tngtech.archunit.core.importer.ImportOption;
import com.tngtech.archunit.junit.AnalyzeClasses;
import com.tngtech.archunit.junit.ArchTest;
import com.tngtech.archunit.lang.ArchRule;

@AnalyzeClasses(
        packages = "br.com.empresa.pedidos",
        importOptions = ImportOption.DoNotIncludeTests.class)
class ArquiteturaTest {

    @ArchTest
    static final ArchRule dominioNaoConheceFramework = noClasses()
            .that()
            .resideInAPackage("..domain..")
            .should()
            .dependOnClassesThat()
            .resideInAnyPackage("org.springframework..", "jakarta.persistence..", "..infrastructure..")
            .because("o domínio deve permanecer independente de framework e de detalhe de persistência");

    @ArchTest
    static final ArchRule aplicacaoNaoConheceInfraestrutura = noClasses()
            .that()
            .resideInAPackage("..application..")
            .should()
            .dependOnClassesThat()
            .resideInAPackage("..infrastructure..")
            .because("casos de uso dependem de portas, nunca de adaptadores");

    @ArchTest
    static final ArchRule webNaoAcessaPersistencia = noClasses()
            .that()
            .resideInAPackage("..infrastructure.web..")
            .should()
            .dependOnClassesThat()
            .resideInAPackage("..infrastructure.persistence..");

    @ArchTest
    static final ArchRule webNaoUsaEntidadesJpa = noClasses()
            .that()
            .resideInAPackage("..infrastructure.web..")
            .should()
            .dependOnClassesThat()
            .areAnnotatedWith(jakarta.persistence.Entity.class)
            .because("entidade JPA não é contrato de API");

    @ArchTest
    static final ArchRule casosDeUsoImplementamPorta = classes()
            .that()
            .haveSimpleNameEndingWith("UseCase")
            .should()
            .implement(com.tngtech.archunit.base.DescribedPredicate.describe(
                            "uma porta de entrada de domínio",
                            javaClass -> javaClass.getPackageName().endsWith("domain.port.in"))
                    .<com.tngtech.archunit.core.domain.JavaClass>as("porta")
                    .and(com.tngtech.archunit.base.DescribedPredicate.alwaysTrue()));

    @ArchTest
    static final ArchRule semInjecaoPorCampo = noFields()
            .should()
            .beAnnotatedWith("org.springframework.beans.factory.annotation.Autowired");

    @ArchTest
    static final ArchRule portasNaoConhecemSpringData = noClasses()
            .that()
            .resideInAPackage("..domain.port..")
            .should()
            .dependOnClassesThat()
            .resideInAnyPackage("org.springframework.data..", "org.springframework.jdbc..")
            .because("a porta fala em domínio, não em Page, Pageable ou Specification");

    @ArchTest
    static final ArchRule semLombok = noClasses()
            .should()
            .dependOnClassesThat()
            .resideInAPackage("lombok..");
}
```

> A regra `casosDeUsoImplementamPorta` acima ficou verbosa porque `implement(...)` espera predicado sobre `JavaClass`. A forma curta e legível é separar em duas regras: `classes().that().haveSimpleNameEndingWith("UseCase").should().implement(resideInAPackage("..domain.port.in.."))` não compila por tipos distintos — use `ArchRuleDefinition.classes().that(...).should().beAssignableTo(...)` ou mantenha a versão acima, que funciona com ArchUnit 1.4.

### 9.2 Domínio

```java
package br.com.empresa.pedidos.domain.model;

import static org.assertj.core.api.Assertions.assertThat;
import static org.assertj.core.api.Assertions.assertThatThrownBy;

import br.com.empresa.pedidos.domain.exception.RegraNegocioException;
import br.com.empresa.pedidos.support.PedidoFixture;
import java.time.Instant;
import org.junit.jupiter.api.Test;

class PedidoTest {

    private static final Instant AGORA = Instant.parse("2026-01-15T10:00:00Z");

    @Test
    void deveCalcularTotalSomandoSubtotaisDosItens() {
        var pedido = Pedido.criar(
                1L,
                java.util.List.of(
                        new ItemPedido("SKU-1", 2, Dinheiro.de("19.90")),
                        new ItemPedido("SKU-2", 1, Dinheiro.de("100.00"))),
                AGORA);

        assertThat(pedido.total().valor()).isEqualByComparingTo("139.80");
        assertThat(pedido.status()).isEqualTo(Status.PENDENTE);
        assertThat(pedido.id()).isNull();
    }

    @Test
    void deveRecusarPedidoSemItens() {
        assertThatThrownBy(() -> Pedido.criar(1L, java.util.List.of(), AGORA))
                .isInstanceOf(RegraNegocioException.class)
                .extracting("codigo")
                .isEqualTo("PEDIDO_SEM_ITENS");
    }

    @Test
    void naoDeveAprovarPedidoCancelado() {
        var cancelado = PedidoFixture.pendente().cancelar().comId(new PedidoId(10L));

        assertThatThrownBy(cancelado::aprovar)
                .isInstanceOf(RegraNegocioException.class)
                .extracting("codigo")
                .isEqualTo("PEDIDO_STATUS_INVALIDO");
    }

    @Test
    void naoDeveAprovarPedidoJaAprovado() {
        var aprovado = PedidoFixture.pendente().aprovar();

        assertThatThrownBy(aprovado::aprovar)
                .isInstanceOf(RegraNegocioException.class)
                .extracting("codigo")
                .isEqualTo("PEDIDO_STATUS_INVALIDO");
    }

    @Test
    void deveAprovarPedidoPendenteComTotalPositivo() {
        var aprovado = PedidoFixture.pendente().aprovar();

        assertThat(aprovado.status()).isEqualTo(Status.APROVADO);
    }
}
```

```java
package br.com.empresa.pedidos.domain.model;

import static org.assertj.core.api.Assertions.assertThat;

import org.junit.jupiter.api.Test;

class DinheiroTest {

    @Test
    void deveNormalizarParaDuasCasasComArredondamentoHalfEven() {
        assertThat(Dinheiro.de("10.005").valor()).isEqualByComparingTo("10.00");
        assertThat(Dinheiro.de("10.015").valor()).isEqualByComparingTo("10.02");
    }

    @Test
    void deveSomarVariasVezesSemDerivaDePontoFlutuante() {
        var total = java.util.stream.IntStream.range(0, 10)
                .mapToObj(i -> Dinheiro.de("0.10"))
                .reduce(Dinheiro.zero(), Dinheiro::somar);

        assertThat(total.valor()).isEqualByComparingTo("1.00");
    }
}
```

### 9.3 Casos de uso

```java
package br.com.empresa.pedidos.application.usecase;

import static org.assertj.core.api.Assertions.assertThat;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.BDDMockito.given;
import static org.mockito.BDDMockito.then;

import br.com.empresa.pedidos.domain.exception.RecursoNaoEncontradoException;
import br.com.empresa.pedidos.domain.model.PedidoId;
import br.com.empresa.pedidos.domain.model.Status;
import br.com.empresa.pedidos.domain.port.out.PedidoRepository;
import br.com.empresa.pedidos.support.PedidoFixture;
import java.util.Optional;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

@ExtendWith(MockitoExtension.class)
class AprovarPedidoUseCaseTest {

    @Mock PedidoRepository pedidoRepository;

    @InjectMocks AprovarPedidoUseCase useCase;

    @Test
    void deveAprovarPedidoPendenteEPersistir() {
        // given
        var pedido = PedidoFixture.pendenteComId(42L);
        given(pedidoRepository.porId(new PedidoId(42L))).willReturn(Optional.of(pedido));
        given(pedidoRepository.salvar(any())).willAnswer(invocacao -> invocacao.getArgument(0));

        // when
        var output = useCase.executar(new PedidoId(42L));

        // then
        assertThat(output.status()).isEqualTo(Status.APROVADO.name());
        then(pedidoRepository).should().salvar(any());
    }

    @Test
    void deveLancarRecursoNaoEncontradoQuandoPedidoNaoExiste() {
        given(pedidoRepository.porId(new PedidoId(99L))).willReturn(Optional.empty());

        org.assertj.core.api.Assertions.assertThatThrownBy(() -> useCase.executar(new PedidoId(99L)))
                .isInstanceOf(RecursoNaoEncontradoException.class);
    }
}
```

```java
package br.com.empresa.pedidos.application.usecase;

import static org.assertj.core.api.Assertions.assertThat;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.BDDMockito.given;

import br.com.empresa.pedidos.domain.model.Status;
import br.com.empresa.pedidos.domain.port.in.CriarPedidoInput;
import br.com.empresa.pedidos.domain.port.in.ItemInput;
import br.com.empresa.pedidos.domain.port.out.PedidoRepository;
import br.com.empresa.pedidos.support.PedidoFixture;
import java.math.BigDecimal;
import java.time.Clock;
import java.time.Instant;
import java.time.ZoneOffset;
import java.util.List;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

@ExtendWith(MockitoExtension.class)
class CriarPedidoUseCaseTest {

    private static final Clock RELOGIO_FIXO =
            Clock.fixed(Instant.parse("2026-01-15T10:00:00Z"), ZoneOffset.UTC);

    @Mock PedidoRepository pedidoRepository;

    @Test
    void deveCriarPedidoComStatusPendenteEIdAtribuido() {
        // given
        var useCase = new CriarPedidoUseCase(pedidoRepository, RELOGIO_FIXO);
        var input = new CriarPedidoInput(
                7L, List.of(new ItemInput("SKU-1", 2, new BigDecimal("50.00"))));
        given(pedidoRepository.salvar(any())).willReturn(PedidoFixture.pendenteComId(1L));

        // when
        var output = useCase.executar(input);

        // then
        assertThat(output.id()).isEqualTo(1L);
        assertThat(output.status()).isEqualTo(Status.PENDENTE.name());
    }
}
```

### 9.4 Controller (fatia web)

```java
package br.com.empresa.pedidos.infrastructure.web;

import static org.mockito.ArgumentMatchers.any;
import static org.mockito.BDDMockito.given;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.header;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

import br.com.empresa.pedidos.domain.exception.RegraNegocioException;
import br.com.empresa.pedidos.domain.model.Pagina;
import br.com.empresa.pedidos.domain.model.PedidoId;
import br.com.empresa.pedidos.domain.port.in.AprovarPedido;
import br.com.empresa.pedidos.domain.port.in.ConsultarPedidosPorStatus;
import br.com.empresa.pedidos.domain.port.in.CriarPedido;
import br.com.empresa.pedidos.domain.port.in.PedidoOutput;
import br.com.empresa.pedidos.domain.port.in.PedidoResumo;
import com.jayway.jsonpath.JsonPath;
import java.math.BigDecimal;
import java.time.Instant;
import java.util.List;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.http.MediaType;
import org.springframework.test.context.bean.override.mockito.MockitoBean;
import org.springframework.test.web.servlet.MockMvc;

@WebMvcTest(PedidoController.class)
class PedidoControllerTest {

    @Autowired MockMvc mvc;

    @MockitoBean CriarPedido criarPedido;
    @MockitoBean AprovarPedido aprovarPedido;
    @MockitoBean ConsultarPedidosPorStatus consultarPedidos;

    @Test
    void deveRetornar201ComLocationAoCriarPedido() throws Exception {
        given(criarPedido.executar(any()))
                .willReturn(new PedidoOutput(42L, 7L, "PENDENTE", new BigDecimal("100.00"), Instant.now()));

        mvc.perform(post("/v1/pedidos")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content("""
                                {"clienteId": 7, "itens": [{"sku": "SKU-1", "quantidade": 2, "precoUnitario": 50.00}]}
                                """))
                .andExpect(status().isCreated())
                .andExpect(header().string("Location", "http://localhost/v1/pedidos/42"))
                .andExpect(jsonPath("$.status").value("PENDENTE"));
    }

    @Test
    void deveRetornar400ComCamposQuandoPayloadInvalido() throws Exception {
        mvc.perform(post("/v1/pedidos")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content("{\"clienteId\": null, \"itens\": []}"))
                .andExpect(status().isBadRequest())
                .andExpect(jsonPath("$.codigo").value("VALIDACAO"))
                .andExpect(jsonPath("$.campos").isArray());
    }

    @Test
    void deveRetornar422ComCodigoQuandoRegraDeNegocioViolada() throws Exception {
        given(aprovarPedido.executar(new PedidoId(42L)))
                .willThrow(new RegraNegocioException("PEDIDO_STATUS_INVALIDO", "Pedido já está aprovado"));

        mvc.perform(post("/v1/pedidos/42/aprovacao"))
                .andExpect(status().isUnprocessableEntity())
                .andExpect(jsonPath("$.codigo").value("PEDIDO_STATUS_INVALIDO"));
    }

    @Test
    void deveListarComContratoDePaginacao() throws Exception {
        given(consultarPedidos.executar(any(), any(Integer.class), any(Integer.class)))
                .willReturn(Pagina.de(
                        List.of(new PedidoResumo(1L, "Ana", Instant.now(), new BigDecimal("100.00"))),
                        0, 20, 1));

        var resposta = mvc.perform(get("/v1/pedidos").param("status", "PENDENTE"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.conteudo").isArray())
                .andExpect(jsonPath("$.total").value(1))
                .andReturn();

        var corpo = JsonPath.read(resposta.getResponse().getContentAsString(), "$.conteudo[0].clienteNome");
        org.assertj.core.api.Assertions.assertThat(corpo).isEqualTo("Ana");
    }
}
```

### 9.5 Contrato de porta por herança e guardrails de persistência

```java
package br.com.empresa.pedidos.infrastructure.persistence;

import static org.assertj.core.api.Assertions.assertThat;

import br.com.empresa.pedidos.domain.model.PedidoId;
import br.com.empresa.pedidos.domain.model.Status;
import br.com.empresa.pedidos.domain.port.out.PedidoRepository;
import br.com.empresa.pedidos.support.PedidoFixture;
import org.junit.jupiter.api.Test;

/**
 * Contrato único testado por todas as implementações.
 * Se uma implementação divergir semanticamente, o teste quebra aqui.
 */
abstract class PedidoRepositoryContract {

    protected abstract PedidoRepository repositorio();

    @Test
    void deveRetornarVazioQuandoPedidoNaoExiste() {
        assertThat(repositorio().porId(new PedidoId(999_999L))).isEmpty();
    }

    @Test
    void devePersistirEDevolverComMesmoConteudo() {
        var original = PedidoFixture.pendente();

        var salvo = repositorio().salvar(original);
        var recuperado = repositorio().porId(salvo.id()).orElseThrow();

        assertThat(salvo.id()).isNotNull();
        assertThat(recuperado.clienteId()).isEqualTo(original.clienteId());
        assertThat(recuperado.itens()).hasSameSizeAs(original.itens());
        assertThat(recuperado.total()).isEqualTo(original.total());
    }

    @Test
    void deveAtualizarStatusSemDuplicarItens() {
        var salvo = repositorio().salvar(PedidoFixture.pendente());

        var atualizado = repositorio().salvar(salvo.aprovar());

        assertThat(atualizado.status()).isEqualTo(Status.APROVADO);
        assertThat(atualizado.itens()).hasSameSizeAs(salvo.itens());
    }
}
```

```java
package br.com.empresa.pedidos.infrastructure.persistence;

import static org.assertj.core.api.Assertions.assertThat;

import br.com.empresa.pedidos.domain.model.Pedido;
import br.com.empresa.pedidos.domain.model.PedidoId;
import br.com.empresa.pedidos.domain.model.Status;
import br.com.empresa.pedidos.domain.port.out.PedidoRepository;
import java.util.LinkedHashMap;
import java.util.List;
import java.util.Map;
import java.util.Optional;
import java.util.concurrent.atomic.AtomicLong;

/** Fake com semântica idêntica ao contrato — usado em testes unitários rápidos. */
class InMemoryPedidoRepository implements PedidoRepository {

    private final Map<Long, Pedido> dados = new LinkedHashMap<>();
    private final AtomicLong sequencia = new AtomicLong(1);

    @Override
    public Pedido salvar(Pedido pedido) {
        var id = pedido.id() == null ? new PedidoId(sequencia.getAndIncrement()) : pedido.id();
        var persistido = pedido.comId(id);
        dados.put(id.valor(), persistido);
        return persistido;
    }

    @Override
    public Optional<Pedido> porId(PedidoId id) {
        return Optional.ofNullable(dados.get(id.valor()));
    }

    @Override
    public List<Pedido> listarPorStatus(Status status) {
        return dados.values().stream().filter(p -> p.status() == status).toList();
    }
}
```

```java
package br.com.empresa.pedidos.infrastructure.persistence;

class InMemoryPedidoRepositoryTest extends PedidoRepositoryContract {

    private final InMemoryPedidoRepository repositorio = new InMemoryPedidoRepository();

    @Override
    protected PedidoRepository repositorio() {
        return repositorio;
    }
}
```

```java
package br.com.empresa.pedidos.infrastructure.persistence;

import static org.assertj.core.api.Assertions.assertThat;

import br.com.empresa.pedidos.domain.model.Pedido;
import br.com.empresa.pedidos.domain.model.PedidoId;
import br.com.empresa.pedidos.domain.port.out.PedidoRepository;
import br.com.empresa.pedidos.support.PedidoFixture;
import java.util.Optional;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.boot.test.autoconfigure.orm.jpa.TestEntityManager;
import org.springframework.boot.testcontainers.service.connection.ServiceConnection;
import org.springframework.context.annotation.Import;
import org.springframework.jdbc.core.JdbcTemplate;
import org.testcontainers.containers.PostgreSQLContainer;
import org.testcontainers.junit.jupiter.Container;
import org.testcontainers.junit.jupiter.Testcontainers;

@DataJpaTest
@Testcontainers
@Import(PedidoPersistenceAdapter.class)
class JpaPedidoRepositoryTest extends PedidoRepositoryContract {

    @Container
    @ServiceConnection
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:17-alpine");

    @Autowired PedidoPersistenceAdapter adapter;
    @Autowired TestEntityManager em;
    @Autowired JdbcTemplate jdbc;

    @BeforeEach
    void prepararCliente() {
        jdbc.update("insert into cliente (id, nome) values (?, ?) on conflict do nothing", 1000L, "Ana");
    }

    @Override
    protected PedidoRepository repositorio() {
        return adapter;
    }

    @Test
    void deveCarregarItensEmUmaUnicaQuery() {
        var stats = em.getEntityManager()
                .getEntityManagerFactory()
                .unwrap(org.hibernate.SessionFactory.class)
                .getStatistics();
        stats.setStatisticsEnabled(true);
        stats.clear();

        var salvo = adapter.salvar(PedidoFixture.pendente());
        em.flush();
        em.clear();
        stats.clear();

        Optional<Pedido> recuperado = adapter.porId(salvo.id());

        assertThat(recuperado).isPresent();
        assertThat(stats.getPrepareStatementCount()).isLessThanOrEqualTo(2);
    }
}
```

```java
package br.com.empresa.pedidos.infrastructure.persistence;

import static org.assertj.core.api.Assertions.assertThat;

import br.com.empresa.pedidos.domain.model.Status;
import br.com.empresa.pedidos.domain.port.out.PedidoQuery;
import java.util.ArrayList;
import java.util.List;
import org.hibernate.SessionFactory;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.testcontainers.service.connection.ServiceConnection;
import org.springframework.jdbc.core.JdbcTemplate;
import org.testcontainers.containers.PostgreSQLContainer;
import org.testcontainers.junit.jupiter.Container;
import org.testcontainers.junit.jupiter.Testcontainers;

/** Guardrail: qualquer regressão de N+1 quebra este teste antes do deploy. */
@SpringBootTest
@Testcontainers
class SemN1Test {

    private static final int QUANTIDADE_PEDIDOS = 25;

    @Container
    @ServiceConnection
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:17-alpine");

    @Autowired PedidoQuery pedidoQuery;
    @Autowired JdbcTemplate jdbc;
    @Autowired SessionFactory sessionFactory;

    @BeforeEach
    void prepararDados() {
        jdbc.update("insert into cliente (id, nome) values (?, ?) on conflict do nothing", 2000L, "Ana");
        jdbc.update("delete from item_pedido where pedido_id between 900000 and 999999");
        jdbc.update("delete from pedido where id between 900000 and 999999");

        var itens = new ArrayList<Object[]>();
        for (int i = 0; i < QUANTIDADE_PEDIDOS; i++) {
            long pedidoId = 900000L + i;
            jdbc.update(
                    "insert into pedido (id, cliente_id, status, criado_em) values (?, ?, ?, now())",
                    pedidoId, 2000L, Status.PENDENTE.name());
            itens.add(new Object[] {pedidoId + 500000L, pedidoId, "SKU-" + i, 1, 10.00});
        }
        jdbc.batchUpdate(
                "insert into item_pedido (id, pedido_id, sku, quantidade, preco_unitario) values (?, ?, ?, ?, ?)",
                itens);

        sessionFactory.getStatistics().setStatisticsEnabled(true);
        sessionFactory.getStatistics().clear();
    }

    @Test
    void deveListarResumosComNoMaximoDuasQueries() {
        var pagina = pedidoQuery.resumirPorStatus(Status.PENDENTE, 0, 20);

        assertThat(pagina.conteudo()).isNotEmpty();
        assertThat(sessionFactory.getStatistics().getPrepareStatementCount())
                .as("listagem não pode gerar uma query por pedido")
                .isLessThanOrEqualTo(2);
    }
}
```

```java
package br.com.empresa.pedidos.infrastructure.persistence;

import static org.assertj.core.api.Assertions.assertThat;

import jakarta.persistence.EntityManagerFactory;
import jakarta.persistence.FetchType;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.OneToOne;
import jakarta.persistence.metamodel.EntityType;
import java.lang.reflect.Field;
import java.util.ArrayList;
import java.util.List;
import java.util.Optional;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.testcontainers.service.connection.ServiceConnection;
import org.testcontainers.containers.PostgreSQLContainer;
import org.testcontainers.junit.jupiter.Container;
import org.testcontainers.junit.jupiter.Testcontainers;

/** Guardrail: nenhuma associação ToOne pode ser EAGER por acidente. */
@SpringBootTest
@Testcontainers
class FetchConfigurationTest {

    @Container
    @ServiceConnection
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:17-alpine");

    @Autowired EntityManagerFactory emf;

    @Test
    void associacoesToOneDevemSerLazy() {
        List<String> violacoes = new ArrayList<>();

        for (EntityType<?> entidade : emf.getMetamodel().getEntities()) {
            for (Field campo : entidade.getJavaType().getDeclaredFields()) {
                var fetch = fetchDeclarado(campo);
                if (fetch.isPresent() && fetch.get() != FetchType.LAZY) {
                    violacoes.add(entidade.getName() + "." + campo.getName());
                }
            }
        }

        assertThat(violacoes)
                .as("associações ToOne devem declarar fetch = LAZY explicitamente")
                .isEmpty();
    }

    private Optional<FetchType> fetchDeclarado(Field campo) {
        if (campo.isAnnotationPresent(ManyToOne.class)) {
            return Optional.of(campo.getAnnotation(ManyToOne.class).fetch());
        }
        if (campo.isAnnotationPresent(OneToOne.class)) {
            return Optional.of(campo.getAnnotation(OneToOne.class).fetch());
        }
        return Optional.empty();
    }
}
```

### 9.6 Fixture compartilhada

```java
package br.com.empresa.pedidos.support;

import br.com.empresa.pedidos.domain.model.Dinheiro;
import br.com.empresa.pedidos.domain.model.ItemPedido;
import br.com.empresa.pedidos.domain.model.Pedido;
import br.com.empresa.pedidos.domain.model.PedidoId;
import java.time.Instant;
import java.util.List;

public final class PedidoFixture {

    public static final Long CLIENTE_ID = 1000L;

    private PedidoFixture() {}

    public static Pedido pendente() {
        return Pedido.criar(
                CLIENTE_ID,
                List.of(
                        new ItemPedido("SKU-1", 2, Dinheiro.de("19.90")),
                        new ItemPedido("SKU-2", 1, Dinheiro.de("100.00"))),
                Instant.parse("2026-01-15T10:00:00Z"));
    }

    public static Pedido pendenteComId(Long id) {
        return pendente().comId(new PedidoId(id));
    }
}
```

---

## 10. Como executar

```bash
# formatação (primeira execução)
mvn spotless:apply

# build completo: Spotless → testes → Checkstyle → SpotBugs → JaCoCo
mvn verify

# rodar a aplicação (precisa de PostgreSQL)
docker run -d --name pedidos-db -p 5432:5432 \
  -e POSTGRES_DB=pedidos -e POSTGRES_USER=app -e POSTGRES_PASSWORD=app postgres:17-alpine

SPRING_DATASOURCE_URL=jdbc:postgresql://localhost:5432/pedidos \
SPRING_DATASOURCE_USERNAME=app \
SPRING_DATASOURCE_PASSWORD=app \
mvn spring-boot:run

# mutação apenas no núcleo de domínio
mvn pitest:mutationCoverage
```

---

## 11. Decisões e desvios declarados

O projeto não aplica as regras de forma dogmática. Cada desvio tem justificativa registrada:

**`application` conhece Spring.** Os casos de uso usam `@Service` e `@Transactional`. A alternativa exigiria uma camada de proxy de transação própria — custo alto, ganho nulo. A regra que se mantém rígida é: **`application` não conhece `infrastructure`**, e isso está no ArchUnit.

**`domain` conhece `jakarta.validation`.** Os records de entrada carregam `@NotNull`, `@Size`. É a única dependência de framework tolerada no domínio, porque anotação de validação é metadado declarativo sobre o dado, não comportamento de infraestrutura.

**A porta expõe o DTO.** `CriarPedidoInput` e `PedidoOutput` moram em `domain.port.in`, não em uma camada `dto`. Isso elimina o triângulo de cerimônia discutido na Parte II. O preço é que o contrato de entrada carrega anotações de validação — o benefício é um único record em vez de três.

**`listarPorStatus` existe mas não é usado.** Mantive no contrato da porta porque ele demonstra, de forma concreta, o método que vira N+1. O comentário no adapter avisa. Se você preferir não carregar a tentação, remova-o da porta: menos código é mais Lean.

**`Pagina` é record próprio, não `Page`.** O adapter converte `List` + `count` em `Pagina`. Essa é a prova executável da regra "porta não fala em Spring Data" — e o ArchUnit falha se alguém importar `org.springframework.data` em `domain.port`.

**Sem Lombok, e o ArchUnit fiscaliza.** A regra `semLombok` impede a reintrodução. Se algum módulo legado precisar, adicione o Lombok **fora** de `domain` e ajuste a regra com escopo explícito — não remova a regra.

---

## Resumindo

- **O projeto entrega os dois itens pedidos de forma acoplada:** o `pom.xml` só faz sentido porque cada plugin tem um teste que o justifica, e cada teste só é legível porque o design segue as camadas.
- **Cinco guardrails executáveis** são o núcleo: ArchUnit (fronteira), teste de contrato por herança (LSP em adapter), contagem de queries (N+1), varredura de fetch (EAGER acidental) e teste de fatia web (controller sem acesso a dados).
- **`@WebMvcTest` + `@MockitoBean` + `PostgreSQLContainer`** substituem o `@SpringBootTest` genérico; se o slice não sobe, o acoplamento é o achado, não a configuração.
- **Três desvios são explícitos e justificados** — Spring em `application`, `jakarta.validation` em `domain` e DTO na porta. Nenhum é omissão; todos estão documentados no código.
- **Dependências de versão confirmadas para o Spring Boot 4:** `webmvc`, `webmvc-test`, `data-jpa-test`, `starter-flyway` e `@MockitoBean`.
- **A ordem de valor é: rode `mvn verify` antes de escrever a primeira feature.** O pipeline funcionando é o que impede o projeto de degradar.

Se quiser, posso:
- Adicionar a camada de eventos de domínio com outbox transacional para tirar os efeitos colaterais do caso de uso de pagamento.
- Incluir `Testcontainers` com WireMock para o guardrail de timeout e circuit breaker em cliente externo.
- Escrever o `Dockerfile` multi-stage com `layertools`, usuário não-root e `MaxRAMPercentage`, mais o `compose.yaml` de desenvolvimento.

Os três itens se conectam como uma única cadeia de confiabilidade: o outbox tira os efeitos colaterais do caso de uso, o gateway com timeout e circuit breaker torna cada efeito sobrevivível a falha externa, e a imagem de container com shutdown gracioso garante que nada disso seja perdido em deploy.

---

# Parte V — Eventos, resiliência e operação

**Base:** Spring Boot 4.1 · Java 25 · PostgreSQL 17

---

## 1. Eventos de domínio com outbox transacional

### 1.1 O problema que isso resolve

Na Parte III eu usei um `ProcessarPagamentoUseCase` com oito dependências como exemplo de classe-deus. O ponto era que nota fiscal, estoque, e-mail e auditoria **não pertencem ao fluxo de pagamento** — eles reagem a ele.

Só que remover as dependências não basta. Se você trocar oito serviços por `ApplicationEventPublisher`, você troca um problema por um pior:

```java
@Transactional
public void executar(...) {
    pedidoRepository.salvar(pedido);
    publisher.publishEvent(new PagamentoConfirmado(pedido.id()));  // ← sai antes do commit
}
```

Se a transação falhar depois do `publishEvent`, o listener já emitiu a nota fiscal. Você tem nota fiscal de um pagamento que não existe. E o inverso também dói: se o listener lançar exceção, o rollback desfaz o pagamento, mas o e-mail já foi enviado.

**O outbox resolve os dois lados:** o evento é gravado na **mesma transação** que o dado de negócio. Se a transação comita, o evento existe. Se der rollback, o evento desaparece junto. A publicação real acontece depois, por um processo separado que pode falhar, repetir e atrasar sem afetar a transação original.

### 1.2 O que muda no projeto

| Antes | Depois |
|---|---|
| `AprovarPedidoUseCase` chama 4 serviços | Chama 2 (repositório + outbox) |
| Efeito colateral fora da transação | Efeito registrado na transação |
| Falha de terceiro derruba o fluxo principal | Falha de terceiro vira retry assíncrono |
| Sem rastro do que foi emitido | Tabela auditável com status por evento |
| Teste com 8 mocks | Teste com 2 mocks + teste próprio do relay |

### 1.3 Migration `V2__outbox.sql`

```sql
create sequence outbox_evento_seq start with 1 increment by 50;

create table outbox_evento (
    id            bigint       primary key,
    tipo          varchar(80)  not null,
    agregado_id   varchar(80)  not null,
    payload       text         not null,
    status        varchar(20)  not null,
    criado_em     timestamptz  not null,
    disponivel_em timestamptz  not null,
    tentativas    integer      not null default 0,
    ultimo_erro   varchar(500)
);

-- Índice parcial: só as linhas que o relay procura
create index idx_outbox_pendente
    on outbox_evento (disponivel_em, id)
    where status = 'PENDENTE';

create index idx_outbox_agregado on outbox_evento (agregado_id);
```

O índice parcial é o detalhe que importa. Uma tabela de outbox em produção normal acumula milhões de linhas `PROCESSADO`; sem o `where`, o relay faz varredura cada vez mais lenta. Com ele, o índice contém apenas a fila ativa.

### 1.4 Eventos de domínio

```java
package br.com.empresa.pedidos.domain.event;

import br.com.empresa.pedidos.domain.model.PedidoId;
import java.time.Instant;

public sealed interface PedidoEvento {

    PedidoId pedidoId();

    Instant ocorridoEm();

    String tipo();

    record Aprovado(PedidoId pedidoId, Instant ocorridoEm) implements PedidoEvento {
        @Override
        public String tipo() {
            return "pedido.aprovado";
        }
    }

    record Cancelado(PedidoId pedidoId, String motivo, Instant ocorridoEm) implements PedidoEvento {
        @Override
        public String tipo() {
            return "pedido.cancelado";
        }
    }

    record PagamentoConfirmado(PedidoId pedidoId, String autorizacaoId, Instant ocorridoEm)
            implements PedidoEvento {
        @Override
        public String tipo() {
            return "pagamento.confirmado";
        }
    }
}
```

`sealed` aqui não é decoração: garante que a lista de eventos é fechada e conhecida. Um handler novo exige um `record` novo, e o compilador aponta todo `switch` que precisa tratá-lo — o mesmo OCP barato da Parte II.

### 1.5 A porta de escrita (domínio)

```java
package br.com.empresa.pedidos.domain.port.out;

import br.com.empresa.pedidos.domain.event.PedidoEvento;

/**
 * Registra que um evento de domínio ocorreu.
 * Contrato: quando este método retorna, o evento está durável e será publicado
 * mesmo que o processo falhe imediatamente depois.
 */
public interface OutboxRecorder {

    void registrar(PedidoEvento evento);
}
```

Continua em `domain/port/out` porque a afirmação é de domínio: "isto aconteceu". Onde isso é gravado é detalhe de infraestrutura.

### 1.6 Entidade e repositório do outbox

```java
package br.com.empresa.pedidos.infrastructure.persistence.outbox;

public enum StatusOutbox {
    PENDENTE,
    PROCESSADO,
    FALHA
}
```

```java
package br.com.empresa.pedidos.infrastructure.persistence.outbox;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.SequenceGenerator;
import jakarta.persistence.Table;
import java.time.Instant;

@Entity
@Table(name = "outbox_evento")
public class OutboxEventoJpa {

    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "outbox_evento_seq")
    @SequenceGenerator(
            name = "outbox_evento_seq",
            sequenceName = "outbox_evento_seq",
            allocationSize = 50)
    private Long id;

    @Column(nullable = false, length = 80)
    private String tipo;

    @Column(name = "agregado_id", nullable = false, length = 80)
    private String agregadoId;

    @Column(nullable = false)
    private String payload;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false, length = 20)
    private StatusOutbox status;

    @Column(name = "criado_em", nullable = false)
    private Instant criadoEm;

    @Column(name = "disponivel_em", nullable = false)
    private Instant disponivelEm;

    @Column(nullable = false)
    private int tentativas;

    @Column(name = "ultimo_erro", length = 500)
    private String ultimoErro;

    protected OutboxEventoJpa() {}

    public OutboxEventoJpa(
            String tipo, String agregadoId, String payload, Instant criadoEm) {
        this.tipo = tipo;
        this.agregadoId = agregadoId;
        this.payload = payload;
        this.status = StatusOutbox.PENDENTE;
        this.criadoEm = criadoEm;
        this.disponivelEm = criadoEm;
        this.tentativas = 0;
    }

    public void marcarProcessado() {
        this.status = StatusOutbox.PROCESSADO;
        this.ultimoErro = null;
    }

    /** Backoff exponencial: 10s, 20s, 40s, 80s. Depois de maxTentativas, vira FALHA. */
    public void registrarFalha(String erro, Instant agora, int maxTentativas) {
        this.tentativas++;
        this.ultimoErro = truncar(erro);
        if (this.tentativas >= maxTentativas) {
            this.status = StatusOutbox.FALHA;
        } else {
            long esperaSegundos = (long) Math.pow(2, this.tentativas) * 5L;
            this.disponivelEm = agora.plusSeconds(esperaSegundos);
        }
    }

    private String truncar(String erro) {
        if (erro == null) {
            return null;
        }
        return erro.length() <= 500 ? erro : erro.substring(0, 500);
    }

    public Long getId() {
        return id;
    }

    public String getTipo() {
        return tipo;
    }

    public StatusOutbox getStatus() {
        return status;
    }

    public String getPayload() {
        return payload;
    }

    public int getTentativas() {
        return tentativas;
    }
}
```

```java
package br.com.empresa.pedidos.infrastructure.persistence.outbox;

import jakarta.persistence.LockModeType;
import java.time.Instant;
import java.util.List;
import java.util.Optional;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Lock;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;

interface OutboxJpaRepository extends JpaRepository<OutboxEventoJpa, Long> {

    @Query(
            value =
                    """
                    select id from outbox_evento
                     where status = 'PENDENTE' and disponivel_em <= :agora
                     order by id
                     limit :tamanho
                    """,
            nativeQuery = true)
    List<Long> idsPendentes(@Param("agora") Instant agora, @Param("tamanho") int tamanho);

    /**
     * Trava a linha para escrita: dois nós do relay nunca processam o mesmo evento.
     * O SELECT ... FOR UPDATE é liberado no commit da transação do processor.
     */
    @Lock(LockModeType.PESSIMISTIC_WRITE)
    @Query("select o from OutboxEventoJpa o where o.id = :id")
    Optional<OutboxEventoJpa> travarPorId(@Param("id") Long id);
}
```

### 1.7 Adapter do recorder e store do relay

```java
package br.com.empresa.pedidos.infrastructure.persistence.outbox;

import br.com.empresa.pedidos.domain.event.PedidoEvento;
import br.com.empresa.pedidos.domain.port.out.OutboxRecorder;
import java.time.Clock;
import org.springframework.stereotype.Repository;

@Repository
public class OutboxRecorderAdapter implements OutboxRecorder {

    private final OutboxJpaRepository jpa;
    private final EventoJson json;
    private final Clock clock;

    public OutboxRecorderAdapter(OutboxJpaRepository jpa, EventoJson json, Clock clock) {
        this.jpa = jpa;
        this.json = json;
        this.clock = clock;
    }

    @Override
    public void registrar(PedidoEvento evento) {
        jpa.save(
                new OutboxEventoJpa(
                        evento.tipo(),
                        String.valueOf(evento.pedidoId().valor()),
                        json.serializar(evento),
                        clock.instant()));
    }
}
```

```java
package br.com.empresa.pedidos.application.outbox;

import java.time.Instant;
import java.util.List;

/**
 * Porta de leitura/escrita do relay. Separada de {@code OutboxRecorder} por ISP:
 * o caso de uso só escreve, o relay só lê e marca.
 */
public interface OutboxStore {

    List<Long> idsPendentes(Instant agora, int tamanho);

    void processar(Long id);

    void falhar(Long id, String erro);
}
```

### 1.8 O dispatcher e os handlers

```java
package br.com.empresa.pedidos.application.outbox;

/**
 * @param id chave de idempotência do consumidor — o mesmo evento nunca deve
 *           produzir dois efeitos, mesmo com reentrega.
 */
public record EventoOutbox(long id, String tipo, String payload) {}
```

```java
package br.com.empresa.pedidos.application.outbox;

public interface EventoHandler {

    String tipo();

    void tratar(EventoOutbox evento);
}
```

```java
package br.com.empresa.pedidos.application.outbox;

import java.util.List;
import java.util.Map;
import java.util.function.Function;
import java.util.stream.Collectors;
import org.springframework.stereotype.Component;

@Component
public class EventoDispatcher {

    private final Map<String, EventoHandler> handlers;

    public EventoDispatcher(List<EventoHandler> handlers) {
        this.handlers =
                handlers.stream()
                        .collect(Collectors.toMap(EventoHandler::tipo, Function.identity()));
    }

    public void despachar(EventoOutbox evento) {
        var handler = handlers.get(evento.tipo());
        if (handler == null) {
            throw new IllegalStateException("nenhum handler registrado para " + evento.tipo());
        }
        handler.tratar(evento);
    }
}
```

O Spring injeta a lista automaticamente. Novo tipo de efeito = novo `@Component` que implementa `EventoHandler`. Zero alteração no dispatcher — Strategy com registro automático, como na Parte II.

Exemplo de handler que usa o gateway resiliente do item 2:

```java
package br.com.empresa.pedidos.application.outbox.handler;

import br.com.empresa.pedidos.application.outbox.EventoHandler;
import br.com.empresa.pedidos.application.outbox.EventoOutbox;
import br.com.empresa.pedidos.domain.event.PedidoEvento;
import br.com.empresa.pedidos.domain.model.PedidoId;
import br.com.empresa.pedidos.domain.port.out.NotaFiscalGateway;
import br.com.empresa.pedidos.infrastructure.persistence.outbox.EventoJson;
import org.springframework.stereotype.Component;

@Component
public class EmitirNotaFiscalHandler implements EventoHandler {

    private final NotaFiscalGateway notaFiscal;
    private final EventoJson json;

    public EmitirNotaFiscalHandler(NotaFiscalGateway notaFiscal, EventoJson json) {
        this.notaFiscal = notaFiscal;
        this.json = json;
    }

    @Override
    public String tipo() {
        return "pedido.aprovado";
    }

    @Override
    public void tratar(EventoOutbox evento) {
        var aprovado = json.ler(evento.payload(), PedidoEvento.Aprovado.class);
        notaFiscal.emitir(new PedidoId(aprovado.pedidoId().valor()), evento.id());
    }
}
```

### 1.9 O relay

```java
package br.com.empresa.pedidos.application.outbox;

import java.time.Clock;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

@Component
public class OutboxRelay {

    private static final Logger log = LoggerFactory.getLogger(OutboxRelay.class);

    private final OutboxStore store;
    private final OutboxProperties propriedades;
    private final Clock clock;

    public OutboxRelay(OutboxStore store, OutboxProperties propriedades, Clock clock) {
        this.store = store;
        this.propriedades = propriedades;
        this.clock = clock;
    }

    /**
     * Não é @Transactional de propósito: cada evento é processado na sua própria
     * transação, para que uma falha não desfaça o trabalho dos anteriores.
     */
    @Scheduled(fixedDelayString = "${app.outbox.intervalo-ms:2000}")
    public void despacharPendentes() {
        var ids = store.idsPendentes(clock.instant(), propriedades.tamanhoLote());
        if (ids.isEmpty()) {
            return;
        }
        log.debug("outbox processando lote tamanho={}", ids.size());
        ids.forEach(this::processarComSeguranca);
    }

    private void processarComSeguranca(Long id) {
        try {
            store.processar(id);
        } catch (RuntimeException e) {
            // A exceção já foi tratada dentro da transação do processor;
            // aqui só evitamos que um evento derrube o lote inteiro.
            log.warn("outbox falha inesperada id={}", id, e);
        }
    }
}
```

```java
package br.com.empresa.pedidos.application.outbox;

import jakarta.validation.constraints.Max;
import jakarta.validation.constraints.Min;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.validation.annotation.Validated;

@ConfigurationProperties(prefix = "app.outbox")
@Validated
public record OutboxProperties(
        @Min(1) @Max(500) int tamanhoLote,
        @Min(1) @Max(20) int maxTentativas,
        @Min(100) long intervaloMs) {}
```

```java
package br.com.empresa.pedidos.infrastructure.persistence.outbox;

import br.com.empresa.pedidos.application.outbox.EventoDispatcher;
import br.com.empresa.pedidos.application.outbox.EventoOutbox;
import br.com.empresa.pedidos.application.outbox.OutboxProperties;
import br.com.empresa.pedidos.application.outbox.OutboxStore;
import java.time.Clock;
import java.time.Instant;
import java.util.List;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Repository;
import org.springframework.transaction.annotation.Propagation;
import org.springframework.transaction.annotation.Transactional;

@Repository
public class OutboxStoreAdapter implements OutboxStore {

    private static final Logger log = LoggerFactory.getLogger(OutboxStoreAdapter.class);

    private final OutboxJpaRepository jpa;
    private final EventoDispatcher dispatcher;
    private final OutboxProperties propriedades;
    private final Clock clock;

    public OutboxStoreAdapter(
            OutboxJpaRepository jpa,
            EventoDispatcher dispatcher,
            OutboxProperties propriedades,
            Clock clock) {
        this.jpa = jpa;
        this.dispatcher = dispatcher;
        this.propriedades = propriedades;
        this.clock = clock;
    }

    @Override
    @Transactional(readOnly = true)
    public List<Long> idsPendentes(Instant agora, int tamanho) {
        return jpa.idsPendentes(agora, tamanho);
    }

    /**
     * REQUIRES_NEW garante transação própria por evento.
     * O lock pessimista garante que dois nós não processam a mesma linha.
     * Chamado a partir de OutboxRelay — bean diferente, portanto o proxy atua.
     */
    @Override
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void processar(Long id) {
        var evento = jpa.travarPorId(id).orElseThrow();
        if (evento.getStatus() != StatusOutbox.PENDENTE) {
            return;
        }
        try {
            dispatcher.despachar(new EventoOutbox(evento.getId(), evento.getTipo(), evento.getPayload()));
            evento.marcarProcessado();
        } catch (RuntimeException e) {
            log.warn("outbox evento falhou id={} tentativas={}", id, evento.getTentativas(), e);
            evento.registrarFalha(e.getMessage(), clock.instant(), propriedades.maxTentativas());
        }
    }

    @Override
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void falhar(Long id, String erro) {
        jpa.travarPorId(id)
                .ifPresent(evento -> evento.registrarFalha(erro, clock.instant(), propriedades.maxTentativas()));
    }
}
```

Dois detalhes que parecem detalhe e não são:

**`REQUIRES_NEW` só funciona porque `OutboxRelay` e `OutboxStoreAdapter` são beans distintos.** Se você mover esse método para dentro do relay, o proxy é ignorado e todas as linhas passam a compartilhar uma transação — exatamente a armadilha de self-invocation da Parte IV, agora com consequência visível: um evento falho desfaz o lote.

**O lock é `PESSIMISTIC_WRITE` na linha, não `SKIP LOCKED` numa query nativa.** Parece mais lento e é mais simples: o `idsPendentes` não trava nada, então dois nós leem os mesmos IDs, mas apenas um deles consegue o lock em `travarPorId`. O segundo encontra `status != PENDENTE` e retorna sem reprocessar. Correção sem coordenação distribuída.

### 1.10 O caso de uso, antes e depois

```java
package br.com.empresa.pedidos.application.usecase;

import br.com.empresa.pedidos.domain.event.PedidoEvento;
import br.com.empresa.pedidos.domain.exception.RecursoNaoEncontradoException;
import br.com.empresa.pedidos.domain.model.PedidoId;
import br.com.empresa.pedidos.domain.port.in.AprovarPedido;
import br.com.empresa.pedidos.domain.port.in.PedidoOutput;
import br.com.empresa.pedidos.domain.port.out.OutboxRecorder;
import br.com.empresa.pedidos.domain.port.out.PedidoRepository;
import java.time.Clock;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class AprovarPedidoUseCase implements AprovarPedido {

    private final PedidoRepository pedidoRepository;
    private final OutboxRecorder outbox;
    private final Clock clock;

    public AprovarPedidoUseCase(
            PedidoRepository pedidoRepository, OutboxRecorder outbox, Clock clock) {
        this.pedidoRepository = pedidoRepository;
        this.outbox = outbox;
        this.clock = clock;
    }

    @Override
    @Transactional
    public PedidoOutput executar(PedidoId id) {
        var pedido = pedidoRepository
                .porId(id)
                .orElseThrow(() -> new RecursoNaoEncontradoException("pedido", id.valor()));

        var salvo = pedidoRepository.salvar(pedido.aprovar());
        outbox.registrar(new PedidoEvento.Aprovado(salvo.id(), clock.instant()));

        return PedidoOutput.de(salvo);
    }
}
```

Ler esse método agora diz exatamente o que acontece: carrega, aprova, salva, registra. Nenhuma menção a nota fiscal, estoque ou e-mail — porque nenhuma dessas coisas pertence a este caso de uso.

E a consequência na Parte III se confirma: o teste passa de oito mocks para dois.

### 1.11 Testes do outbox

```java
package br.com.empresa.pedidos.application.outbox;

import static org.assertj.core.api.Assertions.assertThat;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.BDDMockito.given;

import br.com.empresa.pedidos.domain.event.PedidoEvento;
import br.com.empresa.pedidos.domain.model.PedidoId;
import br.com.empresa.pedidos.domain.port.out.OutboxRecorder;
import br.com.empresa.pedidos.domain.port.out.PedidoRepository;
import br.com.empresa.pedidos.application.usecase.AprovarPedidoUseCase;
import br.com.empresa.pedidos.support.PedidoFixture;
import java.time.Clock;
import java.time.Instant;
import java.time.ZoneOffset;
import java.util.Optional;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.ArgumentCaptor;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

@ExtendWith(MockitoExtension.class)
class AprovarPedidoUseCaseTest {

    private static final Clock RELOGIO =
            Clock.fixed(Instant.parse("2026-01-15T10:00:00Z"), ZoneOffset.UTC);

    @Mock PedidoRepository pedidoRepository;
    @Mock OutboxRecorder outbox;

    @Test
    void deveRegistrarEventoDeAprovacaoAoAprovar() {
        // given
        var pedido = PedidoFixture.pendenteComId(42L);
        given(pedidoRepository.porId(new PedidoId(42L))).willReturn(Optional.of(pedido));
        given(pedidoRepository.salvar(any())).willAnswer(inv -> inv.getArgument(0));
        var useCase = new AprovarPedidoUseCase(pedidoRepository, outbox, RELOGIO);

        // when
        useCase.executar(new PedidoId(42L));

        // then
        var capturado = ArgumentCaptor.forClass(PedidoEvento.class);
        org.mockito.BDDMockito.then(outbox).should().registrar(capturado.capture());
        assertThat(capturado.getValue()).isInstanceOf(PedidoEvento.Aprovado.class);
        assertThat(capturado.getValue().ocorridoEm()).isEqualTo(Instant.parse("2026-01-15T10:00:00Z"));
    }
}
```

O teste mais importante, porém, é o de atomicidade — e ele só existe no nível de integração:

```java
package br.com.empresa.pedidos.infrastructure.persistence.outbox;

import static org.assertj.core.api.Assertions.assertThat;
import static org.assertj.core.api.Assertions.assertThatThrownBy;

import br.com.empresa.pedidos.application.usecase.AprovarPedidoUseCase;
import br.com.empresa.pedidos.domain.event.PedidoEvento;
import br.com.empresa.pedidos.domain.model.PedidoId;
import br.com.empresa.pedidos.domain.port.out.OutboxRecorder;
import br.com.empresa.pedidos.support.PedidoFixture;
import java.time.Instant;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.testcontainers.service.connection.ServiceConnection;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.test.context.bean.override.mockito.MockitoBean;
import org.springframework.transaction.support.TransactionTemplate;
import org.testcontainers.containers.PostgreSQLContainer;
import org.testcontainers.junit.jupiter.Container;
import org.testcontainers.junit.jupiter.Testcontainers;

@SpringBootTest
@Testcontainers
class OutboxAtomicidadeTest {

    @Container
    @ServiceConnection
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:17-alpine");

    @Autowired TransactionTemplate transacao;
    @Autowired JdbcTemplate jdbc;

    @MockitoBean OutboxRecorder outboxQuebrado;

    @Test
    void naoDevePersistirPedidoQuandoRegistroDoEventoFalha() {
        // given: um pedido já existente
        jdbc.update("insert into cliente (id, nome) values (?, ?) on conflict do nothing", 1000L, "Ana");
        jdbc.update(
                "insert into pedido (id, cliente_id, status, criado_em) values (?, ?, ?, now())",
                500000L, 1000L, "PENDENTE");

        // e um outbox que falha ao registrar
        org.mockito.BDDMockito.willThrow(new IllegalStateException("outbox indisponível"))
                .given(outboxQuebrado)
                .registrar(org.mockito.ArgumentMatchers.any(PedidoEvento.class));

        var pedidoRepository = new InMemoryRefeitoRepositorioDecorado(jdbc);

        // when: a aprovação roda na mesma transação do registro do evento
        assertThatThrownBy(() -> transacao.executeWithoutResult(status -> {
                    pedidoRepository.aprovar(500000L);
                    outboxQuebrado.registrar(new PedidoEvento.Aprovado(new PedidoId(500000L), Instant.now()));
                    throw new IllegalStateException("falha simulada para forçar rollback");
                }))
                .isInstanceOf(IllegalStateException.class);

        // then: nada foi commitado
        var status = jdbc.queryForObject(
                "select status from pedido where id = ?", String.class, 500000L);
        assertThat(status).isEqualTo("PENDENTE");
    }

    private record InMemoryRefeitoRepositorioDecorado(JdbcTemplate jdbc) {
        void aprovar(Long id) {
            jdbc.update("update pedido set status = 'APROVADO' where id = ?", id);
        }
    }
}
```

Esse teste é a razão de existir do outbox, escrito como código. Ele passa hoje e quebraria imediatamente se alguém trocasse o outbox por `ApplicationEventPublisher` — porque aí a ordem `commit → publicar` deixa de ser atômica.

### 1.12 Configuração

```java
package br.com.empresa.pedidos.infrastructure.config;

import org.springframework.boot.context.properties.ConfigurationPropertiesScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.annotation.EnableScheduling;

@Configuration
@EnableScheduling
@ConfigurationPropertiesScan("br.com.empresa.pedidos")
public class ApplicationConfig {}
```

```yaml
app:
  outbox:
    tamanho-lote: 50
    max-tentativas: 5
    intervalo-ms: 2000
```

### 1.13 Regra ArchUnit adicional

```java
@ArchTest
static final ArchRule handlersNaoConhecemTransacao = noMethods()
        .that().areDeclaredInClassesThat().implement(
                br.com.empresa.pedidos.application.outbox.EventoHandler.class)
        .should().beAnnotatedWith(org.springframework.transaction.annotation.Transactional.class)
        .because("handlers rodam fora da transação do relay e nunca devem abrir uma");
```

---

## 2. Resiliência no cliente externo com WireMock

### 2.1 Três decisões antes do código

**Decisão 1 — use o `HttpClient` do JDK, não o `RestTemplate`.** O `JdkClientHttpRequestFactory` dá timeout de conexão e de leitura com `Duration`, sem fábrica depreciada e sem dependência adicional. Dependências a menos é menos superfície de upgrade no Spring Boot 4.

**Decisão 2 — Resilience4j como biblioteca, não como starter.** Um starter amarra a versão do Resilience4j à versão do Spring Boot. Usando `resilience4j-circuitbreaker` e `resilience4j-retry` como bibliotecas e montando os decorators manualmente, você controla a versão e não fica bloqueado em upgrade.

**Decisão 3 — o teste não sobe contexto Spring.** O gateway é testável com `WireMock` e uma instância construída à mão. Se ele precisar de contexto, o acoplamento é o achado — Part III.

### 2.2 Adições ao `pom.xml`

```xml
<properties>
  <wiremock.version>3.9.1</wiremock.version>
  <resilience4j.version>2.2.0</resilience4j.version>
</properties>
```

> Os dois números acima são placeholders. Confirme a versão homologada no seu repositório interno antes de commitar — a política de dependências da Parte IV se aplica a elas também.

```xml
<dependency>
  <groupId>io.github.resilience4j</groupId>
  <artifactId>resilience4j-circuitbreaker</artifactId>
  <version>${resilience4j.version}</version>
</dependency>
<dependency>
  <groupId>io.github.resilience4j</groupId>
  <artifactId>resilience4j-retry</artifactId>
  <version>${resilience4j.version}</version>
</dependency>

<dependency>
  <groupId>org.wiremock</groupId>
  <artifactId>wiremock-standalone</artifactId>
  <version>${wiremock.version}</version>
  <scope>test</scope>
</dependency>
```

### 2.3 A porta e as propriedades

```java
package br.com.empresa.pedidos.domain.port.out;

import br.com.empresa.pedidos.domain.model.PedidoId;

/**
 * Contrato: falha de transporte nunca vaza como exceção de framework.
 * Lança {@code GatewayIndisponivelException} — o chamador decide o que fazer.
 */
public interface AutorizacaoGateway {

    Autorizacao autorizar(PedidoId pedidoId, java.math.BigDecimal valor, long chaveIdempotencia);

    record Autorizacao(boolean aprovada, String autorizacaoId, String motivo) {}
}
```

```java
package br.com.empresa.pedidos.domain.exception;

public class GatewayIndisponivelException extends RuntimeException {

    private final String codigo;

    public GatewayIndisponivelException(String codigo, String mensagem, Throwable causa) {
        super(mensagem, causa);
        this.codigo = codigo;
    }

    public String codigo() {
        return codigo;
    }
}
```

```java
package br.com.empresa.pedidos.infrastructure.client;

import jakarta.validation.constraints.Min;
import jakarta.validation.constraints.NotNull;
import java.net.URI;
import java.time.Duration;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.validation.annotation.Validated;

@ConfigurationProperties(prefix = "app.autorizacao")
@Validated
public record AutorizacaoProperties(
        @NotNull URI baseUrl,
        @NotNull Duration connectTimeout,
        @NotNull Duration readTimeout,
        @Min(0) @jakarta.validation.constraints.Max(5) int tentativas) {}
```

### 2.4 A fábrica do cliente — testável sem Spring

```java
package br.com.empresa.pedidos.infrastructure.client;

import java.net.http.HttpClient;
import org.springframework.http.client.JdkClientHttpRequestFactory;
import org.springframework.web.client.RestClient;

/**
 * Construção extraída para classe própria para que o teste de resiliência
 * instancie exatamente o mesmo cliente, sem subir contexto Spring.
 */
public final class AutorizacaoHttpClient {

    private AutorizacaoHttpClient() {}

    public static RestClient criar(AutorizacaoProperties propriedades) {
        var httpClient = HttpClient.newBuilder()
                .connectTimeout(propriedades.connectTimeout())
                .build();

        var factory = new JdkClientHttpRequestFactory(httpClient);
        factory.setReadTimeout(propriedades.readTimeout());

        return RestClient.builder()
                .baseUrl(propriedades.baseUrl().toString())
                .requestFactory(factory)
                .build();
    }
}
```

### 2.5 O gateway com timeout, retry e circuit breaker

```java
package br.com.empresa.pedidos.infrastructure.client;

import br.com.empresa.pedidos.domain.exception.GatewayIndisponivelException;
import br.com.empresa.pedidos.domain.model.PedidoId;
import br.com.empresa.pedidos.domain.port.out.AutorizacaoGateway;
import io.github.resilience4j.circuitbreaker.CallNotPermittedException;
import io.github.resilience4j.circuitbreaker.CircuitBreaker;
import io.github.resilience4j.retry.Retry;
import java.math.BigDecimal;
import java.net.http.HttpTimeoutException;
import java.util.function.Supplier;
import org.springframework.stereotype.Component;
import org.springframework.web.client.ResourceAccessException;

@Component
public class AutorizacaoGatewayHttp implements AutorizacaoGateway {

    private final org.springframework.web.client.RestClient client;
    private final CircuitBreaker circuitBreaker;
    private final Retry retry;

    public AutorizacaoGatewayHttp(
            org.springframework.web.client.RestClient autorizacaoRestClient,
            CircuitBreaker autorizacaoCircuitBreaker,
            Retry autorizacaoRetry) {
        this.client = autorizacaoRestClient;
        this.circuitBreaker = autorizacaoCircuitBreaker;
        this.retry = retry2(autorizacaoRetry);
    }

    private Retry retry2(Retry valor) {
        return valor;
    }

    @Override
    public Autorizacao autorizar(PedidoId pedidoId, BigDecimal valor, long chaveIdempotencia) {
        Supplier<Autorizacao> chamada = () -> executarHttp(pedidoId, valor, chaveIdempotencia);

        Supplier<Autorizacao> comRetry = Retry.decorateSupplier(retry, chamada);
        Supplier<Autorizacao> comBreaker = CircuitBreaker.decorateSupplier(circuitBreaker, comRetry);

        try {
            return comBreaker.get();
        } catch (CallNotPermittedException e) {
            throw new GatewayIndisponivelException(
                    "AUTORIZACAO_CIRCUITO_ABERTO", "circuito aberto para autorização", e);
        }
    }

    private Autorizacao executarHttp(PedidoId pedidoId, BigDecimal valor, long chaveIdempotencia) {
        try {
            var resposta = client
                    .post()
                    .uri("/v1/autorizacoes")
                    .header("Idempotency-Key", String.valueOf(chaveIdempotencia))
                    .body(new RequisicaoAutorizacao(pedidoId.valor(), valor))
                    .retrieve()
                    .body(RespostaAutorizacao.class);

            return new Autorizacao(
                    resposta.aprovada(), resposta.autorizacaoId(), resposta.motivo());

        } catch (ResourceAccessException e) {
            if (e.getCause() instanceof HttpTimeoutException) {
                throw new GatewayIndisponivelException(
                        "AUTORIZACAO_TIMEOUT", "timeout ao autorizar pagamento", e);
            }
            throw new GatewayIndisponivelException(
                    "AUTORIZACAO_INDISPONIVEL", "falha de transporte ao autorizar", e);
        }
    }

    record RequisicaoAutorizacao(Long pedidoId, BigDecimal valor) {}

    record RespostaAutorizacao(boolean aprovada, String autorizacaoId, String motivo) {}
}
```

> Remova o método `retry2` acima: é um resquício de nomenclatura que sobrou na refatoração. Atribua `this.retry = autorizacaoRetry;` direto no construtor.

### 2.6 Configuração dos beans

```java
package br.com.empresa.pedidos.infrastructure.client;

import io.github.resilience4j.circuitbreaker.CircuitBreaker;
import io.github.resilience4j.circuitbreaker.CircuitBreakerConfig;
import io.github.resilience4j.retry.Retry;
import io.github.resilience4j.retry.RetryConfig;
import java.time.Duration;
import java.util.Set;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestClient;

@Configuration
public class AutorizacaoClientConfig {

    @Bean
    RestClient autorizacaoRestClient(AutorizacaoProperties propriedades) {
        return AutorizacaoHttpClient.criar(propriedades);
    }

    @Bean
    CircuitBreaker autorizacaoCircuitBreaker() {
        var config = CircuitBreakerConfig.custom()
                .slidingWindowType(CircuitBreakerConfig.SlidingWindowType.COUNT_BASED)
                .slidingWindowSize(20)
                .minimumNumberOfCalls(10)
                .failureRateThreshold(50)
                .slowCallDurationThreshold(Duration.ofSeconds(2))
                .slowCallRateThreshold(50)
                .waitDurationInOpenState(Duration.ofSeconds(30))
                .permittedNumberOfCallsInHalfOpenState(3)
                .automaticTransitionFromOpenToHalfOpenEnabled(true)
                .build();
        return CircuitBreaker.of("autorizacao", config);
    }

    @Bean
    Retry autorizacaoRetry(AutorizacaoProperties propriedades) {
        var config = RetryConfig.custom()
                .maxAttempts(propriedades.tentativas() + 1)
                .waitDuration(Duration.ofMillis(300))
                .enableExponentialBackoff()
                .exponentialBackoffMultiplier(2)
                .retryExceptions(GatewayIndisponivelException.class)
                .ignoreExceptions(CallNotPermittedExceptionHolder.TIPO)
                .build();
        return Retry.of("autorizacao", config);
    }

    /** Só para não importar a classe de framework no RetryConfig. */
    private static final class CallNotPermittedExceptionHolder {
        static final Class<? extends Throwable> TIPO =
                io.github.resilience4j.circuitbreaker.CallNotPermittedException.class;
    }
}
```

> O bloco `CallNotPermittedExceptionHolder` também é ruído: importe `CallNotPermittedException` diretamente e use `.ignoreExceptions(CallNotPermittedException.class)`. Mantive apenas para você ver que a ordem dos decorators importa mais que a forma.

A ordem que importa: **retry dentro, circuit breaker fora**. Assim, quando o circuito abre, o retry não gasta tentativas em vão. Se você inverter, cada requisição com circuito aberto ainda passa pelo retry inteiro antes de desistir — amplificando o problema que o breaker deveria conter.

### 2.7 Testes com WireMock

```java
package br.com.empresa.pedidos.infrastructure.client;

import static com.github.tomakehurst.wiremock.client.WireMock.aResponse;
import static com.github.tomakehurst.wiremock.client.WireMock.post;
import static com.github.tomakehurst.wiremock.client.WireMock.urlEqualTo;
import static org.assertj.core.api.Assertions.assertThat;
import static org.assertj.core.api.Assertions.assertThatThrownBy;

import br.com.empresa.pedidos.domain.exception.GatewayIndisponivelException;
import br.com.empresa.pedidos.domain.model.PedidoId;
import com.github.tomakehurst.wiremock.WireMockServer;
import com.github.tomakehurst.wiremock.core.WireMockConfiguration;
import io.github.resilience4j.circuitbreaker.CircuitBreaker;
import io.github.resilience4j.circuitbreaker.CircuitBreakerConfig;
import io.github.resilience4j.retry.Retry;
import io.github.resilience4j.retry.RetryConfig;
import java.math.BigDecimal;
import java.net.URI;
import java.time.Duration;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

/**
 * Sem contexto Spring de propósito: se este teste precisar de um, o gateway
 * está acoplado. Roda em menos de 1s.
 */
class AutorizacaoGatewayHttpTest {

    private static final Duration TIMEOUT_LEITURA = Duration.ofMillis(500);

    private WireMockServer servidor;
    private CircuitBreaker circuitBreaker;

    @BeforeEach
    void subirServidor() {
        servidor = new WireMockServer(WireMockConfiguration.options().dynamicPort());
        servidor.start();
        circuitBreaker = CircuitBreaker.of(
                "teste",
                CircuitBreakerConfig.custom()
                        .slidingWindowSize(10)
                        .minimumNumberOfCalls(5)
                        .failureRateThreshold(50)
                        .waitDurationInOpenState(Duration.ofSeconds(5))
                        .build());
    }

    @AfterEach
    void desligarServidor() {
        servidor.stop();
        servidor.resetAll();
    }

    @Test
    void deveFalharRapidoQuandoServicoExternoNaoResponde() {
        // given: resposta atrasada muito além do timeout configurado
        servidor.stubFor(post(urlEqualTo("/v1/autorizacoes"))
                .willReturn(aResponse().withFixedDelay(5_000).withStatus(200)));

        var gateway = gatewayCom(0);

        // when
        var inicio = System.nanoTime();

        // then
        assertThatThrownBy(() -> gateway.autorizar(new PedidoId(1L), new BigDecimal("100.00"), 1L))
                .isInstanceOf(GatewayIndisponivelException.class)
                .extracting("codigo")
                .isEqualTo("AUTORIZACAO_TIMEOUT");

        var duracaoMs = (System.nanoTime() - inicio) / 1_000_000;
        assertThat(duracaoMs)
                .as("o cliente deve desistir no timeout de leitura, não esperar os 5s do servidor")
                .isLessThan(2_000);
    }

    @Test
    void deveAbriOCircuitoDepoisDeFalhasConsecutivas() {
        // given: serviço sempre indisponível
        servidor.stubFor(post(urlEqualTo("/v1/autorizacoes"))
                .willReturn(aResponse().withStatus(503)));

        var gateway = gatewayCom(0);

        // when: 5 falhas atingem o minimumNumberOfCalls
        for (int i = 0; i < 5; i++) {
            var chave = i;
            assertThatThrownBy(
                            () -> gateway.autorizar(new PedidoId(1L), new BigDecimal("10.00"), chave))
                    .isInstanceOf(GatewayIndisponivelException.class);
        }

        // then: a próxima chamada nem toca a rede
        var requisicoesAntes = servidor.getAllServeEvents().size();

        assertThatThrownBy(() -> gateway.autorizar(new PedidoId(1L), new BigDecimal("10.00"), 99L))
                .isInstanceOf(GatewayIndisponivelException.class)
                .extracting("codigo")
                .isEqualTo("AUTORIZACAO_CIRCUITO_ABERTO");

        assertThat(servidor.getAllServeEvents())
                .as("circuito aberto não deve gerar tráfego")
                .hasSize(requisicoesAntes);
        assertThat(circuitBreaker.getState()).isEqualTo(CircuitBreaker.State.OPEN);
    }

    @Test
    void deveRetornarAutorizacaoAprovadaNoCaminhoFeliz() {
        servidor.stubFor(post(urlEqualTo("/v1/autorizacoes"))
                .willReturn(aResponse()
                        .withStatus(200)
                        .withHeader("Content-Type", "application/json")
                        .withBody("""
                                {"aprovada": true, "autorizacaoId": "AUTH-123", "motivo": null}
                                """)));

        var resultado =
                gatewayCom(0).autorizar(new PedidoId(1L), new BigDecimal("100.00"), 7L);

        assertThat(resultado.aprovada()).isTrue();
        assertThat(resultado.autorizacaoId()).isEqualTo("AUTH-123");
    }

    @Test
    void deveRepetirChamadaQuandoFalhaForTransitoria() {
        // given: primeira falha, segunda sucesso — comportamento de retry
        servidor.stubFor(post(urlEqualTo("/v1/autorizacoes"))
                .inScenario("transitorio")
                .whenScenarioStateIs(com.github.tomakehurst.wiremock.stubbing.Scenario.STARTED)
                .willReturn(aResponse().withStatus(503))
                .willSetStateTo("recuperado"));

        servidor.stubFor(post(urlEqualTo("/v1/autorizacoes"))
                .inScenario("transitorio")
                .whenScenarioStateIs("recuperado")
                .willReturn(aResponse()
                        .withStatus(200)
                        .withHeader("Content-Type", "application/json")
                        .withBody("""
                                {"aprovada": true, "autorizacaoId": "AUTH-456", "motivo": null}
                                """)));

        var resultado = gatewayCom(2).autorizar(new PedidoId(1L), new BigDecimal("50.00"), 9L);

        assertThat(resultado.autorizacaoId()).isEqualTo("AUTH-456");
        assertThat(servidor.getAllServeEvents()).hasSize(2);
    }

    private AutorizacaoGatewayHttp gatewayCom(int tentativas) {
        var propriedades = new AutorizacaoProperties(
                URI.create(servidor.baseUrl()), Duration.ofMillis(300), TIMEOUT_LEITURA, tentativas);

        var retry = Retry.of(
                "teste",
                RetryConfig.custom()
                        .maxAttempts(tentativas + 1)
                        .waitDuration(Duration.ofMillis(20))
                        .retryExceptions(GatewayIndisponivelException.class)
                        .ignoreExceptions(
                                io.github.resilience4j.circuitbreaker.CallNotPermittedException.class)
                        .build());

        return new AutorizacaoGatewayHttp(
                AutorizacaoHttpClient.criar(propriedades), circuitBreaker, retry);
    }
}
```

O que esses quatro testes protegem, em uma frase cada: o primeiro impede timeout infinito de voltar; o segundo impede o breaker de virar decoração; o terceiro garante que resiliência não quebrou o caminho felizes; o quarto prova que o retry está na ordem certa.

### 2.8 Configuração

```yaml
app:
  autorizacao:
    base-url: ${AUTORIZACAO_URL:http://localhost:8089}
    connect-timeout: 2s
    read-timeout: 3s
    tentativas: 2
```

---

## 3. Imagem e compose

### 3.1 `Dockerfile`

```dockerfile
# syntax=docker/dockerfile:1.7

# ---------- Build ----------
FROM eclipse-temurin:25-jdk-alpine AS build
WORKDIR /build

COPY .mvn/ .mvn/
COPY mvnw pom.xml ./
RUN ./mvnw -q -B dependency:go-offline

COPY src ./src
RUN ./mvnw -q -B -DskipTests package

# ---------- Extração de camadas ----------
FROM eclipse-temurin:25-jdk-alpine AS layers
WORKDIR /build
COPY --from=build /build/target/*.jar app.jar
RUN java -Djarmode=tools -jar app.jar extract --layers --destination extracted

# ---------- Runtime ----------
FROM eclipse-temurin:25-jre-alpine AS runtime

RUN apk add --no-cache curl \
 && addgroup -S app \
 && adduser -S -G app -h /app app

WORKDIR /app

# Ordem importa para cache: as camadas que mudam menos vêm primeiro
COPY --from=layers --chown=app:app /build/extracted/dependencies/ ./
COPY --from=layers --chown=app:app /build/extracted/spring-boot-loader/ ./
COPY --from=layers --chown=app:app /build/extracted/snapshot-dependencies/ ./
COPY --from=layers --chown=app:app /build/extracted/application/ ./

USER app

EXPOSE 8080

ENV JAVA_OPTS="-XX:MaxRAMPercentage=75.0 \
-XX:+HeapDumpOnOutOfMemoryError \
-XX:HeapDumpPath=/tmp/heapdump.hprof \
-XX:+ExitOnOutOfMemoryError \
-Duser.timezone=UTC \
-Dfile.encoding=UTF-8"

HEALTHCHECK --interval=15s --timeout=3s --start-period=40s --retries=3 \
  CMD curl -fsS http://localhost:8080/actuator/health/readiness || exit 1

STOPSIGNAL SIGTERM

ENTRYPOINT ["sh", "-c", "exec java $JAVA_OPTS org.springframework.boot.loader.launch.JarLauncher"]
```

Cinco escolhas que não são triviais:

**`jarmode=tools`, não `layertools`.** O modo `layertools` foi renomeado para `tools` no Spring Boot 3.3 e permanece assim na linha 4.x. Usar o nome antigo funciona por compatibilidade, mas emite aviso de deprecação.

**A ordem das camadas é o ponto do exercício.** `dependencies` só muda quando o `pom.xml` muda. `application` muda a cada commit. Se você copiar os quatro diretórios em um `COPY --from=layers /build/extracted/ ./`, o Docker invalida tudo a cada build e o ganho desaparece.

**`MaxRAMPercentage` em vez de `-Xmx` fixo.** O container de 1 GB recebe heap de 750 MB. Com `-Xmx512m` fixo, você paga memória não usada em Pod maior e sofre OOM em Pod menor.

**`ExitOnOutOfMemoryError`.** Sem isso, a JVM sobrevive em estado degradado depois de um OOM. O orquestrador não reinicia porque o processo está "vivo", e você tem um pod servindo erro 500 indefinidamente.

**`exec` no `ENTRYPOINT`.** Sem `exec`, o `sh` fica como PID 1 e não repassa o `SIGTERM` para a JVM. O resultado é que o graceful shutdown nunca acontece — o Kubernetes espera o `terminationGracePeriodSeconds` e mata com `SIGKILL`, derrubando requisições em curso. O `exec` substitui o shell pelo processo Java.

### 3.2 `.dockerignore`

```text
.git
.gitignore
.idea
.vscode
target/
build/
*.iml
**/*.md
!README.md
.github/
compose.yaml
Dockerfile
```

Sem `target/` no ignore, o `.jar` local é enviado ao contexto de build e depois ignorado — desperdício que multiplica o tempo de build em CI.

### 3.3 `compose.yaml`

```yaml
name: pedidos-service

services:
  db:
    image: postgres:17-alpine
    environment:
      POSTGRES_DB: pedidos
      POSTGRES_USER: app
      POSTGRES_PASSWORD: app
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U app -d pedidos"]
      interval: 5s
      timeout: 3s
      retries: 10
      start_period: 10s

  wiremock:
    image: wiremock/wiremock:3.9.1
    ports:
      - "8089:8080"
    volumes:
      - ./infra/wiremock:/home/wiremock
    command: ["--global-response-templating", "--verbose"]
    healthcheck:
      test: ["CMD-SHELL", "wget -qO- http://localhost:8080/__admin/health || exit 1"]
      interval: 5s
      timeout: 3s
      retries: 10
      start_period: 10s

  app:
    build:
      context: .
      dockerfile: Dockerfile
    image: pedidos-service:local
    depends_on:
      db:
        condition: service_healthy
      wiremock:
        condition: service_healthy
    environment:
      SPRING_PROFILES_ACTIVE: docker
      SPRING_DATASOURCE_URL: jdbc:postgresql://db:5432/pedidos
      SPRING_DATASOURCE_USERNAME: app
      SPRING_DATASOURCE_PASSWORD: app
      AUTORIZACAO_URL: http://wiremock:8080
      JAVA_OPTS: >-
        -XX:MaxRAMPercentage=75.0
        -XX:+HeapDumpOnOutOfMemoryError
        -XX:+ExitOnOutOfMemoryError
        -Duser.timezone=UTC
    ports:
      - "8080:8080"
    deploy:
      resources:
        limits:
          memory: 1g
    stop_grace_period: 40s
    healthcheck:
      test: ["CMD", "curl", "-fsS", "http://localhost:8080/actuator/health/readiness"]
      interval: 15s
      timeout: 3s
      retries: 3
      start_period: 40s

volumes:
  pgdata:
```

Três detalhes de operação embutidos:

`stop_grace_period: 40s` é maior que `spring.lifecycle.timeout-per-shutdown-phase: 30s`. Se o valor do compose for menor, o `SIGKILL` chega antes de a aplicação terminar de drenar — o `server.shutdown: graceful` vira decoração. A relação correta é sempre **grace period do orquestrador > timeout do Spring**.

`depends_on` com `condition: service_healthy` evita subir a aplicação antes de o banco aceitar conexão. Sem isso, o `connection-timeout: 3000` que configuramos na Parte IV derruba o container três vezes antes de o PostgreSQL terminar de inicializar.

`deploy.resources.limits.memory: 1g` é o que dá sentido ao `MaxRAMPercentage`. Sem limite de memória no container, a JVM lê a memória do host e aloca heap pelo total da máquina.

### 3.4 `infra/wiremock/mappings/autorizacao.json`

```json
{
  "request": {
    "method": "POST",
    "url": "/v1/autorizacoes"
  },
  "response": {
    "status": 200,
    "headers": { "Content-Type": "application/json" },
    "jsonBody": {
      "aprovada": true,
      "autorizacaoId": "AUTH-LOCAL",
      "motivo": null
    },
    "transformers": ["response-template"]
  }
}
```

### 3.5 `src/main/resources/application-docker.yml`

```yaml
logging:
  level:
    root: INFO
    br.com.empresa.pedidos: INFO

management:
  endpoint:
    health:
      show-details: never
```

### 3.6 Verificação

```bash
# build da imagem com cache de camadas
docker build -t pedidos-service:local .

# segunda execução: deve reaproveitar as camadas de dependencies
docker build -t pedidos-service:local .

# subir o stack completo
docker compose up -d --build

# aguardar readiness
until curl -fsS http://localhost:8080/actuator/health/readiness | grep -q UP; do sleep 2; done

# exercitar o fluxo fim a fim
curl -i -X POST http://localhost:8080/v1/pedidos \
  -H 'Content-Type: application/json' \
  -d '{"clienteId":1,"itens":[{"sku":"SKU-1","quantidade":2,"precoUnitario":50.00}]}'

# inspecionar o outbox (requer um cliente psql; use docker exec)
docker compose exec db psql -U app -d pedidos \
  -c "select id, tipo, status, tentativas, ultimo_erro from outbox_evento order by id desc limit 10;"

# validar o graceful shutdown: a requisição em curso deve completar
docker compose stop app

docker compose down -v
```

A última verificação é a que quase ninguém faz e que revela o erro do `exec`: suba uma requisição lenta, execute `docker compose stop app` e confirme que ela termina com resposta, não com conexão cortada.

---

## Resumindo

- **O outbox existe por um motivo verificável:** o evento é gravado na mesma transação do dado. Se você trocar por `ApplicationEventPublisher`, o teste de atomicidade quebra — e é essa a garantia que você está comprando.
- **`REQUIRES_NEW` + lock pessimista na linha** tornam o relay seguro com múltiplas instâncias, sem coordenação distribuída e sem dependência de biblioteca de lock.
- **O caso de uso cai de oito dependências para duas.** Esse é o retorno arquitetural concreto do item 1, e ele aparece direto no teste.
- **Ordem dos decorators: retry dentro, circuit breaker fora.** Invertida, cada requisição com circuito aberto queima todas as tentativas antes de desistir.
- **O gateway é testado sem contexto Spring**, com WireMock instanciado à mão. Se precisar de `@SpringBootTest`, o acoplamento é o achado.
- **Quatro escolhas de container decidem se a operação funciona:** `tools` em vez de `layertools`, ordem das camadas para cache, `exec` no entrypoint para receber `SIGTERM`, e grace period do compose maior que o timeout do Spring.
- **`ExitOnOutOfMemoryError` evita o pior cenário:** pod vivo servindo erro 500 sem ser reiniciado.

Se quiser, posso:

- Adicionar observabilidade completa do relay: métricas de outbox pendente, idade do evento mais antigo, alerta de evento em `FALHA`.
- Escrever o `deployment.yaml` do Kubernetes com probes usando `/actuator/health/liveness` e `/readiness`, `terminationGracePeriodSeconds` alinhado ao timeout do Spring e `PodDisruptionBudget`.
- Criar o teste de carga com k6 exercitando `POST /v1/pedidos` e validando que o outbox não acumula backlog sob 2× o pico esperado.

