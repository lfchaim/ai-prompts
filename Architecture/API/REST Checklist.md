# Prompt
Crie um checklist detalhado para envio ao desenvolvedor de software, abordando os principais aspectos para desenvolver uma integração robusta e segura entre sistemas utilizando API RESTful. Inclua as seguintes seções e itens:

1. **Requisitos e Especificações da Integração**
   - Definição clara dos objetivos da integração
   - Identificação dos sistemas envolvidos (origem e destino dos dados)
   - Especificação dos dados a serem trocados (formatos, estruturas e volumes)
   - Frequência e gatilhos para as chamadas de API

2. **Autenticação e Autorização**
   - Método de autenticação a ser utilizado (OAuth, JWT, Basic Auth, etc.)
   - Mecanismos de autorização para diferentes níveis de acesso
   - Gerenciamento de tokens/sessões (renovação, revogação, etc.)

3. **Design da API**
   - Princípios de design RESTful (recursos, URIs, métodos HTTP)
   - Versionamento da API (estratégia e implementação)
   - Documentação da API (formato, conteúdo e atualizações)

4. **Tratamento de Erros e Exceções**
   - Estratégia para tratamento de erros (códigos HTTP, mensagens de erro)
   - Mecanismos de logging e monitoramento de exceções
   - Procedimentos para notificação de erros críticos

5. **Segurança**
   - Implementação de HTTPS (TLS/SSL)
   - Proteção contra ataques comuns (SQL Injection, XSS, CSRF, etc.)
   - Validação e sanitização de entradas
   - Limitação de taxa (Rate Limiting)

6. **Desempenho e Escalabilidade**
   - Estratégias de cache (quando e como usar)
   - Otimização de consultas e operações pesadas
   - Testes de carga e stress
   - Plano de escalabilidade para aumento de demanda

7. **Testes e Validação**
   - Tipos de testes a serem realizados (unitários, integração, end-to-end)
   - Ferramentas e frameworks de teste recomendados
   - Critérios de aceitação para a integração

8. **Manutenção e Monitoramento**
   - Plano de manutenção (atualizações, patches, etc.)
   - Ferramentas de monitoramento (logs, métricas, alertas)
   - Procedimentos para atualizações da API e comunicação com consumidores

9. **Documentação e Suporte**
   - Nível de detalhe e formato da documentação da API
   - Canais de suporte para consumidores da API (documentação, contato, etc.)
   - Processos para feedback e melhoria contínua da API

Forneça o checklist em formato tabular ou lista numerada, com espaço para anotações ou comentários do desenvolvedor, e indique claramente os itens considerados obrigatórios versus recomendados.

# Abacus
Este checklist foi estruturado para garantir que a integração RESTful seja desenvolvida com foco em robustez, segurança e manutenibilidade. Os itens estão divididos entre **Obrigatório (O)**, essencial para o funcionamento e segurança mínima, e **Recomendado (R)**, que visa excelência operacional e escalabilidade.

### Checklist de Integração de API RESTful

