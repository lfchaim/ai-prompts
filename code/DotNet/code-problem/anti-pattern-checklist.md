# Prompt
Montar um anti-pattern checklist para EF Core.  

# ChatGPT
Perfeito 😄
Segue um **Anti-Pattern Checklist de EF Core**, pensado para **arquitetura limpa, performance, concorrência e manutenção em produção**. Dá pra usar em **code review, RFP, ou CoE de Arquitetura** sem dó.

---

# 🚫 EF Core – Anti-Pattern Checklist

Use como **checklist de “se isso existe, algo está errado”**.

---

## 1️⃣ Concorrência & Async

### ❌ Anti-patterns

* ⛔ `Task.WhenAll` usando **o mesmo `DbContext`**
* ⛔ Executar múltiplos `LoadAsync`, `ToListAsync`, `FirstAsync` em paralelo no mesmo contexto
* ⛔ Reutilizar `DbContext` entre threads
* ⛔ `DbContext` como `Singleton`

### ✅ Boas práticas

* ✔️ Um `DbContext` = uma operação async por vez
* ✔️ Paralelismo → `IDbContextFactory<T>`
* ✔️ `DbContext` sempre `Scoped`

---

## 2️⃣ Performance & Consultas

### ❌ Anti-patterns

* ⛔ **N+1 Queries**
* ⛔ `foreach` com `await context.XYZ.FirstAsync(...)`
* ⛔ `Include` em excesso (Include Hell)
* ⛔ `Select *` implícito (entidade inteira sem necessidade)
* ⛔ Falta de `AsNoTracking()` para leitura

### ✅ Boas práticas

* ✔️ Projeção com `.Select()`
* ✔️ `AsNoTracking()` para read-only
* ✔️ `Include` apenas quando necessário
* ✔️ Avaliar `SplitQuery` vs `SingleQuery`

---

## 3️⃣ Modelagem de Domínio

### ❌ Anti-patterns

* ⛔ Entidades anêmicas (só DTO disfarçado)
* ⛔ Entidades dependentes de EF (`DbContext` dentro da entidade)
* ⛔ Regras de negócio no `DbContext`
* ⛔ Lógica complexa em `OnModelCreating`

### ✅ Boas práticas

* ✔️ Entidades com comportamento
* ✔️ Separar Domínio × Infraestrutura
* ✔️ Configurações via `IEntityTypeConfiguration<T>`

---

## 4️⃣ Ciclo de Vida do DbContext

### ❌ Anti-patterns

* ⛔ Criar `DbContext` manualmente com `new`
* ⛔ Manter `DbContext` vivo por muito tempo
* ⛔ Compartilhar contexto entre requests
* ⛔ Unit of Work gigante

### ✅ Boas práticas

* ✔️ `AddDbContext` ou `AddDbContextFactory`
* ✔️ Contextos curtos
* ✔️ Um contexto por request / use case

---

## 5️⃣ Transações

### ❌ Anti-patterns

* ⛔ `TransactionScope` sem necessidade
* ⛔ `SaveChanges()` múltiplas vezes sem transação explícita
* ⛔ Misturar múltiplos `DbContext` na mesma transação sem controle

### ✅ Boas práticas

* ✔️ `BeginTransactionAsync()` quando necessário
* ✔️ Uma transação → uma responsabilidade clara
* ✔️ Preferir consistência eventual quando possível

---

## 6️⃣ Migrações & Evolução de Schema

### ❌ Anti-patterns

* ⛔ Migração gigante com múltiplas responsabilidades
* ⛔ Alterações destrutivas sem backward compatibility
* ⛔ SQL manual escondido em migrations
* ⛔ Não versionar banco por ambiente

### ✅ Boas práticas

* ✔️ Migrações pequenas e incrementais
* ✔️ Estratégia expand → migrate → contract
* ✔️ Scripts reversíveis

---

## 7️⃣ Logs, Diagnóstico & Observabilidade

### ❌ Anti-patterns

* ⛔ Não logar SQL gerado
* ⛔ Ignorar warnings do EF Core
* ⛔ Descobrir N+1 só em produção
* ⛔ Falta de métricas de query lenta

### ✅ Boas práticas

