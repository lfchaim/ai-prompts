# Prompt 
Definir docker compose para rodar PostgreSQL. defina as melhores ferramentas cliente para gerenciar o banco, que seja possível configurar no compose. identifique ferramentas open source ou gratuita que podem ser baixadas. eu uso Ubuntu 20.

# Qwen
Aqui está uma configuração completa, segura e pronta para produção/desenvolvimento, incluindo o PostgreSQL e uma ferramenta de administração web que roda nativamente no Docker Compose.

### 📦 `docker-compose.yml`
```yaml
services:
  postgres:
    image: postgres:16-alpine
    container_name: postgres_db
    environment:
      POSTGRES_USER: ${DB_USER:-devuser}
      POSTGRES_PASSWORD: ${DB_PASSWORD:-devpassword}
      POSTGRES_DB: ${DB_NAME:-devdb}
    ports:
      - "${DB_PORT:-5432}:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./init-scripts:/docker-entrypoint-initdb.d  # opcional: scripts de inicialização
    networks:
      - app_network
    restart: unless-stopped
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${DB_USER:-devuser}"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 10s

  pgadmin:
    image: dpage/pgadmin4
    container_name: pgadmin_manager
    environment:
      PGADMIN_DEFAULT_EMAIL: ${PGADMIN_EMAIL:-admin@localhost}
      PGADMIN_DEFAULT_PASSWORD: ${PGADMIN_PASSWORD:-admin123}
    ports:
      - "${PGADMIN_PORT:-5050}:80"
    networks:
      - app_network
    restart: unless-stopped
    depends_on:
      postgres:
        condition: service_healthy
    # Opcional: persistir configurações/servidores do pgAdmin
    # volumes:
    #   - pgadmin_data:/var/lib/pgadmin

volumes:
  postgres_data:
  # pgadmin_data:

networks:
  app_network:
    driver: bridge
```

### 🔑 Arquivo `.env` (recomendado)
Crie um arquivo `.env` na mesma pasta do `docker-compose.yml` para não expor credenciais:
```env
DB_USER=meu_usuario
DB_PASSWORD=senha_segura_aqui
DB_NAME=meu_banco
DB_PORT=5432

PGADMIN_EMAIL=seu@email.com
PGADMIN_PASSWORD=senha_do_pgadmin
PGADMIN_PORT=5050
```

---

### 🚀 Como usar no Ubuntu 20.04

1. **Instale Docker e Docker Compose** (se ainda não tiver):
   ```bash
   sudo apt update
   sudo apt install -y docker.io docker-compose-plugin
   sudo usermod -aG docker $USER
   newgrp docker  # ou faça logout/login
   ```

2. **Suba os containers**:
   ```bash
   docker compose up -d
   ```

3. **Verifique se estão saudáveis**:
   ```bash
   docker compose ps
   # Aguarde até que o postgres mostre "healthy"
   ```

4. **Acesse o gerenciador**:
   - pgAdmin: `http://localhost:5050`
   - Login: use as credenciais definidas no `.env`

---

### ⚙️ Configurando o pgAdmin para conectar no PostgreSQL

1. Acesse `http://localhost:5050`
2. Clique em **Add New Server**
3. Na aba **General**:
   - `Name`: `PostgreSQL Local`
4. Na aba **Connection**:
   - `Host name/address`: `postgres_db` (nome do container, não `localhost`)
   - `Port`: `5432`
   - `Maintenance database`: valor de `DB_NAME` no `.env`
   - `Username`: valor de `DB_USER`
   - `Password`: valor de `DB_PASSWORD`
5. Clique em **Save**. Pronto!

---

### 🛠️ Alternativas Open Source / Gratuitas

| Ferramenta | Tipo | Docker Nativo? | Observações |
|------------|------|----------------|-------------|
| **pgAdmin 4** | Web | ✅ Sim | Oficial, completo, suporte a múltiplos bancos, backup/restore, explain visual |
| **Adminer** | Web | ✅ Sim | Leve (~1 container), single-page, ótimo para debug rápido |
| **DBeaver Community** | Desktop | ❌ Não | Java-based, gratuito, suporte a +80 bancos, excelente UI |
| **Beekeeper Studio** | Desktop | ❌ Não | Electron, open-source, interface moderna e rápida |

