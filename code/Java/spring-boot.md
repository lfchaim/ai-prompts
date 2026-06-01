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

Atue como Desenvolvedor Sênior Spring Boot. Gere um boilerplate completo para uma aplicação Spring Boot RESTful API, pronta para implantação em ambiente corporativo.  
Inclua:
1. Um endpoint `/api/v1/products` para operações CRUD em uma entidade `Product` (id: Long, name: String, description: String, price: BigDecimal, stock: Integer).
2. Arquitetura: pacotes de controlador, serviço, repositório, modelo, configuração e utilitários.
3. Versões do software: Spring Boot 3.2.x, Java 21, Maven 3.9.x.
4. Banco de dados: PostgreSQL com Spring Data JPA.
5. Logs: Configurado com SLF4J e Logback para logs detalhados, incluindo formatos de log personalizados.
6.Documentação da API: Integração com Swagger/OpenAPI para todos os endpoints, configurada em uma classe `SwaggerConfig`.
7. Melhores Práticas: Adesão aos princípios SOLID, uso de DTOs para requisições/respostas de API com anotações de validação (@NotNull, @Size, @Min), wrapper de resposta de API comum (status, mensagem, dados) e arquitetura em camadas para facilitar a manutenção e a separação de responsabilidades.
8. Adicione comentários explicativos para todas as seções.
9. Não use Lombok.

