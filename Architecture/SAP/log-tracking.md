# Log and Tracking - SAP - Informatica - Legacy
Evoluir e definir estratégia de Logging e Tracking para integração entre SAP, Informatica e Legado.

# Prompt
Estabeleça uma estratégia especialista de um Software Engineer para atender Observabilidade e Tracking para uma tríplice integração entre:  
1. Sistema Legado - Aplicações Web .Net e Java, On Premise e Cloud  
2. Informatica - Integrador middleware  
3. SAP - ERP conhecido mundialmente  

Avaliar primeiramente, o que cada integração permite, dentro da ferramenta, por exemplo SAP e Informatica possuem maneiras de implementar Log.  
Avaliar soluções de observabilidade e tracking externas, como Datadog, Zabbix, Prometheus, ELK, etc. Ordernar pelas soluções que possuem melhor custo-benefício.  
Adotar identificador único, para cada request, por exemplo Correlation ID.  

# MyHUB (Alibaba Qwen 3 Max)
Luis Fernando, excelente desafio! Para uma arquitetura híbrida com **Sistema Legado (.NET/Java)**, **Informatica (middleware)** e **SAP ERP**, a observabilidade precisa ser **padronizada, centralizada** e baseada em **correlation ID único**. Vamos estruturar isso em 3 camadas:

---

## 🔍 **1. Capacidades Nativas de Cada Sistema**

### **A. SAP ERP**
- **Logging nativo**: Transações como `SM21` (system logs), `ST22` (ABAP dumps), `SLG1` (application logs).
- **RFC/IDoc tracking**: Use `WE02/WE09` para rastrear IDocs; RFCs podem ser monitorados via `SM58`.
- **Limitação**: Logs são siloed, sem correlation ID cross-system por padrão.
- **Solução**: Implemente **enhancements ABAP** ou use **SAP Cloud ALM** / **Solution Manager** para centralizar.

### **B. Informatica PowerCenter / Cloud Data Integration**
- **Logging nativo**: 
  - Session logs detalhados (linhas processadas, erros, performance).
  - Workflow logs com timestamps e status.
- **Custom logging**: Pode injetar variáveis de contexto (ex: correlation ID) via **mapping variables** ou **pre/post-session tasks**.
- **API de monitoramento**: REST APIs para health checks e métricas.

### **C. Sistemas Legados (.NET/Java)**
- **.NET**: Use `ILogger` + **Serilog** com suporte a correlation ID (`Activity.Current?.Id` ou `HttpContext.TraceIdentifier`).
- **Java**: **SLF4J + MDC** (Mapped Diagnostic Context) para propagar correlation ID.
- **On-prem vs Cloud**: Em cloud (Azure/AWS), aproveite **Application Insights** ou **CloudWatch** como sinks intermediários.

---

## 🧩 **2. Estratégia de Correlation ID Único**

Implemente um **ID de correlação global** que flua por todos os sistemas:

```text
[Client] → X-Correlation-ID: abc123 → [.NET/Java] → [Informatica] → [SAP]
```

- **Geração**: Na entrada do sistema legado (primeiro ponto de contato).
- **Propagação**:
  - **HTTP**: Header `X-Correlation-ID`
  - **MQ/JMS**: Propriedade da mensagem
  - **IDoc/RFC no SAP**: Campo customizado no segmento de dados (ex: `ZCORR_ID`)
  - **Informatica**: Variável de sessão mapeada para coluna ou propriedade

> ✅ **Dica crítica**: Valide se o SAP permite campos customizados nos objetos de integração (IDoc, BAPI). Se não, use tabelas Z temporárias associadas ao GUID da transação.

---

## 📊 **3. Soluções Externas de Observabilidade (Custo-Benefício)**

| Ferramenta       | Pontos Fortes                                                                 | Integração com SAP/Informatica                     | Custo-Benefício |
|------------------|-------------------------------------------------------------------------------|----------------------------------------------------|-----------------|
| **Datadog**      | APM completo, tracing distribuído, dashboards ricos, suporte a .NET/Java/SAP* | Agentes + custom integrations (logs via file/API) | ⭐⭐⭐⭐☆ (Alto custo, mas alto valor) |
| **ELK Stack**    | Gratuito (open-source), flexível para parsing de logs legados                | Filebeat para coletar logs de todos os sistemas    | ⭐⭐⭐⭐⭐ (Melhor ROI para orçamentos limitados) |
| **Prometheus + Grafana** | Métricas em tempo real, ideal para health checks                          | Exporters custom para SAP/Informatica              | ⭐⭐⭐☆☆ (Forte em métricas, fraco em tracing) |
| **Zabbix**       | Monitoramento de infraestrutura (CPU, memória, serviços)                     | Templates para SAP, mas fraco em application logs  | ⭐⭐☆☆☆ (Desatualizado para observabilidade moderna) |

