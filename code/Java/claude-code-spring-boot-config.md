# Prompt
Pretendo criar uma configuração com foco em Spring Boot e melhores práticas, para usar no Claude Code, gerando códigos eficientes e mínimo de correção de códigos gerados de maneira errada ou fora de melhores práticas.
Demonstre toda configuração necessária e detalhe o objetivo de cada configuração, como Agents, Skills, etc.

# Abacus Qwen3.7 Max
Vou criar uma configuração completa e otimizada para Claude Code focada em Spring Boot. Esta configuração segue a arquitetura **Command → Agent → Skill** e utiliza progressive disclosure para minimizar o uso de contexto.

## Estrutura de Arquivos

```
.claude/
├── settings.json
├── CLAUDE.md
├── rules/
│   ├── spring-boot-conventions.md
│   ├── testing-standards.md
│   └── api-design.md
├── agents/
│   ├── spring-architect.md
│   ├── code-reviewer.md
│   ├── test-engineer.md
│   └── security-auditor.md
├── skills/
│   ├── create-endpoint/
│   │   └── SKILL.md
│   ├── generate-tests/
│   │   └── SKILL.md
│   ├── refactor-service/
│   │   └── SKILL.md
│   └── api-documentation/
│       └── SKILL.md
└── hooks/
    └── pre-commit-validation.sh
```

---

## 1. CLAUDE.md (Arquivo Principal)

**Objetivo**: Regras globais sempre ativas que definem o comportamento base do Claude para este projeto Spring Boot.

```markdown
# Spring Boot Project Configuration

## Project Overview
This is a Spring Boot application following industry best practices and clean architecture principles.

## Core Principles
- **Simplicity First**: Write minimal code that solves the problem. No over-engineering.
- **Surgical Changes**: Touch only what's necessary. Don't modify unrelated code.
- **Think Before Coding**: State assumptions explicitly before implementation.
- **Goal-Driven**: Define success criteria and verify implementation meets them.

## Technology Stack
- Java 21 (LTS)
- Spring Boot 3.3+
- Spring Data JPA with Hibernate
- Spring Security with OAuth2/JWT
- PostgreSQL/MySQL
- Maven/Gradle
- JUnit 5, Mockito, TestContainers

## Architecture Patterns

### Layered Architecture
```
Controller → Service → Repository → Entity
     ↓          ↓          ↓
   DTO      Domain      Database
```

### Package Structure
```
com.company.project/
├── config/          # Configuration classes
├── controller/      # REST controllers
├── service/         # Business logic
│   └── impl/        # Service implementations
├── repository/      # Data access layer
├── entity/          # JPA entities
├── dto/             # Data Transfer Objects
│   ├── request/     # Request DTOs
│   └── response/    # Response DTOs
├── mapper/          # MapStruct mappers
├── exception/       # Custom exceptions
├── security/        # Security configuration
└── util/            # Utility classes
```

## Code Conventions

### Naming Conventions
- **Classes**: PascalCase (UserService, OrderController)
- **Methods**: camelCase (findById, createOrder)
- **Constants**: UPPER_SNAKE_CASE (MAX_RETRY_ATTEMPTS)
- **Packages**: lowercase (com.company.project.service)
- **Database columns**: snake_case (created_at, user_id)

### Controllers
- Use `@RestController` for REST APIs
- Return `ResponseEntity<T>` for full HTTP control
- Use `@Valid` for request validation
- Prefix API endpoints with `/api/v1/`
- Use appropriate HTTP methods (GET, POST, PUT, DELETE, PATCH)

### Services
- Interface + Implementation pattern
- Use `@Service` annotation
- Inject dependencies via constructor (not `@Autowired` on fields)
- Keep business logic in services, not controllers
- Use `@Transactional` at service level

### Repositories
- Extend `JpaRepository<Entity, ID>`
- Use Spring Data query methods or `@Query` for complex queries
- Never expose entities directly to controllers (use DTOs)

### DTOs
- Use records for immutable DTOs (Java 16+)
- Separate Request and Response DTOs
- Use MapStruct for entity-DTO mapping
- Add validation annotations (@NotNull, @Size, @Email, etc.)

### Exception Handling
- Use `@ControllerAdvice` for global exception handling
- Create custom exceptions extending `RuntimeException`
- Return appropriate HTTP status codes
- Include error details in response body

## Testing Requirements
- Unit tests for services (JUnit 5 + Mockito)
- Integration tests for repositories (TestContainers)
- API tests for controllers (MockMvc)
- Minimum 80% code coverage
- Test naming: `methodName_condition_expectedResult`

## Security
- Never log sensitive data (passwords, tokens, PII)
- Use `@PreAuthorize` for method-level security
- Validate all inputs
- Use parameterized queries (prevent SQL injection)
- Implement CORS properly
- Use HTTPS in production

## Performance
- Use pagination for large result sets
- Implement caching with `@Cacheable` where appropriate
- Use `@EntityGraph` or `JOIN FETCH` to avoid N+1 queries
- Monitor with Spring Boot Actuator

## Documentation
- Document all public APIs with OpenAPI/Swagger annotations
- Use JavaDoc for complex business logic
- Keep README.md updated

## Git Conventions
- Commit messages: `type(scope): description`
- Types: feat, fix, refactor, test, docs, chore
- One commit per logical change
- Never commit secrets or .env files

## Forbidden Practices
- ❌ Field injection with `@Autowired`
- ❌ Returning entities from controllers
- ❌ Business logic in controllers
- ❌ Catching generic `Exception` without rethrowing
- ❌ Using `System.out.println` (use SLF4J logger)
- ❌ Hardcoding configuration values
- ❌ Modifying unrelated code during a task
```

---

## 2. Rules (Regras Carregadas Condicionalmente)

### `.claude/rules/spring-boot-conventions.md`

**Objetivo**: Regras específicas de Spring Boot carregadas apenas quando Claude trabalha com arquivos Java/Kotlin.

```markdown
---
paths: ["**/*.java", "**/*.kt"]
---

## Spring Boot Specific Conventions

### Dependency Injection
```java
// ✅ CORRECT: Constructor injection
@Service
@RequiredArgsConstructor
public class UserService {
    private final UserRepository userRepository;
    private final EmailService emailService;
}

// ❌ WRONG: Field injection
@Service
public class UserService {
    @Autowired
    private UserRepository userRepository;
}
```

### Configuration
```java
// Use @ConfigurationProperties for type-safe config
@ConfigurationProperties(prefix = "app.email")
public record EmailProperties(
    String host,
    int port,
    String username,
    String password
) {}
```

### Transaction Management
```java
// ✅ Apply @Transactional at service level
@Service
public class OrderService {
    
    @Transactional
    public Order createOrder(CreateOrderRequest request) {
        // Business logic
    }
    
    @Transactional(readOnly = true)
    public Order getOrder(Long id) {
        // Read-only operation
    }
}
```

### Validation
```java
// Use Bean Validation annotations
public record CreateUserRequest(
    @NotBlank(message = "Name is required")
    @Size(max = 100)
    String name,
    
    @NotBlank
    @Email(message = "Invalid email format")
    String email,
    
    @NotNull
    @Min(18)
    Integer age
) {}
```
```

