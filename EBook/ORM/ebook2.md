# Prompt
"Criar um eBook abrangente sobre ORM (Mapeamento Objeto-Relacional), intitulado 'Dominando ORM, do zero ao profissional - Uma visão Java e .Net'. O eBook deve explorar a evolução histórica das soluções de ORM, começando com EJB (Enterprise JavaBeans), continuando com Hibernate, NHibernate, JPA (Java Persistence API), Entity Framework, e outras soluções relevantes. O foco principal deve ser nas plataformas Java e .Net.

Organize o conteúdo em ordem cronológica, destacando:

O surgimento e evolução das primeiras soluções de ORM.
Os principais marcos e inovações em cada plataforma (Java e .Net).
As razões por trás do desenvolvimento de novas soluções ou melhorias nas existentes.


Para cada solução ou versão significativa de ORM, discuta:

Os desafios que ela pretendia resolver.
As melhores práticas de implementação.
Casos de estudo que ilustrem tanto o sucesso quanto o fracasso no uso dessas tecnologias.
Lições aprendidas e como elas influenciaram o desenvolvimento de soluções subsequentes.


Ao longo do eBook, inclua:

Exemplos práticos de código para as plataformas Java e .Net, mostrando como implementar ORM de forma eficaz.
Análise de casos reais de uso de ORM, destacando desafios enfrentados e como foram superados.
Discussão sobre as tendências atuais e futuras em ORM, incluindo novas tecnologias e abordagens.


Formato do eBook:

Incluir ilustrações, diagramas e trechos de código para facilitar a compreensão.
Utilizar uma linguagem clara e acessível, evitando jargões excessivamente técnicos quando possível.
Incluir sumário detalhado e referências para leituras adicionais.


Critérios de qualidade:

Precisão técnica e atualidade das informações.
Clareza e organização do conteúdo.
Relevância dos exemplos e casos de estudo para o público-alvo (desenvolvedores Java e .Net).

O objetivo é criar um recurso valioso e informativo que ajude os desenvolvedores a entender a evolução do ORM, suas melhores práticas e como aplicá-los eficazmente em projetos reais."

# Adapta
## Dominando ORM, do zero ao profissional — Uma visão Java e .NET

> Um eBook completo sobre a evolução, as práticas recomendadas e a aplicação eficaz de mapeamento objeto-relacional em projetos reais nas plataformas Java e .NET.

---

## Sumário

1. Introdução: por que ORM?
2. Panorama histórico e linha do tempo (Java e .NET)
3. Primeira era (final dos anos 1990 – meados de 2000)
   - EJB Entity Beans (CMP/BMP)
   - JDO
   - iBATIS/MyBatis (SQL Mapper)
   - ADO.NET e as bases da persistência no .NET
   - LINQ e LINQ to SQL
4. A virada: Hibernate e NHibernate
   - Por que o Hibernate venceu?
   - NHibernate no ecossistema .NET
5. Padronização no Java: JPA
   - JPA 1.0 até Jakarta Persistence 3.x
   - Implementações: Hibernate ORM, EclipseLink, OpenJPA
   - Spring Data JPA
6. A jornada .NET: Entity Framework e EF Core
   - EF 1.x a EF 6.x (clássico)
   - A reescrita: EF Core (1 a 8), recursos e maturidade
   - Micro-ORMs no .NET: Dapper e outros
7. Casos de estudo (sucessos e fracassos) e lições aprendidas
8. Práticas essenciais de ORM (Java e .NET)
   - Modelagem, relacionamentos e herança
   - Transações, UoW, Repository
   - Carregamento (lazy/eager), N+1, paginação e junções
   - Caching (L1/L2), filtros, soft delete
   - Concurrency (optimistic/pessimistic), auditoria
   - Migrations e versionamento de schema
   - Testes (integração, dados seed, ambientes)
   - Observabilidade, performance e segurança
9. Exemplos práticos de código (Java e .NET)
10. Tendências atuais e futuras em ORM
11. Referências e leituras recomendadas

---

## 1. Introdução: por que ORM?

O mapeamento objeto-relacional (ORM) tenta reduzir o “impedance mismatch” entre o modelo orientado a objetos (classes, herança, encapsulamento) e o modelo relacional (tabelas, colunas, chaves). Em resumo:

- Objetivo: produtividade, portabilidade entre bancos, segurança (parametrização), consistência transacional, e reutilização de padrões (Unit of Work, Identity Map, Repository).
- Riscos: abstração excessiva do SQL, consultas ineficientes (N+1), camadas complexas, acoplamento indevido do domínio ao banco.

Este eBook percorre a evolução do ORM em Java e .NET, mostra como aplicá-lo com qualidade e discute práticas modernas para obter desempenho e previsibilidade.

---

## 2. Panorama histórico e linha do tempo (Java e .NET)

Linha do tempo simplificada com marcos importantes:

- 1998–2003 (Java):
  - EJB 1.x/2.x: Entity Beans (BMP/CMP) — complexidade elevada.
  - JDO (2002): tentativa de padronização para persistência de objetos.
- 2001–2005 (Java):
  - Hibernate (1.x: ~2001; 2.x: ~2003; 3.0: 2005) — POJOs, HQL/Criteria, popularidade massiva.
- 2006–2017 (Java):
  - EJB 3.0 (2006) simplifica e nasce JPA 1.0 (JSR 220).
  - JPA 2.0 (2009), 2.1 (2013), 2.2 (2017). Implementações: Hibernate, EclipseLink (ref.), OpenJPA.
- 2020+ (Java):
  - Jakarta Persistence 3.x (mudança de namespace javax -> jakarta).
  - Hibernate 6 (2021/2022+): novo SQL AST, melhorias de performance e Jakarta.
- 2005–2013 (.NET):
  - ADO.NET base; LINQ (2007); LINQ to SQL (2007; limitado).
  - Entity Framework 1.0 (2008); EF 4.0 (2010); EF 4.1 Code First (2011); EF 5 (2012); EF 6 (2013+).
- 2016–2023 (.NET):
  - EF Core 1.0 (2016) — reescrito, cross-platform.
  - EF Core 2 (2017), 3 (2019), 5 (2020), 6 (2021), 7 (2022), 8 (2023).
  - Micro-ORMs: Dapper (~2011+) ganha espaço pelo desempenho.

Razões para a evolução:
- Reduzir complexidade (EJB 2.x -> Hibernate/JPA).
- Padronizar APIs e metadados (JPA).
- Alinhar-se a .NET multiplataforma e performance (EF Core).
- Adotar recursos modernos de bancos (JSON, tipos avançados, colunas temporais) e melhorar o tradutor de consultas.

---

## 3. Primeira era: EJB Entity Beans, JDO, iBATIS/MyBatis, ADO.NET, LINQ to SQL

### EJB Entity Beans (CMP/BMP)

- Desafio que pretendiam resolver:
  - Persistência transacional padronizada em servidores de aplicações (J2EE).
- Problemas:
  - Ciclo de vida complexo, dependência do container, acoplamento (interfaces remotas, descriptors XML extensos), dificuldade de testes.
- Lições:
  - Abstrações “pesadas” e invasivas prejudicam produtividade.
  - A comunidade buscou soluções POJO-friendly.

### JDO (Java Data Objects)

- Objetivo:
  - Padronização de persistência independente de tecnologia de armazenamento (relacional, objeto, etc.).
- Por que não “pegou” como JPA:
  - Adoção limitada no mercado corporativo; Hibernate ganhou tração com foco relacional.

### iBATIS/MyBatis (SQL Mapper)

- Proposta:
  - Você escreve o SQL; o framework mapeia resultados para objetos.
- Quando usar:
  - Consultas altamente otimizadas e controle fino de SQL.
- Limites:
  - Não é ORM completo (não rastreia mudanças como UoW; sem cache L2 nativo como Hibernate).

### .NET: ADO.NET, LINQ e LINQ to SQL

- ADO.NET:
  - Base de acesso; DataReaders, DataSets. Baixo nível e muito código repetitivo.
- LINQ (2007):
  - Introduz consultas tipadas na linguagem.
- LINQ to SQL (2007):
  - ORM para SQL Server; simples, mas limitado (herança, múltiplos provedores).

---

## 4. A virada: Hibernate e NHibernate

### Hibernate (Java)

- O que resolveu:
  - Simplicidade com POJOs, HQL, cache L1/L2, transações declarativas, lazy loading, mapeamento abrangente.