> **\* SAP no Datadog**: Requer configuração manual (ex: coletar logs do SO onde roda o SAP ou via SAP Control API).

### 🏆 **Recomendação por Cenário**:
- **Orçamento limitado + time técnico forte**: **ELK Stack** (Elasticsearch, Logstash, Kibana) + **Jaeger** para tracing.
- **Orçamento alto + time enxuto**: **Datadog** (menos esforço de integração, mais insights prontos).
- **Híbrido**: Use **Prometheus/Grafana** para métricas de infra + **ELK** para logs.

---

## 🛠️ **4. Arquitetura Proposta (Passo a Passo)**

1. **Padronize o Correlation ID**:
   - Gere no sistema legado (ex: GUID v4).
   - Injete em todos os pontos de saída (HTTP headers, mensagens, chamadas RFC).

2. **Coleta de Logs**:
   - **Sistemas Legados**: Envie logs estruturados (JSON) com `correlation_id` para ELK/Datadog.
   - **Informatica**: Redirecione session logs para arquivo parseável + envie via Filebeat.
   - **SAP**: Extraia logs do SO (ex: `/usr/sap/*`) ou use **SAP Operational Process Intelligence** para exportar.

3. **Centralização**:
   - **ELK**: Use Logstash com grok patterns para cada fonte.
   - **Datadog**: Configure agentes em servidores on-prem e use AWS/Azure integrations para cloud.

4. **Dashboards**:
   - Crie views por `correlation_id` que cruzem logs de todos os sistemas.
   - Alertas para falhas em qualquer etapa da cadeia (ex: IDoc não processado no SAP em 5 min).

---

## ⚠️ **Pitfalls Comuns**
- **SAP não aceita correlation ID nativamente**: Solução = tabela Z com mapeamento `GUID_SAP ↔ Correlation_ID`.
- **Informatica sem acesso root**: Use scripts pós-sessão para enviar logs via HTTP.
- **Latência na nuvem**: Garanta que o agente de coleta (ex: Filebeat) tenha rede estável para o endpoint de logs.

Precisa de ajuda para desenhar os grok patterns do Logstash ou scripts de injeção de correlation ID no ABAP? É só pedir!

# Enhanced Prompt
Atue como um Software Engineer especialista em observabilidade, integração de sistemas e arquitetura corporativa.

Estabeleça uma estratégia especialista para atender observabilidade e tracking em uma tríplice integração entre:

1. Sistema Legado — aplicações Web em .Net e Java, em ambientes On Premise e Cloud
2. Informatica — integrador middleware
3. SAP — ERP amplamente utilizado mundialmente

Objetivos:
- Avaliar, primeiramente, o que cada integração permite nativamente dentro da própria ferramenta.
- Considerar, por exemplo, as capacidades de log do SAP e do Informatica.
- Avaliar soluções externas de observabilidade e tracking, como Datadog, Zabbix, Prometheus, ELK, entre outras.
- Ordenar as soluções pela melhor relação custo-benefício.
- Propor a adoção de um identificador único para cada request, por exemplo, um Correlation ID, garantindo rastreabilidade ponta a ponta.

Estruture sua پاسخ da seguinte forma:
1. Diagnóstico inicial de cada plataforma
2. Capacidades nativas de logs, métricas e tracing
3. Limitações e riscos de observabilidade por plataforma
4. Comparativo das ferramentas externas
5. Ranking das soluções por custo-benefício
6. Estratégia recomendada de implementação
7. Padrão de Correlation ID e fluxo de propagação
8. Boas práticas de monitoração, auditoria e troubleshooting
9. Conclusão com recomendação prática

Requisitos:
- Seja técnico, objetivo e prático
- Considere cenários On Premise e Cloud
- Leve em conta complexidade de integração, escalabilidade, manutenção e custo
- Diferencie claramente logging, metrics e tracing
- Se houver lacunas de informação, explicite as premissas adotadas
- Priorize uma abordagem que maximize rastreabilidade e reduza custo operacional

Se possível, apresente também uma tabela comparativa com:
- Ferramenta/Solução
- Tipo de observabilidade suportado
- Facilidade de implementação
- Custo estimado
- Escalabilidade
- Adequação ao cenário descrito