| ID | Item de Verificação | Prioridade | Status / Anotações do Desenvolvedor |
| :--- | :--- | :---: | :--- |
| **1** | **Requisitos e Especificações** | | |
| 1.1 | Documento de objetivos: O que a integração resolve? | **O** | |
| 1.2 | Mapeamento de De/Para: Origem vs. Destino dos dados. | **O** | |
| 1.3 | Definição de payloads: JSON/XML, tipos de dados e tamanhos. | **O** | |
| 1.4 | Gatilhos: Chamadas síncronas, Webhooks ou Polling? | **O** | |
| **2** | **Autenticação e Autorização** | | |
| 2.1 | Implementação de padrão moderno (OAuth2 ou JWT). | **O** | |
| 2.2 | Escopos de acesso (Scopes/Roles) por endpoint. | **O** | |
| 2.3 | Fluxo de renovação de tokens (Refresh Token). | **R** | |
| 2.4 | Política de expiração e revogação de credenciais. | **O** | |
| **3** | **Design da API** | | |
| 3.1 | Uso correto de Verbos HTTP (GET, POST, PUT, DELETE, PATCH). | **O** | |
| 3.2 | URIs semânticas e baseadas em recursos (substantivos). | **O** | |
| 3.3 | Estratégia de versionamento (ex: `/v1/` na URL ou via Header). | **O** | |
| 3.4 | Documentação viva e interativa (Swagger/OpenAPI). | **R** | |
| **4** | **Tratamento de Erros e Exceções** | | |
| 4.1 | Uso de Status Codes padronizados (2xx, 4xx, 5xx). | **O** | |
| 4.2 | Corpo de erro padronizado (Código interno + Mensagem clara). | **O** | |
| 4.3 | Logs estruturados para rastreabilidade (Correlation ID). | **R** | |
| 4.4 | Alerta automático para erros 5xx persistentes. | **O** | |
| **5** | **Segurança** | | |
| 5.1 | Uso mandatório de TLS 1.2+ (HTTPS). | **O** | |
| 5.2 | Sanitização de inputs para evitar Injections e XSS. | **O** | |
| 5.3 | Implementação de Rate Limiting (limite por IP/Token). | **R** | |
| 5.4 | CORS configurado apenas para domínios autorizados. | **O** | |
| **6** | **Desempenho e Escalabilidade** | | |
| 6.1 | Cache de respostas (Headers `ETag` ou `Cache-Control`). | **R** | |
| 6.2 | Paginação obrigatória para listagens volumosas. | **O** | |
| 6.3 | Compressão de dados (Gzip/Brotli) no tráfego. | **R** | |
| 6.4 | Timeout configurado em todas as chamadas externas. | **O** | |
| **7** | **Testes e Validação** | | |
| 7.1 | Testes unitários para lógica de negócio. | **O** | |
| 7.2 | Testes de integração em ambiente de Sandbox/Staging. | **O** | |
| 7.3 | Teste de carga para validar limites de concorrência. | **R** | |
| 7.4 | Validação de contrato (JSON Schema). | **R** | |
| **8** | **Manutenção e Monitoramento** | | |
| 8.1 | Dashboard de saúde da API (Uptime, Latência, Erros). | **R** | |
| 8.2 | Política de Depreciação (avisos prévios para versões antigas). | **O** | |
| 8.3 | Rota de `/health` para monitoramento de infraestrutura. | **O** | |
| **9** | **Documentação e Suporte** | | |
| 9.1 | Guia de "Primeiros Passos" e exemplos de código. | **R** | |
| 9.2 | Canal de suporte técnico (e-mail ou sistema de tickets). | **O** | |
| 9.3 | Changelog público para novas funcionalidades e correções. | **R** | |

#### Observações Adicionais para o Desenvolvedor:
- **Idempotência:** Certifique-se de que operações de criação (POST) ou atualização sejam seguras contra repetições acidentais de rede.
- **Segurança de Dados:** Dados sensíveis (PII) devem ser mascarados nos logs.
- **Circuit Breaker:** Para integrações críticas, considere implementar o padrão Circuit Breaker para evitar falhas em cascata.

# Adapta
Checklist detalhado para integração RESTful robusta e segura

Abaixo segue um checklist prático, organizado por seções. Cada tabela lista itens a verificar, o que precisa ser confirmado/definido e se o item é Obrigatório (O) ou Recomendado (R). A última coluna deixa espaço para anotações do desenvolvedor.

1. Requisitos e Especificações da Integração
Item	O que verificar	Obrigatório (O) / Recomendado (R) — Anotações
Objetivos da integração	Definir claramente resultados esperados, métricas de sucesso, KPIs e casos de uso principais (ex.: sincronização de clientes, criação de pedidos, consulta de status).	O — ____________________________
Sistemas envolvidos	Identificar origem e destino dos dados, responsáveis e SLAs de cada sistema; listar ambientes (desenvolvimento, homologação, produção).	O — ____________________________
Escopo de dados	Mapear entidades e campos que serão trocados; formatos (JSON, CSV em payloads especiais), estruturas (esquemas), obrigatoriedade, tamanhos máximos, limites de volume/dia.	O — ____________________________
Catálogo de eventos/ações	Relacionar operações necessárias (CRUD), eventos que disparam chamadas (webhooks, timers, filas), janelas de processamento e dependências de ordem (sequenciamento).	O — ____________________________
Frequência e gatilhos	Definir periodicidade (em tempo real, batch), cronogramas, atrasos toleráveis, janelas de manutenção e comportamento em picos de tráfego.	O — ____________________________
Requisitos de conformidade	Identificar requisitos regulatórios (LGPD, PCI-DSS, HIPAA, SOX), retenção de dados e políticas de privacidade.	O — ____________________________
Requisitos não funcionais	Definir disponibilidade (SLA), confiabilidade, recuperabilidade (RPO/RTO), latência alvo (P95/P99), capacidade inicial e limites de erro aceitos.	O — ____________________________
Matriz de mapeamento	Mapeamento campo a campo entre os sistemas, com transformações (tipos, normalizações, timezone, unidade de medida) e regras de default.	O — ____________________________
Identificadores e deduplicação	Definir chaves naturais/alternativas, idempotency keys, e regras para lidar com duplicidades e reconciliações tardias.	O — ____________________________
Critérios de fallback manual	Descrever procedimentos de contingência (processo manual, reprocessamento orientado por arquivo/fila) quando a integração estiver indisponível.	R — ____________________________

