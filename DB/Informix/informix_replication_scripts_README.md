# Informix Replication Scripts

Production-ready scripts for setting up, monitoring, and managing IBM Informix replication for **15TB datasets** with real-time continuous replication requirements. Supports both **Enterprise Replication (ER)** and **High-Availability Data Replication (HDR)**.

---

## Directory Structure

```
informix_replication_scripts/
├── README.md                          # This file
├── replication.conf.example           # Configuration template (copy to replication.conf)
├── lib/
│   └── common.sh                      # Shared library (logging, validation, utilities)
├── setup/
│   ├── 01_validate_environment.sh     # Pre-flight environment checks
│   ├── 02_configure_er.sh            # Enterprise Replication setup
│   ├── 03_configure_hdr.sh           # HDR setup
│   └── 04_configure_logical_logs.sh  # Log optimization for 15TB
├── config_templates/
│   ├── onconfig_er.conf              # ER onconfig parameter template
│   ├── onconfig_hdr.conf             # HDR onconfig parameter template
│   ├── sqlhosts.example              # sqlhosts configuration examples
│   └── replicate_definitions.sql     # CDR replicate definition templates
├── initial_sync/
│   ├── 01_batch_data_copy.sh         # Parallel data export/import for 15TB
│   ├── 02_schema_validation.sh       # Schema and data validation
│   └── 03_sync_progress_monitor.sh   # Real-time sync progress dashboard
├── monitoring/
│   ├── 01_replication_lag_monitor.sh  # ER/HDR lag monitoring with alerts
│   ├── 02_queue_size_monitor.sh      # CDR queue depth monitoring
│   ├── 03_conflict_detection.sh      # Conflict detection and reporting
│   ├── 04_health_check.sh           # Comprehensive health assessment
│   └── 05_performance_metrics.sh     # Performance metrics collection (CSV)
├── maintenance/
│   ├── 01_hdr_failover.sh           # Planned/emergency HDR failover
│   ├── 02_er_repair.sh              # ER replicate repair and resync
│   ├── 03_rolling_upgrade.sh        # Rolling upgrade procedure
│   └── 04_backup_recovery.sh        # Replication-compatible backup/restore
├── troubleshooting/
│   ├── 01_log_analyzer.sh           # Replication error log analysis
│   ├── 02_network_latency_test.sh   # Network performance testing
│   └── 03_transaction_throughput.sh  # TPS measurement and capacity planning
└── logs/                              # Log output directory (auto-created)
```

---

## Quick Start

### 1. Configure

```bash
cd /home/ubuntu/informix_replication_scripts
cp replication.conf.example replication.conf
# Edit replication.conf with your environment-specific values
vi replication.conf
```

### 2. Validate Environment

```bash
./setup/01_validate_environment.sh --config replication.conf
```

### 3. Set Up Replication

**For Enterprise Replication (recommended):**
```bash
# Configure ER on source
./setup/02_configure_er.sh --config replication.conf

# Optimize logical logs for 15TB
./setup/04_configure_logical_logs.sh --config replication.conf --resize
```

**For HDR (high-availability failover):**
```bash
# On primary server
./setup/03_configure_hdr.sh --config replication.conf --primary --create-backup

# On secondary server (after restoring level-0 backup)
./setup/03_configure_hdr.sh --config replication.conf --secondary
```

### 4. Initial Data Sync

```bash
# Export and import data in parallel
./initial_sync/01_batch_data_copy.sh --config replication.conf --parallel 8

# Validate sync
./initial_sync/02_schema_validation.sh --config replication.conf

# Monitor progress (in another terminal)
./initial_sync/03_sync_progress_monitor.sh --config replication.conf
```

### 5. Start Monitoring

```bash
# Health check (single run)
./monitoring/04_health_check.sh --config replication.conf --once

# Continuous lag monitoring
./monitoring/01_replication_lag_monitor.sh --config replication.conf --interval 60

# Nagios/Zabbix integration
./monitoring/04_health_check.sh --nagios
```

---

## Detailed Script Documentation

### Setup Scripts

| Script | Purpose | Key Options |
|--------|---------|-------------|
| `01_validate_environment.sh` | Pre-flight checks (versions, network, disk, config) | `--remote` to also check target server |
| `02_configure_er.sh` | Configure Enterprise Replication CDR parameters, SBspaces, replicates | `--source-only`, `--target-only`, `--skip-restart` |
| `03_configure_hdr.sh` | Configure HDR primary/secondary with failover parameters | `--primary`, `--secondary`, `--create-backup` |
| `04_configure_logical_logs.sh` | Optimize log configuration for 15TB dataset | `--analyze`, `--resize`, `--add-logs N` |

### Configuration Templates

| Template | Description |
|----------|-------------|
| `onconfig_er.conf` | All CDR_* parameters tuned for 15TB with ER |
| `onconfig_hdr.conf` | HDR parameters (DRAUTO, DRINTERVAL, etc.) |
| `sqlhosts.example` | sqlhosts examples for ER, HDR, hub-spoke, and combined setups |
| `replicate_definitions.sql` | cdr define replicate templates (full, selective, filtered, bidirectional, custom conflict resolution) |

### Initial Sync Scripts

| Script | Purpose | Key Options |
|--------|---------|-------------|
| `01_batch_data_copy.sh` | Parallel export/import for 15TB initial load | `--parallel N`, `--schema-only`, `--resume` |
| `02_schema_validation.sh` | Validate schema, row counts, indexes between source and target | `--checksums` for data integrity |
| `03_sync_progress_monitor.sh` | Real-time dashboard for sync progress | `--interval N`, `--once` |