# Adapta ONE
Esta é uma análise técnica de arquitetura para estabelecer uma estratégia de observabilidade e rastreabilidade (tracking) ponta a ponta na integração entre sistemas Legados (.NET/Java), Informatica e SAP.

---

## 1. Diagnóstico Inicial das Plataformas

O cenário apresenta uma arquitetura híbrida clássica de alta complexidade. O desafio reside na heterogeneidade dos protocolos (HTTP/REST, SOAP, RFC, IDoc) e na natureza distinta de cada "salto" da integração.

*   **Legado (.NET/Java):** Alta flexibilidade. Permite implementação de bibliotecas modernas de telemetria.
*   **Informatica (PowerCenter ou IICS):** Atua como o "buraco negro" da observabilidade se não for bem configurado. Foca em processamento de dados, não em tracing de mensagens individuais.
*   **SAP:** Ambiente proprietário e robusto. O rastreio depende de transações específicas e, muitas vezes, de logs assíncronos.

## 2. Capacidades Nativas de Logs, Métricas e Tracing

### Sistema Legado
*   **Logs:** Uso de frameworks como Serilog (.NET) ou Logback (Java) para logs estruturados.
*   **Tracing:** Suporte nativo ou via SDKs para OpenTelemetry (OTel).

### Informatica
*   **Nativo:** Session Logs, Workflow Logs e Metadata Manager.
*   **IICS (Cloud):** APIs de monitoramento e integração com CloudWatch/Azure Monitor.
*   **PowerCenter (On-Prem):** Repositories em banco de dados SQL que podem ser consultados para extrair status de execução.

### SAP
*   **Logs Técnicos:** Transações `ST22` (Dumps), `SM21` (System Log), `SLG1` (Application Log).
*   **Interface Tracking:** `WE02`/`WE05` (IDocs), `SRT_MONI` (Web Services), `SM59` (RFC).
*   **Soluções SAP:** SAP Solution Manager (SolMan) e SAP Focused Run.

## 3. Limitações e Riscos de Observabilidade

*   **Quebra de Contexto:** O Informatica frequentemente consome uma mensagem e gera outra, perdendo o cabeçalho original se não houver mapeamento explícito.
*   **Latência de Escrita:** Logs excessivos no SAP podem impactar a performance do banco de dados HANA.
*   **Silos de Dados:** Cada ferramenta possui seu próprio visualizador de log, dificultando o troubleshooting em incidentes que cruzam as três camadas.

## 4. Comparativo de Ferramentas Externas

O foco aqui é centralizar os três pilares: **Logging, Metrics e Tracing**.

|Ferramenta	|Tipo de Observabilidade	|Implementação	|Custo Estimado	|Escalabilidade	|Adequação ao Cenário|
|----|----|-----|-----|-----|-----|
|Datadog |Full Stack (Logs, Metrics, APM)	|Média (Possui conector SAP)	|Alto	|Excelente	|Ideal para missão crítica com SAP.|
|ELK Stack |Logging e Search	|Alta (Complexo On-Prem)	|Médio (Infra)	|Alta	|Ótimo para análise de logs brutos.|
|Grafana LGTM |Metrics, Logs, Tracing	|Média (Open Source/Cloud)	|Baixo/Médio	|Alta	|Melhor flexibilidade com OpenTelemetry.|
|Zabbix |Infra e Métricas	|Baixa	|Baixo	|Média	|Limitado para tracing e logs de app.|
|Azure Monitor|Cloud Native	|Baixa (se em Azure)	|Variável	|Alta	|Excelente para .NET/Java em Cloud.|

## 5. Ranking das Soluções por Custo-Benefício

1.  **Grafana LGTM Stack (Loki, Grafana, Tempo, Mimir):** **Vencedor.** Oferece a melhor flexibilidade para ambientes híbridos e suporte nativo a OpenTelemetry, permitindo unificar logs do Informatica e métricas do SAP em um único dashboard com custo de licenciamento reduzido.
2.  **Datadog:** A melhor tecnicamente, especialmente pelo agente específico para SAP (ABAP metrics), mas o custo por host e volume de logs pode ser proibitivo em integrações de larga escala.
3.  **ELK Stack (Elasticsearch, Logstash, Kibana):** Excelente para análise de logs, mas exige alta manutenção de infraestrutura (especialmente On-Premise) e falha em métricas de performance comparado às anteriores.

---

## 6. Estratégia Recomendada de Implementação

