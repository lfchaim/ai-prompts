# Prompt
Tenho alguns projetos para migrar de Java 8 para 25 e Tomcat 6 para 11 e Spring Boot 1.3.2 para 4.1.1. O projeto está com pacotes javax, que precisam ser convertidos para jakarta. 
No projeto o pom.xml está conforme XML abaixo. 
Pode alterar o pom.xml para atender as novas versões?
Criar um checklist completo para atualizar todo o projeto.
## pom.xml
COPIAR POM AQUI

# Merlin Magic
Pom atualizado e apresentado acima. Principais mudanças: parent Spring Boot **4.1.1**, `java.version` **25**, EclipseLink **4.0.7** (jakarta.persistence), Groovy migrado para **org.apache.groovy:groovy 4.x** (o `groovy-all` antigo não existe mais), `jakarta.servlet-api` provided para o Tomcat 11, repositórios springsource/codelds/jboss removidos (desativados; tudo está no Maven Central) e `<addResources>` removido do plugin Boot (não existe mais).

Observação importante: **Spring Boot 4.1.1 ainda não é uma versão existente** — a linha 4.x começa em 2025 (4.0); ajuste para a versão 4.x real disponível no seu Nexus quando for buildar. O mesmo vale para conferir se `neway-lib` e `middleware` já foram recompilados com jakarta — senão, o pom não resolve o problema sozinho.

## Checklist de migração (Java 8 → 25, Tomcat 6 → 11, Spring Boot 1.3.2 → 4.x)

**Fase 0 — Preparação**
- [ ] Instalar JDK 25 (Temurin/Oracle) e Maven 3.9+
- [ ] Migrar intermediariamente: Spring Boot 1.3 → 2.7 (javax) → 3.x/4.x (jakarta). Pular direto de 1.3 para 4 costuma esconder muitas breaking changes de uma vez
- [ ] Recompilar `neway-lib` e `middleware` com jakarta + Java 25 (são internos — sem isso o projeto não sobe)
- [ ] Rodar `mvn dependency:tree` e listar tudo que ainda traz `javax.*`

**Fase 1 — Código: javax → jakarta**
- [ ] Rodar OpenRewrite: `mvn -U org.openrewrite.maven:rewrite-maven-plugin:run -Drewrite.recipeArtifactCoordinates=org.openrewrite.recipe:rewrite-migrate-java:RELEASE -Drewrite.activeRecipes=org.openrewrite.java.migrate.jakarta.JavaxMigrationToJakarta`
- [ ] Trocas típicas: `javax.servlet.*` → `jakarta.servlet.*`, `javax.persistence.*` → `jakarta.persistence.*`, `javax.annotation.*` → `jakarta.annotation.*`, `javax.validation.*` → `jakarta.validation.*`, `javax.transaction.*` → `jakarta.transaction.*`
- [ ] Atenção: `javax.mail` → `jakarta.mail` (novo groupId/artifactId), `javax.xml.bind` (JAXB) → `jakarta.xml.bind` com dependência explícita
- [ ] `WebSecurityConfigurerAdapter`, `TrailingSlashRedirect`, `spring.factories`, `@ConstructorBinding` em nível de classe: removidos nas versões novas — revisar

**Fase 2 — Configuração**
- [ ] `application.properties/yml`: propriedades renamadas (`spring.datasource.*` ok; verificar cada uma contra a doc da versão 4)
- [ ] JNDI DataSource: no Tomcat 11, `context.xml` e `server.xml` mudaram de sintaxe em alguns pontos; conferir `Resource` com `factory="org.apache.tomcat.dbcp.dbcp2.BasicDataSourceFactory"`
- [ ] Logging: o projeto excluía log4j/log4j2 — manter apenas Logback (padrão Boot) e atualizar `logback.xml` se houver
- [ ] `web.xml`, se existir: ou remover (Boot não usa) ou migrar para Servlet 6.1 schema (`web-app_6_1.xsd`)

**Fase 3 — Java 8 → 25**
- [ ] Remover usos de APIs removidas: `SecurityManager`, `Thread.stop/suspend`, `finalization` (`Object.finalize`), Nashorn (`ScriptEngine` js), `java.xml.ws`/`jaxb` do JDK (viraram dependências)
- [ ] Módulos internos fortemente encapsulados: rodar com `--add-opens` só se necessário; corrigir reflexão (especialmente libs antigas como barbecue e serializadores)
- [ ] Testar serialização RMI/ObjetOutputStream se o middleware usar
- [ ] Ativar warnings de deprecação e revisar