2. Autenticação e
Item	O que verificar	Obrigatório (O) / Recomendado (R) — Anotações
Método de autenticação	Definir padrão (OAuth 2.1, OpenID Connect, JWT Bearer, mTLS, Basic Auth apenas em cenários internos); armazenamento seguro de credenciais e segredo do cliente.	O — ____________________________
Fluxos OAuth	Selecionar fluxo adequado (Client Credentials, Authorization Code com PKCE, Refresh Tokens); escopos mínimos necessários (principle of least privilege).	O — ____________________________
Mecanismos de autorização	Definir RBAC/ABAC; perfis por recurso/ação; checagens no gateway e no serviço; políticas de acesso por ambiente.	O — ____________________________
Gerenciamento de tokens	Expiração, renovação (refresh), rotação e revogação; detecção de reuse; clock skew tolerado; formato e tamanho do token.	O — ____________________________
Distribuição de chaves	Publicação de JWKS, rotação de chaves (kid), algoritmos (RS256/ES256), política de invalidação e pinagem de chave pública quando aplicável.	R — ____________________________
Secret management	Uso de cofre de segredos (ex.: HashiCorp Vault, AWS Secrets Manager, Azure Key Vault), proibição de segredos no código/CI logs.	O — ____________________________

3. Design da API
Item	O que verificar	Obrigatório (O) / Recomendado (R) — Anotações
Princípios RESTful	Recursos bem definidos, URIs estáveis, verbos HTTP adequados (GET, POST, PUT, PATCH, DELETE), uso de códigos HTTP padronizados.	O — ____________________________
Padrões de payload	JSON como padrão; consistência de nomenclatura (snake_case vs camelCase); timezone ISO 8601; paginação (limit/offset ou cursor), sort e filtros consistentes.	O — ____________________________
Versionamento	Estrategia clara (ex.: v1, v2 em caminho ou Accept header); política de depreciação; compatibilidade retroativa e calendário de migração.	O — ____________________________
Idempotência	Idempotency-Key para POST sensíveis; tratamento de duplicidade e retornos 409/422 apropriados; resposta consistente em replays.	O — ____________________________
Hiperlinks e discoverability	Links de paginação, self, next, prev; HATEOAS quando fizer sentido; cabeçalhos ETag/Last-Modified para cache condicional.	R — ____________________________
Documentação	OpenAPI 3.x/Swagger atualizado, exemplos por cenário, códigos de erro e catálogos; changelog versionado, status de estabilidade dos endpoints.	O — ____________________________
Webhooks	Assinatura de mensagens (HMAC), reentrega com backoff, idempotência no consumidor, verificação de origem, endpoints de teste.	R — ____________________________
Headers e convenções	Content-Type, Accept, correlação (X-Request-Id/Trace-Id), rate-limit headers, depreciação (Sunset, Deprecation).	R — ____________________________