A adoção do padrão **OpenTelemetry (OTel)** é a recomendação central. Ele permite que as aplicações enviem dados para um *Collector* central, que então distribui para a ferramenta de backend escolhida (Grafana, Datadog, etc.).

*   **Camada Legada:** Implementar Auto-instrumentação OTel para capturar traces de entrada e saída.
*   **Camada Informatica:** Configurar o Informatica para gravar o `Correlation ID` em tabelas de auditoria ou enviar via API para o Collector ao final de cada execução de workflow.
*   **Camada SAP:** Utilizar o SDK de telemetria para ABAP (se disponível) ou configurar a leitura de logs via RFC/IDoc Status para alimentar o dashboard de observabilidade.

---

## 7. Padrão de Correlation ID e Fluxo de Propagação

Para garantir a rastreabilidade ponta a ponta, deve-se adotar um identificador único universal (UUID).

### Padrão de Propagação:
1.  **Origem (Legado):** Se a requisição nasce aqui, o sistema gera um `X-Correlation-ID`.
2.  **Middleware (Informatica):**
    *   O Informatica deve receber este ID no cabeçalho (se REST) ou no payload (se Arquivo/DB).
    *   **Regra de Ouro:** O ID deve ser mapeado como um campo de metadado em cada transformação.
3.  **Destino (SAP):**
    *   O ID deve ser persistido em um campo customizado (Z-field) nas tabelas do SAP ou incluído no campo de referência do cabeçalho do IDoc/Documento Contábil.

---

## 8. Boas Práticas de Monitoração e Troubleshooting

*   **Logs Estruturados:** Todos os logs devem estar em formato JSON para facilitar o parsing automático.
*   **Health Checks Ativos:** Implementar endpoints de `/health` no Legado e monitorar os serviços do Informatica e instâncias SAP via *Synthetic Monitoring*.
*   **Alertas Inteligentes:** Configurar alertas baseados em desvios de latência (ex: Informatica levando 50% a mais de tempo que a média) em vez de apenas alertas de "erro/sucesso".
*   **Auditoria vs Log:** Diferencie logs de depuração (técnicos) de logs de auditoria (negócio). O `Correlation ID` deve estar em ambos.

---

## 9. Conclusão e Recomendação Prática

Para este cenário de tríplice integração, a recomendação é a **implementação de uma malha de observabilidade baseada em OpenTelemetry com visualização no Grafana**.

**Passos imediatos:**
1.  Padronizar o cabeçalho `X-Correlation-ID` em todos os contratos de interface.
2.  Configurar um **OTel Collector** centralizado para receber dados tanto do ambiente On-Prem quanto Cloud.
3.  Instrumentar o Informatica para que ele não apenas processe dados, mas também "noticie" o início e fim de cada tracking ID para o Collector.

Essa abordagem maximiza a rastreabilidade, reduz o tempo médio de reparo (MTTR) em caso de falhas e evita o *vendor lock-in*, mantendo o custo operacional sob controle.

# Claude - Sonnet 4.6
Aqui está a estratégia completa. Vou estruturar em seções com um diagrama de arquitetura de observabilidade e a tabela comparativa.

---

## Estratégia de Observabilidade e Tracking: Sistema Legado ↔ Informatica ↔ SAP

**Premissas adotadas:**
- Integração bidirecional entre os três sistemas
- Volumes transacionais de médio a alto porte (1k–100k eventos/dia)
- Ambiente híbrido: On Premise (mainframe/datacenter) + Cloud (AWS/Azure)
- Equipe técnica com capacidade de operar ferramentas DevOps/SRE
- Orçamento moderado — solução open-source como base, com SaaS pontual onde necessário

---

## 1. Diagnóstico inicial por plataforma

**Sistema Legado (.NET / Java — On Premise + Cloud)**
Aplicações heterogêneas, sem padrão unificado de log. Frameworks distintos (Log4j, Serilog, NLog), geralmente sem correlation ID propagado entre chamadas. Deployments On Premise tendem a usar file-based logging; aplicações Cloud podem ter integração com CloudWatch, Application Insights ou similares, mas raramente com uma estratégia coesa.

**Informatica (Middleware/iPaaS)**
Plataforma de integração com capacidade de orquestrar fluxos entre sistemas. Possui logging nativo robusto em nível de sessão e workflow, mas a granularidade de tracing a nível de mensagem individual é limitada fora do Informatica Monitor. Em ambientes On Premise (PowerCenter), o acesso aos logs é via sistema de arquivos; em Cloud (IICS), há interface web com retenção curta.