### `.claude/rules/testing-standards.md`

**Objetivo**: Padrões de teste carregados quando Claude trabalha com arquivos de teste.

```markdown
---
paths: ["**/src/test/**/*.java"]
---

## Testing Standards

### Unit Test Structure
```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {
    
    @Mock
    private UserRepository userRepository;
    
    @InjectMocks
    private UserService userService;
    
    @Test
    void findById_whenUserExists_thenReturnUser() {
        // Given
        var user = new User(1L, "John");
        when(userRepository.findById(1L))
            .thenReturn(Optional.of(user));
        
        // When
        var result = userService.findById(1L);
        
        // Then
        assertThat(result).isPresent();
        assertThat(result.get().getName()).isEqualTo("John");
        verify(userRepository).findById(1L);
    }
    
    @Test
    void findById_whenUserNotExists_thenThrowException() {
        // Given
        when(userRepository.findById(1L))
            .thenReturn(Optional.empty());
        
        // When & Then
        assertThatThrownBy(() -> userService.findById(1L))
            .isInstanceOf(UserNotFoundException.class)
            .hasMessage("User not found: 1");
    }
}
```

### Integration Test with TestContainers
```java
@SpringBootTest
@Testcontainers
class UserRepositoryIntegrationTest {
    
    @Container
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:15");
    
    @DynamicPropertySource
    static void configureProperties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", postgres::getJdbcUrl);
        registry.add("spring.datasource.username", postgres::getUsername);
        registry.add("spring.datasource.password", postgres::getPassword);
    }
    
    @Autowired
    private UserRepository userRepository;
    
    @Test
    void save_whenValidUser_thenReturnSavedUser() {
        // Test implementation
    }
}
```

### Test Naming Convention
- Format: `methodName_condition_expectedResult`
- Examples:
  - `createOrder_whenValidRequest_thenReturnOrder`
  - `updateUser_whenUserNotExists_thenThrowNotFoundException`
  - `deleteOrder_whenOrderHasDependencies_thenThrowException`
```

### `.claude/rules/api-design.md`

**Objetivo**: Padrões de design de API REST carregados quando Claude trabalha com controllers.

```markdown
---
paths: ["**/controller/**/*.java"]
---

## REST API Design Standards

### Endpoint Naming
```
GET    /api/v1/users          # List all users
GET    /api/v1/users/{id}     # Get user by ID
POST   /api/v1/users          # Create new user
PUT    /api/v1/users/{id}     # Update user (full)
PATCH  /api/v1/users/{id}     # Update user (partial)
DELETE /api/v1/users/{id}     # Delete user
```

### Controller Implementation
```java
@RestController
@RequestMapping("/api/v1/users")
@RequiredArgsConstructor
@Tag(name = "Users", description = "User management APIs")
public class UserController {
    
    private final UserService userService;
    
    @GetMapping
    @Operation(summary = "List all users")
    public ResponseEntity<Page<UserResponse>> listUsers(
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "20") int size,
            @RequestParam(defaultValue = "id") String sortBy) {
        
        var users = userService.findAll(PageRequest.of(page, size, Sort.by(sortBy)));
        return ResponseEntity.ok(users);
    }
    
    @GetMapping("/{id}")
    @Operation(summary = "Get user by ID")
    public ResponseEntity<UserResponse> getUser(@PathVariable Long id) {
        return userService.findById(id)
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
    }
    
    @PostMapping
    @Operation(summary = "Create new user")
    public ResponseEntity<UserResponse> createUser(
            @Valid @RequestBody CreateUserRequest request) {
        
        var user = userService.create(request);
        var uri = ServletUriComponentsBuilder
            .fromCurrentRequest()
            .path("/{id}")
            .buildAndExpand(user.getId())
            .toUri();
        
        return ResponseEntity.created(uri).body(user);
    }
}
```

### Error Response Format
```json
{
  "timestamp": "2024-01-15T10:30:00",
  "status": 400,
  "error": "Bad Request",
  "message": "Validation failed",
  "details": [
    {
      "field": "email",
      "message": "Invalid email format"
    }
  ],
  "path": "/api/v1/users"
}
```

### HTTP Status Codes
- `200 OK` - Successful GET, PUT, PATCH
- `201 Created` - Successful POST
- `204 No Content` - Successful DELETE
- `400 Bad Request` - Validation errors
- `401 Unauthorized` - Authentication required
- `403 Forbidden` - Insufficient permissions
- `404 Not Found` - Resource not found
- `409 Conflict` - Resource already exists
- `500 Internal Server Error` - Server error
```

---

## 3. Agents (Subagentes Especializados)

### `.claude/agents/spring-architect.md`

**Objetivo**: Agente especializado em arquitetura e design de aplicações Spring Boot. Usado para decisões de design, refatoração e revisão arquitetural.

```markdown
---
name: spring-architect
description: PROACTIVELY use when designing new features, reviewing architecture, or making structural decisions about Spring Boot applications
tools: Read, Grep, Glob, Agent
model: sonnet
maxTurns: 10
---

You are a Spring Boot architecture expert. Your role is to:

1. **Analyze Requirements**: Understand the business requirements and translate them into technical design
2. **Design Architecture**: Propose clean, maintainable architecture following Spring Boot best practices
3. **Review Code Structure**: Ensure proper separation of concerns (Controller → Service → Repository)
4. **Suggest Improvements**: Identify anti-patterns and suggest refactoring strategies

## Architecture Principles

### SOLID Principles
- **Single Responsibility**: Each class has one reason to change
- **Open/Closed**: Open for extension, closed for modification
- **Liskov Substitution**: Subtypes must be substitutable for base types
- **Interface Segregation**: Many specific interfaces > one general interface
- **Dependency Inversion**: Depend on abstractions, not concretions

### Clean Architecture Layers
```
Presentation (Controllers)
    ↓
Application (Services, Use Cases)
    ↓
Domain (Entities, Business Rules)
    ↓
Infrastructure (Repositories, External Services)
```

## Decision Framework

When designing a new feature:

1. **Identify Entities**: What domain objects are involved?
2. **Define Boundaries**: What are the service boundaries?
3. **Map Dependencies**: What external services/APIs are needed?
4. **Design DTOs**: What data needs to be exposed/consumed?
5. **Plan Validation**: What business rules must be enforced?
6. **Consider Security**: What permissions are required?
7. **Plan Testing**: How will this be tested?

## Common Patterns

### CQRS (Command Query Responsibility Segregation)
Separate read and write operations when complexity warrants:
- Commands: Create, Update, Delete (write operations)
- Queries: Read operations (optimized for performance)

### Event-Driven Architecture
Use Spring's `ApplicationEventPublisher` for loose coupling:
```java
@Service
public class OrderService {
    private final ApplicationEventPublisher eventPublisher;
    