4. Tratamento de Erros e Exceções
Item	O que verificar	Obrigatório (O) / Recomendado (R) — Anotações
Estratégia de códigos HTTP	Mapeamento claro (2xx sucesso, 4xx erro do cliente, 5xx erro do servidor); uso de 429 para rate limit; 408/504 para timeouts.	O — ____________________________
Padrão de resposta de erro	Corpo com code, message, details, correlationId, timestamp e documentação de referência; mensagens seguras sem vazar segredos.	O — ____________________________
Re-tentativas e backoff	Política de retry no cliente/servidor para erros transitórios (502/503/504), com exponential backoff e jitter; limites máximos de tentativas.	O — ____________________________
Timeouts e circuit breakers	Timeouts por chamada; circuit breaker e bulkheads para evitar cascatas; handling de cancelamento.	R — ____________________________
Logging de exceções	Logs estruturados com níveis, stack traces sanitizados, PII mascarado, correlação entre serviços; retenção e rotação de logs.	O — ____________________________
Notificação de erros críticos	Alertas automáticos (pager/email/chat), critérios de severidade, procedimentos de on-call e escalonamento; runbooks anexos.	O — ____________________________


5. Segurança
Item	O que verificar	Obrigatório (O) / Recomendado (R) — Anotações
HTTPS/TLS	Forçar TLS 1.2+ (ideal 1.3); certificados válidos e renovação automatizada; HSTS; desabilitar ciphers inseguros; mTLS quando aplicável.	O — ____________________________
Proteção contra ataques comuns	WAF/gateway, proteção contra SQL Injection, XSS, CSRF, SSRF, deserialização insegura; cabeçalhos de segurança; validação rigorosa de origin/CORS.	O — ____________________________
Validação e sanitização	Validação server-side de tipos, ranges, formatos (schemas JSON); sanitização de entradas; limites de tamanho de payload e número de itens por requisição.	O — ____________________________
Rate limiting e quotas	Definir limites por IP/chave/usuário; estratégias por burst e janela; headers de rate limit; bloqueio e desbloqueio automatizados.	O — ____________________________
Proteção contra replay	Assinaturas com nonce/timestamp, validade curta de requisições sensíveis, marcações idempotentes, verificação de reuso de tokens.	R — ____________________________
Gestão de segredos	Criptografia em repouso e em trânsito; rotação periódica; segregação de funções; auditoria de acesso a segredos; zero-trust nos ambientes.	O — ____________________________
Acesso de rede	Allowlist/denylist de IPs, VPC peering/privado para integrações internas, firewall/gateway configurados, escaneamento de portas expostas.	R — ____________________________
Vulnerabilidades	Pipeline de SAST/DAST/Dependency Scanning; correções via patches; política de CVSS e prazos de mitigação; SBOM disponível.	O — ____________________________

6. Desempenho e Escalabilidade
Item	O que verificar	Obrigatório (O) / Recomendado (R) — Anotações
Estrategias de cache	Uso de ETag/If-None-Match, Last-Modified/If-Modified-Since; TTLs; cache em gateway/CDN; invalidação; cache de resultados pesados e lookups comuns.	R — ____________________________
Compressão	Habilitar gzip/brotli; limites para evitar compressão de payloads pequenos; negociar via Content-Encoding.	R — ____________________________
Otimização de consultas	Índices adequados; evitar N+1; paginação eficiente; projeção de campos (fields=...); jobs assíncronos para operações pesadas.	O — ____________________________
Assincronia	Uso de filas/eventos para workloads demorados; webhooks de conclusão; status endpoints para polling com backoff.	R — ____________________________
Testes de carga/stress	Definir cenários realistas, picos, padrões de uso; metas P50/P95/P99; identificar gargalos; testes de resiliência/chaos quando possível.	O — ____________________________
Plano de escalabilidade	Horizontal vs vertical; auto-scaling por métricas (CPU, latência, fila); limites de conexão; particionamento; limites por tenant.	R — ____________________________
Gerenciamento de conexões	Pool de conexões; keep-alive; limites de simultaneidade; timeouts de socket e de app; tuning do servidor HTTP.	R — ____________________________

