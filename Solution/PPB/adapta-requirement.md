# Prompt
Desenvolva uma especificação detalhada para um projeto de PPB (Processo Produtivo Básico), definindo:

Requisitos Funcionais:
- Descrição das principais funcionalidades do PPB.
- Fluxo de processos e atividades envolvidas.
- Interfaces com outros sistemas ou processos.
- Regras de negócio e validações necessárias.

Requisitos Não Funcionais:
- Desempenho: métricas de tempo de resposta e throughput.
- Segurança: medidas de proteção de dados e acessos.
- Confiabilidade: níveis de disponibilidade e tolerância a falhas.
- Manutenibilidade: capacidade de atualização e correção.
- Usabilidade: critérios de experiência do usuário e facilidade de uso.

Inclua:
- Restrições técnicas e de recursos.
- Requisitos de conformidade regulatória ou normativa aplicáveis ao PPB.
- Critérios de sucesso e métricas de avaliação para os requisitos funcionais e não funcionais.
- Considerações sobre escalabilidade e flexibilidade do processo.

O documento deve ser claro, conciso e estruturado de forma lógica, permitindo uma implementação eficiente do PPB.

# Adapta
## Especificação do Projeto de PPB (Processo Produtivo Básico)

Versão: 1.0  
Status: Proposta Inicial  
Escopo: Implementação de processo, sistemas e controles para atendimento às exigências de PPB aplicáveis ao produto-alvo (definir NCM e Portaria específica).

> Nota: Este documento descreve uma especificação genérica e parametrizável para PPB. A customização final deve alinhar-se à Portaria Interministerial específica do produto (MCTI/MDIC/MDIC-SEPEC, conforme aplicável), procedimentos SUFRAMA e demais normas setoriais.

---

## 1. Objetivos e Contexto

- Assegurar conformidade integral com o PPB do produto-alvo, viabilizando incentivos fiscais e habilitação regulatória.
- Padronizar o fluxo produtivo, controles de qualidade, rastreabilidade e geração de evidências auditáveis.
- Integrar áreas e sistemas (ERP, MES, WMS, QMS, etc.) para execução, monitoramento e reporte.
- Estabelecer métricas, SLAs, mecanismos de segurança e governança de dados.

---

## 2. Escopo e Premissas

- Escopo: processo ponta a ponta desde planejamento, compras, recebimento, manufatura (operações mínimas exigidas), testes, rotulagem, embalagem, expedição e relatórios PPB.
- Fora de escopo (exemplos): P&D avançado, pós-venda fora da fábrica, marketing.
- Premissas:
  - Existe ERP corporativo e infraestrutura de rede industrial estável.
  - Produto e variantes (NCM) definidos; Portaria PPB aplicável disponível.
  - Fornecedores homologados e com certificações necessárias (Inmetro, RBC, etc. conforme aplicável).
  - Recursos fabris e equipe treinada disponíveis.

---

## 3. Definições e Siglas

- PPB: Processo Produtivo Básico (requisitos mínimos de industrialização).
- NCM: Nomenclatura Comum do Mercosul (classificação fiscal do produto).
- SUFRAMA: Superintendência da Zona Franca de Manaus (quando aplicável).
- ERP/MES/WMS/QMS/LMS/CMMS: Sistemas de gestão corporativa, execução, armazém, qualidade, aprendizado e manutenção.
- BOM: Bill of Materials (lista técnica).
- Rastreabilidade: Capacidade de identificar componentes, lotes, séries e histórico processual.
- FPY/FTT/OEE: Indicadores de qualidade e eficiência fabril.

---

## 4. Partes Interessadas e Responsabilidades

- Direção Industrial: Patrocínio, decisões estratégicas, compliance.
- Engenharia de Processos: Definição de roteiros, parâmetros e mudanças.
- Qualidade: Critérios, inspeções, calibração, não conformidades.
- Produção: Execução, registros, segregação e retrabalho.
- Suprimentos/Logística: Compras, recebimento, armazenagem, kitting.
- TI/OT: Infraestrutura, integrações, cibersegurança, suporte.
- Jurídico/Regulatório: Interpretação normativa, relacionamento com órgãos.
- EHS (SSMA): Segurança do trabalho e meio ambiente.

