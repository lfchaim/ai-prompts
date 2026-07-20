# Prompt
Aja como um especialista em Application Management Services (AMS) e em estruturação de documentação técnica.

Crie um checklist otimizado para AMS, com foco em organização, clareza e cobertura completa dos principais aspectos de operação, manutenção e suporte de aplicações.

Considere, no mínimo, os seguintes tópicos:
- Arquitetura lógica e física
- Documentação do banco de dados
- Documentação de APIs
- Documentação funcional do sistema

Não se limite aos tópicos acima. Inclua também outros itens relevantes para um checklist robusto de AMS, como governança, suporte, monitoramento, segurança, continuidade, ambientes, integrações, versionamento, processos operacionais e gestão de incidentes, quando aplicável.

Requisitos de estrutura:
- Organize o conteúdo de forma hierárquica
- Defina uma estrutura de tópicos em 2 ou 3 níveis
- Agrupe os itens por categorias lógicas
- Use linguagem objetiva e prática
- Garanta que o checklist seja fácil de revisar e aplicar

Formato de saída:
- Entregue apenas o checklist final
- Estruture em tópicos e subtópicos
- Use marcadores claros e consistentes
- Não inclua introdução, explicações ou comentários adicionais

Critérios de qualidade:
- Cobertura ampla e relevante para AMS
- Itens acionáveis e verificáveis
- Estrutura bem organizada
- Sem redundâncias
- Sem inventar informações específicas do contexto do sistema; mantenha o checklist genérico e adaptável

# Adapta ONE
## 📋 Checklist AMS — Application Management Services

### 🏗️ 1. Arquitetura do Sistema
- **1.1 Arquitetura Lógica**
  - Diagrama de componentes e suas relações documentado
  - Fluxos de dados entre módulos mapeados
  - Padrões de arquitetura definidos (microserviços, monolito, eventos, etc.)
  - Decisões arquiteturais registradas com justificativa (ADRs)

- **1.2 Arquitetura Física**
  - Topologia de infraestrutura documentada (servidores, containers, rede)
  - Mapeamento de nós, balanceadores, firewalls e proxies
  - Requisitos de hardware e escalabilidade especificados
  - Diagrama de deployed/ambientes atualizado

### 🗄️ 2. Banco de Dados
- **2.1 Modelagem**
  - Diagrama entidade-relacionamento (DER) atualizado
  - Dicionário de dados com descrição de cada tabela/coleção
  - Índices, chaves e constraints documentados
  - Estratégia de particionamento e sharding registrada

- **2.2 Operação**
  - Procedimentos de backup e restore testados e documentados
  - Política de retenção de dados definida
  - Scripts de migração versionados e testados
  - Plano de purga/arquivamento de dados históricos
  - Monitoramento de performance de queries ativo

### 🔌 3. APIs e Integrações
- **3.1 Documentação de APIs**
  - Swagger/OpenAPI ou equivalente disponível e atualizado
  - Contratos formais (request/response, headers, códigos HTTP)
  - Políticas de versionamento documentadas
  - Exemplos de uso por cenário

- **3.2 Integrações**
  - Mapa de integrações com sistemas internos e externos
  - SLA de cada integração documentado
  - Tratamento de falhas e retry definido
  - Credenciais e chaves gerenciadas com segurança
  - Testes de contrato e integração contínua

### 📘 4. Documentação Funcional
- **4.1 Funcionalidades**
  - Descrição de cada módulo e funcionalidade do sistema
  - Regras de negócio documentadas e validadas
  - Fluxos de usuário (user journeys) mapeados
  - Matriz de permissões e papéis (RBAC)

- **4.2 Manuais e Treinamento**
  - Guia do usuário disponível e atualizado
  - Material de treinamento para novos operadores
  - FAQ operacional mantido

### 🛠️ 5. Ambientes e Deploy
- **5.1 Ambientes**
  - Ambientes mapeados (dev, homolog, staging, produção)
  - Configurações específicas de cada ambiente documentadas
  - Estratégia de promoção entre ambientes definida
  - Acesso e segregação por ambiente controlados

- **5.2 CI/CD**
  - Pipeline de build, teste e deploy documentada
  - Scripts de release e rollback versionados
  - Política de branch e versionamento semântico definida
  - Checklist de release (pré e pós-deploy)

### 📦 6. Versionamento e Código
- **6.1 Repositórios**
  - Estrutura de repositórios definida (monorepo vs multi-repo)
  - Políticas de commit e code review documentadas
  - Tags e releases gerenciadas
  - Dependências externas mapeadas e auditadas

- **6.2 Qualidade de Código**
  - Cobertura de testes mínima definida
  - Ferramentas de análise estática configuradas
  - Padrões de codificação documentados
  - Dívida técnica registrada e priorizada

### 📊 7. Monitoramento e Observabilidade
- **7.1 Métricas e Logs**
  - Métricas de negócio e técnicas definidas (KPIs/SLOs)
  - Dashboards operacionais disponíveis
  - Centralização de logs com retenção definida
  - Alertas configurados com thresholds e escalonamento