7. Testes e Validação
Item	O que verificar	Obrigatório (O) / Recomendado (R) — Anotações
Tipos de testes	Unitários, integração, contrato (OpenAPI/Pact), end-to-end, segurança (fuzzing), performance (carga/stress), regressão e smoke tests por release.	O — ____________________________
Ambientes de teste	Sandbox isolada, dados sintéticos/mascarados, chaves separadas por ambiente, reset previsível e fixtures documentadas.	O — ____________________________
Ferramentas e frameworks	Postman/Newman, k6/JMeter/Gatling, Pact, WireMock/MockServer, Jest/JUnit/pytest, OWASP ZAP/Burp para segurança; integração em CI/CD.	R — ____________________________
Critérios de aceitação	Definir DoD/DoR: cobertura mínima, latência máx. P95, taxa de erro < limiar, compatibilidade com versões suportadas, conformidade com segurança.	O — ____________________________
Testes de idempotência e retry	Validar que replays não causam efeitos duplicados; simular falhas transitórias e verificar consistência do estado final.	O — ____________________________
Testes de compatibilidade	Backward/forward compatibility entre versões; toggles de recurso; desativação/ativação controlada (feature flags).	R — ____________________________


8. Manutenção e Monitoramento
Item	O que verificar	Obrigatório (O) / Recomendado (R) — Anotações
Plano de manutenção	Calendário de releases, janelas de manutenção, política de patching e hotfix; gestão de depreciação e EOL por versão.	O — ____________________________
Observabilidade	Logs estruturados, métricas (latência, taxa de erro, saturação), tracing distribuído (OpenTelemetry), dashboards por serviço e por versão.	O — ____________________________
Alertas	Alertas baseados em SLOs/SLIs; redução de ruído; canais e escalonamento; verificação periódica de validade dos contatos on-call.	O — ____________________________
Runbooks e playbooks	Procedimentos para incidentes comuns (picos 429/5xx, filas atrasadas, falha de webhook), checklists de mitigação e rollback.	R — ____________________________
Gestão de configuração	Configurações versionadas, segregadas por ambiente, com validação e rollback seguro; feature flags auditáveis.	R — ____________________________
Processo de atualização da API	Changelog detalhado, comunicação proativa com consumidores, períodos de sunset, canais de teste antecipado (beta/preview).	O — ____________________________

9. Documentação e Suporte
Item	O que verificar	Obrigatório (O) / Recomendado (R) — Anotações
Nível de detalhe da documentação	OpenAPI 3.x completo; exemplos por linguagem; guias de início rápido; tutoriais por caso de uso; catálogo de erros; políticas de limite e depreciação.	O — ____________________________
Formatos e acessibilidade	Portal do desenvolvedor, docs HTML navegáveis, SDKs e snippets, Postman collection, ambientes de sandbox acessíveis e instruções de autenticação.	R — ____________________________
Canais de suporte	Contato técnico (e-mail, issue tracker), tempos de resposta, SLA de suporte, fórum/FAQ, status page pública com histórico de incidentes.	O — ____________________________
Processo de feedback	Rota para solicitar melhorias, roadmap público quando possível, critérios de priorização, pesquisas de satisfação do desenvolvedor.	R — ____________________________
Exemplos e testes	Exemplos de requests/responses reais, cenários de erro simulados, dados seed do sandbox, scripts de validação rápida (smoke) para consumidores.	R — ____________________________
Políticas legais e de uso	Termos de uso, AUP, políticas de privacidade, quotas e limites, requisitos de atribuição, restrições de dados sensíveis e compliance aplicável.	O — ____________________________

Itens adicionais altamente recomendados (cross-cutting)
Item	O que verificar	Obrigatório (O) / Recomendado (R) — Anotações
Governança de versões	Uso de SemVer; testes de compatibilidade por CI; intervalos máximos entre breaking changes; política clara de suporte por versão.	R — ____________________________
Qualidade de dados	Regras de validação de negócio, limpeza/normalização, reconciliação periódica entre sistemas, relatórios de divergência.	R — ____________________________
Mensuração do sucesso	KPIs de produto (tempo de integração, adoção, erro por 1k chamadas), metas e revisão trimestral; dashboard de saúde da integração.	R — ____________________________
Segurança de pipeline	Hardening de CI/CD, revisão de permissões, proteção de secrets em pipelines, assinaturas de artefatos, IaC escaneado.	R — ____________________________

Dicas práticas para implementação

