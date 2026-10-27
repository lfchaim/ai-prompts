# Prompt
Criar a definição de solução para observabilidade, usando Azure Cloud, Opentelemetry, Loki, etc. O foco, neste momento é armazenar logs de sistemas co micro serviços.

# Qwen3.8-Max

# Definição de Solução de Observabilidade para Logs de Microsserviços  
**Azure Cloud + OpenTelemetry + Grafana Loki**  
Foco inicial: **coleta, armazenamento, consulta e governança de logs** de sistemas baseados em microsserviços.

---

## 1. Objetivo da Solução

Definir uma plataforma de observabilidade para centralizar logs de microsserviços executados em Azure, permitindo:

- Coleta padronizada de logs usando **OpenTelemetry**.
- Armazenamento escalável e custo-eficiente com **Grafana Loki**.
- Consulta rápida por serviço, ambiente, erro, `trace_id`, `request_id`, usuário técnico, transação e outros atributos.
- Correlação futura com traces e métricas.
- Operação segura em Azure, com controle de acesso, criptografia e governança.
- Evolução posterior para métricas, traces, alertas e dashboards completos.

---

## 2. Escopo Inicial

### Dentro do escopo

- Logs de aplicação de microsserviços.
- Logs estruturados em JSON.
- Coleta via OpenTelemetry ou captura de stdout/stderr.
- Pipeline de ingestão com OpenTelemetry Collector ou Grafana Alloy.
- Armazenamento em Grafana Loki.
- Retenção e expurgo automático de logs.
- Consulta via Grafana / Azure Managed Grafana.
- Segurança, rede privada, identidade e segregação por ambiente.
- Monitoramento do próprio pipeline de observabilidade.

### Fora do escopo neste primeiro momento

- APM completo.
- Traces distribuídos como destino principal.
- Métricas de aplicação como destino principal.
- Logs de infraestrutura Azure muito amplos, salvo necessidade pontual.
- Analytics de longo prazo para business intelligence.
- Auditoria regulatória específica, se houver requisitos adicionais de compliance.

> A arquitetura deve nascer preparada para incluir traces e métricas depois, sem refazer a instrumentação das aplicações.

---

## 3. Princípios de Arquitetura

1. **OpenTelemetry como padrão de instrumentação**
   - Logs, traces e métricas devem seguir, sempre que possível, o padrão OpenTelemetry.
   - Evitar lock-in com agentes proprietários.

2. **Logs estruturados**
   - Logs devem ser emitidos em JSON.
   - Campos como `service.name`, `environment`, `trace_id`, `severity`, `message` e `request_id` devem ser padronizados.

3. **Correlação entre logs e traces**
   - Sempre que houver contexto de trace, os logs devem conter `trace_id` e `span_id`.
   - Isso permite navegar de um erro no log para o trace correspondente no futuro.

4. **Baixa cardinalidade em labels do Loki**
   - Labels devem ser usados apenas para dimensões de alto nível.
   - Exemplo: `environment`, `cluster`, `namespace`, `service`.
   - Campos de alta cardinalidade, como `user_id`, `pod`, `request_id`, `trace_id`, devem ficar como campos estruturados, não como labels.

5. **Pipeline resiliente**
   - Batch, retry, fila persistente e limites de memória no collector.
   - Proteção contra picos de ingestão e loops de erro.

6. **Segurança by default**
   - TLS em trânsito.
   - Criptografia em repouso.
   - Identidade gerenciada do Azure.
   - Acesso mínimo necessário.
   - Rede privada sempre que possível.

7. **Custo controlado**
   - Retenção definida por ambiente.
   - Descarte ou redução de logs `DEBUG` em produção.
   - Controle de volume por serviço.
   - Evitar duplicação de logs.

---

## 4. Arquitetura de Referência

### Arquitetura recomendada