- **7.2 APM e Tracing**
  - Monitoramento de performance de aplicação (APM) ativo
  - Distributed tracing implementado
  - Health checks e endpoints de status disponíveis
  - Testes sintéticos configurados

### 🚨 8. Suporte e Gestão de Incidentes
- **8.1 Processo de Suporte**
  - Níveis de suporte (N1, N2, N3) definidos com responsabilidades
  - Matriz de severidade e prioridade documentada
  - SLA por tipo de chamado definido e mensurado
  - Catálogo de serviços disponível

- **8.2 Gestão de Incidentes**
  - Runbook de incidentes conhecidos documentado
  - Procedimento de escalation e comunicação em crises
  - Post-mortem/RCAs realizados após incidentes críticos
  - Plano de comunicação com stakeholders definido

### 🔐 9. Segurança
- **9.1 Controles de Acesso**
  - Política de autenticação e autorização documentada
  - Gestão de identidades e acessos (IAM) implementada
  - Revisão periódica de acessos realizada

- **9.2 Proteção**
  - Política de senhas e MFA definida
  - Criptografia em repouso e em trânsito implementada
  - Vulnerabilidades gerenciadas com scan periódico
  - Secrets management seguro (vaults, variáveis de ambiente)

### 🔄 10. Continuidade e DR
- **10.1 BCP e DR**
  - Plano de continuidade de negócios (BCP) documentado
  - Plano de disaster recovery (DR) com RTO e RPO definidos
  - Testes de DR executados periodicamente

- **10.2 Alta Disponibilidade**
  - Estratégia de failover e redundância documentada
  - SLAs de disponibilidade definidos e monitorados
  - Manutenções programadas com janela definida

### ⚙️ 11. Processos Operacionais
- **11.1 Operação Rotineira**
  - Procedimentos de start/stop e restart documentados
  - Rotinas de manutenção preventiva agendadas
  - Gestão de capacidade e crescimento (capacity planning)
  - Relatórios operacionais periódicos (status, health, tendências)

- **11.2 Gestão de Mudanças**
  - Processo de change management definido
  - Checklist pré-mudança e pós-mudança documentado
  - Janela de mudanças e aprovações definidas
  - Registro histórico de mudanças mantido

### 📋 12. Governança e Compliance
- **12.1 Políticas e Normas**
  - Políticas de uso aceitável e segurança da informação
  - Conformidade com LGPD, ISO 27001 ou regulamentações aplicáveis
  - Auditoria de acessos e alterações habilitada e revisada

- **12.2 Fornecedores e Contratos**
  - Contratos de suporte e manutenção vigentes
  - Licenciamento de software gerenciado e renovado
  - Dependências de terceiros mapeadas com riscos

### 🔗 13. Dependências e Ativos
- **13.1 Matriz de Dependências**
  - Dependências entre sistemas e serviços mapeadas
  - Bibliotecas e frameworks com versões registradas
  - Cronograma de upgrades e fim de suporte (EOL/EOS)

- **13.2 Inventário de Ativos**
  - Lista de ativos de TI associados à aplicação
  - CMDB ou equivalente atualizado
  - Tags e classificação de ativos padronizadas

# Prompt
Elaborar a Documentação Funcional.

# Adapta ONE
## 📋 Checklist AMS — Documentação Funcional

### 📖 1. Visão Geral do Sistema
- **1.1 Propósito e Escopo**
  - Objetivo de negócio do sistema documentado
  - Público-alvo e usuários definidos
  - Problemas que o sistema resolve descritos
  - Escopo funcional (dentro/fora) claramente delimitado

- **1.2 Jornada do Usuário**
  - Personas de usuário mapeadas com perfis e necessidades
  - Fluxo de valor ponta a ponta (value stream) documentado
  - Telas e funcionalidades por persona organizadas
  - Caminhos alternativos e exceções mapeados

### 🧩 2. Regras de Negócio
- **2.1 Catálogo de Regras**
  - Regras de negócio identificadas e nomeadas
  - Fonte da regra (legislação, política interna, requisito de cliente) registrada
  - Versão/histórico de alterações de cada regra mantido
  - Dependências entre regras mapeadas

- **2.2 Lógica e Decisões**
  - Tabelas de decisão para regras condicionais documentadas
  - Fórmulas e cálculos explicitados com exemplos
  - Validações de entrada e saída descritas
  - Tratamento de exceções e casos de borda registrado

### 📋 3. Especificação de Funcionalidades
- **3.1 Estrutura de Funcionalidades**
  - Módulos e submódulos do sistema organizados em hierarquia
  - Descrição funcional por módulo (objetivo, ator, pré-condições)
  - Fluxo principal (happy path) documentado passo a passo
  - Fluxos alternativos e de exceção mapeados

- **3.2 Detalhamento de Funcionalidades**
  - Campos e regras de cada tela/formulário especificados
  - Trigger de eventos e ações esperadas descritas
  - Regras de navegação entre telas definidas
  - Comportamento offline e em modo degradado documentado

- **3.3 UX/UI (quando aplicável)**
  - Wireframes ou protótipos de telas mantidos
  - Padrão de componentes de interface documentado
  - Mensagens e notificações ao usuário catalogadas
  - Tratamento de estados (carregando, vazio, erro, sucesso) definido