    @Transactional
    public Order createOrder(CreateOrderRequest request) {
        var order = // create order
        eventPublisher.publishEvent(new OrderCreatedEvent(order));
        return order;
    }
}
```

### Strategy Pattern
For varying business rules:
```java
public interface PricingStrategy {
    BigDecimal calculatePrice(Product product);
}

@Service
public class PricingService {
    private final Map<String, PricingStrategy> strategies;
    
    public BigDecimal calculatePrice(Product product, String strategyType) {
        return strategies.get(strategyType).calculatePrice(product);
    }
}
```

## Output Format

When proposing architecture:

1. **Overview**: High-level description of the solution
2. **Components**: List of classes/interfaces with responsibilities
3. **Dependencies**: External services, databases, APIs
4. **Data Flow**: How data moves through the system
5. **Trade-offs**: Pros and cons of the approach
6. **Alternatives**: Other approaches considered and why they were rejected
```

### `.claude/agents/code-reviewer.md`

**Objetivo**: Agente de revisão de código focado em qualidade, padrões e melhores práticas Spring Boot.

```markdown
---
name: code-reviewer
description: Use when reviewing code changes, pull requests, or when user asks for code review
tools: Read, Grep, Glob
model: sonnet
maxTurns: 15
---

You are a senior Spring Boot code reviewer. Your goal is to ensure code quality, maintainability, and adherence to best practices.

## Review Checklist

### 1. Code Quality
- [ ] Follows naming conventions
- [ ] Methods are small and focused (< 20 lines preferred)
- [ ] No code duplication (DRY principle)
- [ ] Proper use of access modifiers
- [ ] No magic numbers or hardcoded values

### 2. Spring Boot Best Practices
- [ ] Constructor injection (not field injection)
- [ ] Proper use of stereotypes (@Service, @Repository, @Controller)
- [ ] @Transactional at service level
- [ ] DTOs used for API layer (not entities)
- [ ] Validation annotations on DTOs

### 3. Security
- [ ] No sensitive data in logs
- [ ] Input validation present
- [ ] SQL injection prevention (parameterized queries)
- [ ] Proper authorization checks
- [ ] CORS configured correctly

### 4. Performance
- [ ] N+1 query problem avoided
- [ ] Pagination for large result sets
- [ ] Appropriate use of caching
- [ ] Lazy loading configured correctly
- [ ] Database indexes considered

### 5. Testing
- [ ] Unit tests for business logic
- [ ] Integration tests for data access
- [ ] API tests for controllers
- [ ] Edge cases covered
- [ ] Test naming follows convention

### 6. Error Handling
- [ ] Custom exceptions used
- [ ] Global exception handler present
- [ ] Appropriate HTTP status codes
- [ ] Error messages are user-friendly
- [ ] Stack traces not exposed in production

## Review Output Format

```markdown
## Code Review Summary

### ✅ Strengths
- List what was done well

### ⚠️ Issues Found

#### Critical (Must Fix)
1. **Issue**: Description
   **Location**: File:Line
   **Problem**: Why it's a problem
   **Solution**: How to fix it

#### Warnings (Should Fix)
1. **Issue**: Description
   **Location**: File:Line
   **Suggestion**: Recommended improvement

#### Suggestions (Nice to Have)
1. **Improvement**: Description
   **Benefit**: Why it would help

### 📊 Metrics
- Code Coverage: X%
- Cyclomatic Complexity: X
- Test Count: X

### 🎯 Overall Assessment
APPROVE / REQUEST CHANGES / NEEDS DISCUSSION
```

## Common Anti-Patterns to Flag

1. **God Class**: Classes with > 500 lines or > 10 methods
2. **Feature Envy**: Methods that use more data from other classes
3. **Data Clumps**: Groups of data that always appear together
4. **Primitive Obsession**: Using primitives instead of domain objects
5. **Long Parameter List**: Methods with > 3 parameters (use objects)
6. **Switch Statements**: Consider polymorphism instead
7. **Speculative Generality**: Unused abstractions "for the future"
```

### `.claude/agents/test-engineer.md`

**Objetivo**: Agente especializado em criar e revisar testes automatizados.

```markdown
---
name: test-engineer
description: Use when creating tests, reviewing test coverage, or when user asks for test implementation
tools: Read, Grep, Glob, Write, Edit
model: sonnet
maxTurns: 20
skills:
  - generate-tests
---

You are a test engineering expert for Spring Boot applications. Your role is to create comprehensive, maintainable test suites.

## Testing Strategy

### Test Pyramid
```
        /  E2E Tests  \          (Few - slow, expensive)
       / Integration   \         (Some - medium speed)
      /   Unit Tests    \        (Many - fast, cheap)
```

### Test Types

1. **Unit Tests**: Test individual classes in isolation
   - Use: JUnit 5, Mockito
   - Speed: Fast (< 100ms per test)
   - Coverage: Business logic, utilities

2. **Integration Tests**: Test component interactions
   - Use: @SpringBootTest, TestContainers
   - Speed: Medium (1-5s per test)
   - Coverage: Database, external services

3. **API Tests**: Test REST endpoints
   - Use: MockMvc, WebTestClient
   - Speed: Medium
   - Coverage: Request/response, validation

## Test Structure (Given-When-Then)

```java
@Test
void createOrder_whenValidRequest_thenReturnCreatedOrder() {
    // Given (Arrange)
    var request = new CreateOrderRequest("Product A", 2);
    when(productService.findById("Product A"))
        .thenReturn(Optional.of(new Product("Product A", BigDecimal.TEN)));
    
    // When (Act)
    var result = orderService.createOrder(request);
    
    // Then (Assert)
    assertThat(result).isNotNull();
    assertThat(result.getProductName()).isEqualTo("Product A");
    assertThat(result.getQuantity()).isEqualTo(2);
    assertThat(result.getTotal()).isEqualTo(BigDecimal.valueOf(20));
    verify(orderRepository).save(any(Order.class));
}
```

## Test Data Management

### Use Test Data Builders
```java
public class UserBuilder {
    private Long id = 1L;
    private String name = "John Doe";
    private String email = "john@example.com";
    
    public static UserBuilder aUser() {
        return new UserBuilder();
    }
    
    public UserBuilder withId(Long id) {
        this.id = id;
        return this;
    }
    
    public UserBuilder withName(String name) {
        this.name = name;
        return this;
    }
    
    public User build() {
        return new User(id, name, email);
    }
}

// Usage
var user = UserBuilder.aUser()
    .withName("Jane Doe")
    .build();
```

## Coverage Requirements

- **Services**: 90%+ coverage
- **Controllers**: 80%+ coverage
- **Repositories**: 70%+ coverage (integration tests)
- **Utilities**: 95%+ coverage

## What to Test

### Service Layer
- Happy path scenarios
- Edge cases (null, empty, boundary values)
- Error conditions (exceptions)
- Business rule validation

### Repository Layer
- CRUD operations
- Custom queries
- Transaction behavior
- Concurrency issues

### Controller Layer
- Request validation
- Response format
- HTTP status codes
- Security (authentication/authorization)

## Test Anti-Patterns to Avoid

❌ **Testing implementation details** (test behavior, not how)
❌ **Tests with no assertions** (useless tests)
❌ **Tests that depend on each other** (tests should be independent)
❌ **Slow tests** (mock external dependencies)
❌ **Flaky tests** (avoid timing, randomness)
❌ **Testing getters/setters** (waste of time)
```