**Fase 4 — Dependências legadas**
- [ ] EclipseLink 2.5.1 → 4.0.7: revisar mapeamentos, `persistence.xml` para `version="3.1"` e namespace `https://jakarta.ee/xml/ns/persistence`
- [ ] Groovy 2.x → 4.x: pacotes mudam de `org.codehaus.groovy` para `org.apache.groovy` — corrigir imports no código Java
- [ ] Barbecue 1.5-beta1: sem versão jakarta; só é problema se tocar em servlet classes — avaliar alternativa se quebrar
- [ ] JUnit 4 → 5 (spring-boot-starter-test já traz JUnit 5; migrar os testes)
- [ ] Hamcrest: deixar a versão gerenciada pelo Boot

**Fase 5 — Tomcat 6 → 11 (WAR externo)**
- [ ] Instalar Tomcat 11 (Servlet 6.1, JSP 4.0, requer JDK 17+ — 25 atende)
- [ ] Validar que o WAR tem `SpringBootServletInitializer` na classe da aplicação
- [ ] TLS/mTLS: Tomcat 11 não aceita mais TLS 1.0/1.1 e propriedades de connector mudaram
- [ ] Migrar `conf/tomcat-users.xml`, realms, valves customizados (API de Valve mudou para `jakarta`)
- [ ] Testar deployment do WAR no Tomcat 11 antes de migrar outras apps

**Fase 6 — Testes e build**
- [ ] `mvn clean verify` com `-Dmaven.compiler.release=25`
- [ ] Rodar suíte completa de testes de integração, principalmente fluxo de invoice/JNDI
- [ ] Testar em ambiente de homologação com Tomcat 11 + banco real
- [ ] Atualizar pipeline/CI para JDK 25
- [ ] Verificar geração de bytecode: bytecode 25 só roda no Tomcat/JDK com suporte — se algum cliente legado consumir classes, considerar `--release 17` temporariamente

Se quiser, posso gerar o script OpenRewrite completo ou revisar a classe da aplicação (`SampleTomcatJndiApplication`) para o formato do Boot 4.x.