- Marcas históricas:
  - Hibernate 3 consolidou recursos; integração com Spring popularizou.
  - Posteriormente tornou-se a implementação JPA mais usada (nas organizações).
- Por que venceu:
  - Menos cerimônia que EJB 2, ótimo material, comunidade ativa e foco pragmaticamente relacional.

### NHibernate (.NET)

- Port do Hibernate para .NET.
- Pontos fortes:
  - Poderoso mapeamento, recursos maduros (cache L2, interceptors, batch fetching).
- Por que perdeu espaço para EF/EF Core:
  - Microsoft impulsionou EF; tooling e integração no ecossistema .NET com forte adoção.

---

## 5. Padronização no Java: JPA até Jakarta

- JPA 1.0 (2006/EJB 3.0):
  - Anotações, EntityManager, JPQL.
- JPA 2.0 (2009):
  - Criteria API, cache de segundo nível padronizado, mapeamentos melhorados.
- JPA 2.1 (2013):
  - Stored procedures, Entity Graphs, Converter.
- JPA 2.2 (2017):
  - Suporte a Java 8 (Streams, Date/Time).
- Jakarta Persistence 3.x (2020+):
  - Migração de javax.persistence para jakarta.persistence sob a Eclipse Foundation.
  - Integração com frameworks modernos (MicroProfile, Quarkus, Spring no namespace jakarta).

Implementações:
- Hibernate ORM: de longe a mais usada.
- EclipseLink: implementação de referência do JPA.
- OpenJPA: alternativa Apache.

Spring Data JPA:
- Repositórios, Query Methods, Specifications, Projections.
- Produtividade e integração com transações (@Transactional).

---

## 6. A jornada .NET: EF clássico até EF Core e micro-ORMs

### Entity Framework (EF 1 a 6)

- EF 1 (2008) foi criticado (designer pesado, pouco “code-first”).
- EF 4 (2010) melhorou; EF 4.1 trouxe Code First (2011).
- EF 5/6 amadureceram:
  - Migrations, interceptors, LINQ robusto, Async (6.x), mapeamentos mais ricos.
- EF 6 permanece relevante para legados .NET Framework.

### EF Core (1 a 8)

- Reescrita para .NET moderno, cross-platform, alta performance e melhor controle.
- Trajetória:
  - 1.x (2016): faltaram recursos; evolução rápida.
  - 2.x (2017) e 3.x (2019): mudanças de tradução de consultas (quebra de comportamento para correctitude).
  - 5 (2020), 6 (2021): recursos sólidos (TPT/TPC, interceptors, melhor desempenho).
  - 7 (2022): ExecuteUpdate/ExecuteDelete server-side, performance, Include aprimorado.
  - 8 (2023): mais performance, coleções primitivas e avanços de mapeamento; suporte a JSON e recursos específicos onde o provedor suporta (por exemplo, PostgreSQL com Npgsql).
- EF Core hoje:
  - Integração forte com ASP.NET Core, DI, Migrations, Health Checks, Observabilidade (logs, eventos).

### Micro-ORMs: Dapper (e outros)

- Dapper:
  - Foco em velocidade e controle do SQL. Excelente para consultas críticas.
- Padrão híbrido:
  - EF/EF Core para CRUD e domínio; Dapper para relatórios e queries altamente otimizadas.

---

## 7. Casos de estudo e lições aprendidas

Nota: os casos a seguir são composições realistas baseadas em experiências comuns de mercado (sem citar empresas específicas).

1) EJB 2.x em banco crítico
- Situação: sistema bancário com Entity Beans CMP.
- Problemas: deploys demorados, testes difíceis, performance irregular.
- Virada: migração gradual para Hibernate + Spring; simplificou domínio, testes unitários e integração.
- Lições: reduzir cerimônia e acoplamento ao container melhora ciclo de vida e qualidade.

2) Hibernate mal configurado e N+1
- Situação: e-commerce com queda em datas promocionais.
- Causa: consultas com lazy loading em coleções, N+1 massivo.
- Correção: fetch joins, batch size, caches e monitoramento; ganhos de 10x em endpoints críticos.
- Lições: medição, carga de relacionamento consciente, e escrita de consultas explícitas.