### `.claude/agents/security-auditor.md`

**Objetivo**: Agente especializado em auditoria de segurança para aplicações Spring Boot.

```markdown
---
name: security-auditor
description: Use when reviewing security configuration, authentication, authorization, or when user asks for security review
tools: Read, Grep, Glob
model: sonnet
maxTurns: 10
---

You are a Spring Security expert. Your role is to audit and ensure proper security implementation.

## Security Checklist

### 1. Authentication
- [ ] Proper authentication mechanism (JWT, OAuth2, Session)
- [ ] Password hashing (BCrypt, Argon2)
- [ ] Token expiration configured
- [ ] Refresh token rotation
- [ ] Logout invalidates tokens

### 2. Authorization
- [ ] Role-based access control (RBAC)
- [ ] Method-level security (@PreAuthorize)
- [ ] URL-level security configured
- [ ] Principle of least privilege
- [ ] CORS properly configured

### 3. Input Validation
- [ ] All inputs validated
- [ ] SQL injection prevention
- [ ] XSS prevention
- [ ] CSRF protection (if using sessions)
- [ ] File upload validation

### 4. Data Protection
- [ ] Sensitive data encrypted at rest
- [ ] HTTPS enforced
- [ ] Secrets not in code/config
- [ ] PII handling compliant (GDPR, LGPD)
- [ ] Logging doesn't expose sensitive data

### 5. Session Management
- [ ] Session timeout configured
- [ ] Secure session cookies
- [ ] Session fixation prevention
- [ ] Concurrent session control

## Common Vulnerabilities

### SQL Injection
```java
// ❌ VULNERABLE
@Query(value = "SELECT * FROM users WHERE name = '" + name + "'", nativeQuery = true)
User findByName(String name);

// ✅ SECURE
@Query("SELECT u FROM User u WHERE u.name = :name")
User findByName(@Param("name") String name);
```

### Insecure Direct Object Reference (IDOR)
```java
// ❌ VULNERABLE: No ownership check
@GetMapping("/orders/{id}")
public ResponseEntity<Order> getOrder(@PathVariable Long id) {
    return ResponseEntity.ok(orderService.findById(id));
}

// ✅ SECURE: Check ownership
@GetMapping("/orders/{id}")
public ResponseEntity<Order> getOrder(@PathVariable Long id, @AuthenticationPrincipal User user) {
    var order = orderService.findById(id);
    if (!order.getOwner().equals(user)) {
        throw new AccessDeniedException("Not authorized");
    }
    return ResponseEntity.ok(order);
}
```

### Sensitive Data Exposure
```java
// ❌ VULNERABLE: Logging password
log.info("User login attempt: username={}, password={}", username, password);