* ✔️ `EnableSensitiveDataLogging` (apenas dev)
* ✔️ `LogTo()` configurado
* ✔️ Monitorar tempo de execução de queries
* ✔️ Usar `TagWith()` para rastreabilidade

---

## 8️⃣ Testes

### ❌ Anti-patterns

* ⛔ Testar EF com `InMemory` achando que é igual a banco real
* ⛔ Testes que dependem da ordem de execução
* ⛔ Mockar `DbContext` manualmente

### ✅ Boas práticas

* ✔️ SQLite in-memory para testes reais
* ✔️ Testcontainers para integração
* ✔️ Repositórios finos ou Query Objects

---

## 9️⃣ Segurança

### ❌ Anti-patterns

* ⛔ SQL Injection via `FromSqlRaw`
* ⛔ Expor entidades diretamente via API
* ⛔ Falta de soft delete ou filtros globais
* ⛔ Ignorar multi-tenancy

### ✅ Boas práticas

* ✔️ `FromSqlInterpolated`
* ✔️ DTOs explícitos
* ✔️ Global Query Filters
* ✔️ `TenantId` como first-class citizen

---

## 🔥 5 Red Flags Absolutas (se viu isso, pare tudo)

🚨 `Task.WhenAll` + mesmo `DbContext`
🚨 `DbContext` como Singleton
🚨 `foreach` + query async
🚨 `Include` em cascata sem entender o SQL
🚨 Entidades expostas diretamente no controller

---

## 🧠 Frase para colar na parede

> **EF Core não é ORM mágico.
> Ele só amplifica decisões boas — e pune decisões ruins.**

---

Se quiser, posso:

* Transformar isso em **checklist de RFP**
* Criar **matriz de avaliação arquitetural**
* Mapear cada item para **ISO/IEC 25010**
* Adaptar para **microserviços / DDD / Clean Architecture**

É só dizer o próximo passo 🚀

# Prompt
Adaptar para microserviços / DDD / Clean Architecture.  

# ChatGPT
Excelente pedido 👌
Vamos **adaptar o checklist de Anti-Patterns do EF Core** especificamente para **Microserviços + DDD + Clean Architecture**, no nível que um **Arquiteto ou CoE** realmente usa (não tutorial).

Pense nisso como um **“guia de sobrevivência”** para evitar acoplamento, vazamento de infraestrutura e bugs de escala.

---

# 🚫 EF Core Anti-Patterns

## Versão: Microserviços + DDD + Clean Architecture

---

## 1️⃣ Limites de Contexto (Bounded Context)

### ❌ Anti-patterns

* ⛔ Um único banco compartilhado por vários microserviços
* ⛔ Um `DbContext` representando múltiplos Bounded Contexts
* ⛔ Entidades referenciando tabelas de outros domínios
* ⛔ Joins entre tabelas de microserviços diferentes

### ✅ Boas práticas

* ✔️ **Um microserviço → um banco**
* ✔️ **Um `DbContext` por Bounded Context**
* ✔️ Comunicação entre serviços via eventos ou APIs
* ✔️ IDs externos tratados como *Value Objects*

🧠 **Regra DDD**

> “Se precisa de `JOIN`, não é microserviço.”

---

## 2️⃣ Dependências (Clean Architecture)

### ❌ Anti-patterns

* ⛔ `DbContext` usado diretamente no Controller
* ⛔ Application Layer retornando `IQueryable`
* ⛔ Entidades do domínio com atributos do EF (`[Key]`, `[Table]`)
* ⛔ Domain dependendo de EF Core

### ✅ Boas práticas

* ✔️ Domínio **100% POCO**
* ✔️ Infraestrutura depende de Application
* ✔️ Repositórios definidos por **interfaces**
* ✔️ Queries encapsuladas (Query Objects / Read Models)

🧠 **Regra Clean**

> “Domínio não sabe que o EF existe.”

---

## 3️⃣ Agregados (DDD)

### ❌ Anti-patterns

* ⛔ `Include` atravessando múltiplos agregados
* ⛔ Alterar entidade fora do Aggregate Root
* ⛔ Agregados gigantes “God Aggregates”
* ⛔ `SaveChanges()` dentro da entidade

### ✅ Boas práticas

* ✔️ Aggregate Root como única porta de entrada
* ✔️ Relacionamentos por ID entre agregados
* ✔️ Invariantes protegidas no domínio
* ✔️ Persistência sempre fora do domínio