3) EF 6 com migrações manuais
- Situação: equipe ignorava migrations e aplicava scripts isolados.
- Resultado: drift de schema entre ambientes, bugs intermitentes.
- Correção: padronizar migrations, revisão de PRs, pipeline CI/CD para aplicar e validar.
- Lições: disciplina de versionamento de banco é tão importante quanto versionar código.

4) EF Core e consultas complexas
- Situação: relatórios avançados com muitas agregações e janelas.
- Solução: usar Dapper/jOOQ-like (no Java) ou raw SQL mapeado para DTOs nessas rotas.
- Lições: ORM é excelente para o “80% do CRUD”, mas SQL explícito brilha no 20% crítico.

---

## 8. Práticas essenciais de ORM

### Modelagem e relacionamentos
- Use chaves artificiais (surrogate keys) na maioria dos casos; preserve naturais como únicas.
- Mapeie relacionamentos com intenção clara:
  - Muitos-para-um (ManyToOne/HasOne) lazy por padrão.
  - Um-para-muitos com cuidado (evite “bag” simultâneo em várias coleções em Hibernate; considere Set/Ordered).
- Herança:
  - TPH (Table-per-Hierarchy): simples e rápido, porém coluna discriminadora e sparse columns.
  - TPT (Table-per-Type): mais normalizado, pode custar joins.
  - TPC (Table-per-Concrete): sem joins, mas mais redundância.

### Unidade de Trabalho e Repositório
- Java:
  - EntityManager/Session já são a UoW; repositórios via Spring Data JPA evitam boilerplate.
- .NET:
  - DbContext é a UoW. Evite repositórios genéricos excessivos; prefira DbContext mais rico + padrões específicos quando necessário.

### Transações
- Delimite transações no nível de serviço (aplicação).
- Java: @Transactional (Spring) ou JTA quando distribuído.
- .NET: TransactionScope ou BeginTransaction no DbContext; preferir transações curtas e no mesmo recurso.

### Carregamento e N+1
- Prefira lazy por padrão; carregue explicitamente o que precisa:
  - Java/JPA: fetch join em JPQL, EntityGraph, batch-size.
  - EF Core: Include/ThenInclude; AsSplitQuery para evitar explosões cartesianas; desative lazy proxies quando confuso.
- Paginação com ordenação estável.

### Caching
- L1 (por sessão/contexto): default.
- L2:
  - Hibernate: Ehcache/Infinispan, entities e collections com cuidado para invalidação.
  - EF/EF Core: sem L2 nativo; use bibliotecas externas ou cache de leitura (DTOs) separado.
- Query cache: só quando necessário e previsível.

### Filtros e soft delete
- Java/Hibernate: @Filter, @Where (soft delete).
- EF Core: HasQueryFilter global; colunas IsDeleted e políticas de exclusão.

### Concorrência
- Optimistic locking:
  - Java: @Version (long, Instant).
  - .NET: [Timestamp]/rowversion.
- Pessimistic locking: apenas quando inevitável; avalie impacto.

### Auditoria
- Java: Envers (Hibernate), listeners.
- .NET: interceptors/SaveChanges override; colunas de “CreatedAt/UpdatedAt/By”.

### Migrations e versionamento de schema
- Java: Flyway ou Liquibase (script-first previsível).
- .NET: EF Core Migrations (automação forte em CI/CD).
- Regra de ouro: nunca aplicar migrations manualmente em produção sem pipeline e backup.

### Testes
- Integração:
  - Java: Testcontainers, H2 apenas para casos simples (diferenças de dialeto podem enganar).
  - .NET: Testcontainers, SQL Server local/containers ou SQLite com ressalvas.
- Dados seed e rollback por teste.

### Observabilidade, performance e segurança
- Logs de SQL com parâmetros.
- Métricas: tempo de query, contagem, pool de conexões.
- Índices adequados, profiling (explain plans).
- Segurança: parâmetros sempre; cuidado com SQL bruto; políticas de acesso por contexto do usuário.

---

## 9. Exemplos práticos de código

### 9.1 Java — JPA/Hibernate com Spring Data

Dependências (Maven, excerto):
```xml
<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
  </dependency>
  <dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
  </dependency>
  <!-- Hibernate é trazido pelo starter; para Jakarta use versões compatíveis -->
</dependencies>
```