### 🔀 4. Fluxos de Processos
- **4.1 Processos de Negócio**
  - BPMN ou diagramas equivalentes mantidos
  - Sequência de atividades e gateways documentada
  - Papéis e responsabilidades de cada etapa definidos
  - SLAs de processo mapeados (tempo de execução, aprovação)

- **4.2 Processos Automatizados**
  - Regras de automação e triggers documentados
  - Jobs agendados e suas dependências registrados
  - Filas de processamento e seus consumidores mapeados
  - Tratamento de falhas e retentativas especificado

### 📝 5. Casos de Uso e Cenários
- **5.1 Casos de Uso**
  - Casos de uso escritos com ator, fluxo principal e alternativos
  - Pré-condições e pós-condições definidas
  - Cenários de sucesso e falha descritos

- **5.2 Cenários de Teste**
  - Critérios de aceite por funcionalidade documentados
  - Casos de teste baseados em regras de negócio
  - Dados de teste representativos mantidos no acervo
  - Evidências de homologação armazenadas

### 🔐 6. Perfis e Permissões
- **6.1 Gestão de Acesso**
  - Perfis de acesso (papéis) do sistema mapeados
  - Funcionalidades disponíveis por perfil documentadas
  - Hierarquia de permissões e heranças registrada
  - Regras de segregação de funções (SoD) definidas

- **6.2 Autenticação e Autorização**
  - Método de autenticação (SSO, LDAP, OAuth) documentado
  - Política de sessão e timeout descrita
  - Auditoria de ações por usuário registrada

### 🔗 7. Configurações e Parametrização
- **7.1 Parâmetros do Sistema**
  - Catálogo de parâmetros configuráveis mantido
  - Valores padrão e faixas permitidas documentados
  - Impacto de cada parâmetro no comportamento do sistema descrito
  - Responsável pela alteração e procedimento registrados

- **7.2 Tabelas de Domínio**
  - Tabelas de lookup (dominio/tipo/categoria) mapeadas
  - Regras de manutenção dessas tabelas definidas
  - Versionamento de valores de domínio gerenciado

### 📤 8. Relatórios e Exportações
- **8.1 Relatórios**
  - Lista de relatórios disponíveis com objetivo e públicos
  - Filtros, parâmetros e agrupamentos documentados
  - Fonte de dados e queries de cada relatório registradas
  - Periodicidade de geração e distribuição definidas

- **8.2 Exportações e Integrações**
  - Leiaute dos arquivos exportados (CSV, XML, JSON) especificado
  - Destino dos arquivos gerados (e-mail, FTP, API) documentado
  - Agendamento das exportações registrado

### ⚠️ 9. Tratamento de Erros e Mensagens
- **9.1 Mensagens ao Usuário**
  - Catálogo de mensagens de erro, aviso e sucesso mantido
  - Texto das mensagens e contexto de exibição documentados
  - Código de erro interno para suporte associado a cada mensagem
  - Sugestão de ação para o usuário incluída nas mensagens de erro

- **9.2 Logs Operacionais**
  - Eventos e informações registradas em log especificados
  - Níveis de log (debug, info, warn, error) por cenário
  - Procedimento de consulta a logs para troubleshooting

### 🔄 10. Versionamento e Rastreabilidade
- **10.1 Gestão de Requisitos**
  - Requisitos funcionais e não-funcionais rastreados
  - Matriz de rastreabilidade (requisito → funcionalidade → teste) mantida
  - Mudanças em requisitos registradas com justificativa e data

- **10.2 Histórico de Documentação**
  - Data, autor e versão de cada documento registrados
  - Changelog funcional por release mantido
  - Documentação antiga preservada com identificação de obsoleto

### ✅ 11. Critérios de Aceitação e Homologação
- **11.1 Critérios por Funcionalidade**
  - Definição de "pronto" (DoR e DoD) documentada
  - Critérios de aceite escritos em linguagem de negócio
  - Condições para considerar uma funcionalidade encerrada

- **11.2 Homologação**
  - Checklist de homologação por release mantido
  - Evidências de aprovação funcional arquivadas
  - Procedimento de validação de correções (regressão) definido

### 📚 12. Manuais e Treinamento
- **12.1 Manual do Usuário**
  - Manual completo com capturas de tela e passo a passo
  - Glossário de termos do sistema disponível
  - Guia rápido (quick start) para funcionalidades principais

- **12.2 Material de Treinamento**
  - Apresentações e vídeos de treinamento mantidos
  - Base de conhecimento (FAQ, artigos) atualizada
  - Script de treinamento para novos operadores N1 e N2

### 🧪 13. Casos de Negócio Específicos
- **13.1 Cenários Sazonais**
  - Processos que ocorrem em períodos específicos (fechamento, safra) documentados
  - Ações preparatórias para eventos sazonais registradas
  - Contatos e dependências externas para esses cenários mapeados

- **13.2 Regulatórios e Compliance**
  - Funcionalidades sujeitas a auditoria identificadas
  - Trilhas de auditoria habilitadas e documentadas
  - Obrigações legais e prazos de retenção registrados