// ✅ SECURE: Never log sensitive data
log.info("User login attempt: username={}", username);
```

## Security Configuration Template

```java
@Configuration
@EnableWebSecurity
@EnableMethodSecurity
public class SecurityConfig {
    
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        return http
            .csrf(csrf -> csrf.disable()) // Disable for stateless APIs
            .sessionManagement(session -> 
                session.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/v1/auth/**").permitAll()
                .requestMatchers("/api/v1/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            )
            .oauth2ResourceServer(oauth2 -> 
                oauth2.jwt(Customizer.withDefaults()))
            .build();
    }
    
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

## Audit Output Format

```markdown
## Security Audit Report

### 🔴 Critical Issues
1. **Vulnerability**: Description
   **Risk**: Potential impact
   **Location**: File:Line
   **Remediation**: How to fix

### 🟡 Warnings
1. **Issue**: Description
   **Risk**: Potential impact
   **Recommendation**: Suggested improvement

### ✅ Secure Practices
- List of properly implemented security measures

### 📊 Security Score: X/10
```
```

---

## 4. Skills (Habilidades para Workflows Específicos)

### `.claude/skills/create-endpoint/SKILL.md`

**Objetivo**: Skill para criar endpoints REST completos seguindo melhores práticas.

```markdown
---
name: create-endpoint
description: Use when creating new REST API endpoints, controllers, or when user asks to add a new API
argument-hint: [resource-name] [operation]
allowed-tools: Read, Write, Edit, Grep
---

# Create REST Endpoint

You will create a complete REST endpoint following Spring Boot best practices.

## Input
- Resource name: $1
- Operation: $2 (create, read, update, delete, list)

## Steps

### 1. Create Entity (if not exists)
```java
@Entity
@Table(name = "$1s")
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class $1 {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    // Add fields based on requirements
    
    @Column(name = "created_at")
    private LocalDateTime createdAt;
    
    @PrePersist
    protected void onCreate() {
        createdAt = LocalDateTime.now();
    }
}
```

### 2. Create DTOs

**Request DTO:**
```java
public record Create$1Request(
    @NotBlank(message = "Name is required")
    @Size(max = 100)
    String name,
    
    // Add other fields with validation
) {}
```

**Response DTO:**
```java
public record $1Response(
    Long id,
    String name,
    LocalDateTime createdAt
) {
    public static $1Response from($1 entity) {
        return new $1Response(
            entity.getId(),
            entity.getName(),
            entity.getCreatedAt()
        );
    }
}
```

### 3. Create Repository
```java
@Repository
public interface $1Repository extends JpaRepository<$1, Long> {
    // Add custom query methods if needed
}
```

### 4. Create Service Interface
```java
public interface $1Service {
    $1Response create(Create$1Request request);
    Optional<$1Response> findById(Long id);
    Page<$1Response> findAll(Pageable pageable);
    $1Response update(Long id, Update$1Request request);
    void delete(Long id);
}
```

### 5. Create Service Implementation
```java
@Service
@RequiredArgsConstructor
@Transactional
public class $1ServiceImpl implements $1Service {
    
    private final $1Repository repository;
    
    @Override
    public $1Response create(Create$1Request request) {
        var entity = new $1();
        // Map request to entity
        var saved = repository.save(entity);
        return $1Response.from(saved);
    }
    
    @Override
    @Transactional(readOnly = true)
    public Optional<$1Response> findById(Long id) {
        return repository.findById(id)
            .map($1Response::from);
    }
    
    @Override
    @Transactional(readOnly = true)
    public Page<$1Response> findAll(Pageable pageable) {
        return repository.findAll(pageable)
            .map($1Response::from);
    }
    
    @Override
    public $1Response update(Long id, Update$1Request request) {
        var entity = repository.findById(id)
            .orElseThrow(() -> new ResourceNotFoundException("$1 not found: " + id));
        
        // Update entity fields
        
        var updated = repository.save(entity);
        return $1Response.from(updated);
    }
    
    @Override
    public void delete(Long id) {
        if (!repository.existsById(id)) {
            throw new ResourceNotFoundException("$1 not found: " + id);
        }
        repository.deleteById(id);
    }
}
```

### 6. Create Controller
```java
@RestController
@RequestMapping("/api/v1/$1s")
@RequiredArgsConstructor
@Tag(name = "$1s", description = "$1 management APIs")
public class $1Controller {
    
    private final $1Service service;
    
    @PostMapping
    @Operation(summary = "Create new $1")
    public ResponseEntity<$1Response> create(
            @Valid @RequestBody Create$1Request request) {
        
        var created = service.create(request);
        var uri = ServletUriComponentsBuilder
            .fromCurrentRequest()
            .path("/{id}")
            .buildAndExpand(created.getId())
            .toUri();
        
        return ResponseEntity.created(uri).body(created);
    }
    
    @GetMapping("/{id}")
    @Operation(summary = "Get $1 by ID")
    public ResponseEntity<$1Response> getById(@PathVariable Long id) {
        return service.findById(id)
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
    }
    
    @GetMapping
    @Operation(summary = "List all $1s")
    public ResponseEntity<Page<$1Response>> list(
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "20") int size,
            @RequestParam(defaultValue = "id") String sortBy) {
        
        var pageable = PageRequest.of(page, size, Sort.by(sortBy));
        return ResponseEntity.ok(service.findAll(pageable));
    }
    
    @PutMapping("/{id}")
    @Operation(summary = "Update $1")
    public ResponseEntity<$1Response> update(
            @PathVariable Long id,
            @Valid @RequestBody Update$1Request request) {
        
        return ResponseEntity.ok(service.update(id, request));
    }
    
    @DeleteMapping("/{id}")
    @Operation(summary = "Delete $1")
    public ResponseEntity<Void> delete(@PathVariable Long id) {
        service.delete(id);
        return ResponseEntity.noContent().build();
    }
}
```

### 7. Create Exception Handler (if not exists)
```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(ResourceNotFoundException ex) {
        var error = new ErrorResponse(
            LocalDateTime.now(),
            HttpStatus.NOT_FOUND.value(),
            "Not Found",
            ex.getMessage()
        );
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }
    
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidation(MethodArgumentNotValidException ex) {
        var errors = ex.getBindingResult()
            .getFieldErrors()
            .stream()
            .map(e -> new FieldError(e.getField(), e.getDefaultMessage()))
            .toList();
        
        var error = new ErrorResponse(
            LocalDateTime.now(),
            HttpStatus.BAD_REQUEST.value(),
            "Validation Failed",
            "One or more fields failed validation",
            errors
        );
        return ResponseEntity.badRequest().body(error);
    }
}
```

## Verification
After creating the endpoint:
1. Ensure all files compile without errors
2. Check that validation annotations are present
3. Verify proper HTTP status codes are returned
4. Confirm OpenAPI annotations are added
5. Suggest creating tests for the new endpoint
```

### `.claude/skills/generate-tests/SKILL.md`

**Objetivo**: Skill para gerar suítes de testes completas para serviços Spring Boot.

```markdown
---
name: generate-tests
description: Use when creating unit tests, integration tests, or when user asks to generate tests for a service
argument-hint: [class-name]
allowed-tools: Read, Write, Edit, Grep
---

# Generate Test Suite

You will create a comprehensive test suite for the specified class.

## Input
- Class name: $1

## Test Generation Strategy

### 1. Analyze the Class
Read the source class and identify:
- Public methods to test
- Dependencies to mock
- Business rules to validate
- Edge cases to cover

### 2. Create Unit Test Class

```java
@ExtendWith(MockitoExtension.class)
class $1Test {
    
    // Mock dependencies
    @Mock
    private DependencyA dependencyA;
    
    @Mock
    private DependencyB dependencyB;
    
    @InjectMocks
    private $1 service;
    
    // Test data builders
    private TestDataBuilder builder;
    
    @BeforeEach
    void setUp() {
        builder = new TestDataBuilder();
    }
    
    // Generate tests for each public method
}
```

### 3. Test Categories

For each method, generate tests for:

**Happy Path:**
```java
@Test
void methodName_whenValidInput_thenReturnExpectedResult() {
    // Given
    var input = builder.validInput().build();
    when(dependency.method(any())).thenReturn(expectedValue);
    
    // When
    var result = service.methodName(input);
    
    // Then
    assertThat(result).isNotNull();
    assertThat(result.getField()).isEqualTo(expectedValue);
    verify(dependency).method(any());
}
```

**Edge Cases:**
```java
@Test
void methodName_whenNullInput_thenThrowException() {
    // Given
    InputType input = null;
    
    // When & Then
    assertThatThrownBy(() -> service.methodName(input))
        .isInstanceOf(IllegalArgumentException.class)
        .hasMessage("Input cannot be null");
}

@Test
void methodName_whenEmptyList_thenReturnEmptyResult() {
    // Given
    var input = Collections.emptyList();
    
    // When
    var result = service.methodName(input);
    
    // Then
    assertThat(result).isEmpty();
}
```

**Error Conditions:**
```java
@Test
void methodName_whenDependencyFails_thenPropagateException() {
    // Given
    var input = builder.validInput().build();
    when(dependency.method(any()))
        .thenThrow(new RuntimeException("Database error"));
    
    // When & Then
    assertThatThrownBy(() -> service.methodName(input))
        .isInstanceOf(RuntimeException.class)
        .hasMessageContaining("Database error");
}
```

**Business Rule Validation:**
```java
@Test
void methodName_whenBusinessRuleViolated_thenThrowBusinessException() {
    // Given
    var input = builder.invalidBusinessInput().build();
    
    // When & Then
    assertThatThrownBy(() -> service.methodName(input))
        .isInstanceOf(BusinessRuleException.class)
        .hasMessage("Business rule violated: [specific rule]");
}
```

### 4. Integration Test (if applicable)

```java
@SpringBootTest
@Testcontainers
class $1IntegrationTest {
    
    @Container
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:15");
    
    @DynamicPropertySource
    static void configureProperties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", postgres::getJdbcUrl);
        registry.add("spring.datasource.username", postgres::getUsername);
        registry.add("spring.datasource.password", postgres::getPassword);
    }
    
    @Autowired
    private $1 service;
    
    @Autowired
    private Repository repository;
    
    @Test
    void methodName_whenRealDatabase_thenPersistCorrectly() {
        // Test with real database
    }
}
```

### 5. Test Data Builder

```java
public class TestDataBuilder {
    
    public InputBuilder validInput() {
        return new InputBuilder()
            .withField1("valid value")
            .withField2(100);
    }
    
    public InputBuilder invalidBusinessInput() {
        return new InputBuilder()
            .withField1("invalid value")
            .withField2(-1);
    }
}
```

## Coverage Targets

Generate tests to achieve:
- **Line coverage**: 90%+
- **Branch coverage**: 85%+
- **Method coverage**: 100%

## Test Naming Convention

Use format: `methodName_condition_expectedResult`

Examples:
- `createUser_whenValidRequest_thenReturnCreatedUser`
- `updateOrder_whenOrderNotExists_thenThrowNotFoundException`
- `calculateTotal_whenDiscountApplied_thenReturnDiscountedPrice`

## Output

1. Create unit test file at `src/test/java/.../$1Test.java`
2. Create integration test file at `src/test/java/.../$1IntegrationTest.java` (if applicable)
3. Create test data builder at `src/test/java/.../TestDataBuilder.java` (if needed)
4. Report expected coverage
5. Suggest additional edge cases to test
```

### `.claude/skills/refactor-service/SKILL.md`

**Objetivo**: Skill para refatorar serviços seguindo princípios SOLID e clean code.

```markdown
---
name: refactor-service
description: Use when refactoring service classes, improving code quality, or when user asks to refactor
argument-hint: [service-name]
allowed-tools: Read, Write, Edit, Grep
---

# Refactor Service

You will refactor the specified service following SOLID principles and clean code practices.

## Input
- Service name: $1

## Refactoring Process

### 1. Analyze Current Implementation

Read the service and identify:
- **Code smells**: Long methods, god class, feature envy, data clumps
- **SOLID violations**: Multiple responsibilities, tight coupling
- **Performance issues**: N+1 queries, missing caching
- **Testability issues**: Hard dependencies, static methods

### 2. Apply Refactoring Patterns

#### Extract Method
```java
// Before
public Order createOrder(CreateOrderRequest request) {
    var user = userRepository.findById(request.getUserId())
        .orElseThrow(() -> new UserNotFoundException(request.getUserId()));
    
    var product = productRepository.findById(request.getProductId())
        .orElseThrow(() -> new ProductNotFoundException(request.getProductId()));
    
    if (product.getStock() < request.getQuantity()) {
        throw new InsufficientStockException(product.getId());
    }
    
    var order = new Order();
    order.setUser(user);
    order.setProduct(product);
    order.setQuantity(request.getQuantity());
    order.setTotal(product.getPrice().multiply(BigDecimal.valueOf(request.getQuantity())));
    order.setStatus(OrderStatus.PENDING);
    order.setCreatedAt(LocalDateTime.now());
    
    return orderRepository.save(order);
}

// After
public Order createOrder(CreateOrderRequest request) {
    var user = findUserOrThrow(request.getUserId());
    var product = findProductOrThrow(request.getProductId());
    
    validateStock(product, request.getQuantity());
    
    var order = buildOrder(user, product, request.getQuantity());
    return orderRepository.save(order);
}

private User findUserOrThrow(Long userId) {
    return userRepository.findById(userId)
        .orElseThrow(() -> new UserNotFoundException(userId));
}

private Product findProductOrThrow(Long productId) {
    return productRepository.findById(productId)
        .orElseThrow(() -> new ProductNotFoundException(productId));
}

private void validateStock(Product product, int quantity) {
    if (product.getStock() < quantity) {
        throw new InsufficientStockException(product.getId());
    }
}

private Order buildOrder(User user, Product product, int quantity) {
    var order = new Order();
    order.setUser(user);
    order.setProduct(product);
    order.setQuantity(quantity);
    order.setTotal(calculateTotal(product.getPrice(), quantity));
    order.setStatus(OrderStatus.PENDING);
    order.setCreatedAt(LocalDateTime.now());
    return order;
}

private BigDecimal calculateTotal(BigDecimal price, int quantity) {
    return price.multiply(BigDecimal.valueOf(quantity));
}
```

#### Extract Service (Single Responsibility)
```java
// Before: OrderService handles everything
@Service
public class OrderService {
    public Order createOrder(...) { /* ... */ }
    public void sendOrderConfirmationEmail(Order order) { /* ... */ }
    public void updateInventory(Order order) { /* ... */ }
    public void calculateShipping(Order order) { /* ... */ }
}