Entidade com relacionamento e versionamento:
```java
import jakarta.persistence.*;
import java.time.Instant;
import java.util.HashSet;
import java.util.Set;

@Entity
@Table(name = "orders")
public class Order {
  @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
  private Long id;

  @Column(nullable = false)
  private String customerEmail;

  @OneToMany(mappedBy = "order", cascade = CascadeType.ALL, orphanRemoval = true, fetch = FetchType.LAZY)
  private Set<OrderItem> items = new HashSet<>();

  @Version
  private Long version;

  @Column(nullable = false, updatable = false)
  private Instant createdAt = Instant.now();

  // getters e setters
}

@Entity
@Table(name = "order_items")
public class OrderItem {
  @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
  private Long id;

  @ManyToOne(fetch = FetchType.LAZY, optional = false)
  private Order order;

  @Column(nullable = false)
  private String sku;

  @Column(nullable = false)
  private int quantity;

  // getters e setters
}
```

Repositório com Spring Data JPA:
```java
import org.springframework.data.jpa.repository.*;
import org.springframework.data.repository.query.Param;
import java.util.List;

public interface OrderRepository extends JpaRepository<Order, Long> {
  // Evitar N+1 com fetch join
  @Query("select o from Order o left join fetch o.items where o.id = :id")
  Order findByIdWithItems(@Param("id") Long id);

  List<Order> findByCustomerEmailIgnoreCase(String email);
}
```

Serviço transacional:
```java
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class OrderService {
  private final OrderRepository repo;

  public OrderService(OrderRepository repo) { this.repo = repo; }

  @Transactional
  public Long placeOrder(String email, List<OrderItem> items) {
    Order o = new Order();
    o.setCustomerEmail(email);
    for (OrderItem it : items) {
      it.setOrder(o);
      o.getItems().add(it);
    }
    return repo.save(o).getId();
  }

  @Transactional(readOnly = true)
  public Order getOrder(Long id) {
    return repo.findByIdWithItems(id);
  }
}
```

Soft delete com filtro (Hibernate):
```java
@Entity
@org.hibernate.annotations.Where(clause = "deleted = false")
public class Product {
  @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
  private Long id;

  private String name;

  @Column(nullable = false)
  private boolean deleted = false;
}
```

Consulta paginada com Criteria API (exemplo simples):
```java
// Com Spring Data, prefira PageRequest. Aqui, pura JPA:
CriteriaBuilder cb = em.getCriteriaBuilder();
CriteriaQuery<Order> cq = cb.createQuery(Order.class);
Root<Order> root = cq.from(Order.class);
cq.select(root).where(cb.equal(cb.lower(root.get("customerEmail")), email.toLowerCase()));
TypedQuery<Order> q = em.createQuery(cq).setFirstResult(0).setMaxResults(20);
List<Order> page = q.getResultList();
```

### 9.2 .NET — EF Core

Pacote:
```bash
dotnet add package Microsoft.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

Modelos e DbContext:
```csharp
using Microsoft.EntityFrameworkCore;
using System;
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;

public class Order {
  public long Id { get; set; }

  [Required]
  public string CustomerEmail { get; set; } = default!;

  public List<OrderItem> Items { get; set; } = new();

  [Timestamp] // concorrência otimista (rowversion no SQL Server)
  public byte[]? RowVersion { get; set; }

  public DateTime CreatedAt { get; set; } = DateTime.UtcNow;
}

public class OrderItem {
  public long Id { get; set; }
  [Required] public string Sku { get; set; } = default!;
  public int Quantity { get; set; }
  public long OrderId { get; set; }
  public Order Order { get; set; } = default!;
}

public class AppDbContext : DbContext {
  public DbSet<Order> Orders => Set<Order>();
  public DbSet<OrderItem> OrderItems => Set<OrderItem>();

  public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }

  protected override void OnModelCreating(ModelBuilder b) {
    b.Entity<Order>(e => {
      e.HasKey(x => x.Id);
      e.Property(x => x.CustomerEmail).IsRequired();
      e.HasMany(x => x.Items).WithOne(i => i.Order).HasForeignKey(i => i.OrderId);
      e.HasIndex(x => x.CustomerEmail);
    });

    // Filtro global (exemplo de soft delete)
    // b.Entity<Product>().HasQueryFilter(p => !p.Deleted);
  }
}
```

Serviço e consultas:
```csharp
public class OrderService {
  private readonly AppDbContext _db;