**SAP (ERP — geralmente On Premise ou SAP BTP)**
Sistema com capacidade nativa de auditoria robusta via SM20/SM21 (system log), ST05 (trace SQL), SLG1 (application log), SXMB_MONI (PI/PO — mensageria), e SAP Solution Manager para monitoração end-to-end. O desafio é a opacidade: esses logs vivem em tabelas próprias (BALHDR, BALDAT), dificultando a correlação com sistemas externos sem uma camada de extração/exportação.

---

## 2. Capacidades nativas por plataforma

| Capacidade | Sistema Legado | Informatica | SAP |
|---|---|---|---|
| **Logging** | Framework-level (Log4j/Serilog) — file ou stdout | Session/Workflow logs, Monitoring Console | SLG1, SM21, SM20, SXMB_MONI |
| **Métricas** | APM via agent (App Insights, Micrometer) | Informatica Monitor (throughput, latência) | SAP Focused Run, Solution Manager |
| **Tracing** | Manual ou via agent (OpenTelemetry) | Não nativo; depende de custom mapping | Praticamente inexistente sem integração externa |
| **Alertas** | Depende do APM/infra | E-mail/SNMP via Workflow Monitor | CCMS, Solution Manager alertas |
| **Correlation ID** | Não padrão — implementação manual | Não propagado automaticamente | Não suportado nativamente cross-system |

**Gap crítico:** Nenhuma das três plataformas propaga nativamente um identificador de correlação cross-system. Isso exige uma estratégia deliberada de implementação.

---

## 3. Limitações e riscos por plataforma

**Sistema Legado**
- Heterogeneidade de frameworks dificulta padronização de log structure
- Aplicações On Premise sem agente podem perder eventos em caso de falha de disco
- Risco de log verbosity excessiva sem política de retenção e sampling

**Informatica**
- Logs de sessão são deletados automaticamente após período configurável — risco de perda de rastreabilidade histórica
- IICS (Cloud) tem retenção de log limitada na camada free/standard
- Não há mecanismo nativo de injeção de headers customizados entre sistemas de origem/destino

**SAP**
- Logs nativos são estruturados em formato proprietário (tabelas ABAP) — exportação requer RFC/BAPI ou extração via SAP Cloud Connector / RFC destinations
- SLG1 não é tempo real para observabilidade operacional — é auditoria post-hoc
- SXMB_MONI cobre apenas mensageria PI/PO; integrações RFC/BAPI diretas não aparecem ali

---

## 4 & 5. Comparativo e Ranking por Custo-Benefício---

## 6. Estratégia recomendada de implementação

A arquitetura proposta é em camadas, combinando open-source como backbone e integrações nativas como fontes de eventos:**Roadmap de implementação em 3 fases:**

**Fase 1 — Fundação (semanas 1–6)**
Implantar ELK Stack centralizado. Configurar Filebeat/Logstash para coletar logs do Sistema Legado e do Informatica. Criar pipeline de extração de SLG1/SM21 via report ABAP agendado exportando para Kafka ou diretamente para Logstash. Definir e implementar o padrão de Correlation ID (veja seção 7). Criar índices Elasticsearch separados por sistema com política de retenção (ex.: 30 dias hot, 90 dias warm).

**Fase 2 — Métricas e Alertas (semanas 7–12)**
Implantar Prometheus com exporters para JVM (micrometer), .NET (dotnet-monitor), e infra (node_exporter). Configurar Grafana conectado a Elasticsearch e Prometheus. Implementar Alertmanager com thresholds de SLA. Criar dashboards por sistema e por fluxo de integração.

**Fase 3 — Tracing Distribuído (semanas 13–20)**
Instrumentar aplicações .NET e Java com OpenTelemetry SDK. Implantar OTel Collector como gateway central. Configurar Jaeger como backend de traces. Criar bridge para incluir eventos SAP como spans sintéticos via extrator ABAP. Validar rastreabilidade ponta a ponta de um request cross-system pelo Correlation ID.

---

## 7. Padrão de Correlation ID e fluxo de propagação

**Formato recomendado:**

```
CorrID: {sistema-origem}-{timestamp-epoch-ms}-{uuid-v4-truncado}
Exemplo: LEG-1718054400123-a3f7c2d1
```

Ou, mais simples e amplamente compatível: UUID v4 puro (sem prefixo), especialmente se o contexto já estiver nos logs estruturados.

**Fluxo de propagação cross-system:****Implementação por sistema:**

