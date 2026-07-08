# Prompt
Aja como especialista em arquitetura e desenvolvimento de APIs .NET e como redator técnico.

Crie um ebook em português sobre desenvolvimento de APIs .NET, com foco em Entity Framework, Dapper e melhores práticas para construção de soluções robustas, performáticas e manuteníveis.

Objetivo:
Produzir um conteúdo didático, técnico e organizado, voltado para desenvolvedores .NET, explicando boas práticas, cenários de uso, problemas comuns e recomendações práticas.

Tópicos obrigatórios:
1. Entity Framework
   - Melhores práticas
   - Problemas comuns
   - Impactos de performance
   - Problema N+1 e como evitá-lo

2. Dapper
   - Em quais situações seu uso é mais indicado
   - Melhores práticas
   - Vantagens e limitações em comparação com Entity Framework

3. DTOs
   - Papel dos DTOs em APIs .NET
   - Quando usar
   - Como estruturar e mapear corretamente

4. Explosão cartesiana
   - O que é
   - Como identificar
   - Como evitar em consultas e relacionamentos

5. Consultas em lote
   - Quando utilizar
   - Benefícios
   - Cuidados com performance e consistência

6. Consultas compostas envolvendo mais de uma tabela
   - Estratégias de modelagem e implementação
   - Boas práticas para joins e carregamento de dados
   - Cuidados com legibilidade e performance

Conteúdo adicional:
- Se identificar outros tópicos importantes para o bom desenvolvimento de soluções .NET, inclua sugestões relevantes.
- Exemplos práticos são bem-vindos.
- Inclua recomendações aplicáveis no dia a dia.
- Destaque armadilhas comuns e como evitá-las.

Estrutura desejada do ebook:
- Título
- Introdução
- Capítulos organizados por tópico
- Exemplos práticos quando fizer sentido
- Resumo final com principais recomendações
- Conclusão

Requisitos de qualidade:
- Linguagem clara, técnica e objetiva
- Conteúdo bem estruturado e didático
- Foco em boas práticas reais de desenvolvimento
- Evitar conteúdo genérico demais
- Priorizar explicações úteis para aplicação prática

Se fizer sentido, proponha também uma ordem lógica de capítulos para melhorar a progressão do conteúdo.

# MyHUB Nitro
# 🏗️ APIs .NET de Alto Desempenho

## Entity Framework, Dapper e Melhores Práticas para Soluções Robustas

---

**Um guia completo para desenvolvedores .NET**

*Por Luis Fernando*

---

## 📋 Sumário