Padronize o tratamento de erros com um schema único de resposta e mantenha um catálogo público de códigos e significados.
Adote Idempotency-Key em operações que criam/alteram estado, com retenção de chaves pelo tempo de risco de replay.
Inclua correlação de requisições com X-Request-Id e propague o Trace-Id em serviços downstream.
Estabeleça SLOs e monitore SLIs principais: disponibilidade, latência P95, taxa de erro, saturação de recursos.
Documente claramente a política de rate limit e retorne headers que ajudem o cliente a se adaptar.
Para webhooks, assine mensagens, valide timestamps e implemente reentrega com backoff e limite de tentativas.
Tenha um ambiente sandbox com dados sintéticos, chaves exclusivas e reset previsível, facilitando o onboarding dos consumidores.
Faça testes de caos/resiliência nos componentes críticos quando possível, ao menos em homologação.

Resumo: as seções acima cobrem requisitos, segurança, design RESTful, erros, desempenho, testes, operação e suporte. Marque todos os itens “Obrigatório” para garantir o mínimo de robustez e priorize os “Recomendado” conforme riscos e metas de negócio. Se quiser, posso converter este checklist em um template editável (Google Docs/Markdown) ou gerar uma checklist interativa para seu repositório.

## Template
# Checklist de Integração RESTful — Template Editável

## Metadados do Projeto
- Nome do projeto/integração: _______________________________
- Data de início: ____/____/________  |  Data prevista de go-live: ____/____/________
- Responsável técnico (dev/tech lead): _______________________________
- Sponsor/PO: _______________________________
- Sistemas envolvidos: Origem: __________ | Destino: __________ | Intermediários (se houver): __________
- Ambientes: [ ] Dev  [ ] Homologação  [ ] Produção
- Repositório/branch principal: ___________________________________
- SLA de disponibilidade/tempo de resposta: _______________________
- Segurança de dados (classificação/sensibilidade): ________________
- Status geral da integração: [ ] Pendente  [ ] Em Progresso  [ ] Em Validação  [ ] Aprovada p/ Go-live

---

## 1) Requisitos e Especificações da Integração
- [ ] (Obrigatório) Objetivos da integração definidos e validados com negócio/PO
  - Descrição objetiva (problema/resultado esperado): ________________________________________
  - Métricas de sucesso (KPIs/SLAs): ________________________________________________________
  - Notas/Comentários: _____________________________________________________________________
  - Responsável: __________________________ | Artefatos/Links: ______________________________

- [ ] (Obrigatório) Sistemas envolvidos identificados (origem/destino) e papéis claros
  - Origem (proprietário/contato): __________________  |  Destino (proprietário/contato): ______________
  - Matriz de responsabilidades (RACI) definida: [ ] Sim  [ ] Não
  - Notas/Comentários: _____________________________________________________________________
  - Responsável: __________________________ | Artefatos/Links: ______________________________

- [ ] (Obrigatório) Especificação dos dados a serem trocados (formatos, estruturas, volumes)
  - Entidades/campos principais: ____________________________________________________________
  - Formatos (ex.: JSON, CSV, XML): __________________
  - Schemas/contratos (OpenAPI/JSON Schema/Avro): __________________________________________
  - Volumetria estimada (RPS, picos, payload médio): ________________________________________
  - Requisitos de ordenação/consistência: _________________________________________________
  - Notas/Comentários: _____________________________________________________________________
  - Responsável: __________________________ | Artefatos/Links: ______________________________

- [ ] (Obrigatório) Frequência e gatilhos das chamadas de API
  - Frequência (event-driven, schedule cron, on-demand): _____________________________________
  - Gatilhos/condições de disparo: __________________________________________________________
  - Janela de manutenção/blackout: _________________________________________________________
  - Notas/Comentários: _____________________________________________________________________
  - Responsável: __________________________ | Artefatos/Links: ______________________________

---

## 2) Autenticação e Autorização
- [ ] (Obrigatório) Método de autenticação definido (OAuth2, JWT, mTLS, Basic Auth, etc.)
  - Fluxo (Client Credentials/Auth Code/PKCE/mTLS): _________________________________________
  - Emissor (issuer), audience, escopos: ____________________________________________________
  - Cabeçalhos e formato do token: _________________________________________________________
  - Notas/Comentários: _____________________________________________________________________
  - Responsável: __________________________ | Artefatos/Links: ______________________________

- [ ] (Obrigatório) Mecanismos de autorização por perfis/escopos/roles
  - Escopos e permissões por recurso/método: _______________________________________________
  - Matrizes RBAC/ABAC (se aplicável): _____________________________________________________
  - Notas/Comentários: _____________________________________________________________________
  - Responsável: __________________________ | Artefatos/Links: ______________________________

