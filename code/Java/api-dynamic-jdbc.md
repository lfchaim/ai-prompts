# Usuário:

Criar projeto Spring Boot, usando JDBC puro que permita métodos API RESTful, executando respectivas operações de INSERT, UPDATE, DELETE e SELECT.  A solução deve ter inteligência para tratar bancos como MySQL, PostgreSQL, Oracle e SQL Server, principalmente no INSERT.
Os endpoints terão o prefixo /api/v1 e na sequência:
1. /datasource - É a chave da conexão com algum banco de dados e deve ser obtida através da tabela DATA_SOURCE (campos: key, jdbc_url, jdbc_user e jdbc_pass
2. /table - É o nome da tabela onde a aplicação efetuará as operações SQL
3. Body - Map representando os dados a serem tratados (INSERT, UPDATE, DELETE e SELECT)
#Bootstrap Spring Boot Sênior


# IA(Adapta):

Projeto criado seguindo o playbook **#Bootstrap Spring Boot Sênior** (Clean Architecture, Java Records, injeção por construtor, sem Lombok, testes Given-When-Then, observabilidade). A solução usa **JDBC puro** (sem ORM), com detecção de dialeto pela JDBC URL e estratégia específica de INSERT por banco (auto-increment vs. identity vs. sequence/Oracle RETURNING).

## Estrutura do projeto

```text
jdbc-dynamic-api/
├── pom.xml
├── README.md
└── src/
    ├── main/
    │   ├── java/com/adapta/jdbcdynamic/
    │   │   ├── JdbcDynamicApplication.java
    │   │   ├── domain/model/          → SqlDialect, DataSourceConfig, TableMetadata, SelectOptions, CrudOperation
    │   │   ├── application/
    │   │   │   ├── dto/               → CrudRequest, CrudResult, CrudResponse, ErrorResponse
    │   │   │   ├── port/in/           → DynamicCrudUseCase
    │   │   │   ├── port/out/          → ControlDataSourcePort, DataSourceRegistryPort, DynamicCrudRepositoryPort
    │   │   │   └── usecase/           → DynamicCrudService
    │   │   ├── infrastructure/
    │   │   │   ├── config/            → DynamicPoolProperties
    │   │   │   ├── adapter/in/web/    → DynamicCrudController, GlobalExceptionHandler, CorrelationIdFilter
    │   │   │   └── adapter/out/       → ControlDataSourceRepository, JdbcDataSourceRegistry,
    │   │   │                           TableMetadataResolver, DynamicJdbcRepository
    │   │   │                           + dialect/ (4 estratégias + factory)
    │   │   └── shared/                → exceptions + SqlIdentifierValidator
    │   └── resources/                 → application.yml, logback-spring.xml, schema.sql
    └── test/java/                     → testes unitários + integração H2
```

## 1. Build e configuração

**pom.xml**

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

    <groupId>com.adapta</groupId>
    <artifactId>jdbc-dynamic-api</artifactId>
    <version>1.0.0</version>
    <name>jdbc-dynamic-api</name>
    <description>API REST dinâmica sobre JDBC puro com suporte multi-banco</description>

    <properties>
        <java.version>25</java.version>
        <logstash-logback.version>8.1</logstash-logback.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
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
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-prometheus</artifactId>
        </dependency>

        <!-- Drivers JDBC dos bancos suportados -->
        <dependency>
            <groupId>com.mysql</groupId>
            <artifactId>mysql-connector-j</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>com.oracle.database.jdbc</groupId>
            <artifactId>ojdbc11</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>com.microsoft.sqlserver</groupId>
            <artifactId>mssql-jdbc</artifactId>
            <scope>runtime</scope>
        </dependency>

        <!-- Logs estruturados JSON -->
        <dependency>
            <groupId>net.logstash.logback</groupId>
            <artifactId>logstash-logback-encoder</artifactId>
            <version>${logstash-logback.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

**src/main/resources/application.yml**

```yaml
spring:
  application:
    name: jdbc-dynamic-api
  datasource:
    # Banco de controle: é nele que vive a tabela DATA_SOURCE
    url: ${CONTROL_DB_URL:jdbc:postgresql://localhost:5432/control}
    username: ${CONTROL_DB_USER:control_user}
    password: ${CONTROL_DB_PASS:control_pass}
    hikari:
      maximum-pool-size: 5

server:
  port: ${SERVER_PORT:8080}

management:
  endpoints:
    web:
      exposure:
        include: health,info,prometheus
  endpoint:
    health:
      show-details: always

app:
  pool:
    max-pool-size: ${DYNAMIC_POOL_MAX:10}
    min-idle: ${DYNAMIC_POOL_MIN_IDLE:2}
    connection-timeout-ms: ${DYNAMIC_POOL_CONN_TIMEOUT:5000}
    idle-timeout-ms: ${DYNAMIC_POOL_IDLE_TIMEOUT:600000}
    max-lifetime-ms: ${DYNAMIC_POOL_MAX_LIFETIME:1800000}

logging:
  level:
    com.adapta.jdbcdynamic: INFO
```

**src/main/resources/logback-spring.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <appender name="JSON" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="net.logstash.logback.encoder.LogstashEncoder">
            <includeMdc>true</includeMdc>
            <customFields>{"app":"jdbc-dynamic-api"}</customFields>
        </encoder>
    </appender>

    <root level="INFO">
        <appender-ref ref="JSON"/>
    </root>
</configuration>
```

**src/main/resources/schema.sql** (banco de controle)

```sql
CREATE TABLE DATA_SOURCE (
    KEY       VARCHAR(100) NOT NULL PRIMARY KEY,
    JDBC_URL  VARCHAR(500) NOT NULL,
    JDBC_USER VARCHAR(200) NOT NULL,
    JDBC_PASS VARCHAR(500) NOT NULL
);

-- Exemplos de conexões cadastradas
INSERT INTO DATA_SOURCE (KEY, JDBC_URL, JDBC_USER, JDBC_PASS) VALUES
('mysql-prod',  'jdbc:mysql://localhost:3306/appdb',                'app', 'secret'),
('pg-prod',     'jdbc:postgresql://localhost:5432/appdb',           'app', 'secret'),
('ora-prod',    'jdbc:oracle:thin:@localhost:1521/XEPDB1',          'app', 'secret'),
('sqlsrv-prod', 'jdbc:sqlserver://localhost:1433;databaseName=appdb;encrypt=true;trustServerCertificate=true', 'app', 'secret');
```

**JdbcDynamicApplication.java**

```java
package com.adapta.jdbcdynamic;

import com.adapta.jdbcdynamic.infrastructure.config.DynamicPoolProperties;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.context.properties.EnableConfigurationProperties;

@SpringBootApplication
@EnableConfigurationProperties(DynamicPoolProperties.class)
public class JdbcDynamicApplication {

    public static void main(String[] args) {
        SpringApplication.run(JdbcDynamicApplication.class, args);
    }
}
```

## 2. Camada domain

**domain/model/SqlDialect.java**

```java
package com.adapta.jdbcdynamic.domain.model;

import com.adapta.jdbcdynamic.shared.exception.UnsupportedDialectException;

public enum SqlDialect {
    MYSQL,
    POSTGRESQL,
    ORACLE,
    SQL_SERVER;

    public static SqlDialect fromJdbcUrl(String jdbcUrl) {
        if (jdbcUrl == null || jdbcUrl.isBlank()) {
            throw new UnsupportedDialectException("JDBC URL não informada");
        }
        if (jdbcUrl.startsWith("jdbc:mysql:")) return MYSQL;
        if (jdbcUrl.startsWith("jdbc:postgresql:")) return POSTGRESQL;
        if (jdbcUrl.startsWith("jdbc:oracle:")) return ORACLE;
        if (jdbcUrl.startsWith("jdbc:sqlserver:")) return SQL_SERVER;
        throw new UnsupportedDialectException("Dialeto não suportado para a URL: " + jdbcUrl);
    }
}
```

**domain/model/DataSourceConfig.java**

```java
package com.adapta.jdbcdynamic.domain.model;

import java.util.Objects;

public record DataSourceConfig(String key, String jdbcUrl, String jdbcUser, String jdbcPass) {

    public DataSourceConfig {
        Objects.requireNonNull(key, "key é obrigatória");
        Objects.requireNonNull(jdbcUrl, "jdbcUrl é obrigatória");
    }
}
```

**domain/model/TableMetadata.java**

```java
package com.adapta.jdbcdynamic.domain.model;

import java.util.Set;

public record TableMetadata(
    String primaryKeyColumn,
    boolean primaryKeyAutoGenerated,
    Set autoIncrementColumns,
    Set allColumns
) {

    public TableMetadata {
        autoIncrementColumns = autoIncrementColumns == null ? Set.of() : Set.copyOf(autoIncrementColumns);
        allColumns = allColumns == null ? Set.of() : Set.copyOf(allColumns);
    }

    public boolean isAutoIncrement(String column) {
        return autoIncrementColumns.stream().anyMatch(c - c.equalsIgnoreCase(column));
    }

    public boolean hasPrimaryKey() {
        return primaryKeyColumn != null && !primaryKeyColumn.isBlank();
    }

    /** Resolve o nome canônico da coluna (case do banco) para evitar falhas de case-sensitivity. */
    public String canonicalColumn(String column) {
        return allColumns.stream()
            .filter(c - c.equalsIgnoreCase(column))
            .findFirst()
            .orElse(column);
    }
}
```

**domain/model/SelectOptions.java**

```java
package com.adapta.jdbcdynamic.domain.model;

import java.util.Map;

public record SelectOptions(Integer limit, Integer offset, String orderBy) {

    public static SelectOptions empty() {
        return new SelectOptions(null, null, null);
    }

    public static SelectOptions from(Map options) {
        if (options == null || options.isEmpty()) return empty();
        Integer limit = toInteger(options.get("limit"));
        Integer offset = toInteger(options.get("offset"));
        String orderBy = options.get("orderBy") == null ? null : String.valueOf(options.get("orderBy"));
        return new SelectOptions(limit, offset, orderBy);
    }

    private static Integer toInteger(Object value) {
        if (value == null) return null;
        if (value instanceof Number number) return number.intValue();
        try {
            return Integer.valueOf(String.valueOf(value));
        } catch (NumberFormatException e) {
            return null;
        }
    }
}
```

**domain/model/CrudOperation.java**

```java
package com.adapta.jdbcdynamic.domain.model;

public enum CrudOperation {
    INSERT, UPDATE, DELETE, SELECT
}
```

## 3. Camada application

**application/dto/CrudRequest.java**

```java
package com.adapta.jdbcdynamic.application.dto;

import com.adapta.jdbcdynamic.domain.model.SelectOptions;
import java.util.Map;

public record CrudRequest(Map data, Map where, SelectOptions options) {

    public CrudRequest {
        data = data == null ? Map.of() : data;
        where = where == null ? Map.of() : where;
        options = options == null ? SelectOptions.empty() : options;
    }

    public static CrudRequest from(Map body) {
        if (body == null || body.isEmpty()) return new CrudRequest(Map.of(), Map.of(), SelectOptions.empty());
        return new CrudRequest(
            castMap(body.get("data")),
            castMap(body.get("where")),
            SelectOptions.from(castMap(body.get("options")))
        );
    }

    @SuppressWarnings("unchecked")
    private static Map castMap(Object value) {
        if (value instanceof Map map) return (Map) map;
        return Map.of();
    }
}
```

**application/dto/CrudResult.java**

```java
package com.adapta.jdbcdynamic.application.dto;

import com.adapta.jdbcdynamic.domain.model.CrudOperation;
import java.util.List;
import java.util.Map;

public record CrudResult(CrudOperation operation, int affectedRows, Object generatedKey, List rows) {

    public static CrudResult insert(int affectedRows, Object generatedKey) {
        return new CrudResult(CrudOperation.INSERT, affectedRows, generatedKey, List.of());
    }

    public static CrudResult update(int affectedRows) {
        return new CrudResult(CrudOperation.UPDATE, affectedRows, null, List.of());
    }

    public static CrudResult delete(int affectedRows) {
        return new CrudResult(CrudOperation.DELETE, affectedRows, null, List.of());
    }

    public static CrudResult select(List rows) {
        return new CrudResult(CrudOperation.SELECT, rows.size(), null, rows);
    }
}
```

**application/dto/CrudResponse.java**

```java
package com.adapta.jdbcdynamic.application.dto;

import com.adapta.jdbcdynamic.domain.model.CrudOperation;
import java.util.List;
import java.util.Map;

public record CrudResponse(
    String datasource,
    String table,
    CrudOperation operation,
    int affectedRows,
    Object generatedKey,
    List rows
) {

    public static CrudResponse of(String datasource, String table, CrudResult result) {
        return new CrudResponse(datasource, table, result.operation(), result.affectedRows(), result.generatedKey(), result.rows());
    }
}
```

**application/dto/ErrorResponse.java**

```java
package com.adapta.jdbcdynamic.application.dto;

import java.time.Instant;

public record ErrorResponse(
    Instant timestamp,
    int status,
    String error,
    String message,
    String path,
    String correlationId
) {}
```

**application/port/in/DynamicCrudUseCase.java**

```java
package com.adapta.jdbcdynamic.application.port.in;

import com.adapta.jdbcdynamic.application.dto.CrudRequest;
import com.adapta.jdbcdynamic.application.dto.CrudResponse;
import com.adapta.jdbcdynamic.domain.model.CrudOperation;

public interface DynamicCrudUseCase {
    CrudResponse execute(String datasourceKey, String table, CrudOperation operation, CrudRequest request);
}
```

**application/port/out/ControlDataSourcePort.java**

```java
package com.adapta.jdbcdynamic.application.port.out;

import com.adapta.jdbcdynamic.domain.model.DataSourceConfig;
import java.util.Optional;

public interface ControlDataSourcePort {
    Optional findByKey(String key);
}
```

**application/port/out/DataSourceRegistryPort.java**

```java
package com.adapta.jdbcdynamic.application.port.out;

import com.adapta.jdbcdynamic.domain.model.DataSourceConfig;
import javax.sql.DataSource;

public interface DataSourceRegistryPort {
    DataSource getOrCreate(DataSourceConfig config);
}
```

**application/port/out/DynamicCrudRepositoryPort.java**

```java
package com.adapta.jdbcdynamic.application.port.out;

import com.adapta.jdbcdynamic.application.dto.CrudResult;
import com.adapta.jdbcdynamic.domain.model.SelectOptions;
import com.adapta.jdbcdynamic.domain.model.SqlDialect;
import javax.sql.DataSource;
import java.util.Map;

public interface DynamicCrudRepositoryPort {
    CrudResult insert(DataSource dataSource, SqlDialect dialect, String table, Map data);
    CrudResult update(DataSource dataSource, SqlDialect dialect, String table, Map data, Map where);
    CrudResult delete(DataSource dataSource, SqlDialect dialect, String table, Map where);
    CrudResult select(DataSource dataSource, SqlDialect dialect, String table, Map where, SelectOptions options);
}
```

**application/usecase/DynamicCrudService.java**

```java
package com.adapta.jdbcdynamic.application.usecase;

import com.adapta.jdbcdynamic.application.dto.CrudRequest;
import com.adapta.jdbcdynamic.application.dto.CrudResponse;
import com.adapta.jdbcdynamic.application.dto.CrudResult;
import com.adapta.jdbcdynamic.application.port.in.DynamicCrudUseCase;
import com.adapta.jdbcdynamic.application.port.out.ControlDataSourcePort;
import com.adapta.jdbcdynamic.application.port.out.DataSourceRegistryPort;
import com.adapta.jdbcdynamic.application.port.out.DynamicCrudRepositoryPort;
import com.adapta.jdbcdynamic.domain.model.CrudOperation;
import com.adapta.jdbcdynamic.domain.model.DataSourceConfig;
import com.adapta.jdbcdynamic.domain.model.SqlDialect;
import com.adapta.jdbcdynamic.shared.exception.DataSourceNotFoundException;
import com.adapta.jdbcdynamic.shared.exception.InvalidPayloadException;
import org.springframework.stereotype.Service;

import javax.sql.DataSource;

@Service
public class DynamicCrudService implements DynamicCrudUseCase {

    private final ControlDataSourcePort controlDataSource;
    private final DataSourceRegistryPort dataSourceRegistry;
    private final DynamicCrudRepositoryPort repository;

    public DynamicCrudService(ControlDataSourcePort controlDataSource,
                              DataSourceRegistryPort dataSourceRegistry,
                              DynamicCrudRepositoryPort repository) {
        this.controlDataSource = controlDataSource;
        this.dataSourceRegistry = dataSourceRegistry;
        this.repository = repository;
    }

    @Override
    public CrudResponse execute(String datasourceKey, String table, CrudOperation operation, CrudRequest request) {
        validate(operation, request);

        DataSourceConfig config = controlDataSource.findByKey(datasourceKey)
            .orElseThrow(() - new DataSourceNotFoundException(datasourceKey));

        SqlDialect dialect = SqlDialect.fromJdbcUrl(config.jdbcUrl());
        DataSource dataSource = dataSourceRegistry.getOrCreate(config);

        CrudResult result = switch (operation) {
            case INSERT - repository.insert(dataSource, dialect, table, request.data());
            case UPDATE - repository.update(dataSource, dialect, table, request.data(), request.where());
            case DELETE - repository.delete(dataSource, dialect, table, request.where());
            case SELECT - repository.select(dataSource, dialect, table, request.where(), request.options());
        };

        return CrudResponse.of(datasourceKey, table, result);
    }

    private void validate(CrudOperation operation, CrudRequest request) {
        switch (operation) {
            case INSERT - {
                if (request.data().isEmpty()) {
                    throw new InvalidPayloadException("INSERT exige o campo 'data' com as colunas e valores");
                }
            }
            case UPDATE - {
                if (request.data().isEmpty()) {
                    throw new InvalidPayloadException("UPDATE exige o campo 'data' com as colunas a atualizar");
                }
                if (request.where().isEmpty()) {
                    throw new InvalidPayloadException("UPDATE exige o campo 'where' — sem ele a operação é bloqueada por segurança");
                }
            }
            case DELETE - {
                if (request.where().isEmpty()) {
                    throw new InvalidPayloadException("DELETE exige o campo 'where' — sem ele a operação é bloqueada por segurança");
                }
            }
            case SELECT - { /* where é opcional */ }
        }
    }
}
```

## 4. Camada infrastructure

**infrastructure/config/DynamicPoolProperties.java**

```java
package com.adapta.jdbcdynamic.infrastructure.config;

import org.springframework.boot.context.properties.ConfigurationProperties;

@ConfigurationProperties(prefix = "app.pool")
public record DynamicPoolProperties(
    int maxPoolSize,
    int minIdle,
    long connectionTimeoutMs,
    long idleTimeoutMs,
    long maxLifetimeMs
) {

    public DynamicPoolProperties {
        if (maxPoolSize  body) {
        SqlIdentifierValidator.validateTable(table);
        return useCase.execute(datasource, table, CrudOperation.INSERT, CrudRequest.from(body));
    }

    @PutMapping("/{datasource}/{table}")
    public CrudResponse update(@PathVariable String datasource,
                               @PathVariable String table,
                               @RequestBody Map body) {
        SqlIdentifierValidator.validateTable(table);
        return useCase.execute(datasource, table, CrudOperation.UPDATE, CrudRequest.from(body));
    }

    @DeleteMapping("/{datasource}/{table}")
    public CrudResponse delete(@PathVariable String datasource,
                               @PathVariable String table,
                               @RequestBody Map body) {
        SqlIdentifierValidator.validateTable(table);
        return useCase.execute(datasource, table, CrudOperation.DELETE, CrudRequest.from(body));
    }

    @GetMapping("/{datasource}/{table}")
    public CrudResponse select(@PathVariable String datasource,
                               @PathVariable String table,
                               @RequestParam Map params,
                               @RequestBody(required = false) Map body) {
        SqlIdentifierValidator.validateTable(table);

        CrudRequest request = CrudRequest.from(body);
        Map where = new HashMap(request.where());
        Map options = new HashMap();

        if (request.options().limit() != null) options.put("limit", request.options().limit());
        if (request.options().offset() != null) options.put("offset", request.options().offset());
        if (request.options().orderBy() != null) options.put("orderBy", request.options().orderBy());

        params.forEach((key, value) - {
            switch (key) {
                case "limit" - options.put("limit", value);
                case "offset" - options.put("offset", value);
                case "orderBy" - options.put("orderBy", value);
                default - {
                    SqlIdentifierValidator.validateColumn(key);
                    where.put(key, value);
                }
            }
        });

        return useCase.execute(datasource, table, CrudOperation.SELECT,
            new CrudRequest(Map.of(), where, SelectOptions.from(options)));
    }
}
```

**infrastructure/adapter/in/web/GlobalExceptionHandler.java**

```java
package com.adapta.jdbcdynamic.infrastructure.adapter.in.web;

import com.adapta.jdbcdynamic.application.dto.ErrorResponse;
import com.adapta.jdbcdynamic.shared.exception.BusinessException;
import com.adapta.jdbcdynamic.shared.exception.PersistenceException;
import jakarta.servlet.http.HttpServletRequest;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.slf4j.MDC;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.http.converter.HttpMessageNotReadableException;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import java.time.Instant;

@RestControllerAdvice
public class GlobalExceptionHandler {

    private static final Logger log = LoggerFactory.getLogger(GlobalExceptionHandler.class);

    @ExceptionHandler(BusinessException.class)
    public ResponseEntity handleBusiness(BusinessException ex, HttpServletRequest request) {
        return build(ex.getStatus(), ex.getMessage(), request);
    }

    @ExceptionHandler(PersistenceException.class)
    public ResponseEntity handlePersistence(PersistenceException ex, HttpServletRequest request) {
        log.error("Falha de persistência", ex);
        return build(HttpStatus.INTERNAL_SERVER_ERROR, "Falha na operação de banco de dados", request);
    }

    @ExceptionHandler({MethodArgumentNotValidException.class, HttpMessageNotReadableException.class})
    public ResponseEntity handleBadRequest(Exception ex, HttpServletRequest request) {
        return build(HttpStatus.BAD_REQUEST, "Requisição inválida: " + ex.getMessage(), request);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity handleGeneric(Exception ex, HttpServletRequest request) {
        log.error("Erro não tratado", ex);
        return build(HttpStatus.INTERNAL_SERVER_ERROR, "Erro interno inesperado", request);
    }

    private ResponseEntity build(HttpStatus status, String message, HttpServletRequest request) {
        ErrorResponse body = new ErrorResponse(
            Instant.now(),
            status.value(),
            status.getReasonPhrase(),
            message,
            request.getRequestURI(),
            MDC.get("correlationId")
        );
        return ResponseEntity.status(status).body(body);
    }
}
```

**infrastructure/adapter/in/web/CorrelationIdFilter.java**

```java
package com.adapta.jdbcdynamic.infrastructure.adapter.in.web;

import jakarta.servlet.FilterChain;
import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.slf4j.MDC;
import org.springframework.stereotype.Component;
import org.springframework.web.filter.OncePerRequestFilter;

import java.io.IOException;
import java.util.UUID;

@Component
public class CorrelationIdFilter extends OncePerRequestFilter {

    public static final String HEADER = "X-Correlation-Id";

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain chain)
            throws ServletException, IOException {
        String correlationId = request.getHeader(HEADER);
        if (correlationId == null || correlationId.isBlank()) {
            correlationId = UUID.randomUUID().toString();
        }
        MDC.put("correlationId", correlationId);
        response.setHeader(HEADER, correlationId);
        try {
            chain.doFilter(request, response);
        } finally {
            MDC.remove("correlationId");
        }
    }
}
```

**infrastructure/adapter/out/ControlDataSourceRepository.java**

```java
package com.adapta.jdbcdynamic.infrastructure.adapter.out;

import com.adapta.jdbcdynamic.application.port.out.ControlDataSourcePort;
import com.adapta.jdbcdynamic.domain.model.DataSourceConfig;
import com.adapta.jdbcdynamic.shared.exception.PersistenceException;
import org.springframework.stereotype.Repository;

import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.Optional;

@Repository
public class ControlDataSourceRepository implements ControlDataSourcePort {

    private final DataSource controlDataSource;

    public ControlDataSourceRepository(DataSource controlDataSource) {
        this.controlDataSource = controlDataSource;
    }

    @Override
    public Optional findByKey(String key) {
        String sql = "SELECT KEY, JDBC_URL, JDBC_USER, JDBC_PASS FROM DATA_SOURCE WHERE KEY = ?";
        try (Connection connection = controlDataSource.getConnection();
             PreparedStatement statement = connection.prepareStatement(sql)) {
            statement.setString(1, key);
            try (ResultSet resultSet = statement.executeQuery()) {
                if (resultSet.next()) {
                    return Optional.of(new DataSourceConfig(
                        resultSet.getString("KEY"),
                        resultSet.getString("JDBC_URL"),
                        resultSet.getString("JDBC_USER"),
                        resultSet.getString("JDBC_PASS")
                    ));
                }
                return Optional.empty();
            }
        } catch (SQLException e) {
            throw new PersistenceException("Falha ao consultar a tabela DATA_SOURCE", e);
        }
    }
}
```

**infrastructure/adapter/out/JdbcDataSourceRegistry.java**

```java
package com.adapta.jdbcdynamic.infrastructure.adapter.out;

import com.adapta.jdbcdynamic.application.port.out.DataSourceRegistryPort;
import com.adapta.jdbcdynamic.domain.model.DataSourceConfig;
import com.adapta.jdbcdynamic.infrastructure.config.DynamicPoolProperties;
import com.zaxxer.hikari.HikariConfig;
import com.zaxxer.hikari.HikariDataSource;
import jakarta.annotation.PreDestroy;
import org.springframework.stereotype.Component;

import javax.sql.DataSource;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;

@Component
public class JdbcDataSourceRegistry implements DataSourceRegistryPort {

    private final Map pools = new ConcurrentHashMap();
    private final DynamicPoolProperties poolProperties;

    public JdbcDataSourceRegistry(DynamicPoolProperties poolProperties) {
        this.poolProperties = poolProperties;
    }

    @Override
    public DataSource getOrCreate(DataSourceConfig config) {
        return pools.computeIfAbsent(config.key(), key - createPool(config));
    }

    private HikariDataSource createPool(DataSourceConfig config) {
        HikariConfig hikariConfig = new HikariConfig();
        hikariConfig.setPoolName("pool-" + config.key());
        hikariConfig.setJdbcUrl(config.jdbcUrl());
        hikariConfig.setUsername(config.jdbcUser());
        hikariConfig.setPassword(config.jdbcPass());
        hikariConfig.setMaximumPoolSize(poolProperties.maxPoolSize());
        hikariConfig.setMinimumIdle(poolProperties.minIdle());
        hikariConfig.setConnectionTimeout(poolProperties.connectionTimeoutMs());
        hikariConfig.setIdleTimeout(poolProperties.idleTimeoutMs());
        hikariConfig.setMaxLifetime(poolProperties.maxLifetimeMs());
        return new HikariDataSource(hikariConfig);
    }

    @PreDestroy
    public void closeAll() {
        pools.values().forEach(HikariDataSource::close);
        pools.clear();
    }
}
```

**infrastructure/adapter/out/TableMetadataResolver.java**

```java
package com.adapta.jdbcdynamic.infrastructure.adapter.out;

import com.adapta.jdbcdynamic.domain.model.TableMetadata;
import com.adapta.jdbcdynamic.shared.exception.TableNotFoundException;
import org.springframework.stereotype.Component;

import java.sql.Connection;
import java.sql.DatabaseMetaData;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.HashSet;
import java.util.Set;

@Component
public class TableMetadataResolver {

    public TableMetadata resolve(Connection connection, String table) throws SQLException {
        DatabaseMetaData metadata = connection.getMetaData();
        String schema = null;
        String tableName = table;
        if (table.contains(".")) {
            String[] parts = table.split("\\.", 2);
            schema = parts[0];
            tableName = parts[1];
        }

        String primaryKey = null;
        try (ResultSet rs = metadata.getPrimaryKeys(null, schema, tableName)) {
            if (rs.next()) {
                primaryKey = rs.getString("COLUMN_NAME");
            }
        }

        Set autoIncrement = new HashSet();
        Set allColumns = new HashSet();
        boolean found = false;
        try (ResultSet rs = metadata.getColumns(null, schema, tableName, null)) {
            while (rs.next()) {
                found = true;
                String column = rs.getString("COLUMN_NAME");
                allColumns.add(column);
                if ("YES".equalsIgnoreCase(rs.getString("IS_AUTOINCREMENT"))) {
                    autoIncrement.add(column);
                }
            }
        }

        if (!found) {
            throw new TableNotFoundException(table);
        }

        return new TableMetadata(primaryKey, primaryKey != null && autoIncrement.contains(primaryKey), autoIncrement, allColumns);
    }
}
```

**infrastructure/adapter/out/dialect/GeneratedKeyMode.java**

```java
package com.adapta.jdbcdynamic.infrastructure.adapter.out.dialect;

public enum GeneratedKeyMode {
    /** getGeneratedKeys() do JDBC (MySQL, PostgreSQL, SQL Server). */
    JDBC_GENERATED_KEYS,
    /** INSERT ... RETURNING pk INTO ? via CallableStatement (Oracle IDENTITY). */
    ORACLE_RETURNING,
    /** Sem chave gerada pelo banco. */
    NONE
}
```

**infrastructure/adapter/out/dialect/DialectStrategy.java**

```java
package com.adapta.jdbcdynamic.infrastructure.adapter.out.dialect;

import com.adapta.jdbcdynamic.domain.model.SelectOptions;
import com.adapta.jdbcdynamic.domain.model.SqlDialect;

import java.util.ArrayList;
import java.util.List;
import java.util.Map;

public interface DialectStrategy {

    SqlDialect dialect();

    String quote(String identifier);

    String normalizeForMetadata(String identifier);

    GeneratedKeyMode generatedKeyMode();

    String insertSql(String table, List columns, String generatedKeyColumn);

    String updateSql(String table, Map data, Map where);

    String deleteSql(String table, Map where);

    String selectSql(String table, Map where, SelectOptions options);

    /** Parâmetros de paginação na ordem dos placeholders do SQL gerado. */
    default List paginationParams(SelectOptions options) {
        List params = new ArrayList();
        if (options.offset() != null) params.add(options.offset());
        if (options.limit() != null) params.add(options.limit());
        return params;
    }
}
```

**infrastructure/adapter/out/dialect/AbstractDialectStrategy.java**

```java
package com.adapta.jdbcdynamic.infrastructure.adapter.out.dialect;

import com.adapta.jdbcdynamic.domain.model.SelectOptions;

import java.util.Arrays;
import java.util.Collection;
import java.util.Map;
import java.util.function.Function;
import java.util.stream.Collectors;

public abstract class AbstractDialectStrategy implements DialectStrategy {

    protected String quoteQualified(String identifier, Function quoter) {
        return Arrays.stream(identifier.split("\\."))
            .map(quoter)
            .collect(Collectors.joining("."));
    }

    protected String setClause(Map data) {
        return data.keySet().stream()
            .map(column - quote(column) + " = ?")
            .collect(Collectors.joining(", "));
    }

    /** Monta o WHERE suportando igualdade e IN (quando o valor é uma lista). */
    protected String whereClause(Map where) {
        return where.entrySet().stream()
            .map(entry - {
                String column = quote(entry.getKey());
                Object value = entry.getValue();
                if (value instanceof Collection collection && !collection.isEmpty()) {
                    String marks = collection.stream().map(item - "?").collect(Collectors.joining(", "));
                    return column + " IN (" + marks + ")";
                }
                return column + " = ?";
            })
            .collect(Collectors.joining(" AND "));
    }

    protected String orderByClause(SelectOptions options) {
        if (options.orderBy() == null || options.orderBy().isBlank()) return "";
        String[] parts = options.orderBy().trim().split("\\s+");
        String column = quote(parts[0]);
        String direction = parts.length  1 ? " " + parts[1].toUpperCase() : "";
        return " ORDER BY " + column + direction;
    }

    @Override
    public String updateSql(String table, Map data, Map where) {
        return "UPDATE " + quote(table) + " SET " + setClause(data) + " WHERE " + whereClause(where);
    }

    @Override
    public String deleteSql(String table, Map where) {
        return "DELETE FROM " + quote(table) + " WHERE " + whereClause(where);
    }

    @Override
    public String normalizeForMetadata(String identifier) {
        return identifier;
    }
}
```

**infrastructure/adapter/out/dialect/MySqlDialectStrategy.java**

```java
package com.adapta.jdbcdynamic.infrastructure.adapter.out.dialect;

import com.adapta.jdbcdynamic.domain.model.SelectOptions;
import com.adapta.jdbcdynamic.domain.model.SqlDialect;

import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

public class MySqlDialectStrategy extends AbstractDialectStrategy {

    @Override
    public SqlDialect dialect() {
        return SqlDialect.MYSQL;
    }

    @Override
    public String quote(String identifier) {
        return quoteQualified(identifier, value - "`" + value + "`");
    }

    @Override
    public GeneratedKeyMode generatedKeyMode() {
        return GeneratedKeyMode.JDBC_GENERATED_KEYS;
    }

    @Override
    public String insertSql(String table, List columns, String generatedKeyColumn) {
        String columnList = columns.stream().map(this::quote).collect(Collectors.joining(", "));
        String marks = columns.stream().map(column - "?").collect(Collectors.joining(", "));
        return "INSERT INTO " + quote(table) + " (" + columnList + ") VALUES (" + marks + ")";
    }

    @Override
    public String selectSql(String table, Map where, SelectOptions options) {
        StringBuilder sql = new StringBuilder("SELECT * FROM ").append(quote(table));
        if (!where.isEmpty()) sql.append(" WHERE ").append(whereClause(where));
        sql.append(orderByClause(options));
        if (options.limit() != null) sql.append(" LIMIT ?");
        if (options.offset() != null) sql.append(" OFFSET ?");
        return sql.toString();
    }

    @Override
    public List paginationParams(SelectOptions options) {
        // MySQL: LIMIT ? OFFSET ? — o limit vem primeiro no SQL
        List params = new ArrayList();
        if (options.limit() != null) params.add(options.limit());
        if (options.offset() != null) params.add(options.offset());
        return params;
    }
}
```

**infrastructure/adapter/out/dialect/PostgreSqlDialectStrategy.java**

```java
package com.adapta.jdbcdynamic.infrastructure.adapter.out.dialect;

import com.adapta.jdbcdynamic.domain.model.SelectOptions;
import com.adapta.jdbcdynamic.domain.model.SqlDialect;

import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

public class PostgreSqlDialectStrategy extends AbstractDialectStrategy {

    @Override
    public SqlDialect dialect() {
        return SqlDialect.POSTGRESQL;
    }

    @Override
    public String quote(String identifier) {
        return quoteQualified(identifier, value - "\"" + value + "\"");
    }

    @Override
    public GeneratedKeyMode generatedKeyMode() {
        return GeneratedKeyMode.JDBC_GENERATED_KEYS;
    }

    @Override
    public String insertSql(String table, List columns, String generatedKeyColumn) {
        String columnList = columns.stream().map(this::quote).collect(Collectors.joining(", "));
        String marks = columns.stream().map(column - "?").collect(Collectors.joining(", "));
        return "INSERT INTO " + quote(table) + " (" + columnList + ") VALUES (" + marks + ")";
    }

    @Override
    public String selectSql(String table, Map where, SelectOptions options) {
        StringBuilder sql = new StringBuilder("SELECT * FROM ").append(quote(table));
        if (!where.isEmpty()) sql.append(" WHERE ").append(whereClause(where));
        sql.append(orderByClause(options));
        if (options.limit() != null) sql.append(" LIMIT ?");
        if (options.offset() != null) sql.append(" OFFSET ?");
        return sql.toString();
    }

    @Override
    public List paginationParams(SelectOptions options) {
        // PostgreSQL: LIMIT ? OFFSET ? — o limit vem primeiro no SQL
        List params = new ArrayList();
        if (options.limit() != null) params.add(options.limit());
        if (options.offset() != null) params.add(options.offset());
        return params;
    }
}
```

**infrastructure/adapter/out/dialect/OracleDialectStrategy.java**

```java
package com.adapta.jdbcdynamic.infrastructure.adapter.out.dialect;

import com.adapta.jdbcdynamic.domain.model.SelectOptions;
import com.adapta.jdbcdynamic.domain.model.SqlDialect;

import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

public class OracleDialectStrategy extends AbstractDialectStrategy {

    @Override
    public SqlDialect dialect() {
        return SqlDialect.ORACLE;
    }

    /** Oracle dobra identificadores não quotados para maiúsculas: normalizamos sem aspas. */
    @Override
    public String quote(String identifier) {
        return quoteQualified(identifier, value - value.toUpperCase());
    }

    @Override
    public String normalizeForMetadata(String identifier) {
        return identifier.toUpperCase();
    }

    @Override
    public GeneratedKeyMode generatedKeyMode() {
        return GeneratedKeyMode.ORACLE_RETURNING;
    }

    @Override
    public String insertSql(String table, List columns, String generatedKeyColumn) {
        String columnList = columns.stream().map(this::quote).collect(Collectors.joining(", "));
        String marks = columns.stream().map(column - "?").collect(Collectors.joining(", "));
        StringBuilder sql = new StringBuilder("INSERT INTO ").append(quote(table))
            .append(" (").append(columnList).append(") VALUES (").append(marks).append(")");
        if (generatedKeyColumn != null) {
            sql.append(" RETURNING ").append(quote(generatedKeyColumn)).append(" INTO ?");
        }
        return sql.toString();
    }

    @Override
    public String selectSql(String table, Map where, SelectOptions options) {
        StringBuilder sql = new StringBuilder("SELECT * FROM ").append(quote(table));
        if (!where.isEmpty()) sql.append(" WHERE ").append(whereClause(where));
        if (options.limit() != null || options.offset() != null) {
            sql.append(orderByClause(options));
            if (options.offset() != null) {
                sql.append(" OFFSET ? ROWS");
            } else {
                sql.append(" OFFSET 0 ROWS");
            }
            if (options.limit() != null) {
                sql.append(" FETCH NEXT ? ROWS ONLY");
            }
        } else {
            sql.append(orderByClause(options));
        }
        return sql.toString();
    }
}
```

**infrastructure/adapter/out/dialect/SqlServerDialectStrategy.java**

```java
package com.adapta.jdbcdynamic.infrastructure.adapter.out.dialect;

import com.adapta.jdbcdynamic.domain.model.SelectOptions;
import com.adapta.jdbcdynamic.domain.model.SqlDialect;

import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

public class SqlServerDialectStrategy extends AbstractDialectStrategy {

    @Override
    public SqlDialect dialect() {
        return SqlDialect.SQL_SERVER;
    }

    @Override
    public String quote(String identifier) {
        return quoteQualified(identifier, value - "[" + value + "]");
    }

    @Override
    public GeneratedKeyMode generatedKeyMode() {
        return GeneratedKeyMode.JDBC_GENERATED_KEYS;
    }

    @Override
    public String insertSql(String table, List columns, String generatedKeyColumn) {
        String columnList = columns.stream().map(this::quote).collect(Collectors.joining(", "));
        String marks = columns.stream().map(column - "?").collect(Collectors.joining(", "));
        return "INSERT INTO " + quote(table) + " (" + columnList + ") VALUES (" + marks + ")";
    }

    @Override
    public String selectSql(String table, Map where, SelectOptions options) {
        StringBuilder sql = new StringBuilder("SELECT * FROM ").append(quote(table));
        if (!where.isEmpty()) sql.append(" WHERE ").append(whereClause(where));
        if (options.limit() != null || options.offset() != null) {
            // OFFSET/FETCH exige ORDER BY no SQL Server
            if (options.orderBy() == null || options.orderBy().isBlank()) {
                sql.append(" ORDER BY 1");
            } else {
                sql.append(orderByClause(options));
            }
            if (options.offset() != null) {
                sql.append(" OFFSET ? ROWS");
            } else {
                sql.append(" OFFSET 0 ROWS");
            }
            if (options.limit() != null) {
                sql.append(" FETCH NEXT ? ROWS ONLY");
            }
        } else {
            sql.append(orderByClause(options));
        }
        return sql.toString();
    }
}
```

**infrastructure/adapter/out/dialect/DialectStrategyFactory.java**

```java
package com.adapta.jdbcdynamic.infrastructure.adapter.out.dialect;

import com.adapta.jdbcdynamic.domain.model.SqlDialect;
import com.adapta.jdbcdynamic.shared.exception.UnsupportedDialectException;
import org.springframework.stereotype.Component;

import java.util.EnumMap;
import java.util.Map;

@Component
public class DialectStrategyFactory {

    private final Map strategies = new EnumMap(SqlDialect.class);

    public DialectStrategyFactory() {
        strategies.put(SqlDialect.MYSQL, new MySqlDialectStrategy());
        strategies.put(SqlDialect.POSTGRESQL, new PostgreSqlDialectStrategy());
        strategies.put(SqlDialect.ORACLE, new OracleDialectStrategy());
        strategies.put(SqlDialect.SQL_SERVER, new SqlServerDialectStrategy());
    }

    public DialectStrategy forDialect(SqlDialect dialect) {
        DialectStrategy strategy = strategies.get(dialect);
        if (strategy == null) {
            throw new UnsupportedDialectException("Dialeto não suportado: " + dialect);
        }
        return strategy;
    }
}
```

**infrastructure/adapter/out/DynamicJdbcRepository.java** — o coração da solução

```java
package com.adapta.jdbcdynamic.infrastructure.adapter.out;

import com.adapta.jdbcdynamic.application.dto.CrudResult;
import com.adapta.jdbcdynamic.application.port.out.DynamicCrudRepositoryPort;
import com.adapta.jdbcdynamic.domain.model.SelectOptions;
import com.adapta.jdbcdynamic.domain.model.SqlDialect;
import com.adapta.jdbcdynamic.domain.model.TableMetadata;
import com.adapta.jdbcdynamic.infrastructure.adapter.out.dialect.DialectStrategy;
import com.adapta.jdbcdynamic.infrastructure.adapter.out.dialect.DialectStrategyFactory;
import com.adapta.jdbcdynamic.infrastructure.adapter.out.dialect.GeneratedKeyMode;
import com.adapta.jdbcdynamic.shared.exception.InvalidPayloadException;
import com.adapta.jdbcdynamic.shared.exception.PersistenceException;
import com.adapta.jdbcdynamic.shared.util.SqlIdentifierValidator;
import org.springframework.stereotype.Repository;

import javax.sql.DataSource;
import java.sql.*;
import java.util.*;

@Repository
public class DynamicJdbcRepository implements DynamicCrudRepositoryPort {

    private final DialectStrategyFactory strategyFactory;
    private final TableMetadataResolver metadataResolver;

    public DynamicJdbcRepository(DialectStrategyFactory strategyFactory, TableMetadataResolver metadataResolver) {
        this.strategyFactory = strategyFactory;
        this.metadataResolver = metadataResolver;
    }

    @Override
    public CrudResult insert(DataSource dataSource, SqlDialect dialect, String table, Map data) {
        if (data == null || data.isEmpty()) {
            throw new InvalidPayloadException("INSERT exige o campo 'data'");
        }
        try (Connection connection = dataSource.getConnection()) {
            DialectStrategy strategy = strategyFactory.forDialect(dialect);
            String tableName = strategy.normalizeForMetadata(table);
            TableMetadata metadata = metadataResolver.resolve(connection, tableName);

            List columns = new ArrayList();
            List values = new ArrayList();
            for (Map.Entry entry : data.entrySet()) {
                String column = metadata.canonicalColumn(entry.getKey());
                SqlIdentifierValidator.validateColumn(column);
                // coluna auto-gerada sem valor: deixa o banco gerar
                if (metadata.isAutoIncrement(column) && entry.getValue() == null) {
                    continue;
                }
                columns.add(column);
                values.add(entry.getValue());
            }
            if (columns.isEmpty()) {
                throw new InvalidPayloadException("Nenhuma coluna válida para INSERT");
            }

            boolean primaryKeyProvided = metadata.hasPrimaryKey()
                && data.entrySet().stream()
                    .anyMatch(e - e.getKey().equalsIgnoreCase(metadata.primaryKeyColumn()) && e.getValue() != null);
            boolean generateKey = metadata.primaryKeyAutoGenerated() && !primaryKeyProvided;

            String sql = strategy.insertSql(tableName, columns, generateKey ? metadata.primaryKeyColumn() : null);
            Object generatedKey = null;
            int affectedRows;

            if (generateKey && strategy.generatedKeyMode() == GeneratedKeyMode.ORACLE_RETURNING) {
                OracleInsertResult result = executeOracleReturning(connection, sql, values);
                affectedRows = result.affectedRows();
                generatedKey = result.generatedKey();
            } else {
                try (PreparedStatement statement = connection.prepareStatement(
                        sql, generateKey ? Statement.RETURN_GENERATED_KEYS : Statement.NO_GENERATED_KEYS)) {
                    bind(statement, values);
                    affectedRows = statement.executeUpdate();
                    if (generateKey) {
                        generatedKey = extractGeneratedKey(statement);
                    }
                }
            }
            return CrudResult.insert(affectedRows, generatedKey);
        } catch (SQLException e) {
            throw new PersistenceException("Falha no INSERT na tabela " + table, e);
        }
    }

    @Override
    public CrudResult update(DataSource dataSource, SqlDialect dialect, String table,
                             Map data, Map where) {
        if (data == null || data.isEmpty()) throw new InvalidPayloadException("UPDATE exige o campo 'data'");
        if (where == null || where.isEmpty()) throw new InvalidPayloadException("UPDATE exige o campo 'where'");
        try (Connection connection = dataSource.getConnection()) {
            DialectStrategy strategy = strategyFactory.forDialect(dialect);
            String tableName = strategy.normalizeForMetadata(table);
            TableMetadata metadata = metadataResolver.resolve(connection, tableName);

            Map canonicalData = canonicalize(data, metadata);
            Map canonicalWhere = canonicalize(where, metadata);

            String sql = strategy.updateSql(tableName, canonicalData, canonicalWhere);
            List params = new ArrayList(canonicalData.values());
            params.addAll(whereParams(canonicalWhere));

            try (PreparedStatement statement = connection.prepareStatement(sql)) {
                bind(statement, params);
                return CrudResult.update(statement.executeUpdate());
            }
        } catch (SQLException e) {
            throw new PersistenceException("Falha no UPDATE na tabela " + table, e);
        }
    }

    @Override
    public CrudResult delete(DataSource dataSource, SqlDialect dialect, String table, Map where) {
        if (where == null || where.isEmpty()) throw new InvalidPayloadException("DELETE exige o campo 'where'");
        try (Connection connection = dataSource.getConnection()) {
            DialectStrategy strategy = strategyFactory.forDialect(dialect);
            String tableName = strategy.normalizeForMetadata(table);
            TableMetadata metadata = metadataResolver.resolve(connection, tableName);

            Map canonicalWhere = canonicalize(where, metadata);
            String sql = strategy.deleteSql(tableName, canonicalWhere);

            try (PreparedStatement statement = connection.prepareStatement(sql)) {
                bind(statement, whereParams(canonicalWhere));
                return CrudResult.delete(statement.executeUpdate());
            }
        } catch (SQLException e) {
            throw new PersistenceException("Falha no DELETE na tabela " + table, e);
        }
    }

    @Override
    public CrudResult select(DataSource dataSource, SqlDialect dialect, String table,
                             Map where, SelectOptions options) {
        try (Connection connection = dataSource.getConnection()) {
            DialectStrategy strategy = strategyFactory.forDialect(dialect);
            String tableName = strategy.normalizeForMetadata(table);
            TableMetadata metadata = metadataResolver.resolve(connection, tableName);

            Map canonicalWhere = canonicalize(where, metadata);
            validateOrderBy(options);

            String sql = strategy.selectSql(tableName, canonicalWhere, options);
            List params = new ArrayList(whereParams(canonicalWhere));
            params.addAll(strategy.paginationParams(options));

            try (PreparedStatement statement = connection.prepareStatement(sql)) {
                bind(statement, params);
                try (ResultSet resultSet = statement.executeQuery()) {
                    return CrudResult.select(mapRows(resultSet));
                }
            }
        } catch (SQLException e) {
            throw new PersistenceException("Falha no SELECT na tabela " + table, e);
        }
    }

    // ---------- helpers ----------

    private OracleInsertResult executeOracleReturning(Connection connection, String sql, List values)
            throws SQLException {
        try (CallableStatement statement = connection.prepareCall(sql)) {
            int index = 1;
            for (Object value : values) {
                statement.setObject(index++, value);
            }
            statement.registerOutParameter(index, Types.NUMERIC);
            int affectedRows = statement.executeUpdate();
            return new OracleInsertResult(affectedRows, statement.getObject(index));
        }
    }

    private record OracleInsertResult(int affectedRows, Object generatedKey) {}

    private Map canonicalize(Map values, TableMetadata metadata) {
        Map result = new LinkedHashMap();
        for (Map.Entry entry : values.entrySet()) {
            String column = metadata.canonicalColumn(entry.getKey());
            SqlIdentifierValidator.validateColumn(column);
            result.put(column, entry.getValue());
        }
        return result;
    }

    private List whereParams(Map where) {
        List params = new ArrayList();
        for (Object value : where.values()) {
            if (value instanceof Collection collection) {
                params.addAll(collection);
            } else {
                params.add(value);
            }
        }
        return params;
    }

    private void validateOrderBy(SelectOptions options) {
        if (options.orderBy() == null || options.orderBy().isBlank()) return;
        String[] parts = options.orderBy().trim().split("\\s+");
        SqlIdentifierValidator.validateColumn(parts[0]);
        if (parts.length  1) {
            String direction = parts[1].toUpperCase();
            if (!direction.equals("ASC") && !direction.equals("DESC")) {
                throw new InvalidPayloadException("orderBy inválido: use 'coluna' ou 'coluna ASC|DESC'");
            }
        }
    }

    private void bind(PreparedStatement statement, List values) throws SQLException {
        for (int i = 0; i  mapRows(ResultSet resultSet) throws SQLException {
        ResultSetMetaData metaData = resultSet.getMetaData();
        int columnCount = metaData.getColumnCount();
        List rows = new ArrayList();
        while (resultSet.next()) {
            Map row = new LinkedHashMap();
            for (int i = 1; i  SqlDialect.fromJdbcUrl("jdbc:db2://localhost:50000/db"))
            .isInstanceOf(UnsupportedDialectException.class);
    }
}
```

**test/.../shared/util/SqlIdentifierValidatorTest.java**

```java
package com.adapta.jdbcdynamic.shared.util;

import com.adapta.jdbcdynamic.shared.exception.InvalidPayloadException;
import org.junit.jupiter.api.Test;

import static org.assertj.core.api.Assertions.assertThatCode;
import static org.assertj.core.api.Assertions.assertThatThrownBy;

class SqlIdentifierValidatorTest {

    @Test
    void deveAceitarTabelaSimples() {
        assertThatCode(() - SqlIdentifierValidator.validateTable("cliente")).doesNotThrowAnyException();
    }

    @Test
    void deveAceitarTabelaComSchema() {
        assertThatCode(() - SqlIdentifierValidator.validateTable("public.cliente")).doesNotThrowAnyException();
    }

    @Test
    void deveRejeitarTabelaComTentativaDeInjecao() {
        assertThatThrownBy(() - SqlIdentifierValidator.validateTable("cliente; DROP TABLE x"))
            .isInstanceOf(InvalidPayloadException.class);
    }

    @Test
    void deveRejeitarColunaComEspaco() {
        assertThatThrownBy(() - SqlIdentifierValidator.validateColumn("nome completo"))
            .isInstanceOf(InvalidPayloadException.class);
    }
}
```

**test/.../infrastructure/adapter/out/dialect/DialectStrategyTest.java**

```java
package com.adapta.jdbcdynamic.infrastructure.adapter.out.dialect;

import com.adapta.jdbcdynamic.domain.model.SelectOptions;
import org.junit.jupiter.api.Test;

import java.util.List;
import java.util.Map;

import static org.assertj.core.api.Assertions.assertThat;

class DialectStrategyTest {

    @Test
    void mysqlDeveGerarInsertSemChaveAutoGerada() {
        // given
        MySqlDialectStrategy strategy = new MySqlDialectStrategy();
        // when
        String sql = strategy.insertSql("cliente", List.of("nome", "idade"), null);
        // then
        assertThat(sql).isEqualTo("INSERT INTO `cliente` (`nome`, `idade`) VALUES (?, ?)");
    }

    @Test
    void oracleDeveGerarInsertComReturning() {
        // given
        OracleDialectStrategy strategy = new OracleDialectStrategy();
        // when
        String sql = strategy.insertSql("cliente", List.of("nome", "idade"), "id");
        // then
        assertThat(sql).isEqualTo("INSERT INTO CLIENTE (NOME, IDADE) VALUES (?, ?) RETURNING ID INTO ?");
    }

    @Test
    void sqlServerDeveGerarSelectComPaginacao() {
        // given
        SqlServerDialectStrategy strategy = new SqlServerDialectStrategy();
        // when
        String sql = strategy.selectSql("cliente", Map.of("ativo", true), new SelectOptions(10, 20, null));
        // then
        assertThat(sql).isEqualTo(
            "SELECT * FROM [cliente] WHERE [ativo] = ? ORDER BY 1 OFFSET ? ROWS FETCH NEXT ? ROWS ONLY");
    }

    @Test
    void postgresDeveGerarSelectComOrderByELimit() {
        // given
        PostgreSqlDialectStrategy strategy = new PostgreSqlDialectStrategy();
        // when
        String sql = strategy.selectSql("cliente", Map.of(), new SelectOptions(10, null, "nome DESC"));
        // then
        assertThat(sql).isEqualTo("SELECT * FROM \"cliente\" ORDER BY \"nome\" DESC LIMIT ?");
    }

    @Test
    void mysqlDeveGerarWhereComIn() {
        // given
        MySqlDialectStrategy strategy = new MySqlDialectStrategy();
        // when
        String sql = strategy.selectSql("cliente", Map.of("id", List.of(1L, 2L, 3L)), SelectOptions.empty());
        // then
        assertThat(sql).isEqualTo("SELECT * FROM `cliente` WHERE `id` IN (?, ?, ?)");
    }
}
```

**test/.../application/usecase/DynamicCrudServiceTest.java**

```java
package com.adapta.jdbcdynamic.application.usecase;

import com.adapta.jdbcdynamic.application.dto.CrudRequest;
import com.adapta.jdbcdynamic.application.dto.CrudResponse;
import com.adapta.jdbcdynamic.application.dto.CrudResult;
import com.adapta.jdbcdynamic.application.port.out.ControlDataSourcePort;
import com.adapta.jdbcdynamic.application.port.out.DataSourceRegistryPort;
import com.adapta.jdbcdynamic.application.port.out.DynamicCrudRepositoryPort;
import com.adapta.jdbcdynamic.domain.model.CrudOperation;
import com.adapta.jdbcdynamic.domain.model.DataSourceConfig;
import com.adapta.jdbcdynamic.domain.model.SelectOptions;
import com.adapta.jdbcdynamic.domain.model.SqlDialect;
import com.adapta.jdbcdynamic.shared.exception.DataSourceNotFoundException;
import com.adapta.jdbcdynamic.shared.exception.InvalidPayloadException;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import javax.sql.DataSource;
import java.util.Map;
import java.util.Optional;

import static org.assertj.core.api.Assertions.assertThat;
import static org.assertj.core.api.Assertions.assertThatThrownBy;
import static org.mockito.ArgumentMatchers.*;
import static org.mockito.BDDMockito.given;
import static org.mockito.Mockito.mock;

@ExtendWith(MockitoExtension.class)
class DynamicCrudServiceTest {

    @Mock private ControlDataSourcePort controlDataSource;
    @Mock private DataSourceRegistryPort dataSourceRegistry;
    @Mock private DynamicCrudRepositoryPort repository;
    @InjectMocks private DynamicCrudService service;

    private static final DataSourceConfig CONFIG =
        new DataSourceConfig("prod", "jdbc:postgresql://localhost:5432/db", "user", "pass");

    @Test
    void deveExecutarInsertQuandoDataSourceExiste() {
        // given
        given(controlDataSource.findByKey("prod")).willReturn(Optional.of(CONFIG));
        given(dataSourceRegistry.getOrCreate(CONFIG)).willReturn(mock(DataSource.class));
        given(repository.insert(any(DataSource.class), eq(SqlDialect.POSTGRESQL), eq("cliente"), anyMap()))
            .willReturn(CrudResult.insert(1, 42L));
        CrudRequest request = new CrudRequest(Map.of("nome", "Maria"), Map.of(), SelectOptions.empty());

        // when
        CrudResponse response = service.execute("prod", "cliente", CrudOperation.INSERT, request);

        // then
        assertThat(response.affectedRows()).isEqualTo(1);
        assertThat(response.generatedKey()).isEqualTo(42L);
        assertThat(response.operation()).isEqualTo(CrudOperation.INSERT);
    }

    @Test
    void deveLancarQuandoDataSourceNaoExiste() {
        // given
        given(controlDataSource.findByKey("inexistente")).willReturn(Optional.empty());
        CrudRequest request = new CrudRequest(Map.of("nome", "Maria"), Map.of(), SelectOptions.empty());

        // when / then
        assertThatThrownBy(() - service.execute("inexistente", "cliente", CrudOperation.INSERT, request))
            .isInstanceOf(DataSourceNotFoundException.class);
    }

    @Test
    void deveBloquearUpdateSemWhere() {
        // given
        CrudRequest request = new CrudRequest(Map.of("nome", "Maria"), Map.of(), SelectOptions.empty());

        // when / then
        assertThatThrownBy(() - service.execute("prod", "cliente", CrudOperation.UPDATE, request))
            .isInstanceOf(InvalidPayloadException.class);
    }

    @Test
    void deveBloquearDeleteSemWhere() {
        // given
        CrudRequest request = new CrudRequest(Map.of(), Map.of(), SelectOptions.empty());

        // when / then
        assertThatThrownBy(() - service.execute("prod", "cliente", CrudOperation.DELETE, request))
            .isInstanceOf(InvalidPayloadException.class);
    }
}
```

**test/.../infrastructure/adapter/out/DynamicJdbcRepositoryIntegrationTest.java** (H2 em modo MySQL, valida o fluxo real de INSERT com chave gerada)

```java
package com.adapta.jdbcdynamic.infrastructure.adapter.out;

import com.adapta.jdbcdynamic.application.dto.CrudResult;
import com.adapta.jdbcdynamic.domain.model.SelectOptions;
import com.adapta.jdbcdynamic.domain.model.SqlDialect;
import com.zaxxer.hikari.HikariConfig;
import com.zaxxer.hikari.HikariDataSource;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.jdbc.core.JdbcTemplate;

import java.util.Map;

import static org.assertj.core.api.Assertions.assertThat;

class DynamicJdbcRepositoryIntegrationTest {

    private HikariDataSource dataSource;
    private DynamicJdbcRepository repository;

    @BeforeEach
    void setUp() {
        HikariConfig config = new HikariConfig();
        config.setJdbcUrl("jdbc:h2:mem:testdb;MODE=MySQL;DATABASE_TO_LOWER=TRUE;DB_CLOSE_DELAY=-1");
        config.setUsername("sa");
        config.setPassword("");
        dataSource = new HikariDataSource(config);

        JdbcTemplate jdbc = new JdbcTemplate(dataSource);
        jdbc.execute("CREATE TABLE cliente (id BIGINT AUTO_INCREMENT PRIMARY KEY, nome VARCHAR(100), idade INT)");

        repository = new DynamicJdbcRepository(new DialectStrategyFactory(), new TableMetadataResolver());
    }

    @AfterEach
    void tearDown() {
        dataSource.close();
    }

    @Test
    void deveInserirEGerarChaveAutomaticamente() {
        // when
        CrudResult result = repository.insert(dataSource, SqlDialect.MYSQL, "cliente",
            Map.of("nome", "Maria", "idade", 30));

        // then
        assertThat(result.affectedRows()).isEqualTo(1);
        assertThat(result.generatedKey()).isNotNull();
    }

    @Test
    void deveAtualizarComWhere() {
        // given
        repository.insert(dataSource, SqlDialect.MYSQL, "cliente", Map.of("nome", "Maria", "idade", 30));

        // when
        CrudResult result = repository.update(dataSource, SqlDialect.MYSQL, "cliente",
            Map.of("idade", 31), Map.of("nome", "Maria"));

        // then
        assertThat(result.affectedRows()).isEqualTo(1);
    }

    @Test
    void deveDeletarComWhere() {
        // given
        repository.insert(dataSource, SqlDialect.MYSQL, "cliente", Map.of("nome", "Maria", "idade", 30));

        // when
        CrudResult result = repository.delete(dataSource, SqlDialect.MYSQL, "cliente", Map.of("nome", "Maria"));

        // then
        assertThat(result.affectedRows()).isEqualTo(1);
    }

    @Test
    void deveSelecionarComFiltro() {
        // given
        repository.insert(dataSource, SqlDialect.MYSQL, "cliente", Map.of("nome", "Maria", "idade", 30));
        repository.insert(dataSource, SqlDialect.MYSQL, "cliente", Map.of("nome", "João", "idade", 25));

        // when
        CrudResult result = repository.select(dataSource, SqlDialect.MYSQL, "cliente",
            Map.of("idade", 30), SelectOptions.empty());

        // then
        assertThat(result.rows()).hasSize(1);
        assertThat(result.rows().getFirst().get("nome")).isEqualTo("Maria");
    }
}
```

## 7. README.md (contrato da API)

```markdown
# jdbc-dynamic-api

API REST dinâmica sobre **JDBC puro** (sem ORM) com suporte multi-banco: MySQL, PostgreSQL, Oracle e SQL Server.

## Como funciona

1. A aplicação consulta a tabela `DATA_SOURCE` no banco de controle usando a chave da URL.
2. Cria/recupera um pool HikariCP para o banco de destino (cache por chave).
3. Detecta o dialeto pela JDBC URL (`jdbc:mysql:`, `jdbc:postgresql:`, `jdbc:oracle:`, `jdbc:sqlserver:`).
4. Lê os metadados da tabela (PK, colunas auto-increment) via `DatabaseMetaData`.
5. Monta o SQL com `PreparedStatement` (anti SQL injection) e executa.

## Endpoints

| Método | Rota | Operação |
|--------|------|----------|
| POST   | /api/v1/{datasource}/{table} | INSERT |
| PUT    | /api/v1/{datasource}/{table} | UPDATE |
| DELETE | /api/v1/{datasource}/{table} | DELETE |
| GET    | /api/v1/{datasource}/{table} | SELECT |

Body: `Map` com as chaves reservadas `data`, `where` e `options`.

## Exemplos

INSERT:

```bash
curl -X POST http://localhost:8080/api/v1/mysql-prod/cliente \
  -H "Content-Type: application/json" \
  -d '{"data": {"nome": "Maria", "idade": 30}}'
```

UPDATE (exige `where`):

```bash
curl -X PUT http://localhost:8080/api/v1/mysql-prod/cliente \
  -H "Content-Type: application/json" \
  -d '{"data": {"idade": 31}, "where": {"id": 1}}'
```

DELETE (exige `where`):

```bash
curl -X DELETE http://localhost:8080/api/v1/mysql-prod/cliente \
  -H "Content-Type: application/json" \
  -d '{"where": {"id": 1}}'
```

SELECT (query params ou body):

```bash
curl "http://localhost:8080/api/v1/mysql-prod/cliente?idade=30&limit=10&offset=0&orderBy=nome"
```

```bash
curl -X GET http://localhost:8080/api/v1/mysql-prod/cliente \
  -H "Content-Type: application/json" \
  -d '{"where": {"id": [1, 2, 3]}, "options": {"limit": 10, "orderBy": "nome DESC"}}'
```

## Inteligência no INSERT (por banco)

| Banco | PK auto-gerada | Estratégia |
|-------|----------------|------------|
| MySQL | AUTO_INCREMENT | omite a coluna e lê `getGeneratedKeys()` |
| PostgreSQL | SERIAL / IDENTITY | omite e lê `getGeneratedKeys()` (driver usa RETURNING) |
| SQL Server | IDENTITY | omite e lê `getGeneratedKeys()` (driver usa OUTPUT INSERTED) |
| Oracle | IDENTITY | omite e usa `INSERT ... RETURNING pk INTO ?` (CallableStatement) |
| Oracle | sem IDENTITY | exige a PK no payload (sem mágica de sequence) |

Se a PK for enviada no payload, ela é respeitada (insert explícito). Colunas auto-increment enviadas como `null` são omitidas.

## Segurança

- 100% `PreparedStatement` — valores nunca concatenados no SQL.
- Whitelist de identificadores (regex) para tabela/coluna — bloqueia injeção via nomes.
- UPDATE e DELETE são bloqueados sem `where`.
- Senhas do `DATA_SOURCE`: recomenda-se criptografia em repouso (KMS/vault) — ponto de extensão no `ControlDataSourceRepository`.
```

## Resumindo

- **JDBC puro + multi-banco**: dialeto detectado pela JDBC URL e estratégia por banco (quoting, paginação e geração de chave diferentes).
- **Inteligência no INSERT**: metadados via `DatabaseMetaData` identificam PK e colunas auto-increment; Oracle usa `RETURNING ... INTO ?`, os demais usam `getGeneratedKeys()`.
- **Segurança**: `PreparedStatement` em tudo, whitelist de identificadores e UPDATE/DELETE bloqueados sem `where`.
- **Clean Architecture**: `domain` / `application` / `infrastructure` / `shared`, records, injeção por construtor, sem Lombok, `@ControllerAdvice`, logs JSON com Correlation ID e Actuator/Prometheus.

Se quiser, posso:
- Gerar um `docker-compose.yml` com os 4 bancos para testar localmente
- Adicionar autenticação JWT nos endpoints
- Criar um health check customizado que valida as conexões do `DATA_SOURCE`
- Adicionar suporte a transações (ex.: `@Transactional` no caso de uso)