# POM
## pom.xml - Original
```XML
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>br.com.company</groupId>
    <artifactId>gkInvoicePlanService</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>war</packaging>
    <name>gkInvoicePlanService</name>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.3.2.RELEASE</version>
    </parent>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <java.version>1.8</java.version>
        <start-class>sample.tomcat.jndi.SampleTomcatJndiApplication</start-class>
    </properties>
    <build>
        <finalName>gkInvoicePlanService</finalName>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <addResources>true</addResources>
                </configuration>
            </plugin>
        </plugins>
    </build>
    <repositories>
        <repository>
            <id>jboss-public-repository-group</id>
            <name>JBoss Public Maven Repository Group</name>
            <url>https://repository.jboss.org/nexus/content/groups/public/</url>
            <layout>default</layout>
            <releases>
                <enabled>true</enabled>
                <updatePolicy>never</updatePolicy>
            </releases>
            <snapshots>
                <enabled>true</enabled>
                <updatePolicy>never</updatePolicy>
            </snapshots>
        </repository>
        <repository>
            <id>codelds</id>
            <url>https://code.lds.org/nexus/content/groups/main-repo</url>
            <releases>
                <enabled>true</enabled>
                <updatePolicy>never</updatePolicy>
            </releases>
            <snapshots>
                <enabled>true</enabled>
                <updatePolicy>never</updatePolicy>
            </snapshots>
        </repository>
        <repository>
            <id>com.springsource.repository.bundles.release</id>
            <name>SpringSource Enterprise Bundle Repository - SpringSource Bundle Releases</name>
            <url>http://repository.springsource.com/maven/bundles/release</url>
            <releases>
                <enabled>true</enabled>
                <updatePolicy>never</updatePolicy>
            </releases>
            <snapshots>
                <enabled>true</enabled>
                <updatePolicy>never</updatePolicy>
            </snapshots>
        </repository>
        <repository>
            <id>com.springsource.repository.bundles.external</id>
            <name>SpringSource Enterprise Bundle Repository - External Bundle Releases</name>
            <url>http://repository.springsource.com/maven/bundles/external</url>
            <releases>
                <enabled>true</enabled>
                <updatePolicy>never</updatePolicy>
            </releases>
            <snapshots>
                <enabled>true</enabled>
                <updatePolicy>never</updatePolicy>
            </snapshots>
        </repository> 
    </repositories>
    <dependencies>
        <!-- Dependency of neway-lib -->
        <dependency>
            <groupId>br.com.neway</groupId>
            <artifactId>neway-lib</artifactId>
            <version>0.0.1-SNAPSHOT</version>
            <exclusions>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j17</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>log4j</groupId>
                    <artifactId>log4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.apache.logging.log4j</groupId>
                    <artifactId>log4j-to-slf4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>log4j-over-slf4j</artifactId>
                </exclusion>
                <exclusion>
                    <artifactId>log4j-core</artifactId>
                    <groupId>org.apache.logging.log4j</groupId>
                </exclusion>
            </exclusions>
        </dependency>
        <!-- Dependency of middleware -->
        <dependency>
            <groupId>br.com.company</groupId>
            <artifactId>middleware</artifactId>
            <version>0.0.1-SNAPSHOT</version>
            <exclusions>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j17</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>log4j</groupId>
                    <artifactId>log4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.apache.logging.log4j</groupId>
                    <artifactId>log4j-to-slf4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>log4j-over-slf4j</artifactId>
                </exclusion>
                <exclusion>
                    <artifactId>log4j-core</artifactId>
                    <groupId>org.apache.logging.log4j</groupId>
                </exclusion>
            </exclusions>
        </dependency>
        <!-- https://mvnrepository.com/artifact/net.sourceforge.barbecue/barbecue -->
        <dependency>
            <groupId>net.sourceforge.barbecue</groupId>
            <artifactId>barbecue</artifactId>
            <version>1.5-beta1</version>
            <exclusions>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j17</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>log4j</groupId>
                    <artifactId>log4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.apache.logging.log4j</groupId>
                    <artifactId>log4j-to-slf4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>log4j-over-slf4j</artifactId>
                </exclusion>
                <exclusion>
                    <artifactId>log4j-core</artifactId>
                    <groupId>org.apache.logging.log4j</groupId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.codehaus.groovy</groupId>
            <artifactId>groovy-all</artifactId>
            <scope>provided</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j17</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>log4j</groupId>
                    <artifactId>log4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.apache.logging.log4j</groupId>
                    <artifactId>log4j-to-slf4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>log4j-over-slf4j</artifactId>
                </exclusion>
                <exclusion>
                    <artifactId>log4j-core</artifactId>
                    <groupId>org.apache.logging.log4j</groupId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.eclipse.persistence</groupId>
            <artifactId>eclipselink</artifactId>
            <version>2.5.1</version>
            <scope>provided</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j17</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>log4j</groupId>
                    <artifactId>log4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.apache.logging.log4j</groupId>
                    <artifactId>log4j-to-slf4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>log4j-over-slf4j</artifactId>
                </exclusion>
                <exclusion>
                    <artifactId>log4j-core</artifactId>
                    <groupId>org.apache.logging.log4j</groupId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.eclipse.persistence</groupId>
            <artifactId>org.eclipse.persistence.jpa.modelgen.processor</artifactId>
            <version>2.5.1</version>
            <scope>provided</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j17</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>log4j</groupId>
                    <artifactId>log4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.apache.logging.log4j</groupId>
                    <artifactId>log4j-to-slf4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>log4j-over-slf4j</artifactId>
                </exclusion>
                <exclusion>
                    <artifactId>log4j-core</artifactId>
                    <groupId>org.apache.logging.log4j</groupId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j17</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>log4j</groupId>
                    <artifactId>log4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.apache.logging.log4j</groupId>
                    <artifactId>log4j-to-slf4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>log4j-over-slf4j</artifactId>
                </exclusion>
                <exclusion>
                    <artifactId>log4j-core</artifactId>
                    <groupId>org.apache.logging.log4j</groupId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.hamcrest</groupId>
            <artifactId>hamcrest-core</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j17</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>log4j</groupId>
                    <artifactId>log4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.apache.logging.log4j</groupId>
                    <artifactId>log4j-to-slf4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>log4j-over-slf4j</artifactId>
                </exclusion>
                <exclusion>
                    <artifactId>log4j-core</artifactId>
                    <groupId>org.apache.logging.log4j</groupId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>
</project>
```