🧠 **Regra de ouro**

> “Se precisa de `Include`, talvez não seja o mesmo agregado.”

---

## 4️⃣ Casos de Uso (Application Layer)

### ❌ Anti-patterns

* ⛔ Caso de uso misturando regra + query complexa
* ⛔ `DbContext` exposto na Application Layer
* ⛔ Queries reutilizadas sem semântica de negócio
* ⛔ Regras críticas no Controller

### ✅ Boas práticas

* ✔️ Um **Use Case = uma intenção**
* ✔️ Command ≠ Query (CQRS light)
* ✔️ Queries específicas por caso de uso
* ✔️ Application controla transações

---

## 5️⃣ EF Core como Infraestrutura

### ❌ Anti-patterns

* ⛔ Lógica de negócio em `OnModelCreating`
* ⛔ Repositório genérico (`Repository<T>`)
* ⛔ `Include` escondido dentro do repositório
* ⛔ EF expondo Lazy Loading

### ✅ Boas práticas

* ✔️ EF só mapeia e persiste
* ✔️ Repositórios orientados ao domínio
* ✔️ Lazy Loading **desabilitado**
* ✔️ Configuração por `IEntityTypeConfiguration<T>`

---

## 6️⃣ Transações & Consistência (Microserviços)

### ❌ Anti-patterns

* ⛔ Transações distribuídas (2PC)
* ⛔ `TransactionScope` entre microserviços
* ⛔ Sincronização forte entre domínios
* ⛔ Rollback entre serviços

### ✅ Boas práticas

* ✔️ Transações locais por serviço
* ✔️ **Eventual Consistency**
* ✔️ Outbox Pattern
* ✔️ Eventos de domínio → eventos de integração

🧠 **Regra Microservices**

> “Se precisa de rollback distribuído, o design está errado.”

---

## 7️⃣ Eventos de Domínio

### ❌ Anti-patterns

* ⛔ Publicar eventos direto do Controller
* ⛔ Eventos acoplados a DTOs de API
* ⛔ Evento carregando entidade inteira
* ⛔ `SaveChanges()` sem disparar eventos

### ✅ Boas práticas

* ✔️ Eventos disparados pelo Aggregate Root
* ✔️ Eventos pequenos e imutáveis
* ✔️ Handlers na Application Layer
* ✔️ Publicação após commit

---

## 8️⃣ Queries (Read Model)

### ❌ Anti-patterns

* ⛔ Reutilizar entidades do domínio para leitura
* ⛔ `Include` profundo para montar ViewModel
* ⛔ Queries “genéricas” para múltiplas telas
* ⛔ Read e Write compartilhando complexidade

### ✅ Boas práticas

* ✔️ Read Models dedicados
* ✔️ Projeção direta (`Select`)
* ✔️ DTOs específicos por tela
* ✔️ Banco otimizado para leitura (quando necessário)

---

## 9️⃣ Testabilidade & Evolução

### ❌ Anti-patterns

* ⛔ Testes dependentes do EF
* ⛔ Mock pesado de `DbContext`
* ⛔ Refactor impossível sem quebrar banco
* ⛔ Migrações globais para múltiplos serviços

### ✅ Boas práticas

* ✔️ Domínio testado sem EF
* ✔️ Infra testada com SQLite / Testcontainers
* ✔️ Migrações por microserviço
* ✔️ Versionamento independente

---

## 🔥 Red Flags Arquiteturais Absolutas

🚨 Um banco para vários microserviços
🚨 `DbContext` no Controller
🚨 `Include` atravessando Bounded Context
🚨 Entidades do domínio com atributos EF
🚨 Transação entre microserviços

---

## 🧠 Frase definitiva para DDD + EF Core

> **EF Core é detalhe de infraestrutura.
> Se ele aparece no domínio, a arquitetura já falhou.**

---

## 🚀 Próximos passos possíveis

Posso:

1️⃣ Transformar isso em **checklist oficial de CoE**
2️⃣ Mapear para **ISO/IEC 25010 + NFRs**
3️⃣ Criar **template de referência (Java/Spring ou .NET)**
4️⃣ Aplicar ao seu **Motor de Regras**
5️⃣ Montar **RFP técnico para fornecedores**

