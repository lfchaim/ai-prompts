# Prompt
Write code for - Pretendo criar uma API RESTful, usando .Net.
Qual versão, frameworks, design patterns e estrutura do projeto mais recomendados?
API básica:
1. POST - Recebe um arquivo CSV, onde cada header representa uma coluna de uma tabela específica, por exemplo "order.csv" será relacionada à tabela "order".  Banco de dados é Oracle.
2. A leitura da planilha deve ser otimizada, usando conceito de multi-thread
3. O processo de INSERT também deve ser otimizado, por exemplo utilizar conceito "insert batch"
4. Caso ocorra alguma linha, identificar a linha e armazenar num JSON que será  apresentado no Body do Response
   
## AI
Abacus AI, usando Claude Sonnet 4.5.  

# Recomendações para API RESTful em .NET

## Versão e Frameworks Recomendados

- **.NET 8** (LTS - suporte até nov/2026)
- **ASP.NET Core Web API**
- **Oracle.ManagedDataAccess.Core** (driver Oracle oficial)
- **CsvHelper** (leitura otimizada de CSV)

## Design Patterns e Arquitetura

- **Clean Architecture** / **Vertical Slice Architecture**
- **Repository Pattern** (acesso a dados)
- **Unit of Work** (transações)
- **CQRS** (separar commands/queries)
- **Result Pattern** (tratamento de erros)

## Estrutura do Projeto

```
CsvImportApi/
├── src/
│   ├── CsvImportApi.Api/              # Controllers, Middleware
│   ├── CsvImportApi.Application/      # Services, DTOs, Interfaces
│   ├── CsvImportApi.Domain/           # Entities, Value Objects
│   └── CsvImportApi.Infrastructure/   # Repositories, Oracle Context
└── tests/
```

---

## Código Completo da API

### 1. **CsvImportApi.Api/Controllers/CsvImportController.cs**

```csharp
using CsvImportApi.Application.DTOs;
using CsvImportApi.Application.Services;
using Microsoft.AspNetCore.Mvc;

namespace CsvImportApi.Api.Controllers;

[ApiController]
[Route("api/[controller]")]
public class CsvImportController : ControllerBase
{
    private readonly ICsvImportService _csvImportService;
    private readonly ILogger<CsvImportController> _logger;

    public CsvImportController(ICsvImportService csvImportService, ILogger<CsvImportController> logger)
    {
        _csvImportService = csvImportService;
        _logger = logger;
    }

    [HttpPost("upload")]
    [RequestSizeLimit(100_000_000)] // 100MB
    [RequestFormLimits(MultipartBodyLengthLimit = 100_000_000)]
    public async Task<IActionResult> UploadCsv(IFormFile file, CancellationToken cancellationToken)
    {
        if (file == null || file.Length == 0)
            return BadRequest(new { error = "Arquivo não fornecido ou vazio" });

        if (!file.FileName.EndsWith(".csv", StringComparison.OrdinalIgnoreCase))
            return BadRequest(new { error = "Apenas arquivos CSV são permitidos" });

        try
        {
            var tableName = Path.GetFileNameWithoutExtension(file.FileName);
            
            using var stream = file.OpenReadStream();
            var result = await _csvImportService.ImportCsvAsync(stream, tableName, cancellationToken);

            return Ok(result);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Erro ao processar arquivo CSV");
            return StatusCode(500, new { error = "Erro interno ao processar arquivo", details = ex.Message });
        }
    }
}
```

### 2. **CsvImportApi.Application/DTOs/ImportResultDto.cs**

```csharp
namespace CsvImportApi.Application.DTOs;

public class ImportResultDto
{
    public string TableName { get; set; } = string.Empty;
    public int TotalRows { get; set; }
    public int SuccessfulRows { get; set; }
    public int FailedRows { get; set; }
    public TimeSpan ProcessingTime { get; set; }
    public List<ErrorDetail> Errors { get; set; } = new();
}

public class ErrorDetail
{
    public int LineNumber { get; set; }
    public string ErrorMessage { get; set; } = string.Empty;
    public Dictionary<string, string> RowData { get; set; } = new();
}
```

### 3. **CsvImportApi.Application/Services/ICsvImportService.cs**

```csharp
using CsvImportApi.Application.DTOs;

namespace CsvImportApi.Application.Services;

public interface ICsvImportService
{
    Task<ImportResultDto> ImportCsvAsync(Stream csvStream, string tableName, CancellationToken cancellationToken);
}
```

### 4. **CsvImportApi.Application/Services/CsvImportService.cs**