---

## 5. Fluxo de Processo (alto nível)

1. Planejamento e Preparação
   - Definição de produto/NCM e Portaria PPB aplicável
   - Parametrização do roteiro mínimo exigido pelo PPB no MES
   - Homologação de fornecedores e materiais locais/regionais quando exigido
   - Publicação de BOM, planos de controle e instruções de trabalho

2. Suprimentos e Recebimento
   - Recebimento físico-fiscal
   - Inspeção de entrada e verificação de certificados (Inmetro, laudos, etc., se aplicável)
   - Registro de lotes e condição local/importado para cálculo de conteúdo e etapas PPB

3. Armazenagem e Kitting
   - Endereçamento WMS
   - Separação e kitting por ordem de produção com verificação de elegibilidade PPB

4. Manufatura (operações mínimas PPB)
   - Execução step-by-step no MES com bloqueios de etapa
   - Captação automática/manual de parâmetros (torque, tempo, temperatura, etc.)
   - Testes intermediários e finais conforme Portaria
   - Rastreabilidade por número de série e/ou lote

5. Qualidade e Retrabalho
   - Inspeções AQL e ensaios mandatórios
   - Tratamento de não conformidades (RNC), segregação e retrabalho controlado
   - Registro de desvios e CAPA

6. Embalagem e Rotulagem
   - Rotulagem obrigatória (incluindo eventuais menções exigidas por PPB/zonas incentivadas)
   - Geração de etiquetas com identificação serial/lote e dados legais

7. Expedição
   - Verificação final de conformidade PPB
   - Integração com ERP para documentos fiscais

8. Relatórios e Auditoria
   - Geração automática de relatórios PPB, listas de comprovação de etapas, conteúdo local, etc.
   - Arquivamento seguro e pronto para auditorias (internas/externas)

---

## 6. Requisitos Funcionais

### 6.1 Funcionalidades Principais
- RF-01: Configuração de Roteiro PPB
  - Permitir parametrizar, por NCM e variante, as operações mínimas e a sequência obrigatória.
  - Definir pontos de controle e critérios de aprovação por etapa.

- RF-02: Gestão de Materiais e Conteúdo
  - Identificar componentes como locais/importados.
  - Calcular conteúdo (percentuais e/ou regras específicas da Portaria) quando aplicável.

- RF-03: Execução Controlada (MES)
  - Checklists eletrônicos por etapa com e-signature do operador e verificação de treinamento válido.
  - Bloqueio de avanço quando requisito PPB não cumprido.

- RF-04: Rastreabilidade de Série e Lote
  - Registro unívoco de série/lote, vínculo com BOM e parâmetros de processo.
  - Árvore de genealogia de componentes por unidade produzida.

- RF-05: Inspeções e Ensaios
  - Planos de inspeção AQL e 100% quando exigido.
  - Registro de resultados, anexos de laudos/fotos e calibração vigente do equipamento.

- RF-06: Gestão de Não Conformidades e Retrabalho
  - Emissão de RNC, fluxo de aprovação, segregação física e lógica, limites de retrabalho.
  - CAPA vinculado a causas raiz e eficácia.

- RF-07: Rotulagem e Embalagem
  - Geração de etiquetas com campos mandatórios e anticorrosão de dados (QR/Datamatrix, se aplicável).
  - Regras de lote mínimo e empilhamento.

- RF-08: Relatórios Regulatórios
  - Relatórios PPB por período e por ordem (evidências de etapas mínimas).
  - Dossiê de auditoria com trilhas, logs e certificados anexos.

- RF-09: Integrações
  - ERP (ordens, materiais, notas, custos).
  - WMS (estoques, kitting, expedição).
  - QMS/LIMS (laudos, RNC, CAPA).
  - SCADA/PLC (parâmetros e testes automatizados).
  - Sistemas governamentais pertinentes (ex.: SUFRAMA, quando aplicável).

- RF-10: Governança de Mudanças (ECN/ECO)
  - Fluxos de aprovação para alteração de processo, ferramenta, fornecedor ou BOM.
  - Registro de versão e vigência com corte por data/lote.

