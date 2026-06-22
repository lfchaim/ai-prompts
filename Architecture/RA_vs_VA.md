# Objetivo
Conversão de Requisitos Arquiteturais em Variáveis de Arquitetura.

# Proposta
Para transformar os seus **Requisitos de Arquitetura (RAs)** em **Variáveis de Arquitetura** (também conhecidas como Atributos de Qualidade ou *Architecture Characteristics*), precisamos traduzir essas necessidades de negócio e técnicas em dimensões mensuráveis e acionáveis para o design do seu SaaS.

Aqui está a conversão e o mapeamento prático para o cenário SaaS:

---

### Tabela de Conversão: RA para Variáveis de Arquitetura

| ID | Requisito de Arquitetura (RA) | Variável de Arquitetura (Atributo) | Foco Principal no Cenário SaaS |
| --- | --- | --- | --- |
| **RA 09** | Segurança & Compliance | **Security & Compliance** | Criptografia (repouso/trânsito), LGPD/GDPR, isolamento de dados (*Multi-tenancy*), IAM (OAuth2/OIDC). |
| **RA 06** | Disponibilidade | **Availability** | SLA/SLO (ex: 99.9%), estratégias de *Multi-AZ*, tolerância a falhas e *Disaster Recovery* (RTO/RPO). |
| **RA 05** | Integrabilidade | **Interoperability / Extensibility** | Design de APIs (REST, GraphQL, gRPC), webhooks, arquitetura orientada a eventos (EDA). |
| **RA 01** | Manutenibilidade | **Maintainability / Modularity** | Arquitetura de microsserviços ou monolito modular, baixo acoplamento, facilidade de refatoração. |
| **RA 03** | Observabilidade | **Observability** | Centralização de Logs, Métricas, Distributed Tracing (APM) e alertas estruturados. |
| **RA 08** | Performance | **Performance / Throughput** | Tempo de resposta (Latência p95/p99), concorrência, estratégias de cache (Redis/CDN). |
| **RA 11** | Não-lockin | **Portability / Portability** | Uso de containers (Docker/K8s), abstração de banco de dados, evitar serviços proprietários difíceis de migrar. |
| **RA 02** | Testabilidade | **Testability** | Cobertura de testes unitários/integração, facilidade de mockar dependências, pipelines de CI robustos. |
| **RA 12** | Custo-benefício | **Cost Efficiency (FinOps)** | Otimização de recursos em nuvem, auto-scaling agressivo, arquitetura serverless onde aplicável. |
| **RA 07** | Escalabilidade | **Scalability** | Escalabilidade horizontal (auto-scaling), particionamento de banco de dados (*sharding* por *tenant*). |
| **RA 10** | User & Customer Experience | **Usability / Performance (Frontend)** | Tempo de carregamento inicial (Core Web Vitals), SPAs fluidas, tolerância a falhas na UI (graceful degradation). |
| **RA 13** | Time to Market | **Agility / Deployability** | Automação de CI/CD (GitOps), feature flags, facilidade de setup de novos ambientes. |
| **RA 04** | Adaptabilidade | **Evolutionability / Flexibility** | Capacidade do sistema de evoluir e aceitar novos modelos de negócio ou regras sem reescrever o core. |

---

### Próximo Passo: O "Trade-off" Arquitetural

No mundo de sistemas SaaS, essas variáveis frequentemente entram em conflito. Por exemplo:

* Maximizar **Segurança (RA 09)** e **Disponibilidade (RA 06)** pode prejudicar o **Custo-benefício (RA 12)**.
* Aumentar a **Manutenibilidade via Microsserviços (RA 01)** pode afetar a **Performance (RA 08)** devido à latência de rede.

> **Dica Prática:** O ideal agora é priorizar essas variáveis (escolha as **3 ou 4 críticas** para o momento atual do produto) e definir cenários arquiteturais (métricas claras) para cada uma delas.