**Sistema Legado (.NET / Java):** Gerar UUID v4 no entry point (controller REST, consumer Kafka, job agendado) se não houver CorrID no header de entrada. Propagar via `X-Correlation-ID` em chamadas HTTP saídas e via MDC (Java) ou `ILogger` enrichment (.NET) para que todos os logs do processo carreguem o campo automaticamente. Com OpenTelemetry, o `trace_id` pode servir diretamente como CorrID.

**Informatica:** Mapear o campo `X-Correlation-ID` do header HTTP de entrada (ou campo do payload de origem) para uma variável de sessão `$$CORR_ID`. Injetar esse valor nos logs via Custom Transformation ou na estrutura do payload de saída. Nos session logs, usar o `$PMSessionLogFile` com sufixo do `$$CORR_ID`.

**SAP:** Incluir um campo customizado `ZEXT_CORR_ID` na BAPI/IDoc de entrada, mapeado desde o Informatica. No ABAP, persistir esse valor no SLG1 com `SLGHNDL` e incluir no header da mensagem PI/PO (SXMB) via mapeamento de Enhancement. O extrator ABAP lê o campo e o inclui no payload exportado ao Elasticsearch.

---

## 8. Boas práticas de monitoração, auditoria e troubleshooting

**Logging — estrutura obrigatória**
Todo log deve ser JSON estruturado com os campos: `timestamp` (ISO 8601), `correlation_id`, `system` (LEGADO/INFORMATICA/SAP), `severity`, `message`, `transaction_type`, `user` ou `service_account`, `environment` (prod/hml). Sem isso, a correlação automática no Elasticsearch falha.

**Métricas — o que medir por sistema**
Para o sistema legado, monitorar latência de chamadas ao Informatica (p50/p95/p99), taxa de erros por endpoint, e tamanho de payload. Para o Informatica, throughput de sessões (rows/min), duração de workflows e taxa de rejeição. Para o SAP, tempo de resposta de BAPI, número de IDocs com erro (BDCP2), e fila de outbound no PI/PO.

**Tracing — identificar gargalos**
Usar spans OpenTelemetry para delimitar: chamada ao Informatica (span externo), processamento interno no Informatica (span sintético via log timestamp), e chamada ao SAP (span externo via RFC/BAPI). Com Jaeger, é possível visualizar onde o request passa mais tempo em uma transação cross-system.

**Auditoria**
Manter índice separado no Elasticsearch com retenção de 12 meses (tier cold/frozen) para logs de auditoria de alterações em dados mestre SAP e transações financeiras. Jamais mixar logs operacionais com logs de auditoria — políticas de retenção e acesso são distintas.

**Troubleshooting com Correlation ID**
Quando um incidente é reportado, o fluxo padrão é: (1) obter o CorrID do usuário/sistema reportante, (2) buscar no Kibana todos os eventos com aquele CorrID, (3) ordenar por timestamp e identificar em qual sistema ocorreu a falha, (4) abrir trace no Jaeger para ver a latência de cada etapa, (5) correlacionar com métricas do Prometheus no mesmo período.

---

## 9. Conclusão com recomendação prática

A recomendação consolidada para o cenário descrito é uma **stack open-source com três pilares complementares**:

**ELK Stack** como backbone de logging — centraliza os logs heterogêneos dos três sistemas, permite busca por Correlation ID e é a ferramenta de troubleshooting diário da operação. É a maior entrega de valor com o menor custo.

**Prometheus + Grafana** como plataforma de métricas e alertas — monitora a saúde dos sistemas em tempo real, com alertas configuráveis por SLA. Dashboards operacionais que a equipe de suporte pode usar sem acesso aos sistemas de origem.

**OpenTelemetry + Jaeger** como camada de tracing distribuído — a peça mais trabalhosa de implementar, mas a que entrega rastreabilidade verdadeira ponta a ponta, especialmente valioso para diagnosticar latências e falhas silenciosas em integrações entre Legado e Informatica.

O **Correlation ID** é a cola que une tudo: sem ele, as três ferramentas acima funcionam como silos independentes. Com ele, um único identificador permite navegar de um log do Kibana para o trace no Jaeger e para a métrica no Grafana, eliminando o tempo de troubleshooting em ambientes de integração complexa.

Evitar Datadog ou Dynatrace como solução principal nesse contexto — o custo de ingestão de logs em escala e o licenciamento por host tornam inviável para um cenário On Premise com SAP, a não ser que já exista budget enterprise aprovado e a equipe prefira terceirizar a operação da plataforma de observabilidade.