Qual desses você quer atacar agora?

# Prompt
Criar template de referência .Net. 
Usar as seguintes tabelas de banco de dados, como exemplo: 
- User
- Group
- UserGroup

# ChatGPT
Perfeito. Vamos montar um **template de referência em .NET** já **DDD + Clean Architecture + Microserviço**, usando **EF Core apenas como infraestrutura**, e com as tabelas:

* `User`
* `Group`
* `UserGroup`

👉 Vou assumir **.NET 8**, **EF Core 8**, **PostgreSQL** (fácil adaptar), e um **relacionamento N:N explícito** (bom exemplo de Aggregate).

---

# 🧱 Estrutura do Template (Clean Architecture)

```
src/
 ├─ MyService.Domain
 │   ├─ Users
 │   │   ├─ User.cs
 │   │   ├─ UserId.cs
 │   │   └─ UserGroup.cs
 │   ├─ Groups
 │   │   ├─ Group.cs
 │   │   └─ GroupId.cs
 │   └─ Common
 │       ├─ Entity.cs
 │       ├─ AggregateRoot.cs
 │       └─ DomainEvent.cs
 │
 ├─ MyService.Application
 │   ├─ Users
 │   │   ├─ CreateUser
 │   │   ├─ AddUserToGroup
 │   │   └─ GetUserGroups
 │   └─ Abstractions
 │       └─ IUserRepository.cs
 │
 ├─ MyService.Infrastructure
 │   ├─ Persistence
 │   │   ├─ AppDbContext.cs
 │   │   ├─ Configurations
 │   │   │   ├─ UserConfig.cs
 │   │   │   ├─ GroupConfig.cs
 │   │   │   └─ UserGroupConfig.cs
 │   │   └─ Repositories
 │   │       └─ UserRepository.cs
 │
 └─ MyService.Api
     ├─ Controllers
     │   └─ UsersController.cs
     └─ Program.cs
```

---

## 🧠 Decisão de Modelagem (DDD)

### Aggregate Roots

* `User` → Aggregate Root
* `Group` → Aggregate Root
* `UserGroup` → **entidade de associação**, controlada pelo `User`

📌 **Não existe Include automático entre agregados.**

---

# 🟦 DOMAIN LAYER

## Base Classes

```csharp
public abstract class Entity
{
    public Guid Id { get; protected set; }
}

public abstract class AggregateRoot : Entity
{
    private readonly List<DomainEvent> _events = [];
    public IReadOnlyCollection<DomainEvent> Events => _events;

    protected void Raise(DomainEvent @event) => _events.Add(@event);
}
```

---

## User Aggregate

```csharp
public class User : AggregateRoot
{
    private readonly List<UserGroup> _groups = [];

    public string Email { get; private set; }

    protected User() { }

    public User(string email)
    {
        Id = Guid.NewGuid();
        Email = email;
    }

    public void AddToGroup(GroupId groupId)
    {
        if (_groups.Any(g => g.GroupId == groupId))
            throw new DomainException("User already in group");

        _groups.Add(new UserGroup(Id, groupId));
    }
}
```

---

## Group Aggregate

```csharp
public class Group : AggregateRoot
{
    public string Name { get; private set; }

    protected Group() { }

    public Group(string name)
    {
        Id = Guid.NewGuid();
        Name = name;
    }
}
```

---

## UserGroup (Entidade de Associação)

```csharp
public class UserGroup : Entity
{
    public Guid UserId { get; private set; }
    public Guid GroupId { get; private set; }

    protected UserGroup() { }

    public UserGroup(Guid userId, GroupId groupId)
    {
        Id = Guid.NewGuid();
        UserId = userId;
        GroupId = groupId.Value;
    }
}
```

---

# 🟩 APPLICATION LAYER

## Repository Abstraction

```csharp
public interface IUserRepository
{
    Task<User?> GetAsync(Guid userId);
    Task AddAsync(User user);
}
```

---

## Use Case – Add User To Group

```csharp
public sealed class AddUserToGroupHandler
{
    private readonly IUserRepository _users;

    public AddUserToGroupHandler(IUserRepository users)
    {
        _users = users;
    }

    public async Task Handle(Guid userId, Guid groupId)
    {
        var user = await _users.GetAsync(userId)
            ?? throw new NotFoundException("User");

        user.AddToGroup(new GroupId(groupId));

        await _users.UnitOfWork.SaveChangesAsync();
    }
}
```