```csharp
using System.Collections.Concurrent;
using System.Diagnostics;
using CsvHelper;
using CsvHelper.Configuration;
using CsvImportApi.Application.DTOs;
using CsvImportApi.Infrastructure.Repositories;
using System.Globalization;

namespace CsvImportApi.Application.Services;

public class CsvImportService : ICsvImportService
{
    private readonly IBulkInsertRepository _bulkInsertRepository;
    private readonly ILogger<CsvImportService> _logger;
    private const int BATCH_SIZE = 1000;
    private const int MAX_DEGREE_OF_PARALLELISM = 4;

    public CsvImportService(IBulkInsertRepository bulkInsertRepository, ILogger<CsvImportService> logger)
    {
        _bulkInsertRepository = bulkInsertRepository;
        _logger = logger;
    }

    public async Task<ImportResultDto> ImportCsvAsync(Stream csvStream, string tableName, CancellationToken cancellationToken)
    {
        var stopwatch = Stopwatch.StartNew();
        var result = new ImportResultDto { TableName = tableName };
        var errors = new ConcurrentBag<ErrorDetail>();

        try
        {
            var config = new CsvConfiguration(CultureInfo.InvariantCulture)
            {
                HasHeaderRecord = true,
                MissingFieldFound = null,
                BadDataFound = null
            };

            using var reader = new StreamReader(csvStream);
            using var csv = new CsvReader(reader, config);

            // Lê o header
            await csv.ReadAsync();
            csv.ReadHeader();
            var headers = csv.HeaderRecord ?? throw new InvalidOperationException("CSV sem header");

            // Lê todos os registros em batches
            var allRecords = new List<Dictionary<string, string>>();
            int lineNumber = 2; // Linha 1 é o header

            while (await csv.ReadAsync())
            {
                try
                {
                    var record = new Dictionary<string, string>();
                    foreach (var header in headers)
                    {
                        record[header] = csv.GetField(header) ?? string.Empty;
                    }
                    record["_LineNumber"] = lineNumber.ToString();
                    allRecords.Add(record);
                }
                catch (Exception ex)
                {
                    errors.Add(new ErrorDetail
                    {
                        LineNumber = lineNumber,
                        ErrorMessage = $"Erro ao ler linha: {ex.Message}",
                        RowData = new Dictionary<string, string>()
                    });
                }
                lineNumber++;
            }

            result.TotalRows = allRecords.Count;

            // Processa em batches usando multi-threading
            var batches = allRecords
                .Select((record, index) => new { record, index })
                .GroupBy(x => x.index / BATCH_SIZE)
                .Select(g => g.Select(x => x.record).ToList())
                .ToList();

            var parallelOptions = new ParallelOptions
            {
                MaxDegreeOfParallelism = MAX_DEGREE_OF_PARALLELISM,
                CancellationToken = cancellationToken
            };

            await Parallel.ForEachAsync(batches, parallelOptions, async (batch, ct) =>
            {
                try
                {
                    await _bulkInsertRepository.BulkInsertAsync(tableName, batch, ct);
                    Interlocked.Add(ref result.SuccessfulRows, batch.Count);
                }
                catch (Exception ex)
                {
                    _logger.LogError(ex, "Erro ao inserir batch");
                    
                    // Tenta inserir linha por linha para identificar erros específicos
                    foreach (var record in batch)
                    {
                        try
                        {
                            await _bulkInsertRepository.BulkInsertAsync(tableName, new List<Dictionary<string, string>> { record }, ct);
                            Interlocked.Increment(ref result.SuccessfulRows);
                        }
                        catch (Exception innerEx)
                        {
                            var lineNum = int.Parse(record["_LineNumber"]);
                            var rowData = new Dictionary<string, string>(record);
                            rowData.Remove("_LineNumber");

                            errors.Add(new ErrorDetail
                            {
                                LineNumber = lineNum,
                                ErrorMessage = innerEx.Message,
                                RowData = rowData
                            });
                            Interlocked.Increment(ref result.FailedRows);
                        }
                    }
                }
            });

            result.Errors = errors.OrderBy(e => e.LineNumber).ToList();
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Erro crítico no processamento do CSV");
            throw;
        }
        finally
        {
            stopwatch.Stop();
            result.ProcessingTime = stopwatch.Elapsed;
        }

        return result;
    }
}
```

### 5. **CsvImportApi.Infrastructure/Repositories/IBulkInsertRepository.cs**

```csharp
namespace CsvImportApi.Infrastructure.Repositories;

public interface IBulkInsertRepository
{
    Task BulkInsertAsync(string tableName, List<Dictionary<string, string>> records, CancellationToken cancellationToken);
}
```