```text
┌─────────────────────────────────────────────────────────────────────┐
│                          Microsserviços                            │
│                                                                     │
│  App A   App B   App C   App D                                     │
│   │       │       │       │                                        │
│   └───────┴───────┴───────┘                                        │
│           Logs OTLP / JSON estruturado                             │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                   OpenTelemetry Collector                          │
│                     ou Grafana Alloy                               │
│                                                                     │
│  - Recebe OTLP                                                      │
│  - Enriquece com metadata Kubernetes/Azure                          │
│  - Aplica redaction/masking                                         │
│  - Filtra DEBUG                                                     │
│  - Batch/retry/queue                                                │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                          Grafana Loki                              │
│                                                                     │
│  - Ingestão de logs                                                 │
│  - Index/TSDB                                                       │
│  - Retention/compactor                                              │
│  - Query API                                                        │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                       Azure Blob Storage                           │
│                                                                     │
│  - Chunks                                                           │
│  - Index                                                            │
│  - Retention de longo prazo                                         │
└─────────────────────────────────────────────────────────────────────┘

                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                  Grafana / Azure Managed Grafana                   │
│                                                                     │
│  - Consulta de logs                                                 │
│  - Dashboards                                                       │
│  - Alertas futuros                                                  │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 5. Componentes da Solução

| Componente | Função | Recomendação |
|---|---|---|
| **Microsserviços** | Emitir logs estruturados | Usar OpenTelemetry Logs SDK ou logging estruturado JSON |
| **OpenTelemetry SDK** | Instrumentar aplicações | Padrão para correlação e portabilidade |
| **OpenTelemetry Collector** | Receber, processar e encaminhar logs | `otelcol-contrib`, DaemonSet e/ou Deployment |
| **Grafana Alloy** | Alternativa ou complemento ao OTel Collector | Boa opção para integração com Loki e descoberta Kubernetes |
| **Grafana Loki** | Armazenar e consultar logs | Modo distribuído em produção |
| **Azure Blob Storage** | Armazenamento de objetos do Loki | Backend principal de chunks/index |
| **AKS** | Execução do Loki e collectors | Recomendado para produção |
| **Azure Managed Grafana** | Visualização | Opcional, mas recomendado se quiser gestão simplificada |
| **Azure Key Vault** | Segredos | Connection strings, certificados, tokens |
| **Azure Managed Identity / Workload Identity** | Acesso seguro ao Blob Storage | Evitar chaves fixas |
| **Azure Monitor** | Monitorar AKS e infraestrutura | Complementar, não substituto ao Loki neste escopo |
| **Azure Event Hubs** | Buffer opcional para alta escala | Somente se necessário |

---

## 6. Decisão Recomendada de Arquitetura

### Recomendação principal

Usar:

- **OpenTelemetry** como padrão de instrumentação.
- **OpenTelemetry Collector** ou **Grafana Alloy** como pipeline de ingestão.
- **Grafana Loki** como backend de logs.
- **Azure Blob Storage** como armazenamento persistente.
- **AKS** para execução dos componentes de observabilidade.
- **Grafana** ou **Azure Managed Grafana** para consulta e dashboards.

### Justificativa

- Baixo custo relativo comparado a soluções full-index tradicionais.
- Integração natural com Kubernetes e microsserviços.
- Compatibilidade com OpenTelemetry.
- Possibilidade de correlação futura com traces.
- Boa experiência de consulta via LogQL.
- Armazenamento em objeto no Azure, escalável e gerenciado.

---

## 7. Padrões de Instrumentação das Aplicações

### 7.1. Formato de log

Os serviços devem emitir logs estruturados em JSON.

Exemplo:

```json
{
  "timestamp": "2026-10-27T14:35:12.123Z",
  "severity": "ERROR",
  "service.name": "order-service",
  "service.version": "1.8.2",
  "deployment.environment": "production",
  "k8s.namespace.name": "orders",
  "k8s.pod.name": "order-service-7d9f8b6c5-xk2lp",
  "trace_id": "4bf92f3577b34da6a3ce929d0e0e4736",
  "span_id": "00f067aa0ba902b7",
  "request_id": "req-987123",
  "user_id_hash": "hashed-user-123",
  "message": "Falha ao processar pagamento",
  "error.type": "PaymentGatewayTimeout",
  "error.message": "Timeout after 5000ms",
  "http.method": "POST",
  "http.status_code": 504,
  "http.url": "/api/v1/payments"
}
```

### 7.2. Campos obrigatórios mínimos

| Campo | Obrigatório | Descrição |
|---|---:|---|
| `timestamp` | Sim | Data/hora do evento |
| `severity` | Sim | Nível do log: DEBUG, INFO, WARN, ERROR, FATAL |
| `service.name` | Sim | Nome lógico do serviço |
| `deployment.environment` | Sim | dev, qa, staging, prod |
| `message` | Sim | Mensagem principal |
| `trace_id` | Quando houver trace | Correlação com traces |
| `span_id` | Quando houver trace | Correlação com span |
| `request_id` | Recomendado | Correlação de requisição |
| `service.version` | Recomendado | Versão da aplicação |
| `k8s.namespace.name` | Recomendado | Namespace Kubernetes |
| `k8s.pod.name` | Recomendado | Pod de origem |
| `error.type` | Para erros | Tipo/classificação do erro |
| `error.message` | Para erros | Detalhe do erro |

### 7.3. O que não colocar como label no Loki

Evitar labels de alta cardinalidade:

- `user_id`
- `request_id`
- `trace_id`
- `session_id`
- `pod_name`, se houver alta rotatividade e muitos pods
- `customer_id`
- `transaction_id`

Esses campos devem ficar como **campos estruturados**, pesquisáveis via LogQL, mas não como labels de stream.

---

## 8. Estratégia de Coleta de Logs

Existem dois padrões principais.

---

### Opção A — Recomendada: Aplicação envia logs via OTLP

A aplicação usa OpenTelemetry Logs SDK e envia logs via OTLP para o collector.

Fluxo:

```text
App → OTLP → OpenTelemetry Collector → Loki
```

Vantagens:

- Padronização.
- Correlação com traces.
- Enriquecimento consistente.
- Menos dependência de parsing de stdout.
- Preparado para métricas e traces no futuro.

Desvantagens:

- Requer instrumentação nas aplicações.
- Pode exigir ajuste em frameworks de logging existentes.

---

### Opção B — Compatibilidade: Captura de stdout estruturado

Quando a aplicação não consegue enviar OTLP diretamente, ela escreve JSON no stdout e um agente coleta.

Fluxo:

```text
App stdout JSON → Fluent Bit / Promtail / Grafana Alloy / OTel Collector → Loki
```

Vantagens:

- Simples para aplicações legadas.
- Não exige SDK OTel imediato.

Desvantagens:

- Mais parsing.
- Pode perder contexto se o JSON não for padronizado.
- Correlação depende de a aplicação incluir `trace_id` no log.

---

### Recomendação

Adotar **Opção A como padrão alvo**.

Usar **Opção B apenas para transição** ou serviços que não consigam emitir OTLP imediatamente.

Importante: **evitar duplicidade**. Se o serviço envia logs via OTLP e também escreve no stdout, deve haver controle para não ingerir o mesmo evento duas vezes.

---

## 9. Arquitetura do OpenTelemetry Collector

### Topologia recomendada

Para ambientes Kubernetes:

#### Fase inicial

```text
Apps → OTel Collector DaemonSet → Loki
```

O DaemonSet recebe logs dos pods no próprio node.

#### Fase intermediária/avançada

```text
Apps → OTel Collector DaemonSet → OTel Collector Gateway → Loki
```

O gateway centraliza saída, autenticação, rate limiting, multi-tenancy e políticas mais complexas.

### Benefícios do Collector

- Batch de logs.
- Retry com fila persistente.
- Redaction de dados sensíveis.
- Enriquecimento com metadata Kubernetes.
- Filtro de logs desnecessários.
- Roteamento por ambiente ou severidade.
- Proteção contra picos.

---

## 10. Pipeline Lógico do Collector

### Receivers

- `otlp`
  - gRPC: porta 4317
  - HTTP: porta 4318

### Processors recomendados

| Processor | Função |
|---|---|
| `memory_limiter` | Evitar OOM do collector |
| `batch` | Agrupar logs antes do envio |
| `k8sattributes` | Adicionar namespace, pod, labels Kubernetes |
| `resourcedetection` | Detectar cloud, cluster, host |
| `transform` | Normalizar campos |
| `attributes` | Remover/renomear atributos |
| `filter` | Descartar logs indesejados |
| `redaction` | Mascarar dados sensíveis |

### Exporters

Dependendo da versão e estratégia:

- Envio OTLP para Loki, se a versão do Loki suportar ingestão OTLP.
- Envio para Grafana Alloy, que converte e escreve no Loki.
- Uso de exporter suportado para Loki, quando aplicável e validado.

> Recomendação prática: manter **OTLP como contrato de entrada** e escolher a peça de tradução para Loki conforme versão homologada. Isso reduz lock-in e facilita evolução.

---

## 11. Arquitetura do Grafana Loki

### Modos de implantação

#### Não produção

Pode usar modo simples:

```text
Loki single binary
```

Vantagens:

- Simples.
- Menos custo.
- Fácil operação.

Desvantagens:

- Escalabilidade limitada.
- Menos resiliência.

#### Produção

Recomendado usar modo distribuído:

```text
Distributor
Ingester
Query Frontend
Querier
Compactor
Gateway
```

Vantagens:

- Alta disponibilidade.
- Escalabilidade horizontal.
- Retenção mais robusta.
- Melhor desempenho para múltiplos serviços.

---

## 12. Armazenamento no Azure

### Backend recomendado

Usar **Azure Blob Storage** como backend do Loki.

O Loki pode armazenar:

- Chunks.
- Index.
- Artefatos de retenção/compactor.

### Recomendações

- Usar Storage Account dedicada para observabilidade.
- Habilitar criptografia padrão.
- Usar Private Endpoint.
- Evitar expor a storage publicamente.
- Usar Managed Identity/Workload Identity para acesso.
- Avaliar ZRS para maior resiliência em produção.
- Não usar Azure Files como backend principal de chunks do Loki.

### Containers sugeridos

Exemplo conceitual:

```text
loki-storage-account
├── loki-chunks
├── loki-index
├── loki-ruler
└── loki-admin
```

Os nomes exatos dependem do Helm chart e versão do Loki.

---

## 13. Estratégia de Labels no Loki

### Labels recomendados

| Label | Exemplo | Observação |
|---|---|---|
| `environment` | `prod`, `staging`, `dev` | Baixa cardinalidade |
| `cluster` | `aks-prod-eastus2` | Baixa cardinalidade |
| `namespace` | `orders`, `payments` | Média cardinalidade |
| `service` | `order-service` | Média cardinalidade |
| `tenant` | `tenant-a` | Somente se multi-tenant |

### Labels a evitar

| Label | Motivo |
|---|---|
| `user_id` | Alta cardinalidade |
| `request_id` | Alta cardinalidade |
| `trace_id` | Alta cardinalidade |
| `session_id` | Alta cardinalidade |
| `customer_id` | Alta cardinalidade |
| `pod` | Pode ser alta cardinalidade; melhor como campo estruturado |

### Exemplo de stream Loki

```text
{environment="prod", cluster="aks-prod-eastus2", namespace="orders", service="order-service"}
```

### Exemplo de consulta LogQL

Buscar erros do serviço `order-service`:

```logql
{environment="prod", service="order-service"} |= "ERROR"
```

Buscar por `trace_id`:

```logql
{environment="prod", service="order-service"} | json | trace_id="4bf92f3577b34da6a3ce929d0e0e4736"
```

Buscar erros de pagamento:

```logql
{environment="prod", namespace="orders"} | json | severity="ERROR" | error_type="PaymentGatewayTimeout"
```

---

## 14. Modelo de Dados Recomendado

### Resource attributes OpenTelemetry

As aplicações devem configurar resource attributes padronizados:

```text
service.name=order-service
service.version=1.8.2
deployment.environment=production
k8s.namespace.name=orders
k8s.pod.name=order-service-xyz
cloud.provider=azure
cloud.region=eastus2
```

### Convenção de nomenclatura

Usar padrão consistente:

```text
service.name: nome lógico do serviço
deployment.environment: ambiente
component: módulo interno, se necessário
error.type: classificação do erro
request.id: identificador da requisição
trace.id ou trace_id: correlação com trace
```

> É importante padronizar se os campos usarão `.` ou `_`. Exemplo: `trace_id` versus `trace.id`. A recomendação é definir um padrão único por organização e aplicar via processor/transform.

---

## 15. Segurança da Solução

### 15.1. Identidade

- Usar **Azure Managed Identity** para componentes Azure.
- Usar **Workload Identity** para pods no AKS acessarem Azure Blob Storage.
- Evitar chaves de storage em variáveis de ambiente ou ConfigMaps.
- Segredos devem ficar no **Azure Key Vault**.

### 15.2. Rede

- AKS em VNet privada.
- Storage Account com Private Endpoint.
- Loki acessível apenas internamente.
- Grafana com acesso controlado.
- Uso de Network Policies no AKS.
- TLS entre collector e Loki.
- TLS entre aplicações e collector, quando aplicável.

### 15.3. Dados sensíveis

Implementar máscaras para:

- CPF/CNPJ.
- Cartão de crédito.
- Token de autenticação.
- Senha.
- E-mail, se classificado como sensível.
- Telefone.
- Endereços.
- Dados de saúde, se aplicável.

Camadas de proteção:

1. Aplicação não deve logar dado sensível.
2. Collector pode aplicar redaction.
3. Política de acesso restringe quem consulta logs.
4. Retenção limita exposição temporal.

### 15.4. Controle de acesso

- RBAC no AKS.
- RBAC no Grafana.
- Acesso por ambiente.
- Times podem ter acesso apenas aos namespaces/serviços relevantes.
- Auditoria de acesso quando necessário.

---

## 16. Retenção e Ciclo de Vida

### Política recomendada por ambiente

| Ambiente | Retenção sugerida | Observação |
|---|---:|---|
| Dev | 7 dias | Reduz custo |
| QA | 14 dias | Permite testes e validação |
| Staging | 15 a 30 dias | Depende do uso |
| Produção | 30 a 90 dias | Ajustar conforme compliance |
| Auditoria regulatória | Conforme requisito | Pode exigir pipeline separado |

### Retenção no Loki

- Usar compactor para aplicar retenção.
- Configurar regras por tenant, se multi-tenant.
- Validar impacto de retenção curta demais para troubleshooting.
- Avaliar exportação para Blob Archive se houver necessidade de longo prazo.

### Observação importante

Logs de auditoria crítica ou requisitos legais podem exigir:

- Imutabilidade.
- Retenção longa.
- WORM.
- Trilha de auditoria de acesso.
- Pipeline separado.

Nesses casos, não depender apenas do Loki sem análise específica.

---

## 17. Resiliência e Alta Disponibilidade

### Collector

- Múltiplas réplicas quando usar gateway.
- Resource requests/limits definidos.
- `memory_limiter` habilitado.
- Fila persistente para retries.
- Backpressure configurado.
- Health checks.
- Métricas de auto-monitoramento.

### Loki

- Réplicas de ingester, se necessário.
- Anti-affinity entre componentes críticos.
- Storage com redundância adequada.
- Query frontend para melhorar desempenho.
- Compactor ativo para retenção.
- Limites de ingestão e query.

### Aplicações

- Exporter OTel assíncrono.
- Batch de logs.
- Retry limitado.
- Não bloquear thread principal da aplicação.
- Descarte controlado em caso de fila cheia, se necessário.

---

## 18. Buffer com Azure Event Hubs — Quando usar

O uso de **Azure Event Hubs** pode ser considerado se houver:

- Picos muito grandes de ingestão.
- Necessidade de replay de logs.
- Múltiplos clusters enviando para o mesmo backend.
- Necessidade de desacoplar produtores do Loki.
- Janelas de indisponibilidade do Loki que não podem causar perda.

Arquitetura opcional:

```text
Apps / Collectors → Azure Event Hubs → Collector Consumer → Loki
```

Porém, para o primeiro momento, recomenda-se começar sem Event Hubs, usando:

- Batch.
- Retry.
- Persistent queue.
- Monitoramento de fila.

Somente adicionar Event Hubs se houver requisito claro de escala ou resiliência.

---

## 19. Monitoramento da Própria Plataforma de Logs

A plataforma de observabilidade também precisa ser observada.

### Métricas mínimas

| Métrica | Objetivo |
|---|---|
| Logs recebidos por serviço | Detectar aumento anormal |
| Logs exportados para Loki | Validar ingestão |
| Erros de exportação | Detectar falha no pipeline |
| Tamanho da fila do collector | Detectar atraso |
| Latência de ingestão | Avaliar saúde do Loki |
| Uso de memória do collector | Evitar OOM |
| Uso de CPU do collector | Dimensionamento |
| Erros no Loki | Saúde do backend |
| Queries lentas | Experiência do usuário |
| Espaço consumido no Blob | Controle de custo |

### Alertas recomendados

- Collector com alta taxa de erro.
- Fila de exportação crescente.
- Loki sem ingerir logs por X minutos.
- Aumento abrupto de volume de logs de erro.
- Estouro de memória no collector.
- Falha de autenticação no Blob Storage.
- Query latency acima do esperado.
- Retenção não aplicada.

---

## 20. Visualização e Consulta

### Ferramenta recomendada

- **Grafana** self-hosted no AKS, ou
- **Azure Managed Grafana**, se quiser reduzir operação.

### Dashboards iniciais

1. **Visão geral de logs por ambiente**
   - Volume por serviço.
   - Erros por serviço.
   - Logs por namespace.

2. **Dashboard de serviço**
   - Logs recentes.
   - Erros agrupados por `error.type`.
   - Top serviços com erro.
   - Logs por `request_id`.

3. **Dashboard de pipeline**
   - Volume ingerido.
   - Erros de collector.
   - Fila de exportação.
   - Saúde do Loki.

4. **Dashboard de capacidade**
   - Consumo de storage.
   - Crescimento diário.
   - Retenção por ambiente.

---

## 21. Estratégia para Ambientes

### Recomendação

Criar ambientes isolados:

```text
observability-dev
observability-qa
observability-staging
observability-prod
```

Ou, se houver custo limitado:

```text
observability-nonprod
observability-prod
```

### Separação mínima

- Resource Groups separados.
- AKS separado para produção.
- Storage Accounts separadas.
- Loki separado por produção e não produção.
- Grafana com permissões por ambiente.

---

## 22. Implantação com Infraestrutura como Código

### Ferramentas recomendadas

- **Terraform** ou **Bicep** para recursos Azure.
- **Helm** para Loki, Grafana, OTel Collector.
- **GitOps** com Argo CD ou Flux, se já existir no padrão.

### Recursos Azure a provisionar

- Resource Group.
- VNet.
- AKS.
- Azure Storage Account.
- Private Endpoint.
- Key Vault.
- Managed Identity.
- Azure Managed Grafana, opcional.
- Azure Monitor workspace, se necessário.
- Log Analytics para diagnóstico do AKS, opcional.

### Recursos Kubernetes

- Namespace `observability`.
- Helm release do Loki.
- Helm release do Grafana.
- Helm release do OpenTelemetry Collector.
- ConfigMaps/Secrets.
- Network Policies.
- ServiceAccounts com Workload Identity.

---

## 23. Exemplo de Pipeline de Implantação

### Fase 0 — Descoberta

- Listar microsserviços.
- Definir ambientes.
- Estimar volume de logs.
- Definir retenção.
- Identificar dados sensíveis.
- Definir requisitos de compliance.

### Fase 1 — Fundação Azure

- Criar Resource Group.
- Criar VNet.
- Criar AKS.
- Criar Storage Account.
- Configurar Private Endpoint.
- Configurar Managed Identity.
- Configurar Key Vault.

### Fase 2 — Backend de Logs

- Instalar Loki em modo single binary para não produção.
- Validar armazenamento no Azure Blob.
- Validar retenção.
- Validar queries básicas.

### Fase 3 — Coleta

- Instalar OpenTelemetry Collector.
- Configurar receivers OTLP.
- Configurar processors.
- Configurar exporter para Loki.
- Validar ingestão.

### Fase 4 — Onboarding de Serviços

- Instrumentar serviço piloto.
- Padronizar JSON.
- Validar `trace_id`.
- Validar labels.
- Validar volume.

### Fase 5 — Visualização

- Conectar Grafana ao Loki.
- Criar dashboards.
- Criar alertas básicos.
- Treinar times.

### Fase 6 — Produção

- Implantar Loki distribuído.
- Aplicar HA.
- Aplicar segurança.
- Validar carga.
- Documentar runbooks.

---

## 24. Estimativa de Capacidade

A capacidade depende de:

- Número de serviços.
- Requests por segundo.
- Quantidade de logs por request.
- Tamanho médio de cada log.
- Retenção.
- Compressão.
- Replicação.
- Indexação.

Fórmula simples:

```text
Volume diário = requests/dia × logs_por_request × tamanho_médio_log
```

Exemplo conceitual:

```text
10.000.000 requests/dia
5 logs por request
2 KB por log