  public OrderService(AppDbContext db) => _db = db;

  public async Task<long> PlaceOrderAsync(string email, IEnumerable<(string sku, int qty)> items, CancellationToken ct = default) {
    var order = new Order { CustomerEmail = email };
    foreach (var (sku, qty) in items) {
      order.Items.Add(new OrderItem { Sku = sku, Quantity = qty });
    }
    _db.Orders.Add(order);
    await _db.SaveChangesAsync(ct);
    return order.Id;
  }

  public async Task<Order?> GetOrderWithItemsAsync(long id, CancellationToken ct = default) {
    return await _db.Orders
      .AsNoTracking()
      .Include(o => o.Items)
      .FirstOrDefaultAsync(o => o.Id == id, ct);
  }
}
```

Migrations:
```bash
dotnet ef migrations add Initial
dotnet ef database update
```

Evitar N+1 e cartesian explosion:
```csharp
// Inclua o necessário:
var orders = await _db.Orders
  .Include(o => o.Items)
  .AsSplitQuery() // quando muitos Includes, para evitar multiplicação de linhas
  .Where(o => o.CustomerEmail == email)
  .OrderByDescending(o => o.Id)
  .Take(20)
  .ToListAsync();
```

Atualizações em massa (EF Core 7+):
```csharp
await _db.Orders
  .Where(o => o.CreatedAt < DateTime.UtcNow.AddYears(-1))
  .ExecuteUpdateAsync(s => s.SetProperty(o => o.CustomerEmail, o => o.CustomerEmail.ToLower()));
```

Dapper em rotas específicas:
```csharp
using Dapper;
using (var conn = new SqlConnection(connStr)) {
  var results = await conn.QueryAsync<OrderReportRow>(
    "SELECT o.Id, COUNT(i.Id) as ItemCount FROM Orders o LEFT JOIN OrderItems i ON i.OrderId = o.Id GROUP BY o.Id");
}
```

---

## 10. Tendências atuais e futuras em ORM

- Jakarta Persistence e modernização do ecossistema Java:
  - Namespace jakarta e integração com frameworks cloud-native (Quarkus, Micronaut, Spring).
  - Hibernate 6: novo SQL AST, melhores traduções, integração com features de bancos modernos via dialects.
- EF Core contínua evolução:
  - Melhorias em desempenho, translation correctness, recursos como ExecuteUpdate/Delete, coleções primitivas e suporte ampliado a tipos e recursos específicos de provedores (por exemplo, JSON em bancos que nativamente suportam JSON).
- Padrão híbrido ORM + SQL:
  - Uso de micro-ORMs (Dapper) ou mapeadores SQL (MyBatis) para consultas complexas; ORM para o restante.
- Tipagem forte para consultas:
  - QueryDSL, jOOQ (Java; não é ORM, mas um DSL para SQL), LINQ avançado em .NET. O movimento privilegia a segurança de tipos sem abrir mão do SQL explícito.
- Persistência orientada a eventos:
  - Outbox pattern, CDC, audit trails — ORM convive com filas/eventos.
- Serverless e conexões:
  - Pools gerenciados, proxies (RDS Proxy), e estratégias para evitar exaustão de conexões.
- Observabilidade:
  - Telemetria de queries, tracing distribuído (OpenTelemetry) integrado às camadas de persistência.

---

## 11. Ilustrações e diagramas (texto)

Arquitetura típica de aplicação com ORM:

```
[Controller/API]
      |
      v
[Service Layer]  <-- @Transactional (Java) | DbContext scope (.NET)
      |
      v
[ORM (JPA/Hibernate | EF Core)]
      |
      v
[Driver/JDBC or ADO.NET]
      |
      v
[DB: Postgres, SQL Server, etc.]
```

Ciclo de uma operação com UoW:

```
Begin request
  -> Resolve DbContext/EntityManager (scope)
  -> Carregar entidades (L1 cache participa)
  -> Modificar agregados
  -> SaveChanges/flush (gera SQL, transação)
  -> Commit
End request (dispose/close)
```

Estratégias para N+1:

```
Sem otimização:
  Carrega 20 Orders
  Para cada Order -> consulta separada para Items (20+1 queries)