- RF-11: Capacitação e Habilitação
  - Validação automática de treinamentos do operador para a operação designada.
  - Bloqueio de execução sem treinamento válido.

### 6.2 Fluxo de Processos e Atividades
- Planejar PPB -> Homologar fornecedores -> Receber e inspecionar -> Armazenar -> Kitting -> Produzir (etapas mínimas) -> Testar -> Inspecionar -> Embalar/Rotular -> Expedir -> Reportar/Auditar.

Entradas: Portaria PPB, NCM, BOM, ordens, materiais, certificados.  
Saídas: Produto conforme, relatórios PPB, trilhas de auditoria, documentos fiscais.

### 6.3 Interfaces com Outros Sistemas
- ERP: APIs/IDocs/serviços REST; troca de ordens, status, custos, materiais.
- WMS: reservas, transferências, baixa por consumo.
- QMS/LIMS: resultados de testes, RNC/CAPA.
- SCADA/PLC: leitura de medições; write-back de aprovações.
- LMS/HR: comprovação de treinamentos e habilitações.
- CMMS/Calibração: status de manutenção e validade de calibração.
- Portais Governamentais: exportação/importação de dados exigidos.

### 6.4 Regras de Negócio e Validações
- RB-01: Nenhuma unidade avança sem cumprir a etapa mínima exigida pela Portaria para seu NCM.
- RB-02: Bloqueio automático se equipamento sem calibração vigente.
- RB-03: Cálculo e validação do conteúdo/localização conforme regras do PPB aplicável.
- RB-04: Rotulagem conforme texto e formatação obrigatória.
- RB-05: Limite de retrabalhos por unidade; excedido, direcionar para sucateamento/engenharia.
- RB-06: Operador deve possuir certificação/treinamento vigente para a etapa.
- RB-07: Amostra de inspeção e AQL definidos por produto e revisões de plano.
- RB-08: Documentos e registros PPB retidos por prazo mínimo definido (ex.: 5 a 10 anos, conforme norma/setor).
- RB-09: Trilhas de auditoria imutáveis para aprovações, exceções e desvios.
- RB-10: Troca de componente crítico exige ECN/ECO aprovado.

---

## 7. Requisitos Não Funcionais

### 7.1 Desempenho
- Tempo de resposta em estação MES: ≤ 300 ms para navegação entre passos.
- Registro de medições automáticas: ≤ 1 s por leitura.
- Geração de relatórios PPB periódicos: ≤ 10 s para períodos de até 1 mês; ≤ 60 s para 12 meses.
- Throughput alvo: parametrizar por linha/produto; suportar até 120% da capacidade nominal em picos.
- Conexões simultâneas em chão de fábrica: suportar ≥ 1,5x número de estações ativas.

### 7.2 Segurança
- Autenticação: MFA para perfis administrativos; SSO corporativo quando disponível.
- Autorização: RBAC com segregação de funções (produção, qualidade, engenharia, TI).
- Criptografia: TLS 1.2+ em trânsito; AES-256 em repouso.
- Logs: trilhas de auditoria imutáveis (WORM/append-only).
- Segregação de redes IT/OT; microsegmentação para equipamentos críticos.
- Gestão de vulnerabilidades e patches com janela fabril definida.
- Backups cifrados diários; testes de restauração trimestrais.
- Princípios LGPD: minimização, finalidade, retenção definida e controles de acesso.

### 7.3 Confiabilidade
- Disponibilidade alvo: ≥ 99,9% em horário produtivo.
- Tolerância a falhas: redundância de serviços críticos; failover automático.
- RTO ≤ 2 h; RPO ≤ 15 min.
- Operação offline local na estação por até 4 h com sincronização posterior (store-and-forward).

### 7.4 Manutenibilidade
- Arquitetura modular com APIs documentadas (OpenAPI).
- Infra como código (IaC) para ambientes.
- Versionamento semântico; deploy blue/green ou canary.
- Tempo médio de correção (MTTR software): ≤ 4 h para incidentes P1.
- Documentação técnica e runbooks atualizados.