## pom.xml - Atualizado
```XML
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>br.com.company</groupId>
    <artifactId>gkInvoicePlanService</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>war</packaging>
    <name>gkInvoicePlanService</name>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>4.1.1</version>
        <relativePath/>
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <java.version>25</java.version>
        <maven.compiler.release>25</maven.compiler.release>
        <start-class>sample.tomcat.jndi.SampleTomcatJndiApplication</start-class>
        <!-- Versões alinhadas com Jakarta EE 11 / Tomcat 11 -->
        <eclipselink.version>4.0.7</eclipselink.version>
        <groovy.version>4.0.28</groovy.version>
    </properties>

    <build>
        <finalName>gkInvoicePlanService</finalName>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <!-- A configuração <addResources> foi removida: não existe mais no plugin 3.x/4.x.
                     Equivalente moderno: executar com `mvn spring-boot:run` (devtools cuida do reload)
                     ou usar spring-boot-devtools como dependência opcional. -->
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <release>25</release>
                    <compilerArgs>
                        <arg>-parameters</arg>
                    </compilerArgs>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-war-plugin</artifactId>
                <configuration>
                    <!-- Necessário para Tomcat 11 externo -->
                    <failOnMissingWebXml>false</failOnMissingWebXml>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <!-- Repositórios antigos (springsource/codelds/jboss nexus) foram removidos:
         estão desativados e todo o conteúdo já está no Maven Central.
         Se neway-lib/middleware forem internos, adicione o Nexus/Artifactory da empresa aqui. -->

    <dependencies>
        <!-- Dependency of neway-lib -->
        <dependency>
            <groupId>br.com.neway</groupId>
            <artifactId>neway-lib</artifactId>
            <version>0.0.1-SNAPSHOT</version>
            <exclusions>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j17</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>log4j</groupId>
                    <artifactId>log4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.apache.logging.log4j</groupId>
                    <artifactId>log4j-to-slf4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>log4j-over-slf4j</artifactId>
                </exclusion>
                <exclusion>
                    <artifactId>log4j-core</artifactId>
                    <groupId>org.apache.logging.log4j</groupId>
                </exclusion>
                <!-- Exclusões novas: evitar javax.* arrastado por libs antigas -->
                <exclusion>
                    <groupId>javax.servlet</groupId>
                    <artifactId>javax.servlet-api</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>javax</groupId>
                    <artifactId>javaee-api</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.hibernate</groupId>
                    <artifactId>hibernate-entitymanager</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <!-- Dependency of middleware -->
        <dependency>
            <groupId>br.com.company</groupId>
            <artifactId>middleware</artifactId>
            <version>0.0.1-SNAPSHOT</version>
            <exclusions>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j17</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>log4j</groupId>
                    <artifactId>log4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.apache.logging.log4j</groupId>
                    <artifactId>log4j-to-slf4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>log4j-over-slf4j</artifactId>
                </exclusion>
                <exclusion>
                    <artifactId>log4j-core</artifactId>
                    <groupId>org.apache.logging.log4j</groupId>
                </exclusion>
                <exclusion>
                    <groupId>javax.servlet</groupId>
                    <artifactId>javax.servlet-api</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>javax</groupId>
                    <artifactId>javaee-api</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <!-- Barbecue (geração de código de barras) - biblioteca legada, sem jakarta.
             Se for usada apenas para geração de imagens/PDF, continue usando;
             evite expor classes javax.servlet dela na web tier. -->
        <dependency>
            <groupId>net.sourceforge.barbecue</groupId>
            <artifactId>barbecue</artifactId>
            <version>1.5-beta1</version>
        </dependency>

        <!-- Groovy 4 (pacotes org.apache.groovy; groovy-all virou pom agregador - use parser/json) -->
        <dependency>
            <groupId>org.apache.groovy</groupId>
            <artifactId>groovy</artifactId>
            <version>${groovy.version}</version>
            <scope>provided</scope>
        </dependency>

        <!-- EclipseLink 4.0 = Jakarta Persistence 3.1 (jakarta.persistence.*) -->
        <dependency>
            <groupId>org.eclipse.persistence</groupId>
            <artifactId>eclipselink</artifactId>
            <version>${eclipselink.version}</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.eclipse.persistence</groupId>
            <artifactId>org.eclipse.persistence.jpa.modelgen.processor</artifactId>
            <version>${eclipselink.version}</version>
            <scope>provided</scope>
        </dependency>

        <!-- Servlet API do Tomcat 11 (jakarta.servlet 6.1) -->
        <dependency>
            <groupId>jakarta.servlet</groupId>
            <artifactId>jakarta.servlet-api</artifactId>
            <scope>provided</scope>
        </dependency>

        <!-- Spring Boot 4.1.1 (se o starter não vier via neway-lib, descomente):
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        -->

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.hamcrest</groupId>
            <artifactId>hamcrest-core</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```