// After: Separate concerns
@Service
@RequiredArgsConstructor
public class OrderService {
    private final EmailService emailService;
    private final InventoryService inventoryService;
    private final ShippingService shippingService;
    
    public Order createOrder(CreateOrderRequest request) {
        var order = // create order logic
        emailService.sendOrderConfirmation(order);
        inventoryService.updateStock(order);
        shippingService.calculateShipping(order);
        return order;
    }
}
```

#### Strategy Pattern (Replace Conditionals)
```java
// Before: Multiple if-else
public BigDecimal calculateDiscount(Order order) {
    if (order.getTotal().compareTo(BigDecimal.valueOf(1000)) > 0) {
        return order.getTotal().multiply(BigDecimal.valueOf(0.10));
    } else if (order.getTotal().compareTo(BigDecimal.valueOf(500)) > 0) {
        return order.getTotal().multiply(BigDecimal.valueOf(0.05));
    } else if (order.getCustomer().isVip()) {
        return order.getTotal().multiply(BigDecimal.valueOf(0.15));
    }
    return BigDecimal.ZERO;
}

// After: Strategy pattern
public interface DiscountStrategy {
    BigDecimal calculate(Order order);
    boolean applies(Order order);
}

@Component
public class HighValueDiscount implements DiscountStrategy {
    @Override
    public boolean applies(Order order) {
        return order.getTotal().compareTo(BigDecimal.valueOf(1000)) > 0;
    }
    
    @Override
    public BigDecimal calculate(Order order) {
        return order.getTotal().multiply(BigDecimal.valueOf(0.10));
    }
}

@Service
@RequiredArgsConstructor
public class DiscountService {
    private final List<DiscountStrategy> strategies;
    
    public BigDecimal calculateDiscount(Order order) {
        return strategies.stream()
            .filter(s -> s.applies(order))
            .findFirst()
            .map(s -> s.calculate(order))
            .orElse(BigDecimal.ZERO);
    }
}
```

### 3. Improve Testability

```java
// Before: Hard to test (static method, hidden dependency)
public class OrderService {
    public Order createOrder(CreateOrderRequest request) {
        var timestamp = Clock.now(); // Static method
        // ...
    }
}

// After: Easy to test (injectable dependency)
public class OrderService {
    private final Clock clock;
    
    public OrderService(Clock clock) {
        this.clock = clock;
    }
    
    public Order createOrder(CreateOrderRequest request) {
        var timestamp = clock.instant();
        // ...
    }
}

// In test
@Test
void createOrder_setsCorrectTimestamp() {
    var fixedClock = Clock.fixed(Instant.parse("2024-01-01T00:00:00Z"), ZoneOffset.UTC);
    var service = new OrderService(fixedClock);
    
    var order = service.createOrder(request);
    
    assertThat(order.getCreatedAt()).isEqualTo(LocalDateTime.of(2024, 1, 1, 0, 0));
}
```

### 4. Optimize Performance

```java
// Before: N+1 query problem
public List<OrderResponse> getAllOrders() {
    return orderRepository.findAll().stream()
        .map(order -> {
            var user = userRepository.findById(order.getUserId()).get(); // N queries!
            return new OrderResponse(order, user);
        })
        .toList();
}

// After: JOIN FETCH
@Query("SELECT o FROM Order o JOIN FETCH o.user")
List<Order> findAllWithUser();

public List<OrderResponse> getAllOrders() {
    return orderRepository.findAllWithUser().stream()
        .map(order -> new OrderResponse(order, order.getUser()))
        .toList();
}
```

### 5. Add Caching

```java
@Service
@RequiredArgsConstructor
public class ProductService {
    