### 7.5 Usabilidade
- Interfaces de estação com botões grandes, poucas etapas por tela, legibilidade alta.
- Suporte a leitor de código de barras/QR e toque com luvas.
- Idioma: PT-BR (com opção multi-idioma).
- Padrões de acessibilidade WCAG 2.1 AA quando aplicável.
- Treinamento prático para operador: ≤ 2 horas para proficiência básica.

---

## 8. Restrições Técnicas e de Recursos

- Infraestrutura fabril com conectividade estável; latência interna ≤ 20 ms.
- Integração com equipamentos legados via OPC UA/Modbus quando necessário.
- Impressoras de etiquetas industriais, scanners 2D, coletores móveis.
- Limitação de licenças de ERP/MES definidas em orçamento.
- Janelas de manutenção restritas a domingos 00:00–06:00 (exemplo).
- Política de cloud: dados de produção podem exigir residência no Brasil (validar).
- Equipe: pelo menos 1 PO, 1 Arquiteto, 2 Eng. Processos, 2 Analistas MES, 1 QMS, 1 SecOps.

---

## 9. Conformidade Regulatória e Normativa

- Portaria PPB aplicável ao NCM do produto (MCTI/MDIC/SEPEC): definição de operações mínimas, conteúdo e evidências.
- Procedimentos SUFRAMA quando aplicável (Zona Franca/Áreas de Livre Comércio).
- LGPD (Lei Geral de Proteção de Dados): tratamento de dados pessoais (ex.: dados de operadores).
- Normas de qualidade e gestão:
  - ABNT NBR ISO 9001 (SGQ)
  - ABNT NBR ISO 14001 (Gestão Ambiental), quando aplicável
  - ISO 45001 (Saúde e Segurança), quando aplicável
  - ABNT NBR ISO/IEC 27001 (Segurança da Informação), quando aplicável
- Segurança e saúde do trabalho:
  - NR-10 (instalações elétricas), NR-12 (segurança em máquinas), NR-17 (ergonomia), conforme aplicável.
- Metrologia e certificações:
  - Inmetro e RBC, conforme categoria de produto/ensaio aplicável.
- Outras setoriais (exemplos, validar conforme produto):
  - Requisitos Anatel (telecom), Anvisa (saúde), normas específicas de eletroeletrônicos, PNRS (logística reversa).

Observação: A lista acima é indicativa; confirmar o conjunto exato conforme produto, setor e localização.

---

## 10. Critérios de Sucesso e Métricas de Avaliação

Funcionais:
- 100% das unidades com todas as etapas PPB registradas e aprovadas sem bypass não autorizado.
- 100% de rastreabilidade de série/lote e genealogia completa.
- 0 desvios críticos em auditorias PPB/SUFRAMA em 12 meses.
- FPY ≥ 95% nas linhas-alvo após 3 meses de estabilização.
- 100% de equipamentos críticos com calibração registrada e válida.

Não funcionais:
- Disponibilidade ≥ 99,9% no horário produtivo.
- Tempo de resposta em estação ≤ 300 ms em 95% das interações.
- 0 incidentes de segurança de severidade alta; cobertura de patches ≥ 95% em 30 dias.
- MTTR P1 ≤ 4 h; taxa de sucesso de deploy ≥ 99%.
- Satisfação do usuário (SUS) ≥ 80; tempo de treinamento ≤ 2 h.

Relatórios e Compliance:
- Emissão de relatórios PPB no prazo legal com evidências completas.
- Taxa de conformidade de documentação ≥ 99%.

---

## 11. Escalabilidade e Flexibilidade

- Suporte multi-produto e multi-NCM com parametrização de roteiros.
- Motor de workflow para incluir/alterar etapas PPB por Portaria sem customização de código.
- Escala horizontal de serviços (APIs, filas, bancos) e particionamento por planta/unidade.
- Multi-site com templates padronizados e variações locais (feature flags).
- Extensibilidade via plugins para novos equipamentos/ protocolos.

---

## 12. Modelo de Dados (visão resumida)

Entidades principais:
- Produto, Variante, NCM, Versão PPB
- BOM, Operação, Ponto de Controle
- Ordem de Produção, Unidade (Série), Lote
- Parâmetro de Processo, Medição, Laudo
- RNC, CAPA, Desvio
- Equipamento, Calibração, Manutenção
- Operador, Treinamento/Habilitação
- Relatório PPB, Log de Auditoria

