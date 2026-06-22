# Prompt
Dimensionar Kafka para receber logs centralizados de todos os sistemas. 
A volumetria é de 100 TPS com Payload de 10KB. 
Serão implementados 5 workers desenvolvidos em Quarkus e Kotlin, para consumir a fila e encaminhar para o Elasticsearch, assim que o worker gravar o log no Elasticsearch, deverá apagar o item na fila.

# ChatGPT
Para dimensionar essa solução, é importante considerar que o **Kafka não funciona como uma fila tradicional com deleção de mensagens após consumo**. No Kafka, as mensagens permanecem no tópico durante o período de retenção configurado, independentemente de terem sido consumidas.

O fluxo correto seria:

```text
Sistemas
    ↓
Kafka Topic Logs
    ↓
Consumer Group (5 Workers Quarkus/Kotlin)
    ↓
Elasticsearch
```

Após gravar no Elasticsearch:

1. Worker confirma o offset (commit).
2. Kafka marca a mensagem como consumida para aquele consumer group.
3. A mensagem continua armazenada até expirar pela política de retenção.

---

# 1. Cálculo de Volumetria

## Entrada

TPS: 100 mensagens/s

Payload: 10 KB

Volume por segundo:

```text
100 × 10 KB
= 1.000 KB/s
≈ 1 MB/s
```

---

## Volume diário

```text
1 MB/s × 86.400
= 86.400 MB
≈ 84 GB/dia
```

---

## Volume mensal

```text
84 GB × 30
≈ 2,5 TB/mês
```

---

# 2. Tráfego Kafka

Considerando fator de replicação 3:

```text
84 GB/dia × 3
≈ 252 GB/dia
```

Tráfego interno:

```text
1 MB/s ingestão
+
2 MB/s replicação
=
3 MB/s cluster
```

Carga extremamente baixa para Kafka.

---

# 3. Dimensionamento dos Tópicos

## Throughput esperado

```text
100 TPS
```

Kafka consegue facilmente:

```text
>100.000 TPS por broker
```

Portanto o gargalo será o Elasticsearch.

---

## Partições

Para 5 workers:

```text
Partitions >= Workers
```

Recomendação:

```text
Topic: centralized-logs

Partitions: 12
Replication Factor: 3
```

Isso permite:

* 5 consumidores ativos
* crescimento futuro
* rebalanceamentos suaves

---

# 4. Dimensionamento dos Workers

## Taxa de consumo

Entrada:

```text
100 logs/s
```

Cada worker:

```text
100 / 5
=
20 logs/s
```

---

## Escrita no Elasticsearch

20 documentos/s por worker é muito baixo.

Mesmo um único worker provavelmente suportaria essa carga.

Recomendação:

```text
5 workers
2 vCPU
2 GB RAM
```

cada.

---

# 5. Uso de Bulk no Elasticsearch

Não grave documento a documento.

Use:

```text
Bulk API
```

Exemplo:

```text
100 documentos
ou
5 segundos
```

o que ocorrer primeiro.

---

Ganho típico:

```text
10x a 50x
```

de throughput.

---

# 6. Dimensionamento do Elasticsearch

## Volume diário

```text
84 GB/dia
```

Logs geralmente comprimem entre 30% e 60%.

Assumindo 50%:

```text
84 GB → 42 GB
```

---

## Retenção de 90 dias

```text
42 × 90
=
3,78 TB
```

Com réplicas:

```text
3,78 × 2
=
7,56 TB
```

---

## Cluster sugerido

Produção:

| Nó       | CPU    | RAM   | Disco    |
| -------- | ------ | ----- | -------- |
| Master 1 | 2 vCPU | 4 GB  | 50 GB    |
| Master 2 | 2 vCPU | 4 GB  | 50 GB    |
| Master 3 | 2 vCPU | 4 GB  | 50 GB    |
| Data 1   | 8 vCPU | 32 GB | 2 TB SSD |
| Data 2   | 8 vCPU | 32 GB | 2 TB SSD |
| Data 3   | 8 vCPU | 32 GB | 2 TB SSD |

Total:

```text
3 masters
3 data nodes
```

---

# 7. Configuração Kafka Recomendada

```properties
num.partitions=12

replication.factor=3

min.insync.replicas=2

compression.type=zstd

retention.ms=604800000      # 7 dias

segment.bytes=1073741824    # 1GB

cleanup.policy=delete
```

---

# 8. Configuração dos Consumers

```properties
enable.auto.commit=false

max.poll.records=500

fetch.min.bytes=1048576

fetch.max.wait.ms=500
```

Fluxo:

```text
1. Consome lote
2. Bulk Index Elasticsearch
3. Commit Offset
4. Próximo lote
```

---

# 9. Cenário de Falha

Se Elasticsearch ficar indisponível:

```text
Kafka continua armazenando logs
```

Capacidade com retenção de 7 dias:

```text
84 GB/dia × 7
=
588 GB
```

Com RF=3:

```text
1,76 TB
```

Portanto, o Kafka atua como buffer de proteção.

---

# Arquitetura Recomendada

```text
+------------------+
| Sistemas         |
+------------------+
          |
          v
+------------------+
| Kafka Cluster    |
| 3 Brokers        |
| 12 Partitions    |
| RF=3             |
+------------------+
          |
          v
+------------------+
| Consumer Group   |
| 5 Workers        |
| Quarkus/Kotlin   |
+------------------+
          |
          v
+------------------+
| Elasticsearch    |
| 3 Masters        |
| 3 Data Nodes     |
+------------------+
```

Para a carga informada (**100 TPS × 10 KB = ~84 GB/dia**), um cluster Kafka com **3 brokers (4 vCPU, 16 GB RAM, SSD NVMe)** e um cluster Elasticsearch com **3 nós de dados (8 vCPU, 32 GB RAM)** já suportam a operação com ampla margem de crescimento e alta disponibilidade.