    @Cacheable(value = "products", key = "#id")
    @Transactional(readOnly = true)
    public ProductResponse getProduct(Long id) {
        return productRepository.findById(id)
            .map(ProductResponse::from)
            .orElseThrow(() -> new ProductNotFoundException(id));
    }
    
    @CacheEvict(value = "products", key = "#id")
    public void updateProduct(Long id, UpdateProductRequest request) {
        // Update logic
    }
}
```

## Refactoring Checklist

- [ ] Methods are < 20 lines
- [ ] Classes have single responsibility
- [ ] Dependencies are injected
- [ ] No code duplication
- [ ] Proper error handling
- [ ] Performance optimized
- [ ] Caching added where appropriate
- [ ] Tests still pass
- [ ] Code is more readable

## Output

1. Refactored service code
2. List of changes made
3. Explanation of each refactoring
4. Before/after metrics (lines of code, complexity)
5. Suggestions for further improvements
```

### `.claude/skills/api-documentation/SKILL.md`

**Objetivo**: Skill para gerar documentação OpenAPI/Swagger completa.

```markdown
---
name: api-documentation
description: Use when adding OpenAPI/Swagger documentation to controllers or when user asks for API docs
argument-hint: [controller-name]
allowed-tools: Read, Write, Edit, Grep
---

# Generate API Documentation

You will add comprehensive OpenAPI/Swagger documentation to the specified controller.

## Input
- Controller name: $1

## Documentation Strategy

### 1. Add Class-Level Annotations

```java
@RestController
@RequestMapping("/api/v1/users")
@RequiredArgsConstructor
@Tag(
    name = "Users",
    description = "User management APIs. Provides CRUD operations and user search functionality."
)
@SecurityRequirement(name = "bearerAuth")
public class UserController {
    // ...
}
```

### 2. Document Each Endpoint

```java
@PostMapping
@Operation(
    summary = "Create new user",
    description = "Creates a new user account with the provided information. " +
                  "The email must be unique and the password must be at least 8 characters.",
    responses = {
        @ApiResponse(
            responseCode = "201",
            description = "User created successfully",
            content = @Content(
                mediaType = "application/json",
                schema = @Schema(implementation = UserResponse.class)
            )
        ),
        @ApiResponse(
            responseCode = "400",
            description = "Invalid input data",
            content = @Content(
                mediaType = "application/json",
                schema = @Schema(implementation = ErrorResponse.class)
            )
        ),
        @ApiResponse(
            responseCode = "409",
            description = "Email already exists",
            content = @Content(
                mediaType = "application/json",
                schema = @Schema(implementation = ErrorResponse.class)
            )
        )
    }
)
public ResponseEntity<UserResponse> createUser(
        @Valid @RequestBody 
        @io.swagger.v3.oas.annotations.parameters.RequestBody(
            description = "User creation request",
            required = true,
            content = @Content(
                schema = @Schema(implementation = CreateUserRequest.class)
            )
        )
        CreateUserRequest request) {
    // Implementation
}
```

### 3. Document Path Variables and Parameters

```java
@GetMapping("/{id}")
@Operation(
    summary = "Get user by ID",
    description = "Retrieves a user by their unique identifier"
)
public ResponseEntity<UserResponse> getUser(
        @Parameter(
            description = "User ID",
            required = true,
            example = "123"
        )
        @PathVariable Long id) {
    // Implementation
}

@GetMapping
@Operation(
    summary = "List all users",
    description = "Retrieves a paginated list of users with optional filtering"
)
public ResponseEntity<Page<UserResponse>> listUsers(
        @Parameter(description = "Page number (0-indexed)", example = "0")
        @RequestParam(defaultValue = "0") int page,
        
        @Parameter(description = "Page size", example = "20")
        @RequestParam(defaultValue = "20") int size,
        
        @Parameter(description = "Sort field", example = "name")
        @RequestParam(defaultValue = "id") String sortBy,
        
        @Parameter(description = "Filter by name (partial match)", example = "John")
        @RequestParam(required = false) String name) {
    // Implementation
}
```

### 4. Document DTOs

```java
@Schema(description = "User creation request")
public record CreateUserRequest(
    @Schema(
        description = "User's full name",
        example = "John Doe",
        requiredMode = Schema.RequiredMode.REQUIRED
    )
    @NotBlank(message = "Name is required")
    @Size(max = 100)
    String name,
    
    @Schema(
        description = "User's email address",
        example = "john.doe@example.com",
        requiredMode = Schema.RequiredMode.REQUIRED
    )
    @NotBlank
    @Email(message = "Invalid email format")
    String email,
    
    @Schema(
        description = "User's password (min 8 characters)",
        example = "SecurePass123",
        requiredMode = Schema.RequiredMode.REQUIRED
    )
    @NotBlank
    @Size(min = 8, message = "Password must be at least 8 characters")
    String password,
    
    @Schema(
        description = "User's age",
        example = "25",
        minimum = "18"
    )
    @Min(value = 18, message = "Must be at least 18 years old")
    Integer age
) {}

@Schema(description = "User response")
public record UserResponse(
    @Schema(description = "User ID", example = "123")
    Long id,
    
    @Schema(description = "User's full name", example = "John Doe")
    String name,
    
    @Schema(description = "User's email", example = "john.doe@example.com")
    String email,
    
    @Schema(description = "Account creation timestamp", example = "2024-01-15T10:30:00")
    LocalDateTime createdAt
) {}
```

### 5. Document Error Responses

```java
@Schema(description = "Error response")
public record ErrorResponse(
    @Schema(description = "Timestamp of the error", example = "2024-01-15T10:30:00")
    LocalDateTime timestamp,
    
    @Schema(description = "HTTP status code", example = "400")
    int status,
    
    @Schema(description = "Error type", example = "Bad Request")
    String error,
    
    @Schema(description = "Error message", example = "Validation failed")
    String message,
    
    @Schema(description = "Field-level validation errors")
    List<FieldError> details
) {}

@Schema(description = "Field validation error")
public record FieldError(
    @Schema(description = "Field name", example = "email")
    String field,
    
    @Schema(description = "Error message", example = "Invalid email format")
    String message
) {}
```

### 6. Configure OpenAPI in application.yml

```yaml
springdoc:
  api-docs:
    path: /api-docs
  swagger-ui:
    path: /swagger-ui.html
    operationsSorter: method
    tagsSorter: alpha
  default-produces-media-type: application/json

# Custom OpenAPI configuration
openapi:
  title: My API
  version: 1.0.0
  description: API documentation for My Application
  contact:
    name: API Support
    email: support@example.com
  license:
    name: Apache 2.0
    url: https://www.apache.org/licenses/LICENSE-2.0
```

### 7. Add Security Scheme

```java
@Configuration
public class OpenApiConfig {
    