10.000.000 × 5 × 2 KB = 100 GB/dia
```

Com retenção de 30 dias:

```text
100 GB × 30 = 3 TB brutos
```

Adicionar margem para:

- Index.
- Metadados.
- Reprocessamento.
- Replicação.
- Crescimento.

Recomendação: planejar com 30% a 50% de margem inicial.

---

## 25. Controle de Custos

Estratégias principais:

1. Não ingerir `DEBUG` em produção por padrão.
2. Descartar logs redundantes ou health checks excessivos.
3. Evitar labels de alta cardinalidade.
4. Definir retenção por ambiente.
5. Monitorar volume por serviço.
6. Aplicar rate limit no collector ou Loki.
7. Alertar crescimento anormal de volume.
8. Revisar periodicamente serviços com maior custo.

Exemplo de filtro no collector para reduzir DEBUG em produção:

```yaml
processors:
  filter/drop_debug_prod:
    logs:
      log_record:
        - 'severity_text == "DEBUG" and resource_attributes["deployment.environment"] == "production"'
```

> A sintaxe exata pode variar conforme versão do Collector. Validar na documentação da versão usada.

---

## 26. Governança de Logs

### Política recomendada

Cada serviço deve:

- Definir dono técnico.
- Classificar dados presentes nos logs.
- Declarar se contém PII.
- Definir nível padrão de log por ambiente.
- Definir eventos críticos que devem ser logados.
- Garantir correlação por `request_id` e `trace_id`.
- Evitar logar payloads completos sem necessidade.
- Evitar logar secrets ou tokens.

### Níveis de log recomendados

| Nível | Uso recomendado |
|---|---|
| ERROR | Falhas que afetam operação ou request |
| WARN | Situações anormais recuperáveis |
| INFO | Eventos de negócio relevantes |
| DEBUG | Detalhes técnicos para diagnóstico |
| TRACE | Muito verboso, apenas troubleshooting local |

---

## 27. Multi-Tenancy

Se vários times ou produtos usarem a mesma plataforma, avaliar multi-tenancy no Loki.

Opções:

1. **Tenant por ambiente**
   - Exemplo: `dev`, `prod`.

2. **Tenant por negócio**
   - Exemplo: `payments`, `billing`, `customers`.

3. **Tenant por produto**
   - Exemplo: `product-a`, `product-b`.

Controles necessários:

- Header de tenant no Loki.
- RBAC no Grafana.
- Limites de ingestão por tenant.
- Retenção por tenant.
- Dashboards segregados.

---

## 28. Estratégia para Traces e Métricas no Futuro

Embora o foco atual seja logs, a arquitetura deve permitir evolução.

### Traces

- Manter OpenTelemetry SDK.
- Exportar traces OTLP.
- Backend futuro:
  - Grafana Tempo.
  - Azure Application Insights, se necessário.
  - Jaeger.
  - Outro backend OTLP compatível.

### Métricas

- Exportar métricas OTLP.
- Backend futuro:
  - Prometheus.
  - Azure Managed Prometheus.
  - Grafana Mimir.
  - Azure Monitor, se necessário.

### Correlação

Com logs, traces e métricas:

- Log contém `trace_id`.
- Trace contém `service.name`.
- Métricas usam mesmos resource attributes.
- Grafana conecta as três camadas.

---

## 29. Alternativas Avaliadas

### Alternativa 1 — Azure Monitor Logs / Log Analytics

Prós:

- Nativo Azure.
- Menor operação.
- Integração com Azure Monitor.
- Bom para compliance em alguns cenários.

Contras:

- Custo pode crescer rapidamente.
- Menos aderência ao ecossistema Grafana/Loki.
- Query language diferente.
- Pode não ser ideal se o objetivo é unificar com Loki e Grafana.

Quando considerar:

- Forte requisito de operação totalmente gerenciada.
- Necessidade de integração nativa com Azure.
- Baixa maturidade operacional para operar Loki.

---

### Alternativa 2 — Event Hubs + Azure Data Explorer

Prós:

- Alta escala.
- Analytics avançado.
- Boa retenção e query em grandes volumes.

Contras:

- Mais complexo.
- Custo maior.
- Mais componentes para operar.

Quando considerar:

- Volumes muito altos.
- Necessidade analítica além de troubleshooting.
- Necessidade de replay e processamento complexo.

---

### Alternativa 3 — Grafana Cloud

Prós:

- Operação simplificada.
- Loki gerenciado.
- Integração nativa com Grafana.

Contras:

- Dados fora do Azure, dependendo do requisito.
- Custo por ingestão.
- Necessidade de avaliar compliance e residência de dados.

Quando considerar:

- Time pequeno.
- Baixa disposição para operar Loki.
- Aceitação de SaaS externo.

---

## 30. ADRs — Decisões de Arquitetura

### ADR-001 — Usar OpenTelemetry como padrão de instrumentação

**Status:** Recomendado.

**Decisão:**  
As aplicações devem usar OpenTelemetry para emissão de logs e, futuramente, traces e métricas.

**Motivo:**  
Padrão aberto, portabilidade, correlação e redução de lock-in.

---

### ADR-002 — Usar Grafana Loki como backend de logs

**Status:** Recomendado.

**Decisão:**  
Logs serão armazenados e consultados via Loki.

**Motivo:**  
Custo, integração com Kubernetes, Grafana, OpenTelemetry e foco em logs com labels/index leve.

---

### ADR-003 — Usar Azure Blob Storage como storage do Loki

**Status:** Recomendado.

**Decisão:**  
O backend persistente do Loki será Azure Blob Storage.

**Motivo:**  
Escalabilidade, gerenciamento, integração com Azure e custo.

---

### ADR-004 — Usar AKS para executar componentes de observabilidade

**Status:** Recomendado.

**Decisão:**  
Loki, collector e eventualmente Grafana self-hosted serão executados em AKS.

**Motivo:**  
Ambiente natural para microsserviços, flexibilidade e integração com Kubernetes.

---

### ADR-005 — Labels de baixa cardinalidade

**Status:** Recomendado.

**Decisão:**  
Usar labels apenas para ambiente, cluster, namespace e serviço.

**Motivo:**  
Evitar explosão de streams, custo e problemas de performance.

---

### ADR-006 — Não usar Event Hubs inicialmente

**Status:** Recomendado, salvo requisitos específicos.

**Decisão:**  
Não introduzir Event Hubs na primeira fase.

**Motivo:**  
Reduz complexidade. O collector com batch, retry e fila persistente deve atender a primeira versão.

---

## 31. Critérios de Aceite da Solução

A solução pode ser considerada pronta para MVP quando:

- [ ] Serviços piloto enviam logs estruturados.
- [ ] Logs aparecem no Loki em até poucos segundos/minutos.
- [ ] É possível filtrar por `environment`, `service`, `namespace`.
- [ ] É possível buscar por `trace_id`.
- [ ] É possível buscar por `request_id`.
- [ ] Logs de erro são facilmente identificáveis.
- [ ] Retenção está configurada.
- [ ] Storage está criptografado.
- [ ] Acesso à storage usa identidade gerenciada.
- [ ] Não há endpoints públicos desnecessários.
- [ ] Dashboards básicos estão disponíveis.
- [ ] Alertas de falha de ingestão estão configurados.
- [ ] O pipeline suporta reinício de pods sem perda significativa.
- [ ] Há documentação de onboarding para novos serviços.
- [ ] Há runbook para incidentes de ingestão.
- [ ] Volume e custo estão sendo monitorados.

---

## 32. Riscos e Mitigações

| Risco | Impacto | Mitigação |
|---|---|---|
| Alto volume de logs | Custo elevado | Filtros, retenção, controle de DEBUG |
| Labels de alta cardinalidade | Performance ruim no Loki | Governança de labels |
| Dados sensíveis em logs | Risco de compliance | Redaction, políticas, revisão |
| Falha no collector | Perda ou atraso de logs | Réplicas, fila persistente, alertas |
| Falha no Loki | Indisponibilidade de consulta | HA, storage resiliente, monitoramento |
| Duplicidade de logs | Custo e ruído | Escolher uma estratégia por serviço |
| Falta de padronização | Queries ruins | Template de log, validação no onboarding |
| Crescimento descontrolado | Estouro de storage | Alertas de volume e retenção |
| Complexidade operacional | Incidentes | IaC, runbooks, automação |

---

## 33. Recomendação Final

A arquitetura recomendada para o momento é:

```text
Microsserviços
  → OpenTelemetry SDK / logs estruturados
  → OpenTelemetry Collector ou Grafana Alloy
  → Grafana Loki
  → Azure Blob Storage
  → Grafana / Azure Managed Grafana
```

Complementado por:

- AKS para execução.
- Azure Managed Identity/Workload Identity.
- Azure Key Vault.
- Private Endpoints.
- Monitoramento do próprio pipeline.
- Políticas de retenção e governança.

Essa solução atende o foco atual em logs, mas deixa caminho aberto para evoluir para traces, métricas, alertas e correlação completa usando OpenTelemetry e Grafana.

---

## 34. Próximos Passos Sugeridos

1. Definir ambientes e subscrições Azure.
2. Estimar volume de logs por serviço.
3. Definir política de retenção.
4. Escolher entre OpenTelemetry Collector puro ou Grafana Alloy.
5. Provisionar AKS, Storage Account e Key Vault.
6. Instalar Loki em modo não produtivo.
7. Instrumentar um serviço piloto.
8. Validar ingestão, busca e correlação.
9. Criar dashboards iniciais.
10. Preparar rollout para produção com Loki distribuído.