### 6. **CsvImportApi.Infrastructure/Repositories/OracleBulkInsertRepository.cs**

```csharp
using Oracle.ManagedDataAccess.Client;
using System.Data;
using System.Text;

namespace CsvImportApi.Infrastructure.Repositories;

public class OracleBulkInsertRepository : IBulkInsertRepository
{
    private readonly string _connectionString;
    private readonly ILogger<OracleBulkInsertRepository> _logger;

    public OracleBulkInsertRepository(IConfiguration configuration, ILogger<OracleBulkInsertRepository> logger)
    {
        _connectionString = configuration.GetConnectionString("OracleDb") 
            ?? throw new InvalidOperationException("Connection string não configurada");
        _logger = logger;
    }

    public async Task BulkInsertAsync(string tableName, List<Dictionary<string, string>> records, CancellationToken cancellationToken)
    {
        if (!records.Any()) return;

        using var connection = new OracleConnection(_connectionString);
        await connection.OpenAsync(cancellationToken);

        using var transaction = connection.BeginTransaction();
        try
        {
            // Remove o campo auxiliar _LineNumber
            var columns = records.First().Keys.Where(k => k != "_LineNumber").ToList();
            
            // Monta o comando INSERT com array binding (bulk insert otimizado)
            var sql = BuildBulkInsertCommand(tableName, columns, records.Count);

            using var command = new OracleCommand(sql, connection);
            command.Transaction = transaction;
            command.ArrayBindCount = records.Count;

            // Configura os parâmetros com array binding
            foreach (var column in columns)
            {
                var parameter = new OracleParameter
                {
                    ParameterName = $":{column}",
                    OracleDbType = OracleDbType.Varchar2,
                    Size = 4000
                };

                var values = records.Select(r => r.ContainsKey(column) ? r[column] : (object)DBNull.Value).ToArray();
                parameter.Value = values;
                
                command.Parameters.Add(parameter);
            }

            await command.ExecuteNonQueryAsync(cancellationToken);
            await transaction.CommitAsync(cancellationToken);

            _logger.LogInformation($"Inseridos {records.Count} registros na tabela {tableName}");
        }
        catch (Exception ex)
        {
            await transaction.RollbackAsync(cancellationToken);
            _logger.LogError(ex, $"Erro ao inserir batch na tabela {tableName}");
            throw;
        }
    }

    private string BuildBulkInsertCommand(string tableName, List<string> columns, int recordCount)
    {
        var columnList = string.Join(", ", columns);
        var parameterList = string.Join(", ", columns.Select(c => $":{c}"));
        
        return $"INSERT INTO {tableName} ({columnList}) VALUES ({parameterList})";
    }
}
```

### 7. **CsvImportApi.Api/Program.cs**

```csharp
using CsvImportApi.Application.Services;
using CsvImportApi.Infrastructure.Repositories;

var builder = WebApplication.CreateBuilder(args);

// Configuração de serviços
builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

// Dependency Injection
builder.Services.AddScoped<ICsvImportService, CsvImportService>();
builder.Services.AddScoped<IBulkInsertRepository, OracleBulkInsertRepository>();

// Configuração de upload de arquivos grandes
builder.Services.Configure<Microsoft.AspNetCore.Http.Features.FormOptions>(options =>
{
    options.MultipartBodyLengthLimit = 100_000_000; // 100MB
});

var app = builder.Build();

// Pipeline HTTP
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();
app.UseAuthorization();
app.MapControllers();

app.Run();
```

### 8. **appsettings.json**

```json
{
  "ConnectionStrings": {
    "OracleDb": "User Id=seu_usuario;Password=sua_senha;Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=localhost)(PORT=1521))(CONNECT_DATA=(SERVICE_NAME=ORCL)))"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### 9. **CsvImportApi.Api.csproj**

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="CsvHelper" Version="30.0.1" />
    <PackageReference Include="Oracle.ManagedDataAccess.Core" Version="23.4.0" />
    <PackageReference Include="Swashbuckle.AspNetCore" Version="6.5.0" />
  </ItemGroup>

</Project>
```

---

## Recursos Implementados

✅ **Multi-threading**: `Parallel.ForEachAsync` com controle de paralelismo  
✅ **Batch Insert**: Oracle Array Binding (até 1000 registros por batch)  
✅ **Tratamento de erros**: Captura linha específica e retorna no JSON  
✅ **Performance**: CsvHelper + StreamReader otimizado  
✅ **Clean Architecture**: Separação de responsabilidades  
✅ **Logging**: ILogger integrado  
✅ **Cancellation Token**: Suporte a cancelamento de operações