📌 **A Application controla a transação.**

---

# 🟧 INFRASTRUCTURE LAYER

## DbContext

```csharp
public sealed class AppDbContext : DbContext
{
    public DbSet<User> Users => Set<User>();
    public DbSet<Group> Groups => Set<Group>();

    public AppDbContext(DbContextOptions<AppDbContext> options)
        : base(options) { }

    protected override void OnModelCreating(ModelBuilder model)
    {
        model.ApplyConfigurationsFromAssembly(GetType().Assembly);
    }
}
```

---

## User Mapping

```csharp
public class UserConfig : IEntityTypeConfiguration<User>
{
    public void Configure(EntityTypeBuilder<User> builder)
    {
        builder.ToTable("users");

        builder.HasKey(x => x.Id);

        builder.Property(x => x.Email)
               .IsRequired();

        builder.OwnsMany<UserGroup>("_groups", ug =>
        {
            ug.ToTable("user_groups");
            ug.HasKey(x => x.Id);
        });
    }
}
```

✔️ Sem `Include`
✔️ Sem Lazy Loading
✔️ EF só mapeia

---

## Repository Implementation

```csharp
public sealed class UserRepository : IUserRepository
{
    private readonly AppDbContext _context;

    public UserRepository(AppDbContext context)
    {
        _context = context;
    }

    public Task<User?> GetAsync(Guid id) =>
        _context.Users
            .SingleOrDefaultAsync(u => u.Id == id);

    public Task AddAsync(User user) =>
        _context.Users.AddAsync(user);
}
```

---

# 🟪 API LAYER

```csharp
[ApiController]
[Route("users")]
public class UsersController : ControllerBase
{
    [HttpPost("{userId}/groups/{groupId}")]
    public async Task<IActionResult> AddToGroup(
        Guid userId,
        Guid groupId,
        AddUserToGroupHandler handler)
    {
        await handler.Handle(userId, groupId);
        return NoContent();
    }
}
```

---

# 🧪 Testabilidade

✔️ Domínio testável sem EF
✔️ Infra testável com SQLite/Testcontainers
✔️ Casos de uso isolados

---

# 🧠 O que esse template PROVA

✔️ EF Core isolado
✔️ DDD real (não anêmico)
✔️ Clean Architecture respeitada
✔️ Sem `Include Hell`
✔️ Pronto para microserviços

---

## 🚀 Próximos passos possíveis

Posso:

1️⃣ Adicionar **CQRS Read Model**
2️⃣ Adicionar **Domain Events + Outbox**
3️⃣ Gerar **DDL PostgreSQL**
4️⃣ Criar **template GitHub pronto**
5️⃣ Adaptar para **IAM / Auth / Multi-Tenancy**

Qual você quer evoluir agora?

# Prompt
Criar template Github pronto.  

# ChatGPT
Perfeito 😄
Vou te entregar um **TEMPLATE DE GITHUB PRONTO**, no estilo **“clonar e começar”**, alinhado com **.NET 8 + EF Core + DDD + Clean Architecture + Microserviço**, usando `User / Group / UserGroup` como referência didática.

Você pode literalmente **copiar isso e publicar no GitHub**.

---

# 🧩 Nome do Repositório

```
dotnet-clean-architecture-ddd-efcore-template
```

**Descrição (GitHub):**

> Reference template for building .NET microservices using Clean Architecture, DDD principles and EF Core as infrastructure detail.
> Includes real-world patterns, anti-pattern protection and production-ready structure.

---

# 📁 Estrutura Final do Repositório

