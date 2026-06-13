# Prompt
Utilizando a skill #Bootstrap Spring Boot Sênior, gere a estrutura de uma API de Usuários (API Users) seguindo estas diretrizes:
1. Arquitetura: Aplique rigorosamente Clean Architecture (Domain, Application, Infrastructure).
2. Persistência: Use PostgreSQL com JDBC puro (JdbcTemplate). Proibido o uso de JPA/Hibernate.
3. Modelo de Dados: Tabela users com campos uuid (PK), login, email e senha_cripto.
4. Padrões Java 21: Use Java Records para DTOs e Entidades. Implemente a Injeção de Dependência apenas via construtor.
5. Adicione a configuração do Flyway ou Liquibase para versionamento desse banco de dados
6. Restrições: Sem Lombok. O código deve ser nativo e limpo.
7. Adicione uma camada de Validation (Bean Validation) nos Records de entrada
8. Inclua a estrutura de Global Exception Handling conforme previsto no playbook da skill
0. Gere os Testes Unitários para o CreateUserUseCase usando JUnit 5
10. Configurar o Spring Security para realizar o hash da senha_cripto usando BCrypt
11. Adicione suporte a Logs Estruturados (JSON) para melhorar a observabilidade
12. Implemente a Documentação com Swagger/OpenAPI para que você possa testar os endpoints visualmente
13. Adicionar Correlation ID nos logs para rastrear uma requisição de ponta a ponta
14. Configure o Docker e Docker Compose para subir a aplicação e o banco PostgreSQL com um único comando
15. Entregáveis: Gere o schema.sql, a interface e implementação do repositório, o Use Case de criação e o Controller REST.