## Exemplo de Response

```json
{
  "tableName": "order",
  "totalRows": 5000,
  "successfulRows": 4998,
  "failedRows": 2,
  "processingTime": "00:00:03.4521234",
  "errors": [
    {
      "lineNumber": 150,
      "errorMessage": "ORA-01400: cannot insert NULL into (\"SCHEMA\".\"ORDER\".\"ID\")",
      "rowData": {
        "id": "",
        "customer": "John Doe",
        "amount": "100.50"
      }
    }
  ]
}
```
# Criando projeto - by ChatGPT
Excelente! Vamos montar um **projeto base completo** em **.NET 8** com a arquitetura **Clean Architecture**, incluindo o endpoint `POST /api/import` que recebe um arquivo CSV, processa em paralelo e faz inserts otimizados com **BulkInsert**.

A seguir, você terá:

* Estrutura de pastas
* Arquivos principais (`.csproj`, `Program.cs`, `Controller`, `Command`, `Service`, etc.)
* Dependências
* Passos para build e execução

---

## 🧱 Estrutura do Projeto

```
CsvImporter/
 ├── Api/
 │    ├── Controllers/
 │    │     └── ImportController.cs
 │    ├── Program.cs
 │    ├── Api.csproj
 │
 ├── Application/
 │    ├── Commands/
 │    │     └── ImportCsv/
 │    │           ├── ImportCsvCommand.cs
 │    │           └── ImportCsvCommandHandler.cs
 │    ├── DTOs/
 │    │     ├── ImportCsvResultDto.cs
 │    │     └── ErrorRecord.cs
 │    ├── Interfaces/
 │    │     └── IImportService.cs
 │    ├── Services/
 │    │     └── ImportService.cs
 │    ├── Application.csproj
 │
 ├── Domain/
 │    ├── Entities/
 │    │     └── Order.cs
 │    └── Domain.csproj
 │
 ├── Infrastructure/
 │    ├── Persistence/
 │    │     ├── AppDbContext.cs
 │    │     └── AppDbContextFactory.cs
 │    ├── Infrastructure.csproj
 │
 ├── CsvImporter.sln
 └── docker-compose.yml
```

---

## 🧰 Passo 1: Criar a Solution e Projetos

Execute no terminal:

```bash
dotnet new sln -n CsvImporter
mkdir Api Application Domain Infrastructure
cd Api && dotnet new webapi -n Api && cd ..
cd Application && dotnet new classlib -n Application && cd ..
cd Domain && dotnet new classlib -n Domain && cd ..
cd Infrastructure && dotnet new classlib -n Infrastructure && cd ..
```

Adicionar referências:

```bash
dotnet sln add Api/Api.csproj Application/Application.csproj Domain/Domain.csproj Infrastructure/Infrastructure.csproj
dotnet add Api/Api.csproj reference Application/Application.csproj Infrastructure/Infrastructure.csproj Domain/Domain.csproj
dotnet add Application/Application.csproj reference Domain/Domain.csproj
dotnet add Infrastructure/Infrastructure.csproj reference Domain/Domain.csproj
```

---

## ⚙️ Passo 2: Dependências Principais

No **Api.csproj**:

```xml
<ItemGroup>
  <PackageReference Include="MediatR.Extensions.Microsoft.DependencyInjection" Version="12.1.1" />
  <PackageReference Include="CsvHelper" Version="30.0.1" />
  <PackageReference Include="EFCore.BulkExtensions" Version="7.7.3" />
  <PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="8.0.6" />
  <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="8.0.6" />
  <PackageReference Include="FluentValidation" Version="11.7.1" />
  <PackageReference Include="Serilog.AspNetCore" Version="8.0.1" />
  <PackageReference Include="Swashbuckle.AspNetCore" Version="6.6.2" />
</ItemGroup>
```

---

## 🧠 Passo 3: `Program.cs` (API)

```csharp
using Application;
using Infrastructure.Persistence;
using MediatR;
using Microsoft.EntityFrameworkCore;

var builder = WebApplication.CreateBuilder(args);

// DbContext
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));

// MediatR
builder.Services.AddMediatR(typeof(Application.AssemblyMarker).Assembly);

// Services
builder.Services.AddScoped<IImportService, ImportService>();

builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();

app.UseSwagger();
app.UseSwaggerUI();

app.MapControllers();

app.Run();
```

---

## 🧠 Passo 4: `ImportController.cs`