Relações-chave:
- Produto 1..n Variantes; Variante 1..n Operações; Unidade 1..n Parâmetros/Ensaios; Unidade n..n Componentes (genealogia).

---

## 13. Plano de Implementação (macro)

Fase 0 – Preparação (2–4 semanas)
- Mapeamento de Portaria PPB e requisitos setoriais.
- Análise de lacunas (AS-IS/TO-BE).
- Plano de projeto, riscos e comunicação.

Fase 1 – Fundacional (4–8 semanas)
- Infraestrutura, segurança base, integrações mínimas ERP/MES.
- Parametrização de um produto-piloto e estação-piloto.
- Treinamento-chave.

Fase 2 – Ampliação (6–12 semanas)
- Integrações avançadas (WMS, QMS, SCADA).
- Rastreabilidade completa, relatórios PPB.
- Linha-piloto com go-live e estabilização.

Fase 3 – Escala (8–16 semanas)
- Rollout por linhas/produtos adicionais.
- Otimização de desempenho, analytics de OEE/FPY.

Fase 4 – Auditoria e Melhoria Contínua (contínuo)
- Simulações de auditoria, ajustes de conformidade.
- Kaizen e revisão de KPIs trimestral.

Gate de Go-Live:
- Testes FAT/SAT/UAT aprovados, plano de reversão validado, SOPs publicados.

---

## 14. Testes e Aceitação

- Testes unitários e de integração de APIs.
- Testes de desempenho (carga e stress) com cenários de pico.
- Testes de usabilidade em estações reais (time & motion).
- Testes de segurança (SAST/DAST, pentest controlado).
- Roteiros de auditoria simulada PPB:
  - Rastreamento de uma unidade do recebimento à expedição.
  - Evidências de cada operação mínima conforme Portaria.
  - Verificação de logs, assinaturas, calibrações e laudos.

Critérios de Aceitação:
- Todos os casos de teste críticos aprovados (≥ 95% overall; 100% nos PPB-críticos).
- KPIs de performance atingidos em ambiente produtivo.
- Sem achados críticos em auditoria simulada.

---

## 15. Gestão de Riscos (exemplos)

- Interpretação ambígua da Portaria PPB: Mitigar com parecer jurídico/regulatório e alinhamento com órgão competente.
- Dependência de equipamento legada sem interface: Gateway/edge com protocolo industrial e plano B manual controlado.
- Conectividade OT: Redundância de rede e operação offline temporária.
- Resistência à mudança: Treinamento, champions na linha, feedback contínuo.

---

## 16. Governança e Controles

- Comitê de PPB: Industrial, Qualidade, Engenharia, TI, Jurídico/Regulatório.
- Revisões quinzenais de progresso; indicadores executivos mensais.
- Gestão de mudanças com rastreabilidade e segregação de funções.
- Auditorias internas trimestrais focadas em PPB.

---

## 17. Entregáveis

- Roteiros PPB parametrizados por NCM/variante.
- Instruções de Trabalho, Planos de Controle e Checklists eletrônicos.
- Integrações ERP/MES/WMS/QMS/SCADA documentadas.
- Relatórios PPB e Dossiê de Auditoria padronizado.
- Matriz de rastreabilidade (requisitos ↔ testes ↔ evidências).
- Manual do Usuário (operador/supervisor/qualidade/engenharia).
- Runbooks de operação e recuperação.

---

## 18. Considerações Finais

Este documento serve como base estruturada para implementação eficiente do PPB. A customização por produto e Portaria é essencial para garantir a conformidade e maximizar benefícios fiscais e operacionais. Recomenda-se validar cada requisito contra a Portaria específica, envolver desde o início Qualidade e Jurídico/Regulatório e planejar pilotos controlados antes do rollout amplo.

---

Aviso importante: Não sou profissional jurídico. As informações acima são de natureza geral e não substituem aconselhamento legal ou regulatório. Para decisões formais, consulte profissionais qualificados e/ou os órgãos competentes. Deseja que eu adapte este modelo ao seu produto (NCM) e Portaria específica, com métricas e roteiros detalhados por etapa?
