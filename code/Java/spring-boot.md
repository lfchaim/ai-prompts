# Example 1
## Prompt
Act as a Senior Spring Boot Developer. Generate an exhaustive boilerplate for a Spring Boot RESTful API application, ready for enterprise deployment.  
Include:
1. A `/api/v1/products` endpoint for CRUD operations on a `Product` entity (id: Long, name: String, description: String, price: BigDecimal, stock: Integer).
2. Architecture: controller, service, repository, model, config, and util packages.
3. Software versions: Spring Boot 3.2.x, Java 21, Maven 3.9.x.
4. Database: PostgreSQL with Spring Data JPA.
5. Create Docker Compose configuration for the specified database.
6. Logging: Configured with SLF4J and Logback for fine-grained logs, including custom log formats.
7. API Documentation: Swagger/OpenAPI integration for all endpoints, configured in a `SwaggerConfig` class.
8. Best Practices: Adherence to SOLID principles, use of DTOs for API request/response with validation annotations (@NotNull, @Size, @Min), common API response wrapper (status, message, data), and layered architecture for maintainability and separation of concerns.
9. Add explanatory comments for all sections.
10. Do not use Lombok

Atue como Desenvolvedor Sênior Spring Boot. Gere um boilerplate completo para uma aplicação Spring Boot RESTful API, pronta para implantação em ambiente corporativo.  
Inclua:
1. Um endpoint `/api/v1/products` para operações CRUD em uma entidade `Product` (id: Long, name: String, description: String, price: BigDecimal, stock: Integer).
2. Arquitetura: pacotes de controlador, serviço, repositório, modelo, configuração e utilitários.
3. Versões do software: Spring Boot 3.2.x, Java 21, Maven 3.9.x.
4. Banco de dados: PostgreSQL com Spring Data JPA.
5. Crie uma configuração do Docker Compose para o banco de dados especificado.
6. Logs: Configurado com SLF4J e Logback para logs detalhados, incluindo formatos de log personalizados.
6.Documentação da API: Integração com Swagger/OpenAPI para todos os endpoints, configurada em uma classe `SwaggerConfig`.
7. Melhores Práticas: Adesão aos princípios SOLID, uso de DTOs para requisições/respostas de API com anotações de validação (@NotNull, @Size, @Min), wrapper de resposta de API comum (status, mensagem, dados) e arquitetura em camadas para facilitar a manutenção e a separação de responsabilidades.
8. Adicione comentários explicativos para todas as seções.
9. Não use Lombok.

# Example 2
## Prompt
Act as a Senior Spring Boot Developer. Generate an exhaustive boilerplate for a Spring Boot RESTful API application, ready for enterprise deployment.
Include:
1. A /api/v1/[DATA_SOURCE_ID]/[TABLE_NAME] endpoint for CRUD operations for each table found in DataBase. The DATA_SOURCE_ID represents a JDBC Database configuration (key_source, url, user and password) stored in PostgreSQL table named "data_source".
1.1. When the application up, it must load all data_source using Hikari Connection Pool and key_source as key of Pool
2. Architecture: controller, service, repository, model, config, and util packages.
3. Software versions: Spring Boot 3.2.x, Java 21, Maven 3.9.x.
4. Database: PostgreSQL with JDBC implementation.
5. Create Docker Compose configuration for the specified database.
6. Logging: Configured with SLF4J and Logback for fine-grained logs, including custom log formats.
7. API Documentation: Swagger/OpenAPI integration for all endpoints, configured in a SwaggerConfig class.
8. Best Practices: Adherence to SOLID principles, use of DTOs for API request/response with validation annotations (@NotNull, @Size, @Min), common API response wrapper (status, message, data), and layered architecture for maintainability and separation of concerns.
9. Add explanatory comments for all sections.
10. Use Spring Actuator and Health Check
11. Do not use Lombok

## Enhanced
Act as a Senior Spring Boot Developer. Generate an exhaustive, enterprise-ready boilerplate for a Spring Boot RESTful API application.

Requirements:
1. Create a generic REST API structure that exposes a /api/v1/[DATA_SOURCE_ID]/[TABLE_NAME] endpoint for CRUD operations for every table discovered in the database.
2. The DATA_SOURCE_ID must represent a JDBC database configuration stored in a PostgreSQL table named data_source, with the fields key_source, url, user, and password.
3. On application startup, load all data_source records and initialize a Hikari connection pool for each one, using key_source as the pool key.
4. Use a layered architecture with the following packages:
   - controller
   - service
   - repository
   - model
   - config
   - util
5. Target the following software versions:
   - Spring Boot 3.2.x
   - Java 21
   - Maven 3.9.x
6. Use PostgreSQL with JDBC implementation.
7. Include a Docker Compose configuration for the database.
8. Configure logging with SLF4J and Logback, including fine-grained logs and custom log formats.
9. Integrate Swagger/OpenAPI for all endpoints, configured in a SwaggerConfig class.
10. Follow best practices:
   - SOLID principles
   - DTOs for API request and response objects
   - validation annotations such as @NotNull, @Size, and @Min
   - a common API response wrapper containing status, message, and data
   - maintainable layered architecture with clear separation of concerns
11. Include Spring Actuator and health check endpoints.
12. Do not use Lombok.
13. Add explanatory comments for all sections.
Provide the complete boilerplate with a clear, organized structure suitable for direct enterprise use.