Com fetch join / Include:
  1-2 queries totais (AsSplitQuery em EF Core quando necessário)
```

---

## 12. Checklists rápidos

- Modelagem
  - [ ] Chaves, índices e restrições desenhadas.
  - [ ] Relacionamentos e cardinalidades claros.
  - [ ] Estratégia de herança definida (TPH/TPT/TPC).

- Consultas
  - [ ] Evitar N+1 com fetch join / Include.
  - [ ] Paginação com ordenação estável.
  - [ ] AsNoTracking em consultas de leitura intensiva (EF Core).

- Transações e concorrência
  - [ ] @Transactional / TransactionScope corretos.
  - [ ] @Version / [Timestamp] nas entidades críticas.

- Caching
  - [ ] Decidir sobre cache L2 (Hibernate) e invalidação.
  - [ ] Cache de leitura em consultas caras (DTOs).

- Migrations e DevOps
  - [ ] Migrations em pipeline.
  - [ ] Backups e rollback strategy.
  - [ ] Scripts idempotentes quando necessário.

- Testes
  - [ ] Testcontainers/in-memory com cautela.
  - [ ] Seeds previsíveis, limpeza entre testes.

---

## 13. Perguntas frequentes

- Devo usar ORM ou SQL direto?
  - Use ORM para o grosso do CRUD e transações. Use SQL direto/micro-ORM para consultas críticas complexas.

- Lazy loading é ruim?
  - Não é ruim por si só. É perigoso sem controle. Combine com includes/fetch e perfis de carregamento.

- Posso usar repositórios genéricos?
  - Use com parcimônia. O DbContext/EntityManager já oferece UoW. Repositórios específicos (orientados ao domínio) agregam mais valor.

---

## 14. Referências e leituras recomendadas

Java e JPA/Hibernate:
- Hibernate ORM — Documentação oficial {target="_blank"}: https://hibernate.org/orm/documentation/
- Jakarta Persistence — Especificação {target="_blank"}: https://jakarta.ee/specifications/persistence/
- EclipseLink — Documentação {target="_blank"}: https://www.eclipse.org/eclipselink/documentation/
- Spring Data JPA — Referência {target="_blank"}: https://docs.spring.io/spring-data/jpa/reference/

MyBatis:
- MyBatis — Documentação {target="_blank"}: https://mybatis.org/mybatis-3/

.NET e EF:
- EF Core — Documentação {target="_blank"}: https://learn.microsoft.com/ef/core/
- EF 6 — Documentação {target="_blank"}: https://learn.microsoft.com/ef/ef6/
- Dapper — Repositório {target="_blank"}: https://github.com/DapperLib/Dapper

Boas práticas e ferramentas:
- Flyway {target="_blank"}: https://flywaydb.org/
- Liquibase {target="_blank"}: https://www.liquibase.org/
- Testcontainers (Java) {target="_blank"}: https://testcontainers.com/
- Testcontainers para .NET {target="_blank"}: https://github.com/testcontainers/testcontainers-dotnet
- OpenTelemetry {target="_blank"}: https://opentelemetry.io/

---

## 15. Conclusão

- A história do ORM em Java e .NET reflete a busca por simplicidade, padronização e desempenho.
- De EJB 2.x a Hibernate/JPA e de LINQ to SQL a EF Core, a indústria aprendeu a equilibrar produtividade e controle.
- Na prática, “ORM + SQL” é uma estratégia vencedora: use o ORM onde ele brilha e complemente com consultas explícitas quando preciso.
- Foque em modelagem intencional, transações bem delimitadas, carga consciente (lazy/eager), observabilidade e disciplina de migrations.

Resumo de ouro:
- Modele bem, meça sempre, otimize o necessário e automatize o ciclo de vida (build, migrations, deploy, observabilidade). Assim, ORM deixa de ser obstáculo e vira acelerador de entrega.

Se quiser, posso adaptar este eBook como um conjunto de capítulos independentes em PDF/Docx, incluir mais diagramas, ou adicionar exemplos avançados (multi-tenancy, sharding, herança complexa, JSON columns específicas de provedores). Quer que eu estruture também um cronograma de estudos com exercícios práticos para Java e .NET?