### Monitoring Scripts

| Script | Purpose | Output Formats |
|--------|---------|----------------|
| `01_replication_lag_monitor.sh` | ER queue lag and HDR apply lag | Console, JSON |
| `02_queue_size_monitor.sh` | CDR send/receive queue depth and SBspace usage | Console with alerts |
| `03_conflict_detection.sh` | Detect duplicate key, update, and other conflicts | Report file, auto-resolve |
| `04_health_check.sh` | Overall replication health score (0-100) | Console, JSON, Nagios |
| `05_performance_metrics.sh` | TPS, cache hit, I/O stats collection | CSV files for trending |

### Maintenance Scripts

| Script | Purpose | Key Options |
|--------|---------|-------------|
| `01_hdr_failover.sh` | Planned and emergency HDR failover/failback | `--planned`, `--emergency`, `--failback` |
| `02_er_repair.sh` | Check, repair, resync ER replicates | `--check`, `--repair NAME`, `--repair-all`, `--resync NAME` |
| `03_rolling_upgrade.sh` | Minimal-downtime Informix version upgrade | `--plan`, `--phase 1-4` |
| `04_backup_recovery.sh` | Replication-compatible backup and restore | `--full`, `--incremental`, `--log-backup`, `--restore` |

### Troubleshooting Scripts

| Script | Purpose | Key Options |
|--------|---------|-------------|
| `01_log_analyzer.sh` | Analyze online.log for replication errors and trends | `--last-hours N`, `--severity LEVEL` |
| `02_network_latency_test.sh` | ICMP, TCP, bandwidth, and DNS testing | `--full`, `--bandwidth` |
| `03_transaction_throughput.sh` | Measure TPS and replication capacity | `--duration N`, `--csv` |

---

## Configuration Reference

### Key `replication.conf` Settings

```bash
# Server identification
SOURCE_SERVER_NAME="ifx_primary"       # Source Informix server name
TARGET_SERVER_NAME="ifx_secondary"     # Target Informix server name
SOURCE_HOSTNAME="primary-host"         # Source hostname/IP
TARGET_HOSTNAME="secondary-host"       # Target hostname/IP

# Databases to replicate
REPLICATE_DATABASES="production_db,analytics_db"

# Performance tuning for 15TB
LOGICAL_LOG_SIZE_MB=500                # 500MB per log file
LOGICAL_LOG_COUNT=50                   # 50 log files = 25GB total
SYNC_PARALLEL_THREADS=8               # Parallel export/import streams

# Monitoring thresholds
LAG_WARN_THRESHOLD=30                  # Warn at 30s lag
LAG_CRITICAL_THRESHOLD=120             # Critical at 2min lag
QUEUE_WARN_THRESHOLD=10000             # Warn at 10K queue entries
QUEUE_CRITICAL_THRESHOLD=100000        # Critical at 100K entries
```

### 15TB Sizing Recommendations

| Parameter | Recommended | Rationale |
|-----------|-------------|-----------|
| Log file size | 500MB - 1GB | Balance between switch frequency and recovery |
| Log file count | 40-60 | Handle burst writes without log-full |
| CDR_QUEUEMEM | 4GB | Reduce disk I/O for queue operations |
| CDR_EVALTHREADS | 4-8 | Parallel processing of replication data |
| DS_TOTAL_MEMORY | 40GB+ | Buffer pool for 15TB dataset |
| BUFFERPOOL | 500K+ buffers | Cache hit ratio > 95% target |

---

## Operational Runbooks

### Daily Operations
1. Run health check: `./monitoring/04_health_check.sh --once`
2. Check for conflicts: `./monitoring/03_conflict_detection.sh --once`
3. Verify backup status: `./maintenance/04_backup_recovery.sh --verify`

### Planned Maintenance
1. Pre-check: `./setup/01_validate_environment.sh`
2. Suspend replication: `cdr suspend replicate --all`
3. Perform maintenance
4. Resume replication: `cdr resume replicate --all`
5. Verify: `./monitoring/04_health_check.sh --once`

### Emergency Failover
1. Verify primary is down: `./maintenance/01_hdr_failover.sh --status`
2. Promote secondary: `./maintenance/01_hdr_failover.sh --emergency`
3. Redirect applications
4. When primary recovers: Restore from backup, reconfigure as secondary

### Troubleshooting Replication Issues
1. Analyze logs: `./troubleshooting/01_log_analyzer.sh --last-hours 4`
2. Check network: `./troubleshooting/02_network_latency_test.sh`
3. Measure throughput: `./troubleshooting/03_transaction_throughput.sh --duration 120`
4. Repair replicates: `./maintenance/02_er_repair.sh --check && --repair-all`

---

## Prerequisites

- **IBM Informix 12.10+** (14.10+ recommended for 15TB datasets)
- **Bash 4.0+**
- Standard utilities: `bc`, `ping`, `nc` (netcat), `df`, `du`
- Informix environment variables set: `INFORMIXDIR`, `INFORMIXSERVER`, `ONCONFIG`
- User with Informix DBA privileges

## Notes

- All scripts include `--help` for detailed usage information
- Logs are written to `./logs/` with timestamps for audit trails
- Set `BATCH_MODE=1` in config for non-interactive (cron/automated) execution
- Set `DEBUG=1` for verbose debug output
- Scripts support both interactive confirmation and batch-mode auto-confirmation

## License

Internal use. Adapt as needed for your environment.
