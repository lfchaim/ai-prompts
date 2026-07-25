# Prommpt
Criar um prompt para criar um sistema de API RESTful CRUD, usando Java e JDBC para identificar tabelas, colunas, primary keys e foreign keys.

# ChatGPT
Aqui está um **prompt completo e estruturado** que você pode usar para gerar um sistema de API RESTful CRUD em Java com descoberta automática de metadados via JDBC:

---

## 🚀 Prompt para geração do sistema

> Crie um sistema backend completo de API RESTful CRUD utilizando **Java 17+** e **JDBC puro (sem ORM como Hibernate/JPA)**.
> O sistema deve ser capaz de **inspecionar dinamicamente o banco de dados** e gerar operações CRUD automaticamente com base na estrutura das tabelas.
>
> ### 🎯 Objetivo principal
>
> Construir uma API genérica que:
>
> * Leia metadados do banco via JDBC
> * Identifique:
>
>   * Tabelas
>   * Colunas
>   * Primary Keys
>   * Foreign Keys
> * Gere endpoints CRUD dinâmicos para qualquer tabela
>
> ---
>
> ### 🧱 Requisitos técnicos
>
> #### 1. Stack
>
> * Java 17+
> * JDBC puro
> * Spring Boot (apenas para REST e DI)
> * Maven ou Gradle
> * Banco de dados: PostgreSQL (preferencial)
>
> ---
>
> #### 2. Leitura de metadados (JDBC)
>
> Utilizar `DatabaseMetaData` para:
>
> * `getTables()` → listar tabelas
> * `getColumns()` → listar colunas e tipos
> * `getPrimaryKeys()` → identificar PK
> * `getImportedKeys()` → identificar FKs
>
> Criar uma camada de serviço que monte um modelo interno como:
>
> ```java
> class TableMetaData {
>     String tableName;
>     List<ColumnMetaData> columns;
>     List<String> primaryKeys;
>     List<ForeignKeyMetaData> foreignKeys;
> }
> ```
>
> ---
>
> #### 3. API REST genérica
>
> Criar endpoints dinâmicos no padrão:
>
> ```
> GET    /api/{table}          → listar registros
> GET    /api/{table}/{id}     → buscar por id
> POST   /api/{table}          → inserir
> PUT    /api/{table}/{id}     → atualizar
> DELETE /api/{table}/{id}     → deletar
> ```
>
> ---
>
> #### 4. Construção dinâmica de SQL
>
> * Gerar queries dinamicamente com base no metadata:
>
>   * SELECT
>   * INSERT
>   * UPDATE
>   * DELETE
> * Usar `PreparedStatement`
> * Mapear tipos JDBC corretamente
>
> ---
>
> #### 5. Mapeamento dinâmico
>
> * Usar `Map<String, Object>` para representar entidades
> * Converter `ResultSet` → JSON automaticamente
>
> ---
>
> #### 6. Relacionamentos (FK)
>
> * Permitir:
>
>   * Inclusão de FK no payload
>   * (Opcional) Expandir relacionamentos via query param:
>
> ```
> GET /api/orders?expand=customer
> ```
>
> ---
>
> #### 7. Validações básicas
>
> * Campos obrigatórios (NOT NULL)
> * Tipos de dados
> * PK obrigatória para update/delete
>
> ---
>
> #### 8. Estrutura do projeto
>
> Organizar em camadas:
>
> ```
> controller/
> service/
> repository/
> metadata/
> util/
> ```
>
> ---
>
> #### 9. Segurança (opcional)
>
> * JWT simples com endpoint `/login`
>
> ---
>
> #### 10. Documentação
>
> * Swagger/OpenAPI
>
> ---
>
> ### 📦 Extras desejáveis
>
> * Cache de metadata (para evitar leitura constante do banco)
> * Suporte a paginação:
>
> ```
> GET /api/{table}?page=0&size=10
> ```
>
> * Filtros dinâmicos:
>
> ```
> GET /api/users?name=John
> ```
>
> ---
>
> ### 🧪 Entregáveis esperados
>
> * Projeto completo compilável
> * Exemplos de endpoints funcionando
> * Classe de leitura de metadata implementada
> * Exemplo de banco (DDL)
> * Testes básicos
>
> ---
>
> ### ⚠️ Restrições
>
> * NÃO usar Hibernate/JPA
> * NÃO usar frameworks de ORM
> * Toda lógica deve ser baseada em JDBC e metadata
>
> ---
>
> ### 💡 Diferencial
>
> O sistema deve se comportar como um **mini framework tipo "Spring Data dinâmico"**, porém baseado em introspecção do banco em tempo de execução.

---

## 💡 Dica de uso

Você pode usar esse prompt em:

* ChatGPT
* GitHub Copilot
* Cursor / Windsurf
* Ferramentas de geração de código

---

Se quiser, posso ir além e **gerar o projeto base completo (Spring Boot + JDBC + metadata engine)** já pronto pra rodar.
