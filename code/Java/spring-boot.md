# Example 1
## Prompt
Act as a Senior Spring Boot Developer. Generate an exhaustive boilerplate for a Spring Boot RESTful API application, ready for enterprise deployment.
Include:
1. A `/api/v1/products` endpoint for CRUD operations on a `Product` entity (id: Long, name: String, description: String, price: BigDecimal, stock: Integer).
2. Architecture: controller, service, repository, model, config, and util packages.
3. Software versions: Spring Boot 3.2.x, Java 21, Maven 3.9.x.
4. Database: PostgreSQL with Spring Data JPA.
5. Logging: Configured with SLF4J and Logback for fine-grained logs, including custom log formats.
6. API Documentation: Swagger/OpenAPI integration for all endpoints, configured in a `SwaggerConfig` class.
7. Best Practices: Adherence to SOLID principles, use of DTOs for API request/response with validation annotations (@NotNull, @Size, @Min), common API response wrapper (status, message, data), and layered architecture for maintainability and separation of concerns.
8. Add explanatory comments for all sections.
9. Do not use Lombok