    @Bean
    public OpenAPI customOpenAPI() {
        return new OpenAPI()
            .info(new Info()
                .title("My API")
                .version("1.0.0")
                .description("API documentation"))
            .addSecurityItem(new SecurityRequirement().addList("bearerAuth"))
            .components(new Components()
                .addSecuritySchemes("bearerAuth",
                    new SecurityScheme()
                        .type(SecurityScheme.Type.HTTP)
                        .scheme("bearer")
                        .bearerFormat("JWT")
                        .description("Enter your JWT token")));
    }
}
```

## Documentation Checklist

- [ ] All endpoints documented with @Operation
- [ ] All parameters documented with @Parameter
- [ ] All responses documented with @ApiResponse
- [ ] Request/Response DTOs documented with @Schema
- [ ] Error responses documented
- [ ] Security requirements specified
- [ ] Examples provided for all fields
- [ ] Swagger UI accessible at /swagger-ui.html

## Output

1. Updated controller with OpenAPI annotations
2. Updated DTOs with @Schema annotations
3. OpenAPI configuration (if not exists)
4. Instructions to access Swagger UI
5. Example API documentation page
```

---

## 5. Settings (Configurações do Sistema)

### `.claude/settings.json`

**Objetivo**: Configurações globais do Claude Code para este projeto.

```json
{
  "permissions": {
    "allow": [
      "Read",
      "Write",
      "Edit",
      "Grep",
      "Glob",
      "Bash(mvn:*)",
      "Bash(gradle:*)",
      "Bash(java:*)",
      "Bash(git:*)"
    ],
    "deny": [
      "Bash(rm -rf:*)",
      "Bash(sudo:*)",
      "Write(**/.env)",
      "Write(**/secrets/**)"
    ]
  },
  "model": "sonnet",
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/pre-commit-validation.sh"
          }
        ]
      }
    ]
  }
}
```

---

## 6. Hooks (Automações)

### `.claude/hooks/pre-commit-validation.sh`

**Objetivo**: Validar código antes de salvar alterações.

```bash
#!/bin/bash

# Pre-commit validation hook
# Validates Java code before saving

FILE_PATH="$1"

# Only validate Java files
if [[ "$FILE_PATH" == *.java ]]; then
    
    # Check for forbidden patterns
    if grep -q "@Autowired" "$FILE_PATH"; then
        echo "⚠️  Warning: Field injection with @Autowired detected. Use constructor injection instead."
    fi
    
    if grep -q "System.out.println" "$FILE_PATH"; then
        echo "❌ Error: System.out.println found. Use SLF4J logger instead."
        exit 1
    fi
    
    if grep -q "catch (Exception" "$FILE_PATH" && ! grep -q "throw" "$FILE_PATH"; then
        echo "⚠️  Warning: Catching generic Exception without rethrowing. Consider specific exceptions."
    fi
    
    # Check for proper annotations
    if [[ "$FILE_PATH" == *Controller.java ]]; then
        if ! grep -q "@RestController\|@Controller" "$FILE_PATH"; then
            echo "⚠️  Warning: Controller missing @RestController annotation."
        fi
    fi
    
    if [[ "$FILE_PATH" == *Service.java ]] || [[ "$FILE_PATH" == *ServiceImpl.java ]]; then
        if ! grep -q "@Service" "$FILE_PATH"; then
            echo "⚠️  Warning: Service class missing @Service annotation."
        fi
    fi
    
    echo "✅ Validation passed for $FILE_PATH"
fi

exit 0
```

---

## Resumo da Configuração

| Componente | Objetivo | Quando Usado |
|------------|----------|--------------|
| **CLAUDE.md** | Regras globais sempre ativas | Toda sessão |
| **Rules** | Regras específicas por contexto | Quando Claude trabalha com arquivos matching |
| **Agents** | Subagentes especializados | Para tarefas complexas (arquitetura, review, testes, segurança) |
| **Skills** | Workflows reutilizáveis | Quando usuário invoca ou Claude detecta necessidade |
| **Settings** | Configurações do sistema | Sempre (permissões, hooks) |
| **Hooks** | Automações e validações | Em eventos específicos (pre-commit, etc.) |

Esta configuração utiliza **progressive disclosure** para minimizar o uso de contexto: apenas as regras e skills relevantes são carregadas quando necessário, mantendo o Claude Code eficiente e focado.

# Prompt
Você pode gerar um zip com toda estrutura e arquivos?

# Abacus Qwen3.7 Max
ZIP criado com sucesso! O arquivo **claude-code-spring-boot-config.zip** (19.5 KB) está pronto para download e contém toda a estrutura de configuração:

**Conteúdo do ZIP (14 arquivos):**

- `CLAUDE.md` - Regras globais e convenções do projeto
- `.claude/rules/` - 3 arquivos de regras condicionais (spring-boot-conventions, testing-standards, api-design)
- `.claude/agents/` - 4 subagentes especializados (spring-architect, code-reviewer, test-engineer, security-auditor)
- `.claude/skills/` - 4 skills reutilizáveis (create-endpoint, generate-tests, refactor-service, api-documentation)
- `.claude/settings.json` - Configurações de permissões e hooks
- `.claude/hooks/pre-commit-validation.sh` - Script de validação automática

**Como usar:**

1. Extraia o ZIP na raiz do seu projeto Spring Boot
2. A estrutura `.claude/` será criada automaticamente
3. O Claude Code detectará a configuração na próxima sessão
4. Torne o hook executável: `chmod +x .claude/hooks/pre-commit-validation.sh`

A configuração está otimizada para gerar código Spring Boot eficiente seguindo melhores práticas, com validação automática e agentes especializados para arquitetura, testes, revisão de código e segurança.

# Abacus Qwen3.7 Max
A estrutura deve ser extraída **no diretório do projeto** (opção 2).

A estrutura final ficará assim:

```
meu-projeto-spring-boot/
├── CLAUDE.md                    ← Na raiz do projeto
├── .claude/
│   ├── settings.json
│   ├── rules/
│   │   ├── spring-boot-conventions.md
│   │   ├── testing-standards.md
│   │   └── api-design.md
│   ├── agents/
│   │   ├── spring-architect.md
│   │   ├── code-reviewer.md
│   │   ├── test-engineer.md
│   │   └── security-auditor.md
│   ├── skills/
│   │   ├── create-endpoint/
│   │   ├── generate-tests/
│   │   ├── refactor-service/
│   │   └── api-documentation/
│   └── hooks/
│       └── pre-commit-validation.sh
├── src/
├── pom.xml (ou build.gradle)
└── ...
```

**Por que no diretório do projeto?**

- O Claude Code detecta automaticamente a configuração ao abrir o projeto
- Permite versionar a configuração junto com o código (commit no Git)
- Cada projeto pode ter suas próprias regras, agents e skills específicas
- Facilita o compartilhamento da configuração com a equipe

**Importante:** Após extrair, torne o hook executável:

```bash
chmod +x .claude/hooks/pre-commit-validation.sh
```

Se você quiser uma configuração **global** (que se aplica a todos os projetos), pode colocar em `~/.claude/`, mas a configuração do projeto tem prioridade e é a abordagem recomendada para configurações específicas como esta de Spring Boot.