- [ ] (Obrigatório) Gerenciamento de tokens/sessões
  - Expiração/renovação (TTL/refresh): ______________________________________________________
  - Revogação/rotação de chaves (KMS/JWKs): ________________________________________________
  - Armazenamento seguro de segredos (cofre/secrets manager): [ ] Sim  [ ] Não  [ ] N/A
  - Notas/Comentários: _____________________________________________________________________
  - Responsável: __________________________ | Artefatos/Links: ______________________________

- [ ] (Recomendado) mTLS entre serviços sensíveis/privados
  - Autoridade certificadora, emissão/rotação: ______________________________________________
  - Notas/Comentários: _____________________________________________________________________

---

## 3) Design da API
- [ ] (Obrigatório) Princípios RESTful aplicados (recursos, URIs, métodos HTTP corretos)
  - Padrão de URIs (plural, hierarquia, sub-recursos): ______________________________________
  - Idempotência (PUT/PATCH/DELETE) e regras de repetição: __________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Recomendado) Suporte a Idempotency-Key para POST sensíveis a duplicidade
  - Cabeçalho/timeout/replay handling: ______________________________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Obrigatório) Versionamento da API
  - Estratégia (path /v1, header, param): ___________________________________________________
  - Política de depreciação e compatibilidade: _____________________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Obrigatório) Documentação da API
  - OpenAPI/Swagger publicado: __________________________
  - Exemplos de requests/responses e códigos de erro: _______________________________________
  - SDKs/snippets (linguagens): _____________________________________________________________
  - Processo de atualização/changelog: _____________________________________________________
  - Notas/Comentários: _____________________________________________________________________

---

## 4) Tratamento de Erros e Exceções
- [ ] (Obrigatório) Convenção de códigos HTTP e erros de domínio
  - Mapeamento 2xx/4xx/5xx e mensagens padronizadas: ________________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Obrigatório) Corpo de erro consistente
  - Campos mínimos (timestamp, code, message, details, correlationId/traceId): _______________
  - Internacionalização (se aplicável): _____________________________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Obrigatório) Logging e monitoramento de exceções
  - Logs estruturados (JSON), mascaramento de PII/segredos: _________________________________
  - Integração com observabilidade (APM/ELK/OTel): __________________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Obrigatório) Notificação de erros críticos
  - Canais (PagerDuty/Email/Slack), severidade e SLA de resposta: ____________________________
  - Runbook de incidentes: _________________________________________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Recomendado) Propagação de correlation/trace IDs ponta a ponta
  - Padrão (W3C TraceContext, B3, etc.): ____________________________________________________
  - Notas/Comentários: _____________________________________________________________________

---

## 5) Segurança
- [ ] (Obrigatório) HTTPS com TLS 1.2+ em todos os endpoints
  - Certificados, HSTS, desativar ciphers fracos: ___________________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Obrigatório) Proteção contra ataques comuns (OWASP Top 10)
  - SQL/NoSQL injection, XSS, XXE, SSRF, CSRF (se aplicável), path traversal: ________________
  - WAF/WAAP configurado (Recomendado): [ ] Sim  [ ] Não
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Obrigatório) Validação e sanitização de entradas/saídas
  - Schemas/validators, limites de tamanho e tipos: _________________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Obrigatório) Rate Limiting/Throttling e quotas
  - Regras por IP/chave/usuário, resposta 429 com Retry-After: ______________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Obrigatório) Política de CORS mínima necessária (origens, métodos, headers)
  - Lista de allowlist e tempos de cache (preflight): _______________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Recomendado) Scans SAST/DAST/Dependency e correções de vulnerabilidades
  - Frequência/pipeline CI: _________________________________________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Recomendado) Security headers (CSP, X-Content-Type-Options, X-Frame-Options, etc.)
  - Política definida/testada: ______________________________________________________________
  - Notas/Comentários: _____________________________________________________________________

---