1. [Introdução](#introdução)
2. [Fundamentos e Arquitetura de APIs .NET](#capítulo-1--fundamentos-e-arquitetura-de-apis-net)
3. [Entity Framework Core — Melhores Práticas](#capítulo-2--entity-framework-core--melhores-práticas)
4. [Entity Framework Core — Problemas Comuns e Performance](#capítulo-3--entity-framework-core--problemas-comuns-e-performance)
5. [O Problema N+1 — O Vilão Silencioso](#capítulo-4--o-problema-n1--o-vilão-silencioso)
6. [Dapper — Quando e Como Usar](#capítulo-5--dapper--quando-e-como-usar)
7. [Entity Framework vs Dapper — O Dilema Real](#capítulo-6--entity-framework-vs-dapper--o-dilema-real)
8. [DTOs — O Papel dos Objetos de Transferência](#capítulo-7--dtos--o-papel-dos-objetos-de-transferência)
9. [Explosão Cartesiana — Diagnóstico e Prevenção](#capítulo-8--explosão-cartesiana--diagnóstico-e-prevenção)
10. [Consultas em Lote — Eficiência em Escala](#capítulo-9--consultas-em-lote--eficiência-em-escala)
11. [Consultas Compostas — Múltiplas Tabelas com Elegância](#capítulo-10--consultas-compostas--múltiplas-tabelas-com-elegância)
12. [Tópicos Complementares Essenciais](#capítulo-11--tópicos-complementares-essenciais)
13. [Resumo Final — Checklist de Boas Práticas](#resumo-final--checklist-de-boas-práticas)
14. [Conclusão](#conclusão)

---

## Introdução

Desenvolver APIs .NET vai muito além de conectar controladores a um banco de dados. A diferença entre uma API que funciona e uma API que **escala, performa e se mantém ao longo do tempo** está nas decisões de arquitetura e nos detalhes de implementação que muitas vezes passam despercebidos.

Este ebook nasceu da necessidade de consolidar, em um único lugar, as práticas que realmente fazem diferença no dia a dia de desenvolvimento. Não se trata de um manual de referência da Microsoft — para isso já existe a documentação oficial. A proposta aqui é **destilar experiência prática**: aquilo que funcionou (e o que falhou) em projetos reais, com prazos reais e equipes reais.

Ao longo dos capítulos, você encontrará:

- **Explicações diretas** dos conceitos, sem enrolação
- **Exemplos de código** que simulam cenários reais de aplicação
- **Armadilhas comuns** com soluções práticas
- **Checklists** para aplicar imediatamente no seu projeto

Se você já se perguntou "devo usar Entity Framework ou Dapper para esta consulta?", "como evitar que minha API fique lenta com o crescimento dos dados?" ou "o que é essa explosão cartesiana que tanto falam?" — você está no lugar certo.

Vamos construir APIs melhores. 🚀

---

## Capítulo 1 • Fundamentos e Arquitetura de APIs .NET

### 1.1 A base importa

Antes de mergulharmos em ORMs e queries, é essencial estabelecer uma arquitetura que suporte boas decisões. Uma API .NET bem projetada segue princípios que vão além da tecnologia:

| Princípio | O que significa na prática |
|---|---|
| **Separação de responsabilidades** | Controllers não acessam o banco diretamente |
| **Inversão de dependência** | Dependa de abstrações, não de implementações concretas |
| **Fail fast** | Valide cedo, retorne erros claros |
| **Stateless** | Cada requisição é independente |

### 1.2 A estrutura que recomendamos

```
📁 MyApi/
├── 📁 Controllers/        ← Endpoints HTTP (fino, só orquestra)
├── 📁 Services/           ← Regras de negócio
├── 📁 Repositories/       ← Acesso a dados (EF, Dapper)
├── 📁 Models/
│   ├── 📁 Entities/       ← Entidades do domínio/banco
│   ├── 📁 DTOs/           ← Objetos de transferência
│   └── 📁 ViewModels/     ← Modelos específicos de resposta
├── 📁 Data/
│   ├── AppDbContext.cs
│   └── Configurations/    ← Fluent API (IEntityTypeConfiguration)
├── 📁 Middlewares/        ← Global exception handler, logging
└── 📁 Extensions/         ← Métodos de extensão, DI registration
```

> **💡 Regra de ouro**: o Controller deve ser burro. Ele recebe a requisição, chama o Service, e retorna a resposta. Nada de lógica de negócio ou queries complexas no Controller.

### 1.3 Configuração que evita dores de cabeça

```csharp
// Program.cs — Configurações essenciais
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(connectionString)
           .EnableSensitiveDataLogging(environment.IsDevelopment()) // ⚠️ NUNCA em produção
           .LogTo(Console.WriteLine, LogLevel.Information));

// Registrar repositórios como Scoped (ciclo de vida por requisição)
builder.Services.AddScoped<IProductRepository, ProductRepository>();
```

---

## Capítulo 2 • Entity Framework Core — Melhores Práticas

### 2.1 Use `AsNoTracking()` para consultas somente leitura

Esta é a otimização mais simples e mais negligenciada. Quando você sabe que os dados não serão alterados, desabilite o tracking:

```csharp
// ❌ Ruim — Tracking desnecessário
var products = await _context.Products
    .Where(p => p.Active)
    .ToListAsync();

// ✅ Bom — Leitura otimizada
var products = await _context.Products
    .AsNoTracking()
    .Where(p => p.Active)
    .ToListAsync();
```

> **🎯 Impacto**: `AsNoTracking()` reduz o consumo de memória e CPU, pois o EF não precisa manter snapshots das entidades. Em consultas que retornam centenas de registros, a diferença é gritante.

**Quando NÃO usar**: se você for modificar as entidades retornadas e chamar `SaveChanges()`.

### 2.2 Projeções com `Select()` — Consulte só o necessário

Trazer colunas que você não usa é desperdício de rede, memória e I/O:

```csharp
// ❌ Ruim — Traz TODAS as colunas da tabela (incluindo BLOBs talvez)
var products = await _context.Products
    .AsNoTracking()
    .ToListAsync();

// ✅ Bom — Traz só o que será exibido
var summaries = await _context.Products
    .AsNoTracking()
    .Where(p => p.Active)
    .Select(p => new ProductSummaryDto
    {
        Id = p.Id,
        Name = p.Name,
        Price = p.Price
    })
    .ToListAsync();
```

### 2.3 Configuração via Fluent API — Separe para não poluir

Evite entidades poluídas com Data Annotations extensas. Prefira classes de configuração separadas:

```csharp
// ✅ Bom — Configuração isolada
public class ProductConfiguration : IEntityTypeConfiguration<Product>
{
    public void Configure(EntityTypeBuilder<Product> builder)
    {
        builder.ToTable("Products");
        builder.HasKey(p => p.Id);
        
        builder.Property(p => p.Name)
            .IsRequired()
            .HasMaxLength(200);
        
        builder.Property(p => p.Price)
            .HasColumnType("decimal(18,2)");
        
        builder.HasIndex(p => p.CategoryId)
            .HasDatabaseName("IX_Products_CategoryId");
    }
}

// No DbContext
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.ApplyConfigurationsFromAssembly(typeof(AppDbContext).Assembly);
}
```

### 2.4 Índices — Pense neles desde o início

```csharp
// Índice para busca por nome (uso comum em filtros)
builder.HasIndex(p => p.Name);

// Índice composto para consultas frequentes
builder.HasIndex(p => new { p.CategoryId, p.Active });

// Índice único
builder.HasIndex(p => p.Sku).IsUnique();

// Índice com filtro (disponível em alguns providers)
builder.HasIndex(p => p.DeletedAt)
    .HasFilter("[DeletedAt] IS NOT NULL");
```

---

## Capítulo 3 • Entity Framework Core — Problemas Comuns e Performance

### 3.1 O perigo do `Include()` descontrolado

```csharp
// ❌ Perigoso — Pode carregar a árvore inteira de objetos
var order = await _context.Orders
    .Include(o => o.Customer)
        .ThenInclude(c => c.Addresses)
    .Include(o => o.Items)
        .ThenInclude(i => i.Product)
            .ThenInclude(p => p.Category)
    .FirstOrDefaultAsync(o => o.Id == orderId);
```

Essa cadeia de `Include/ThenInclude` pode gerar uma consulta SQL monstruosa com múltiplos JOINs, trazendo dados redundantes.

**Solução**: Use **split queries** quando precisar de múltiplos relacionamentos:

```csharp
// ✅ Melhor — Divide em queries separadas
var order = await _context.Orders
    .AsSplitQuery()
    .Include(o => o.Customer)
    .Include(o => o.Items)
        .ThenInclude(i => i.Product)
    .FirstOrDefaultAsync(o => o.Id == orderId);
```

> ⚠️ **Atenção**: Split queries evitam o problema do produto cartesiano, mas os dados podem ficar inconsistentes se houver alterações entre as queries. Use transações quando necessário.

### 3.2 `FirstOrDefault()` vs `SingleOrDefault()`

| Método | Comportamento | Quando usar |
|---|---|---|
| `FirstOrDefault()` | Retorna o primeiro que encontrar | Quando você só quer um registro qualquer que atenda ao filtro |
| `SingleOrDefault()` | Garante que há no máximo 1 registro | Quando deveria existir exatamente 0 ou 1 registro |

```csharp
// ❌ Errado — Se houver dois produtos com o mesmo SKU, passa batido
var product = await _context.Products
    .FirstOrDefaultAsync(p => p.Sku == sku);

// ✅ Correto — Se houver duplicata, estoura exceção (que é o comportamento esperado)
var product = await _context.Products
    .SingleOrDefaultAsync(p => p.Sku == sku);
```

### 3.3 O método `Find()` — Quando usar

```csharp
// Find: busca pela chave primária. Primeiro olha no cache de tracking, depois no banco
var product = await _context.Products.FindAsync(productId);

// É equivalente a fazer isso, mas mais eficiente se a entidade já estiver trackeada
var product = await _context.Products.FirstOrDefaultAsync(p => p.Id == productId);
```

### 3.4 Updates — Cuidado com atualizações em massa

```csharp
// ❌ Péssimo — Carrega N entidades só pra mudar uma propriedade
var products = await _context.Products
    .Where(p => p.CategoryId == oldCategoryId)
    .ToListAsync();
products.ForEach(p => p.CategoryId = newCategoryId);
await _context.SaveChangesAsync();

// ✅ Bom — ExecuteUpdate (EF Core 7+)
await _context.Products
    .Where(p => p.CategoryId == oldCategoryId)
    .ExecuteUpdateAsync(s => s
        .SetProperty(p => p.CategoryId, newCategoryId)
        .SetProperty(p => p.UpdatedAt, DateTime.UtcNow));
```

> 🚀 O `ExecuteUpdate` e `ExecuteDelete` geram SQL direto (UPDATE/DELETE com WHERE), sem carregar entidades na memória — **ganho massivo de performance**.

---

## Capítulo 4 • O Problema N+1 — O Vilão Silencioso

### 4.1 O que é?

O problema N+1 ocorre quando você faz **1 consulta** para carregar uma lista de entidades e depois, para cada entidade, dispara **mais 1 consulta** para carregar seus relacionamentos. Resultado: N+1 consultas ao banco.

### 4.2 Exemplo clássico

```csharp
// ❌ PROBLEMA N+1
var orders = await _context.Orders
    .Where(o => o.Date >= startDate)
    .ToListAsync(); // 1ª consulta: pega os pedidos

foreach (var order in orders)
{
    // N consultas adicionais (uma por pedido!) — o EF dispara lazy loading
    Console.WriteLine($"Cliente: {order.Customer.Name}");
}
```

Se retornar 100 pedidos, você fará **101 consultas ao banco**. Com 1000 pedidos, **1001 consultas**. Isso escala pessimamente.

### 4.3 Como evitar — Três estratégias

#### Estratégia 1: Eager Loading (`Include`)

```csharp
var orders = await _context.Orders
    .Include(o => o.Customer)  // Carrega o relacionamento na mesma consulta
    .Where(o => o.Date >= startDate)
    .ToListAsync(); // 1 única consulta com JOIN
```

#### Estratégia 2: Projeção explícita (`Select`)

```csharp
var orders = await _context.Orders
    .Where(o => o.Date >= startDate)
    .Select(o => new OrderDto
    {
        Id = o.Id,
        Date = o.Date,
        CustomerName = o.Customer.Name  // O EF monta o JOIN automaticamente
    })
    .ToListAsync(); // 1 consulta otimizada, só com as colunas necessárias
```

#### Estratégia 3: Desabilitar Lazy Loading globalmente

```csharp
// No DbContext
public AppDbContext(DbContextOptions<AppDbContext> options) : base(options)
{
    ChangeTracker.LazyLoadingEnabled = false; // 💣 Acaba com o N+1 acidental
}
```

> **Recomendação**: Desabilite Lazy Loading e seja explícito sobre quando carregar relacionamentos. Isso força a equipe a pensar sobre performance desde o design.

---

## Capítulo 5 • Dapper — Quando e Como Usar

### 5.1 O que é Dapper?

Dapper é um **micro-ORM** criado pelo time do Stack Overflow. Ele estende a `IDbConnection` com métodos como `Query`, `Execute`, `QueryFirstOrDefault`, mantendo você próximo do SQL, mas eliminando o código boilerplate de `SqlDataReader`.

### 5.2 Quando usar Dapper é a escolha certa

| Cenário | Por que Dapper |
|---|---|
| **Consultas de alta performance** | Menos overhead que EF; zero tracking |
| **Relatórios e dashboards** | Queries complexas com múltiplos JOINs |
| **Operações em lote** | Bulk inserts, updates massivos |
| **Stored procedures complexas** | Mapeamento simples de resultados |
| **APIs com altíssimo throughput** | Aloca menos, executa mais rápido |
| **Legado com SQL complexo** | Quando reescrever as queries não é viável |

### 5.3 Configuração rápida

```csharp
// Program.cs
builder.Services.AddScoped<IDbConnection>(_ =>
    new SqlConnection(connectionString));

// Repositório Dapper
public class ProductDapperRepository : IProductReadRepository
{
    private readonly IDbConnection _connection;
    
    public ProductDapperRepository(IDbConnection connection)
        => _connection = connection;
    
    public async Task<ProductSummaryDto?> GetByIdAsync(int id)
    {
        const string sql = """
            SELECT Id, Name, Price, CategoryName = c.Name
            FROM Products p
            INNER JOIN Categories c ON c.Id = p.CategoryId
            WHERE p.Id = @Id
            """;
        
        return await _connection.QueryFirstOrDefaultAsync<ProductSummaryDto>(
            sql, new { Id = id });
    }
}
```

### 5.4 Mapeamento avançado — Multi-mapping

Quando sua consulta retorna dados de múltiplas tabelas e você quer mapear para objetos aninhados:

```csharp
const string sql = """
    SELECT o.*, c.*
    FROM Orders o
    INNER JOIN Customers c ON c.Id = o.CustomerId
    WHERE o.Id = @Id
    """;

var order = await _connection.QueryAsync<Order, Customer, Order>(
    sql,
    (order, customer) =>
    {
        order.Customer = customer;
        return order;
    },
    new { Id = orderId },
    splitOn: "Id"); // Coluna onde começa o segundo mapeamento
```

### 5.5 Cuidados com Dapper

- **Sem tracking de mudanças**: você precisa construir os comandos UPDATE manualmente
- **Sem migrações**: Dapper não gerencia schema de banco
- **Magical strings**: queries SQL são strings, sem verificação em tempo de compilação
- **Manutenção**: mudanças no schema exigem atualização manual de todas as queries afetadas

---

## Capítulo 6 • Entity Framework vs Dapper — O Dilema Real

### 6.1 Não é uma guerra, é uma composição

O erro mais comum é tratar como uma escolha binária. A pergunta não é **"EF ou Dapper?"**, mas sim **"onde cada um entrega mais valor?"**.

### 6.2 Matriz de decisão

| Critério | Entity Framework | Dapper |
|---|---|---|
| Produtividade inicial | ⭐⭐⭐⭐⭐ | ⭐⭐ |
| Performance bruta | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Manutenibilidade | ⭐⭐⭐⭐ | ⭐⭐⭐ |
| Controle sobre SQL | ⭐⭐ | ⭐⭐⭐⭐⭐ |
| Operações CRUD simples | ⭐⭐⭐⭐⭐ | ⭐⭐ |
| Queries complexas | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Migrations | ⭐⭐⭐⭐⭐ | ❌ |
| Curva de aprendizado | Média | Baixa |

### 6.3 O modelo híbrido — O melhor dos dois mundos

A abordagem que recomendamos:

```
📁 Data/
├── AppDbContext.cs           ← EF para comandos (CUD) e queries simples
├── Repositories/
│   ├── ProductEfRepository.cs    ← EF: CRUD, queries com tracking
│   └── ProductDapperRepository.cs ← Dapper: queries de leitura complexas, relatórios
```

```csharp
// Registro no DI — ambos compartilham a mesma conexão
builder.Services.AddDbContext<AppDbContext>(...);
builder.Services.AddScoped<IDbConnection>(sp =>
{
    var context = sp.GetRequiredService<AppDbContext>();
    return context.Database.GetDbConnection(); // Reusa a conexão do EF!
});

// Repositórios
builder.Services.AddScoped<IProductCommandRepository, ProductEfRepository>();
builder.Services.AddScoped<IProductReadRepository, ProductDapperRepository>();
```

> 🎯 **Regra prática**: EF para comandos (INSERT, UPDATE, DELETE) e queries simples com tracking. Dapper para **queries de leitura complexas**, relatórios e endpoints de alto throughput. Ambos compartilhando a mesma conexão e transação.

---

## Capítulo 7 • DTOs — O Papel dos Objetos de Transferência

### 7.1 O problema de expor entidades

```csharp
// ❌ Péssimo — Expondo a entidade diretamente
[HttpGet("{id}")]
public async Task<ActionResult<Product>> GetProduct(int id)
{
    var product = await _repository.GetByIdAsync(id);
    return Ok(product);
    // ⚠️ Problemas:
    // - Vaza estrutura interna do banco
    // - Dados sensíveis podem ser expostos (CostPrice, SupplierId)
    // - Cliente recebe mais dados do que precisa
    // - Mudar o schema quebra a API
}
```

### 7.2 O que DTOs resolvem

| Problema | Como o DTO resolve |
|---|---|
| **Over-fetching** | Define exatamente o que será exposto |
| **Acoplamento** | Desacopla contrato da API do modelo de dados |
| **Segurança** | Impede exposição acidental de campos sensíveis |
| **Versionamento** | Permite evoluir o banco sem quebrar clientes |
| **Agregação** | Combina dados de múltiplas fontes em um único objeto |

### 7.3 Tipos de DTOs e quando usar cada um

```csharp
// DTO de resposta — o que sua API devolve
public class ProductResponseDto
{
    public int Id { get; init; }
    public string Name { get; init; }
    public decimal Price { get; init; }
    public string CategoryName { get; init; }
}

// DTO de criação — dados necessários para criar
public class CreateProductDto
{
    public string Name { get; init; }
    public decimal Price { get; init; }
    public int CategoryId { get; init; }
}

// DTO de atualização — só campos que podem mudar
public class UpdateProductDto
{
    public string? Name { get; init; }      // Null = não altera
    public decimal? Price { get; init; }
}

// DTO de listagem — versão resumida para listas
public class ProductSummaryDto
{
    public int Id { get; init; }
    public string Name { get; init; }
    public decimal Price { get; init; }
}
```

### 7.4 Mapeamento — Automático ou manual?

```csharp
// ✅ Manual — Claro, explícito, testável, zero magic
public static class ProductMapper
{
    public static ProductResponseDto ToResponse(this Product entity)
        => new()
        {
            Id = entity.Id,
            Name = entity.Name,
            Price = entity.Price,
            CategoryName = entity.Category?.Name ?? string.Empty
        };
    
    public static Product ToEntity(this CreateProductDto dto)
        => new()
        {
            Name = dto.Name,
            Price = dto.Price,
            CategoryId = dto.CategoryId,
            CreatedAt = DateTime.UtcNow
        };
}
```

> 💡 **Nossa recomendação**: Prefira mapeamento manual. AutoMapper resolve 80% dos casos com 20% de esforço, mas os 20% restantes (mapeamentos condicionais, nested objects, custom resolvers) consomem 80% do tempo de debug. Mapeamento manual é chato de escrever, mas fácil de manter e debugar.

---

## Capítulo 8 • Explosão Cartesiana — Diagnóstico e Prevenção

### 8.1 O que é?

A **explosão cartesiana** (ou *cartesian explosion*) acontece quando você carrega múltiplos relacionamentos `1:N` (ou `N:M`) na mesma consulta, e o banco de dados produz um **produto cartesiano** entre eles, multiplicando as linhas retornadas.

### 8.2 Exemplo prático

Imagine um pedido que tem:
- 10 itens (`OrderItems`)
- 5 anexos (`Attachments`)

Se você carrega ambos com `Include`:

```csharp
var order = await _context.Orders
    .Include(o => o.Items)
    .Include(o => o.Attachments)
    .FirstOrDefaultAsync(o => o.Id == 1);
```

O SQL gerado seria algo como:

```sql
SELECT o.*, i.*, a.*
FROM Orders o
LEFT JOIN OrderItems i ON i.OrderId = o.Id
LEFT JOIN Attachments a ON a.OrderId = o.Id
WHERE o.Id = 1
```

Resultado: **50 linhas** retornadas do banco (10 × 5), mas você esperava 1 pedido com 10 itens e 5 anexos.

### 8.3 Como identificar

- **Monitoramento**: queries que retornam muito mais linhas que o esperado
- **Log do EF**: ative o log em desenvolvimento e observe o SQL gerado
- **Perfil de performance**: tempo de execução da query desproporcional ao volume esperado de dados

### 8.4 Como evitar — Três estratégias

#### Estratégia 1: Split Queries

```csharp
// ✅ Cada relacionamento gera uma query separada
var order = await _context.Orders
    .AsSplitQuery()
    .Include(o => o.Items)
    .Include(o => o.Attachments)
    .FirstOrDefaultAsync(o => o.Id == 1);
```

#### Estratégia 2: Projeções (Select)

```csharp
// ✅ Consulta otimizada — sem produto cartesiano
var orderDto = await _context.Orders
    .Where(o => o.Id == 1)
    .Select(o => new OrderDetailDto
    {
        Id = o.Id,
        Items = o.Items.Select(i => new ItemDto { ... }).ToList(),
        Attachments = o.Attachments.Select(a => new AttachmentDto { ... }).ToList()
    })
    .FirstOrDefaultAsync();
```

#### Estratégia 3: Carregamento separado com Query

```csharp
var order = await _context.Orders.FirstOrDefaultAsync(o => o.Id == 1);

// Carrega os relacionamentos explicitamente em queries separadas
await _context.Entry(order)
    .Collection(o => o.Items).LoadAsync();

await _context.Entry(order)
    .Collection(o => o.Attachments).LoadAsync();
```

> 🎯 **Regra**: quando uma entidade tem **dois ou mais relacionamentos `1:N`** que você precisa carregar simultaneamente, use split queries ou projeções. Explosão cartesiana é uma das causas mais comuns de degradação de performance em APIs .NET.

---

## Capítulo 9 • Consultas em Lote — Eficiência em Escala

### 9.1 O antipadrão do loop

```csharp
// ❌ Desastroso — N chamadas individuais ao banco
foreach (var item in items)
{
    await _context.OrderItems.AddAsync(item);
    await _context.SaveChangesAsync(); // ⚠️ N transações!
}
```

### 9.2 Inserção em lote

```csharp
// ✅ EF: Adiciona tudo e salva uma vez
await _context.OrderItems.AddRangeAsync(items);
await _context.SaveChangesAsync(); // 1 transação, 1 round-trip
```

Mas para volumes muito grandes (milhares de registros), mesmo o `AddRange` pode ser lento. Alternativas:

```csharp
// ✅ SqlBulkCopy (via biblioteca) — milhares de registros em milissegundos
using var bulkCopy = new SqlBulkCopy(connectionString);
bulkCopy.DestinationTableName = "OrderItems";
bulkCopy.WriteToServer(dataTable);

// ✅ Dapper + Table-Valued Parameters
var tvp = items.AsTableValuedParameter("dbo.OrderItemType");
await _connection.ExecuteAsync("usp_InsertOrderItems", 
    new { Items = tvp }, commandType: CommandType.StoredProcedure);
```

### 9.3 Atualização em lote com EF Core 7+

```csharp
// ✅ Uma única instrução SQL, sem carregar entidades
await _context.Products
    .Where(p => p.CategoryId == categoryId)
    .ExecuteUpdateAsync(s => s
        .SetProperty(p => p.IsActive, false)
        .SetProperty(p => p.UpdatedAt, DateTime.UtcNow));
```

### 9.4 Deleção em lote

```csharp
// ✅ Direto no banco
await _context.OrderItems
    .Where(i => i.OrderId == orderId)
    .ExecuteDeleteAsync();
```

### 9.5 Cuidados com operações em lote

| Cuidado | Por quê |
|---|---|
| **Tamanho do lote** | Lotes muito grandes podem estourar timeout ou memória |
| **Transações** | Operações em lote devem ser atômicas quando fizer sentido |
| **Lock escalation** | Grandes atualizações podem escalar para table lock |
| **Índices** | Operações massivas são mais rápidas com índices desabilitados temporariamente |
| **Log do banco** | Grandes operações geram muito log; considere particionar em lotes menores |

```csharp
// ✅ Particionamento manual para lotes muito grandes
const int batchSize = 1000;
for (int i = 0; i < ids.Count; i += batchSize)
{
    var batch = ids.Skip(i).Take(batchSize);
    await _context.Products
        .Where(p => batch.Contains(p.Id))
        .ExecuteUpdateAsync(s => s.SetProperty(p => p.IsActive, false));
}
```

---

## Capítulo 10 • Consultas Compostas — Múltiplas Tabelas com Elegância

### 10.1 O desafio

Consultas que envolvem múltiplas tabelas são inevitáveis. O desafio é fazê-las de forma legível, performática e manutenível.

### 10.2 Entity Framework — Joins com sintaxe fluente

```csharp
// Join explícito
var query = from p in _context.Products
            join c in _context.Categories on p.CategoryId equals c.Id
            join s in _context.Suppliers on p.SupplierId equals s.Id
            where p.Active
            select new ProductFullDto
            {
                Id = p.Id,
                Name = p.Name,
                CategoryName = c.Name,
                SupplierName = s.Name,
                Price = p.Price
            };

// Mesma coisa com método de extensão
var query = _context.Products
    .Join(_context.Categories,
        p => p.CategoryId, c => c.Id,
        (p, c) => new { p, c })
    .Join(_context.Suppliers,
        pc => pc.p.SupplierId, s => s.Id,
        (pc, s) => new ProductFullDto { ... });
```

> 💡 Prefira navigation properties a joins explícitos sempre que possível. O EF otimiza melhor com navigation properties. Use joins explícitos apenas quando a navigation property não existe ou para cenários específicos.

### 10.3 Dapper — Queries SQL completas

Aqui o Dapper brilha. Você escreve exatamente o SQL que precisa:

```csharp
const string sql = """
    SELECT 
        p.Id, p.Name, p.Price,
        c.Name AS CategoryName,
        s.Name AS SupplierName,
        COALESCE(SUM(oi.Quantity), 0) AS TotalSold
    FROM Products p
    INNER JOIN Categories c ON c.Id = p.CategoryId
    INNER JOIN Suppliers s ON s.Id = p.SupplierId
    LEFT JOIN OrderItems oi ON oi.ProductId = p.Id
    WHERE p.Active = 1
    GROUP BY p.Id, p.Name, p.Price, c.Name, s.Name
    ORDER BY TotalSold DESC
    OFFSET @Offset ROWS FETCH NEXT @PageSize ROWS ONLY
    """;

var products = await _connection.QueryAsync<ProductFullDto>(sql,
    new { Offset = (page - 1) * pageSize, PageSize = pageSize });
```

### 10.4 Boas práticas para consultas compostas

1. **Comece pelo FROM principal**: identifique a tabela raiz e construa a partir dela
2. **Joins antes de WHEREs**: ajuda o otimizador do banco
3. **Prefira INNER JOIN a LEFT JOIN** quando souber que o relacionamento sempre existe
4. **WHERE antes de GROUP BY**: filtre o máximo possível antes de agrupar
5. **Evite subqueries correlacionadas**: use JOINs ou CTEs
6. **CTEs para queries complexas**: melhoram drasticamente a legibilidade

```sql
-- ✅ CTE para organizar queries complexas
WITH ActiveProducts AS (
    SELECT p.*, c.Name AS CategoryName
    FROM Products p
    INNER JOIN Categories c ON c.Id = p.CategoryId
    WHERE p.Active = 1
),
SalesData AS (
    SELECT ProductId, SUM(Quantity) AS TotalSold
    FROM OrderItems
    WHERE OrderDate >= @StartDate
    GROUP BY ProductId
)
SELECT ap.*, COALESCE(sd.TotalSold, 0) AS TotalSold
FROM ActiveProducts ap
LEFT JOIN SalesData sd ON sd.ProductId = ap.Id
ORDER BY TotalSold DESC;
```

### 10.5 Sinalizadores de problema

| Sinal | Possível causa | Ação |
|---|---|---|
| Query com 5+ JOINs | Modelagem pode estar pedindo uma view ou stored procedure | Considere criar uma view no banco |
| Subqueries no SELECT | Carga excessiva por linha | Refatore para JOIN ou CTE |
| DISTINCT em queries compostas | Possível explosão cartesiana oculta | Revise os relacionamentos |
| Mesma tabela com múltiplos JOINs | Pode ser caso para pivot ou modelagem hierárquica | Considere CTE recursiva |

---

## Capítulo 11 • Tópicos Complementares Essenciais

### 11.1 Paginação eficiente

```csharp
// ✅ Keyset pagination (muito mais rápida que offset para grandes volumes)
public async Task<List<ProductDto>> GetNextPageAsync(int lastId, int pageSize)
{
    return await _context.Products
        .AsNoTracking()
        .Where(p => p.Id > lastId)  // ⬅ Usa índice da PK, performance constante
        .OrderBy(p => p.Id)
        .Take(pageSize)
        .Select(p => new ProductDto { ... })
        .ToListAsync();
}
```

> Offset pagination (`Skip/Take`) fica mais lenta conforme você avança nas páginas. Keyset pagination mantém performance constante.

### 11.2 Caching estratégico

Não cacheie tudo. Cacheie inteligentemente:

```csharp
// Cache de categorias — muda raramente
public async Task<List<CategoryDto>> GetCategoriesAsync()
{
    return await _cache.GetOrCreateAsync("categories", async entry =>
    {
        entry.AbsoluteExpirationRelativeToNow = TimeSpan.FromHours(1);
        return await _context.Categories
            .AsNoTracking()
            .Select(c => new CategoryDto { ... })
            .ToListAsync();
    });
}
```

### 11.3 Health Checks

```csharp
builder.Services.AddHealthChecks()
    .AddDbContextCheck<AppDbContext>("Database")
    .AddUrlGroup(new Uri("https://api.externa.com/health"), "ExternalAPI");

app.MapHealthChecks("/health");
```

### 11.4 Resilience com Polly

```csharp
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(connectionString, sqlOptions =>
    {
        sqlOptions.EnableRetryOnFailure(
            maxRetryCount: 3,
            maxRetryDelay: TimeSpan.FromSeconds(10),
            errorNumbersToAdd: null);
    }));
```

### 11.5 Logging estruturado

```csharp
// ✅ Log estruturado — fácil de consultar no Seq, Elastic, etc.
_logger.LogInformation(
    "Produto {ProductId} atualizado. Preço alterado de {OldPrice} para {NewPrice}",
    productId, oldPrice, newPrice);

// ❌ Não faça interpolação manual que perde a estrutura
_logger.LogInformation($"Produto {productId} atualizado");
```

### 11.6 Tratamento global de exceções

```csharp
public class GlobalExceptionMiddleware
{
    public async Task InvokeAsync(HttpContext context, RequestDelegate next)
    {
        try
        {
            await next(context);
        }
        catch (NotFoundException ex)
        {
            context.Response.StatusCode = 404;
            await context.Response.WriteAsJsonAsync(new { error = ex.Message });
        }
        catch (ValidationException ex)
        {
            context.Response.StatusCode = 400;
            await context.Response.WriteAsJsonAsync(new { errors = ex.Errors });
        }
        // ⚠️ Log completo, mas retorne mensagem genérica para erros 500
        catch (Exception ex)
        {
            _logger.LogError(ex, "Erro não tratado");
            context.Response.StatusCode = 500;
            await context.Response.WriteAsJsonAsync(new { error = "Erro interno" });
        }
    }
}
```

### 11.7 Testes com banco real (Integration Tests)

```csharp
public class ProductRepositoryTests : IClassFixture<DatabaseFixture>
{
    private readonly DatabaseFixture _fixture;

    [Fact]
    public async Task GetById_ExistingProduct_ReturnsProduct()
    {
        using var context = _fixture.CreateContext();
        var repo = new ProductRepository(context);
        
        var result = await repo.GetByIdAsync(_fixture.SeededProductId);
        
        Assert.NotNull(result);
        Assert.Equal("Product A", result.Name);
    }
}
```

> Use um banco real em container Docker para testes de integração. Evite mockar DbContext — você acaba testando o mock, não o comportamento real.

---

## Resumo Final • Checklist de Boas Práticas

### 🔴 Pontos críticos (faça isso agora)

- [ ] **Desabilite Lazy Loading** ou, no mínimo, esteja ciente de onde ele acontece
- [ ] Use `AsNoTracking()` em TODAS as consultas somente leitura
- [ ] Troque `FirstOrDefault` por `SingleOrDefault` em consultas por chave única
- [ ] Substitua `foreach` + `SaveChanges` por operações em lote (`AddRange`, `ExecuteUpdate`)
- [ ] Crie DTOs específicos para cada endpoint — nunca exponha entidades diretamente
- [ ] Revise todos os `Include` múltiplos: há risco de explosão cartesiana?

### 🟡 Melhorias importantes (prioridade alta)

- [ ] Use split queries (`AsSplitQuery`) quando carregar múltiplos `1:N`
- [ ] Adote `ExecuteUpdate` / `ExecuteDelete` para operações em massa (EF Core 7+)
- [ ] Implemente paginação por keyset em endpoints de listagem com muitos dados
- [ ] Separe repositórios de leitura (Dapper) e escrita (EF)
- [ ] Configure índices estrategicamente com base nas queries mais frequentes
- [ ] Adicione health checks e retry policies

### 🟢 Refinamentos (quando o básico estiver sólido)

- [ ] Implemente caching para dados que mudam raramente
- [ ] Adote logging estruturado em todo o fluxo
- [ ] Crie testes de integração com banco real
- [ ] Considere views no banco para queries compostas muito frequentes
- [ ] Monitore queries lentas com Application Insights ou ferramenta similar

---

## Conclusão

Desenvolver APIs .NET robustas, performáticas e manuteníveis não é sobre decorar todas as funcionalidades do Entity Framework ou do Dapper. É sobre **entender os trade-offs** e fazer escolhas conscientes.

Recapitulando as decisões mais importantes que você levará deste ebook:

| Decisão | Resumo |
|---|---|
| **EF vs Dapper** | Use os dois. EF para comandos e CRUD simples, Dapper para leituras complexas. |
| **DTOs** | Sempre. Desacople o contrato da API do modelo de dados. |
| **N+1** | O vilão mais comum. Combata com `Include`, projeções e Lazy Loading desabilitado. |
| **Explosão cartesiana** | Múltiplos `1:N` na mesma query = perigo. Use split queries. |
| **Consultas em lote** | Nunca faça loop com `SaveChanges`. Use `AddRange`, `ExecuteUpdate` ou bulk operations. |
| **Paginação** | Prefira keyset sobre offset para grandes volumes. |

O mercado valoriza cada vez mais profissionais que vão além do "funciona" e entregam soluções pensadas para escala, manutenção e evolução. Cada tópico abordado aqui é um degrau nessa direção.

A melhor API não é a que usa a tecnologia mais recente — é aquela que **resolve o problema com clareza, escala quando necessário e não dá dor de cabeça para quem faz manutenção seis meses depois**.

Bom código. 🚀

---

*Este ebook foi produzido com foco em aplicação prática. Guarde-o como referência rápida e revise seus projetos com o checklist do resumo final.*