💡 **Se preferir o Adminer no lugar do pgAdmin**, basta substituir o serviço `pgadmin` por:
```yaml
  adminer:
    image: adminer
    container_name: adminer
    ports:
      - "8080:8080"
    networks:
      - app_network
    restart: unless-stopped
    depends_on:
      postgres:
        condition: service_healthy
```
Acesse em `http://localhost:8080`, selecione `PostgreSQL` como sistema e use `postgres_db` como host.

---

### 🔒 Boas Práticas para Produção
- Nunca use credenciais padrão em produção. Gere senhas fortes e armazene em `.env` (adicione `.env` ao `.gitignore`).
- Para expor o PostgreSQL apenas localmente, remova `ports` do serviço `postgres` e use `networks` para comunicação interna.
- Backup automático: adicione um serviço com `pg_dump` agendado via cron ou use `docker exec` com scripts.
- Mantenha a imagem `postgres:16-alpine` (menor footprint, atualizações de segurança mais rápidas).

Precisa de um script de backup automático, integração com pgBouncer (pool de conexões) ou monitoramento com `pg_stat_statements`? É só avisar que monto a extensão no Compose.

# Prompt 
Sim

# Qwen
Aqui está a configuração completa integrando **`pg_stat_statements`**, **pgBouncer** e **backup automático**. O arquivo está pronto para uso, com foco em simplicidade e boas práticas.

### 📁 Estrutura de pastas recomendada
```
seu-projeto/
├── docker-compose.yml
├── .env
├── init-scripts/
│   └── 01-enable-pg_stat_statements.sql
├── pgbouncer/
│   ├── pgbouncer.ini
│   └── userlist.txt
└── backups/              (criado automaticamente)
```

### 📦 `docker-compose.yml` (Atualizado)
```yaml
services:
  postgres:
    image: postgres:16-alpine
    container_name: postgres_db
    environment:
      POSTGRES_USER: ${DB_USER:-devuser}
      POSTGRES_PASSWORD: ${DB_PASSWORD:-devpassword}
      POSTGRES_DB: ${DB_NAME:-devdb}
    # Habilita pg_stat_statements na inicialização
    command: ["postgres", "-c", "shared_preload_libraries=pg_stat_statements"]
    ports:
      - "${DB_PORT:-5432}:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./init-scripts:/docker-entrypoint-initdb.d
    networks:
      - app_network
    restart: unless-stopped
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${DB_USER:-devuser}"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 10s

  pgadmin:
    image: dpage/pgadmin4
    container_name: pgadmin_manager
    environment:
      PGADMIN_DEFAULT_EMAIL: ${PGADMIN_EMAIL:-admin@localhost}
      PGADMIN_DEFAULT_PASSWORD: ${PGADMIN_PASSWORD:-admin123}
    ports:
      - "${PGADMIN_PORT:-5050}:80"
    networks:
      - app_network
    restart: unless-stopped
    depends_on:
      postgres:
        condition: service_healthy

  pgbouncer:
    image: pgbouncer/pgbouncer:latest
    container_name: pgbouncer_pool
    ports:
      - "6432:6432"
    volumes:
      - ./pgbouncer/pgbouncer.ini:/etc/pgbouncer/pgbouncer.ini:ro
      - ./pgbouncer/userlist.txt:/etc/pgbouncer/userlist.txt:ro
    networks:
      - app_network
    restart: unless-stopped
    depends_on:
      postgres:
        condition: service_healthy

  pgbackup:
    image: prodrigestivill/postgres-backup-local:16
    container_name: pgbackup
    environment:
      POSTGRES_HOST: postgres
      POSTGRES_DB: ${DB_NAME:-devdb}
      POSTGRES_USER: ${DB_USER:-devuser}
      POSTGRES_PASSWORD: ${DB_PASSWORD:-devpassword}
      POSTGRES_EXTRA_OPTS: '--schema=public --blobs'
      SCHEDULE: "@daily"
      BACKUP_KEEP_DAYS: 7
      BACKUP_KEEP_WEEKS: 4
      BACKUP_KEEP_MONTHS: 6
    volumes:
      - ./backups:/backups
    networks:
      - app_network
    restart: unless-stopped
    depends_on:
      postgres:
        condition: service_healthy

volumes:
  postgres_data:

networks:
  app_network:
    driver: bridge
```