## 6) Desempenho e Escalabilidade
- [ ] (Obrigatório) Paginação, filtragem e ordenação para coleções
  - Limites padrão/máximos; paginação determinística: _______________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Obrigatório) Otimização de consultas e operações pesadas
  - Índices, N+1 avoidance, batch/bulk endpoints: ___________________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Obrigatório) Testes de carga/stress com metas registradas
  - TPS/RPS alvo, latência p95/p99, erro máximo permitido: __________________________________
  - Ferramentas e cenários: ________________________________________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Recomendado) Estratégias de cache
  - ETag/Last-Modified, Cache-Control, caching por camada (client/edge/app/db): ______________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Recomendado) Compressão e limites de payload
  - gzip/br, max payload e chunking/streaming: ______________________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Obrigatório) Timeouts, retries com backoff exponencial e circuit breaker
  - Políticas por integração/endpoint: ______________________________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Recomendado) Plano de escalabilidade
  - Autoscaling/horizontal scaling, separação de leitura/escrita: ____________________________
  - Notas/Comentários: _____________________________________________________________________

---

## 7) Testes e Validação
- [ ] (Obrigatório) Testes unitários (cobertura mínima: ____%)
  - Áreas críticas cobertas: ________________________________________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Obrigatório) Testes de integração (contratos e ambientes realistas)
  - Testcontainers/mocks/stubs; dados de teste gerenciados: _________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Obrigatório) Testes end-to-end e de contrato (Consumer-Driven Contracts)
  - Pact/contract tests e regressão automática: _____________________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Recomendado) Testes de segurança (SAST/DAST/IAST) e fuzzing básico
  - Escopo/periodicidade: __________________________________________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Recomendado) Testes de resiliência/caos em homologação
  - Falhas simuladas (latência, timeout, quedas): ___________________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Obrigatório) Critérios de aceitação
  - Critérios formais, casos representativos, critérios de rollback: _________________________
  - Notas/Comentários: _____________________________________________________________________

---

## 8) Manutenção e Monitoramento
- [ ] (Obrigatório) Observabilidade
  - Logs estruturados, métricas de negócio/técnicas, tracing distribuído: ____________________
  - Dashboards e alertas com limiares definidos: ____________________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Obrigatório) Procedimentos de atualização (backward compatibility)
  - Blue/green ou canary; rollback e plano de contingência: _________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Obrigatório) Política de depreciação e comunicação com consumidores
  - Janelas e milestones; comunicação antecipada: ___________________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Recomendado) Plano de manutenção (patches, janelas)
  - Patching de SO/runtime/deps; cronograma: ________________________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Recomendado) Backups/exportações (se dados persistidos)
  - Escopo/retention/restauração testada: ___________________________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Recomendado) Runbooks e playbooks de incidentes
  - Procedimentos por severidade; contatos 24x7 (se aplicável): _____________________________
  - Notas/Comentários: _____________________________________________________________________

---

## 9) Documentação e Suporte
- [ ] (Obrigatório) Nível de detalhe e formato da documentação
  - Guia de visão geral, autenticação, recursos, exemplos e erros: ___________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Recomendado) Guia de início rápido (Quickstart) e exemplos executáveis
  - Coleções Postman/Insomnia; SDKs/snippets: ______________________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Obrigatório) Canais de suporte e SLAs
  - Contatos, horários de atendimento, issue tracker: _______________________________________
  - Notas/Comentários: _____________________________________________________________________

- [ ] (Recomendado) Processo de feedback e melhoria contínua
  - Roadmap, votações, changelog público: _________________________________________________
  - Notas/Comentários: _____________________________________________________________________

---

## Anexos/Artefatos Úteis (opcional)
- Mapa de campos (origem -> destino): ______________________________________________________
- Dicionário de dados: _____________________________________________________________________
- Diagrama de sequência/fluxo: _____________________________________________________________
- Matriz de compatibilidade de versões: ____________________________________________________
- Lista de dependências externas: __________________________________________________________
- Inventário de segredos/variáveis de ambiente (em cofre): _________________________________

---

## Aprovações
- Revisão técnica (arquitetura): _______________________  Data: ____/____/____  Status: __________
- Segurança/appsec: ____________________________________  Data: ____/____/____  Status: __________
- QA/Qualidade: ________________________________________  Data: ____/____/____  Status: __________
- Negócio/PO: __________________________________________  Data: ____/____/____  Status: __________

## Histórico de Revisões
- Versão: ______  |  Data: ____/____/____  |  Mudanças: ______________________________________
- Versão: ______  |  Data: ____/____/____  |  Mudanças: ______________________________________
