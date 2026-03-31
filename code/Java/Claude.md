# CLAUDE.md Example

# CLAUDE.md — Spring Boot Project Guidelines

This file defines the conventions, architecture rules, and coding standards for this project.
Claude Code must follow these guidelines in every interaction: when generating, reviewing, or refactoring code.

---

## Project Stack

- **Java 21+** with Virtual Threads enabled
- **Spring Boot 3.x**
- **Spring WebFlux** (reactive web layer)
- **Spring Data R2DBC** (reactive database access)
- **JUnit 5 + Mockito + AssertJ** (testing)
- **Testcontainers** (integration tests)
- **MapStruct** (DTO mapping)
- **Lombok** (boilerplate reduction)

---

## Architecture

Follow a **layered architecture** with strict separation of concerns:

```
controller/        → HTTP layer: receives requests, delegates to service, returns responses
service/           → Business logic: orchestration, transformations, rules
repository/        → Data access: R2DBC repositories and custom queries
domain/            → Entities and domain objects (no framework annotations except persistence)
dto/               → Request/Response objects (never expose domain entities directly)
mapper/            → MapStruct interfaces for domain ↔ DTO conversion
config/            → Spring configuration classes
exception/         → Custom exceptions and global error handling
```

**Rules:**
- Controllers must never contain business logic.
- Services must never directly return entities — always map to DTOs.
- Repositories must never be called from controllers.
- Domain entities must not have Jackson annotations (`@JsonProperty`, etc.).

---

## Clean Code Standards

### Naming
- Classes: `PascalCase`, descriptive nouns — `OrderService`, `CustomerRepository`
- Methods: `camelCase`, verb-led — `findActiveOrders()`, `processPayment()`
- Constants: `UPPER_SNAKE_CASE`
- Booleans: prefix with `is`, `has`, `can` — `isActive`, `hasPermission`
- Avoid abbreviations: `CustomerService` not `CustSvc`

### Methods
- Maximum **20 lines** per method. Extract if longer.
- Do **one thing** only (Single Responsibility).
- Maximum **3 parameters**. Use a record or DTO object if more are needed.
- Avoid `boolean` flag parameters — split into two methods instead.

### Classes
- Maximum **200–300 lines**. Split if larger.
- Each class has a single, well-defined responsibility.
- Prefer composition over inheritance.

### Comments
- Code should be self-documenting. Avoid redundant comments.
- Use Javadoc only on public API methods in controllers and services.
- Comment **why**, never **what**: `// Retry required due to upstream instability` ✅

### Error Handling
- Never swallow exceptions silently.
- Always use custom, domain-specific exceptions: `OrderNotFoundException`, `PaymentFailedException`.
- Use a global `@RestControllerAdvice` for consistent error responses.
- Log errors with context (never just `e.getMessage()`).

---

## REST API Conventions

- Use **nouns** for resources: `/orders`, `/customers/{id}`
- HTTP verbs map to actions: `GET` (read), `POST` (create), `PUT` (full update), `PATCH` (partial), `DELETE`
- Return standard HTTP status codes: `200`, `201`, `204`, `400`, `404`, `422`, `500`
- All responses wrapped in a consistent structure:

```json
{
  "data": { },
  "error": null,
  "timestamp": "2026-03-31T10:00:00Z"
}
```

- Use `ProblemDetail` (RFC 7807) for error responses.
- Never return raw stack traces to the client.
- Version the API via URL prefix: `/api/v1/`

---

## Testing Standards

### Unit Tests
- Framework: **JUnit 5 + Mockito + AssertJ**
- One test class per production class: `OrderService` → `OrderServiceTest`
- Test method naming: `methodName_whenCondition_shouldExpectedResult`
  - Example: `createOrder_whenCustomerNotFound_shouldThrowException`
- Use `@ExtendWith(MockitoExtension.class)` — no Spring context in unit tests.
- Each test: **Arrange → Act → Assert** structure, clearly separated.
- Aim for **100% coverage** on service and domain logic.
- Never assert on log output; test behavior, not implementation.