```
dotnet-clean-architecture-ddd-efcore-template/
├─ README.md
├─ .editorconfig
├─ .gitignore
├─ docker-compose.yml
├─ Directory.Build.props
├─ src/
│  ├─ MyService.Domain/
│  │  ├─ Common/
│  │  │  ├─ Entity.cs
│  │  │  ├─ AggregateRoot.cs
│  │  │  └─ DomainEvent.cs
│  │  ├─ Users/
│  │  │  ├─ User.cs
│  │  │  ├─ UserGroup.cs
│  │  │  └─ UserId.cs
│  │  └─ Groups/
│  │     ├─ Group.cs
│  │     └─ GroupId.cs
│  │
│  ├─ MyService.Application/
│  │  ├─ Abstractions/
│  │  │  ├─ Persistence/
│  │  │  │  ├─ IUserRepository.cs
│  │  │  │  └─ IUnitOfWork.cs
│  │  ├─ Users/
│  │  │  ├─ Commands/
│  │  │  │  ├─ CreateUser/
│  │  │  │  └─ AddUserToGroup/
│  │  │  └─ Queries/
│  │  │     └─ GetUserGroups/
│  │
│  ├─ MyService.Infrastructure/
│  │  ├─ Persistence/
│  │  │  ├─ AppDbContext.cs
│  │  │  ├─ Configurations/
│  │  │  │  ├─ UserConfig.cs
│  │  │  │  ├─ GroupConfig.cs
│  │  │  │  └─ UserGroupConfig.cs
│  │  │  └─ Repositories/
│  │  │     └─ UserRepository.cs
│  │  └─ DependencyInjection.cs
│  │
│  └─ MyService.Api/
│     ├─ Controllers/
│     │  └─ UsersController.cs
│     ├─ Program.cs
│     └─ appsettings.json
│
└─ tests/
   ├─ MyService.Domain.Tests/
   └─ MyService.Application.Tests/
```

---

# 🧠 Arquivos-Chave do Template

## 📄 README.md (conteúdo pronto)

```md
# .NET Clean Architecture + DDD + EF Core Template

This repository is a **reference template** for building **.NET microservices**
using:

- Clean Architecture
- Domain-Driven Design (DDD)
- EF Core as infrastructure detail
- Explicit Aggregates and transactional boundaries

## Goals

- No EF Core leaking into Domain or Application
- No anemic domain model
- No Include Hell
- No shared database between services

## Architecture

```

API
└── Application
└── Domain
└── Infrastructure

````

Dependency rule is strictly enforced.

## Example Domain

This template models a real-world scenario:

- User (Aggregate Root)
- Group (Aggregate Root)
- UserGroup (Association Entity)

## Key Rules

- One DbContext per microservice
- One Aggregate per transaction
- Domain has no EF references
- Queries are explicit and optimized

## How to run

```bash
docker-compose up -d
dotnet run --project src/MyService.Api
````

````

---

## 🐳 docker-compose.yml

```yaml
version: "3.9"

services:
  postgres:
    image: postgres:16
    container_name: myservice-postgres
    environment:
      POSTGRES_USER: myservice
      POSTGRES_PASSWORD: myservice
      POSTGRES_DB: myservice
    ports:
      - "5432:5432"
````

---

## ⚙️ Infrastructure Dependency Injection

```csharp
public static class DependencyInjection
{
    public static IServiceCollection AddInfrastructure(
        this IServiceCollection services,
        IConfiguration config)
    {
        services.AddDbContext<AppDbContext>(options =>
            options.UseNpgsql(
                config.GetConnectionString("Default")));

        services.AddScoped<IUserRepository, UserRepository>();
        services.AddScoped<IUnitOfWork>(
            sp => sp.GetRequiredService<AppDbContext>());

        return services;
    }
}
```

---

## 🧪 Test Strategy (documentado no repo)

* **Domain Tests** → sem EF
* **Application Tests** → mocks leves
* **Infrastructure Tests** → SQLite / Testcontainers
* **API Tests** → opcional

---

# 🚫 Anti-Patterns BLOQUEADOS por Design

✔️ `DbContext` no Controller
✔️ `Include` atravessando agregados
✔️ Lazy Loading
✔️ Repositório genérico
✔️ Transação distribuída

---

# 🏷️ Tags sugeridas no GitHub

```
dotnet
clean-architecture
ddd
ef-core
microservices
template
reference-architecture
```

---

# 🚀 Próximos upgrades (já pensados para o template)

Posso evoluir o repositório com:

1️⃣ **CQRS + Read Model separado**
2️⃣ **Domain Events + Outbox Pattern**
3️⃣ **Multi-Tenancy**
4️⃣ **Auth / IAM style**
5️⃣ **GitHub Actions (CI)**
6️⃣ **OpenAPI + API First**

👉 Quer que eu gere **a próxima versão do template** com qual desses itens?