### 🔧 Arquivos de configuração auxiliares

**`init-scripts/01-enable-pg_stat_statements.sql`**
```sql
CREATE EXTENSION IF NOT EXISTS pg_stat_statements;
-- Opcional: conceder acesso para seu usuário de app
GRANT EXECUTE ON FUNCTION pg_stat_statements_reset() TO ${DB_USER:-devuser};
GRANT SELECT ON pg_stat_statements TO ${DB_USER:-devuser};
```

**`pgbouncer/pgbouncer.ini`**
```ini
[databases]
* = host=postgres port=5432

[pgbouncer]
listen_port = 6432
listen_addr = *
auth_type = md5
auth_file = /etc/pgbouncer/userlist.txt
admin_users = ${DB_USER:-devuser}
pool_mode = transaction
max_client_conn = 100
default_pool_size = 20
```

**`pgbouncer/userlist.txt`**
*(Substitua pelas mesmas credenciais do `.env`)*
```text
"devuser" "devpassword"
```

---

### 🚀 Como usar no Ubuntu 20.04

1. **Crie os arquivos e pastas**:
   ```bash
   mkdir -p init-scripts pgbouncer backups
   # Cole os conteúdos acima nos respectivos arquivos
   ```

2. **Suba tudo**:
   ```bash
   docker compose up -d
   docker compose ps  # aguarde até postgres mostrar "healthy"
   ```

3. **Verifique o `pg_stat_statements`**:
   Acesse o pgAdmin ou rode via `docker exec`:
   ```bash
   docker exec -it postgres_db psql -U devuser -d devdb -c "SELECT query, calls, mean_exec_time FROM pg_stat_statements ORDER BY calls DESC LIMIT 5;"
   ```

4. **Conecte sua aplicação via pgBouncer** (Pool de conexões):
   - Altere a string de conexão da sua app para usar **porta `6432`** e host `localhost` (ou IP do servidor).
   - Exemplo (Node.js/Prisma): `postgresql://devuser:devpassword@localhost:6432/devdb`
   - O pgBouncer gerenciará e reutilizará conexões, evitando *too many clients*.

5. **Teste/Restaure Backup**:
   - Os dumps ficarão em `./backups/` (formato `.gz`).
   - Para restaurar manualmente:
     ```bash
     gunzip -c ./backups/devdb_2026-05-23T10:00:00.gz | docker exec -i postgres_db psql -U devuser -d devdb
     ```

---

### ⚠️ Notas Importantes para Produção
| Componente | Ajuste Recomendado |
|------------|-------------------|
| `pg_stat_statements` | Monitore `mean_exec_time` e `calls`. Queries com `mean_exec_time > 1000ms` são candidatas a otimização. |
| `pgbouncer` | Em produção, use `auth_type = scram-sha-256` e gere hashes com `pg_md5`. `pool_mode = transaction` é ideal para ORMs modernos. |
| `backup` | Adicione `S3_BUCKET` e credenciais AWS no container `pgbackup` para envio automático à nuvem. |
| Segurança | Remova `"ports"` do serviço `postgres` se nenhuma aplicação externa precisar acessar diretamente a `5432`. Use apenas a rede interna ou o pgBouncer. |

Precisa de um dashboard de monitoramento (ex: `pg_stat_activity` + Grafana/Prometheus) ou integração com `pg_cron` para jobs agendados dentro do banco? É só avisar que monto a extensão no mesmo Compose.