```java
@Test
void createOrder_whenCustomerNotFound_shouldThrowNotFoundException() {
    // Arrange
    when(customerRepository.findById(CUSTOMER_ID)).thenReturn(Mono.empty());

    // Act
    Mono<OrderResponse> result = orderService.createOrder(request);

    // Assert
    StepVerifier.create(result)
        .expectError(CustomerNotFoundException.class)
        .verify();
}
```

### Integration Tests
- Use **Testcontainers** to spin up a real database.
- Annotate with `@SpringBootTest` + `@ActiveProfiles("test")`.
- Each integration test must be fully isolated: reset state between tests.
- Use `@Sql` or a `DatabaseCleaner` component to reset the DB.
- Test the full slice: HTTP request → repository → DB.

### Controller (Slice) Tests
- Use `@WebFluxTest` with `@MockBean` for the service layer.
- Test HTTP status codes, response bodies, and validation errors.
- Never start the full application context in controller tests.

### Test Profile
- `src/test/resources/application-test.yml` must override all external dependencies.
- No real external HTTP calls in tests — use WireMock for third-party APIs.

---

## Reactive Programming Rules (WebFlux / R2DBC)

- Never **block** inside a reactive chain (`block()`, `blockFirst()` are forbidden in production code).
- Handle errors with `.onErrorMap()`, `.onErrorResume()` — never let `Mono.error` propagate unhandled.
- Use `StepVerifier` in all reactive unit tests.
- Prefer `Mono<Void>` over `Mono<ResponseEntity<Void>>` for no-content responses.
- Avoid mixing imperative and reactive styles in the same method.

---

## Virtual Threads

- Virtual Threads are enabled globally via `spring.threads.virtual.enabled=true`.
- Do **not** configure custom thread pools for standard use cases — the scheduler handles it.
- Blocking calls (JDBC, file I/O) are acceptable on Virtual Threads but must be intentional and documented.
- Never use `synchronized` blocks with Virtual Threads — use `ReentrantLock` instead.

---

## Security

- Never log sensitive data: passwords, tokens, CPF, credit card numbers.
- Never commit secrets to source control — use environment variables or a secrets manager.
- Validate all input at the controller layer using Bean Validation (`@Valid`, `@NotNull`, etc.).
- Always sanitize inputs before using them in queries (R2DBC parameterized queries prevent SQL injection by default — keep it that way).
- Use `@PreAuthorize` for method-level security where applicable.

---

## Dependency & Build Rules

- **Do not add dependencies without justification.** Prefer what's already on the classpath.
- Keep `pom.xml` / `build.gradle` clean: no unused dependencies.
- Pin dependency versions in a BOM or `dependencyManagement` block.
- No `SNAPSHOT` dependencies in production builds.

---

## Git & Code Review

- Commits follow **Conventional Commits**: `feat:`, `fix:`, `refactor:`, `test:`, `docs:`, `chore:`
- Each commit must be atomic and focused on a single concern.
- PRs must include tests covering the new behavior.
- No PR merges with failing tests or Checkstyle violations.
- Branch naming: `feature/short-description`, `fix/issue-id-description`

---

## What Claude Should Always Do

- Follow the layered architecture — never shortcut layers.
- Write tests alongside every new class or method, not after.
- Use `AssertJ` fluent assertions, not JUnit `assertEquals`.
- Map entities to DTOs using MapStruct — never in the service manually.
- Prefer records for immutable DTOs in Java 21.
- Use `sealed` classes for controlled type hierarchies (e.g., result types).
- Prefer `Optional` over returning `null`.
- Use `@Slf4j` (Lombok) for logging — never `System.out.println`.

## What Claude Should Never Do

- Add business logic to controllers.
- Expose JPA/R2DBC entities in HTTP responses.
- Use `Thread.sleep()` or `block()` in reactive code.
- Leave `TODO` comments without a linked issue.
- Generate code without tests.
- Use `@Autowired` on fields — prefer constructor injection.
- Suppress warnings without a comment explaining why.