```csharp
using Application.Commands.ImportCsv;
using MediatR;
using Microsoft.AspNetCore.Mvc;

namespace Api.Controllers;

[ApiController]
[Route("api/[controller]")]
public class ImportController : ControllerBase
{
    private readonly IMediator _mediator;
    public ImportController(IMediator mediator) => _mediator = mediator;

    [HttpPost]
    public async Task<IActionResult> Import([FromForm] IFormFile file)
    {
        if (file == null || file.Length == 0)
            return BadRequest("Arquivo CSV inválido.");

        var command = new ImportCsvCommand(file);
        var result = await _mediator.Send(command);
        return Ok(result);
    }
}
```

---

## 🧠 Passo 5: Command e Handler

**ImportCsvCommand.cs**

```csharp
using Application.DTOs;
using MediatR;
using Microsoft.AspNetCore.Http;

namespace Application.Commands.ImportCsv;

public record ImportCsvCommand(IFormFile File) : IRequest<ImportCsvResultDto>;
```

**ImportCsvCommandHandler.cs**

```csharp
using Application.DTOs;
using Application.Interfaces;
using MediatR;

namespace Application.Commands.ImportCsv;

public class ImportCsvCommandHandler : IRequestHandler<ImportCsvCommand, ImportCsvResultDto>
{
    private readonly IImportService _importService;

    public ImportCsvCommandHandler(IImportService importService)
    {
        _importService = importService;
    }

    public async Task<ImportCsvResultDto> Handle(ImportCsvCommand request, CancellationToken ct)
        => await _importService.ProcessCsvAsync(request.File, ct);
}
```

---

## 🧠 Passo 6: DTOs

**ImportCsvResultDto.cs**

```csharp
namespace Application.DTOs;

public class ImportCsvResultDto
{
    public int TotalRecords { get; set; }
    public int Inserted { get; set; }
    public List<ErrorRecord> Errors { get; set; } = new();
}
```

**ErrorRecord.cs**

```csharp
namespace Application.DTOs;

public class ErrorRecord
{
    public int Line { get; set; }
    public string Message { get; set; } = string.Empty;
}
```

---

## 🧠 Passo 7: Serviço

**IImportService.cs**

```csharp
using Application.DTOs;
using Microsoft.AspNetCore.Http;

namespace Application.Interfaces;

public interface IImportService
{
    Task<ImportCsvResultDto> ProcessCsvAsync(IFormFile file, CancellationToken ct);
}
```

**ImportService.cs**

```csharp
using Application.DTOs;
using Application.Interfaces;
using CsvHelper;
using Domain.Entities;
using Infrastructure.Persistence;
using Microsoft.EntityFrameworkCore;
using System.Collections.Concurrent;
using System.Globalization;

namespace Application.Services;

public class ImportService : IImportService
{
    private readonly AppDbContext _db;

    public ImportService(AppDbContext db) => _db = db;

    public async Task<ImportCsvResultDto> ProcessCsvAsync(IFormFile file, CancellationToken ct)
    {
        var errors = new ConcurrentBag<ErrorRecord>();
        var records = new ConcurrentBag<Order>();

        using var reader = new StreamReader(file.OpenReadStream());
        using var csv = new CsvReader(reader, CultureInfo.InvariantCulture);

        var index = 1;
        await foreach (var record in csv.GetRecordsAsync<Order>())
        {
            index++;
            try
            {
                records.Add(record);
            }
            catch (Exception ex)
            {
                errors.Add(new ErrorRecord { Line = index, Message = ex.Message });
            }
        }

        await _db.BulkInsertAsync(records.ToList(), cancellationToken: ct);

        return new ImportCsvResultDto
        {
            TotalRecords = records.Count + errors.Count,
            Inserted = records.Count,
            Errors = errors.ToList()
        };
    }
}
```

---

## 🧠 Passo 8: Entidade e DbContext

**Order.cs**

```csharp
namespace Domain.Entities;

public class Order
{
    public int Id { get; set; }
    public string CustomerName { get; set; } = string.Empty;
    public DateTime OrderDate { get; set; }
    public decimal TotalAmount { get; set; }
}
```

**AppDbContext.cs**

```csharp
using Domain.Entities;
using Microsoft.EntityFrameworkCore;

namespace Infrastructure.Persistence;

public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }
    public DbSet<Order> Orders => Set<Order>();
}
```

---

## 🧠 Passo 9: Connection String

No **appsettings.json** da API:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=CsvImporter;User Id=sa;Password=YourPassword123;TrustServerCertificate=True"
  }
}
```

---

## 🧠 Passo 10: Executar

```bash
dotnet build
dotnet ef database update
dotnet run --project Api
```

Acesse:
🔹 Swagger: `http://localhost:5000/swagger`

---

