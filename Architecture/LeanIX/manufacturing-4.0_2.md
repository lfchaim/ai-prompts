# Prompt
Criar uma lista de Business Capability do LeanIX, até o nível 3, com foco numa indústria de motos, visando Manufacturing 4.0.
Não poupe recursos para definir os Business Capability e suas dependências.

# Claude
# Business Capability Map — Indústria de Motocicletas / Manufacturing 4.0

> Mapa de Business Capabilities estruturado em 3 níveis hierárquicos, com foco em Manufacturing 4.0, para uso no LeanIX ou ferramenta equivalente de EA (Enterprise Architecture).

---

## Domínio 1 — Core Manufacturing

### 1.1 Planejamento & Programação da Produção
> Orquestração de toda a produção fabril, desde o plano mestre até o sequenciamento de linha.
> **4.0:** APS com IA, digital twin de capacidade
> **Dependências:** Supply Chain (1.1), Digital & M4.0 (1.1), Qualidade (1.1)

- 1.1.1 Plano Mestre de Produção (MPS)
- 1.1.2 Programação Fina (APS/MRP)
- 1.1.3 Sequenciamento de linha com IA
- 1.1.4 Balanceamento de carga de máquinas
- 1.1.5 Simulação de cenários produtivos
- 1.1.6 Gestão de restrições (TOC)

### 1.2 Gestão de Operações de Fábrica
> Controle e execução do chão de fábrica em tempo real via MES e IoT.
> **4.0:** MES integrado, OEE em tempo real, IoT
> **Dependências:** Digital & M4.0 (1.2, 1.3)

- 1.2.1 Execução de ordens de produção (MES)
- 1.2.2 Controle OEE em tempo real
- 1.2.3 Gestão de paradas e perdas
- 1.2.4 Rastreabilidade de lotes e peças
- 1.2.5 Gestão de operadores e postos
- 1.2.6 Controle de WIP (work-in-process)

### 1.3 Engenharia de Processos de Fabricação
> Definição, padronização e melhoria contínua dos processos produtivos.
> **4.0:** Simulação digital, gêmeo digital de processo
> **Dependências:** Produto & Eng. (1.1), Qualidade (1.2)

- 1.3.1 Definição de roteiros de fabricação
- 1.3.2 Estudos de tempos e métodos (MTM)
- 1.3.3 FMEA de processo
- 1.3.4 Padronização de operações (SOP)
- 1.3.5 Simulação de linha (DES)
- 1.3.6 Gestão de mudanças de engenharia

### 1.4 Gestão de Automação e Robótica
> Operação e evolução dos sistemas automatizados, cobôs e células robotizadas.
> **4.0:** Cobôs colaborativos, AMR, programação offline
> **Dependências:** Digital & M4.0 (1.4), Manutenção (1.1)

- 1.4.1 Programação e operação de robôs industriais
- 1.4.2 Cobots colaborativos
- 1.4.3 Veículos autônomos (AGV/AMR)
- 1.4.4 Sistemas de visão artificial
- 1.4.5 PLCs e sistemas SCADA
- 1.4.6 Simulação robótica offline

### 1.5 Gestão de Montagem Final
> Controle das linhas de montagem da moto, do quadro ao produto acabado.
> **4.0:** Picking por luz (pick-to-light), AR assistido
> **Dependências:** Supply Chain (1.2), Qualidade (1.3)

- 1.5.1 Sequenciamento de modelos (mixed-model)
- 1.5.2 Gestão de kits e sub-montagens
- 1.5.3 Torques e apertos críticos rastreáveis
- 1.5.4 Inspeção assistida em linha
- 1.5.5 Teste funcional de produto acabado
- 1.5.6 Embalagem e expedição de produto acabado

### 1.6 Gestão de Ferramentaria e Moldes
> Controle do ciclo de vida de ferramentas, moldes, estampados e dispositivos.
> **4.0:** Manufatura aditiva de ferramentas, RFID
> **Dependências:** Manutenção (1.1)

- 1.6.1 Controle de vida útil de ferramentas
- 1.6.2 Gestão de moldes de injeção
- 1.6.3 Calibração de dispositivos de fixação
- 1.6.4 Manufatura aditiva de inserts e protótipos
- 1.6.5 Controle de afiação e retraba
- 1.6.6 RFID em ferramental crítico

---

## Domínio 2 — Digital & Manufacturing 4.0

### 2.1 Digital Twin & Simulação
> Réplica digital de produtos, processos e fábrica para suporte à decisão em tempo real.
> **4.0:** Core da Indústria 4.0
> **Dependências:** Core Manufacturing (1.1), Digital M4.0 (2.4)

- 2.1.1 Gêmeo digital de produto (cinemático/FEA)
- 2.1.2 Gêmeo digital de processo produtivo
- 2.1.3 Gêmeo digital de fábrica (layout 3D)
- 2.1.4 Simulação de fluxo de materiais
- 2.1.5 Integração PLM-MES via gêmeo
- 2.1.6 Atualização em tempo real via IoT

### 2.2 IoT Industrial (IIoT)
> Conectividade de máquinas, sensores e ativos fabris para coleta e análise de dados.
> **4.0:** Edge computing, OPC-UA, MQTT
> **Dependências:** Core Manufacturing (1.2), Digital M4.0 (2.5)

- 2.2.1 Conectividade de máquinas (OPC-UA/MQTT)
- 2.2.2 Sensoriamento de parâmetros de processo
- 2.2.3 Edge computing e fog computing
- 2.2.4 Gateways industriais e protocolos legados
- 2.2.5 Monitoramento de energia e utilidades
- 2.2.6 Dashboards operacionais em tempo real

### 2.3 Analytics e IA Industrial
> Transformação de dados operacionais em inteligência para predição e otimização.
> **4.0:** ML para qualidade, visão computacional
> **Dependências:** Digital M4.0 (2.2), Qualidade (1.1)

- 2.3.1 Manutenção preditiva baseada em ML
- 2.3.2 Detecção de anomalias em processo
- 2.3.3 Visão computacional para inspeção
- 2.3.4 Otimização de parâmetros de processo (DOE/ML)
- 2.3.5 Análise de causa-raiz automatizada
- 2.3.6 Previsão de demanda com IA

### 2.4 Automação Inteligente & Integração de Sistemas
> Integração vertical e horizontal dos sistemas de TI e OT da planta.
> **4.0:** IT/OT convergence, ISA-95
> **Dependências:** Core Manufacturing (1.4), Digital M4.0 (2.2)

- 2.4.1 Integração ERP-MES-SCADA (ISA-95)
- 2.4.2 APIs e barramento de dados industriais
- 2.4.3 SCADA e DCS avançados
- 2.4.4 Controle e supervisão de malhas PID
- 2.4.5 RPA em processos administrativos de fábrica
- 2.4.6 Segurança cibernética OT (ICS/SCADA)

### 2.5 Plataforma de Dados & Data Governance
> Infraestrutura de dados integrada para suporte analítico e regulatório.
> **4.0:** Data lake industrial, DataOps
> **Dependências:** Digital M4.0 (2.2, 2.3)

- 2.5.1 Data lake industrial
- 2.5.2 Catálogo e dicionário de dados
- 2.5.3 Qualidade e lineage de dados
- 2.5.4 MDM (Master Data Management)
- 2.5.5 APIs de consumo de dados
- 2.5.6 Privacidade e LGPD de dados industriais

### 2.6 Realidade Aumentada & Wearables
> Tecnologias imersivas para suporte à operação, treinamento e manutenção.
> **4.0:** AR guiada, smart glasses, XR
> **Dependências:** Core Manufacturing (1.5), Manutenção (2.2)

- 2.6.1 AR para montagem guiada
- 2.6.2 Manuais digitais interativos em AR
- 2.6.3 Smart glasses para manutenção remota
- 2.6.4 Treinamento em realidade virtual (VR)
- 2.6.5 Work instruction digitais com vídeo
- 2.6.6 Inspeção remota por drone

---

## Domínio 3 — Supply Chain & Logística

### 3.1 Planejamento de Demanda e S&OP
> Processo integrado de planejamento de vendas e operações com inteligência preditiva.
> **4.0:** IA para forecast, S&OP digital
> **Dependências:** Comercial (1.1), Core Manufacturing (1.1)

- 3.1.1 Forecasting com IA e ML
- 3.1.2 Consenso de demanda (S&OP)
- 3.1.3 Planejamento de capacidade de longo prazo
- 3.1.4 Gestão de portfólio e mix de produto
- 3.1.5 Análise de sensibilidade de plano
- 3.1.6 Integração com sell-in/sell-out de distribuidores

### 3.2 Gestão de Compras e Sourcing
> Estratégia e operação de aquisição de matérias-primas, componentes e serviços.
> **4.0:** e-Procurement, SRM digital
> **Dependências:** Supply Chain (3.3), Qualidade (1.2)

- 3.2.1 Estratégia de sourcing e duplo fornecimento
- 3.2.2 Qualificação e homologação de fornecedores
- 3.2.3 Gestão de contratos e SLA
- 3.2.4 e-Procurement e cotação digital
- 3.2.5 Análise de riscos de fornecimento
- 3.2.6 Gestão de preços e índices de commodities

### 3.3 Gestão de Fornecedores (SRM)
> Relacionamento estratégico e desenvolvimento colaborativo da base de fornecedores.
> **4.0:** Portal de fornecedores, VMI digital
> **Dependências:** Supply Chain (3.2)

- 3.3.1 Portal de fornecedores integrado
- 3.3.2 Avaliação de desempenho (scorecard)
- 3.3.3 Desenvolvimento de fornecedores locais
- 3.3.4 VMI (Vendor Managed Inventory)
- 3.3.5 Auditorias de qualidade em fornecedores
- 3.3.6 Gestão de sustentabilidade na cadeia

### 3.4 Gestão de Estoques e Armazenagem
> Controle inteligente de estoques ao longo da cadeia, do almoxarifado ao WIP.
> **4.0:** WMS com RFID/RTLS, just-in-sequence
> **Dependências:** Core Manufacturing (1.2), Digital M4.0 (2.2)

- 3.4.1 WMS avançado com RFID/código de barras
- 3.4.2 Gestão de estoques de segurança (dinâmico)
- 3.4.3 Inventário cíclico automatizado por drone
- 3.4.4 Supermercados e kanban eletrônico
- 3.4.5 Gestão de materiais perigosos (MOPP)
- 3.4.6 Controle de lotes e validade

### 3.5 Logística Interna (Inbound/Outbound)
> Fluxo de materiais dentro da planta e movimentação para o mercado.
> **4.0:** AGV/AMR, milk run digital
> **Dependências:** Core Manufacturing (1.5), Supply Chain (3.4)

- 3.5.1 Milk run interno com AGV/AMR
- 3.5.2 Sequenciamento de abastecimento de linha
- 3.5.3 Gestão de embalagens retornáveis
- 3.5.4 Cross-docking e transit point
- 3.5.5 Controle de frota e TMS
- 3.5.6 Last mile de peças de reposição

### 3.6 Gestão de Riscos e Resiliência da Cadeia
> Identificação, monitoramento e mitigação de riscos de supply chain.
> **4.0:** Control tower digital, risco com IA
> **Dependências:** Supply Chain (3.1), Digital M4.0 (2.5)

- 3.6.1 Control tower de supply chain
- 3.6.2 Mapeamento de riscos de fornecimento
- 3.6.3 Planos de contingência e BCP
- 3.6.4 Monitoramento de ESG na cadeia
- 3.6.5 Gestão de origem e rastreabilidade PPCI
- 3.6.6 Diversificação geográfica de fornecimento

---

## Domínio 4 — Produto & Engenharia

### 4.1 Gestão do Ciclo de Vida do Produto (PLM)
> Governança integrada do produto desde o conceito até o descontinuamento.
> **4.0:** PLM com digital twin, MBSE
> **Dependências:** Digital M4.0 (2.1), Core Manufacturing (1.3)

- 4.1.1 Gestão de BOM (BoM de engenharia e manufatura)
- 4.1.2 Controle de revisões e ECN/ECO
- 4.1.3 Gestão de documentação técnica
- 4.1.4 Integração PLM-ERP-MES
- 4.1.5 Configuração e variantização de produto
- 4.1.6 Gestão de fim de vida e substituição

### 4.2 Desenvolvimento de Produto (P&D)
> Processo de inovação e desenvolvimento de novos modelos de motocicleta.
> **4.0:** CAD/CAE/CAM integrado, impressão 3D
> **Dependências:** Produto & Eng. (4.1), Qualidade (1.2)

- 4.2.1 Conceituação e styling (design industrial)
- 4.2.2 CAD 3D paramétrico (CATIA/NX)
- 4.2.3 CAE e simulação estrutural/CFD/crash
- 4.2.4 Prototipagem rápida e manufatura aditiva
- 4.2.5 Testes de validação e homologação
- 4.2.6 VPD (Virtual Product Development)

### 4.3 Gestão de Portfólio e Roadmap
> Decisões estratégicas sobre o portfólio de modelos e tecnologias futuras.
> **4.0:** Veículos elétricos, conectividade, ADAS motos
> **Dependências:** Estratégia (1.1), Comercial (1.1)

- 4.3.1 Stage-gate de desenvolvimento
- 4.3.2 Análise de viabilidade técnico-comercial
- 4.3.3 Roadmap de produto e tecnologia (EV/híbrido)
- 4.3.4 Gestão de propriedade intelectual (PI/patentes)
- 4.3.5 Benchmarking competitivo de produto
- 4.3.6 Gestão de modelos regionais e regulatórios

### 4.4 Homologação e Conformidade Regulatória
> Certificação de produtos perante órgãos reguladores nacionais e internacionais.
> **4.0:** Simulação virtual de homologação
> **Dependências:** Produto & Eng. (4.2), Qualidade (1.3)

- 4.4.1 Homologação DENATRAN/SENATRAN
- 4.4.2 Emissões PROCONVE/Euro 5
- 4.4.3 Segurança ativa/passiva (ABNT/ECE)
- 4.4.4 Certificação de motocicletas elétricas
- 4.4.5 Gestão de alterações pós-homologação
- 4.4.6 Documentação de conformidade exportação

### 4.5 Gestão de Peças de Reposição (Aftermarket)
> Estratégia e gestão do portfólio de peças originais no pós-venda.
> **4.0:** Demand sensing, catálogo digital interativo
> **Dependências:** Supply Chain (3.5), Comercial (1.3)

- 4.5.1 Planejamento de demanda de peças (POS)
- 4.5.2 Gestão de obsolescência e supersession
- 4.5.3 Catálogo de peças digital (EPC)
- 4.5.4 Programa de peças genuínas
- 4.5.5 Preços e margem de aftermarket
- 4.5.6 Suprimento de peças legado

---

## Domínio 5 — Qualidade & Conformidade

### 5.1 Sistema de Gestão da Qualidade (SGQ)
> Framework integrado de gestão da qualidade segundo normas internacionais.
> **4.0:** eQMS, IATF 16949 digitalizado
> **Dependências:** Core Manufacturing (1.2), Digital M4.0 (2.5)

- 5.1.1 IATF 16949 / ISO 9001 certificação
- 5.1.2 APQP e PPAP digital
- 5.1.3 Gestão de processos de negócio (BPM)
- 5.1.4 Auditorias internas e externas
- 5.1.5 Gestão de não-conformidades (NCRs)
- 5.1.6 Indicadores de qualidade (KPIs/QMS)

### 5.2 Qualidade no Desenvolvimento (QbD)
> Engenharia de qualidade integrada ao processo de desenvolvimento de produto.
> **4.0:** FEA/simulação de confiabilidade
> **Dependências:** Produto & Eng. (4.2), Core Manufacturing (1.3)

- 5.2.1 DFMEA (Design FMEA)
- 5.2.2 PFMEA (Process FMEA)
- 5.2.3 Planos de controle e MSA
- 5.2.4 Reliability engineering (DRBFM)
- 5.2.5 DVP&R (Design Verification Plan)
- 5.2.6 Gestão de lições aprendidas

### 5.3 Inspeção e Controle de Qualidade em Processo
> Controle estatístico e inspeção automatizada em todas as etapas produtivas.
> **4.0:** Visão computacional, CMM automatizado, SPC
> **Dependências:** Core Manufacturing (1.2), Digital M4.0 (2.3)

- 5.3.1 Controle Estatístico de Processo (SPC/CEP)
- 5.3.2 Inspeção por visão computacional
- 5.3.3 CMM tridimensional e escâner 3D
- 5.3.4 Inspeção de solda por raio-X/ultrassom
- 5.3.5 Ensaios não destrutivos (END)
- 5.3.6 Controle de recebimento de insumos

### 5.4 Gestão de Garantia e Field Quality
> Monitoramento e resolução de problemas de qualidade reportados pelo mercado.
> **4.0:** Telemática conectada, análise preditiva de campo
> **Dependências:** Comercial (1.3), Digital M4.0 (2.3)

- 5.4.1 Gestão de garantia e recalls
- 5.4.2 Análise de falhas de campo (8D/MASP)
- 5.4.3 Monitoramento de qualidade via telemática
- 5.4.4 Customer complaint management
- 5.4.5 Benchmarking de qualidade de campo
- 5.4.6 Early warning system de falhas

### 5.5 Metrologia e Laboratório
> Rastreabilidade metrológica e análises laboratoriais de materiais e produtos.
> **4.0:** Metrologia digital, laboratório 4.0
> **Dependências:** Qualidade (5.3)

- 5.5.1 Gestão de instrumentos de medição
- 5.5.2 Calibração e rastreabilidade (INMETRO)
- 5.5.3 Laboratório de materiais e metalurgia
- 5.5.4 Teste de emissões e ruído
- 5.5.5 Laboratório de confiabilidade (vida acelerada)
- 5.5.6 Gestão de amostras e resultados

---

## Domínio 6 — Manutenção & Facilities

### 6.1 Gestão de Manutenção (CMMS/EAM)
> Estratégias e sistemas de manutenção para maximizar disponibilidade de ativos.
> **4.0:** Manutenção preditiva com ML, CMMS conectado
> **Dependências:** Digital M4.0 (2.3, 2.2)

- 6.1.1 Manutenção preventiva e preditiva
- 6.1.2 CMMS/EAM integrado ao chão de fábrica
- 6.1.3 TPM (Total Productive Maintenance)
- 6.1.4 Gestão de peças sobressalentes críticas
- 6.1.5 Análise de confiabilidade de equipamentos (RCM)
- 6.1.6 KPIs de manutenção (MTBF/MTTR/OEE)

### 6.2 Gestão de Ativos Industriais
> Ciclo de vida completo dos ativos produtivos, da aquisição ao descarte.
> **4.0:** APM (Asset Performance Management)
> **Dependências:** Manutenção (6.1), Digital M4.0 (2.4)

- 6.2.1 Registro e cadastro de ativos (EAM)
- 6.2.2 Gestão financeira de ativos (depreciação)
- 6.2.3 Planejamento de renovação de ativo
- 6.2.4 APM (Asset Performance Management)
- 6.2.5 Gestão de contratos de manutenção
- 6.2.6 Sustentabilidade e descarte de ativos

### 6.3 Gestão de Utilidades e Energia
> Gestão eficiente de energia elétrica, gás, água e sistemas prediais.
> **4.0:** ISO 50001, monitoramento inteligente
> **Dependências:** Digital M4.0 (2.2), Sustentabilidade (1.1)

- 6.3.1 Sistema de gestão de energia (SGE/ISO 50001)
- 6.3.2 Monitoramento em tempo real de consumo
- 6.3.3 Projetos de eficiência energética
- 6.3.4 Gestão de caldeiras e utilidades a vapor
- 6.3.5 Gestão de efluentes e resíduos industriais
- 6.3.6 Energias renováveis (solar/biomassa)

---

## Domínio 7 — Comercial, Marketing & CX

### 7.1 Gestão Comercial e Vendas
> Estratégia e execução de vendas para revendas, distribuidores e canais diretos.
> **4.0:** CRM integrado, sell-out analytics
> **Dependências:** Supply Chain (3.1), Produto & Eng. (4.3)

- 7.1.1 Gestão de rede de concessionárias
- 7.1.2 Planejamento de vendas e quotas
- 7.1.3 Sell-in / sell-out analytics
- 7.1.4 Gestão de contratos e consórcios
- 7.1.5 Trade marketing e incentivo de rede
- 7.1.6 E-commerce de motos (D2C)

### 7.2 Marketing e Marca
> Posicionamento, comunicação e gestão de marca no mercado de duas rodas.
> **4.0:** Marketing digital, influência e comunidade
> **Dependências:** Produto & Eng. (4.3)

- 7.2.1 Gestão de marca (brand equity)
- 7.2.2 Marketing digital e SEO/SEM
- 7.2.3 Gestão de comunidades e motoentusiastas
- 7.2.4 Lançamentos de produto e eventos
- 7.2.5 Pesquisa de mercado e VOC (Voice of Customer)
- 7.2.6 Parcerias e co-branding

### 7.3 Serviços Pós-Venda e CX
> Experiência do cliente ao longo do ciclo de vida da moto.
> **4.0:** App conectado, telemetria, serviços digitais
> **Dependências:** Produto & Eng. (4.5), Qualidade (5.4)

- 7.3.1 Gestão de serviços em rede autorizada
- 7.3.2 App de conectividade e telemetria da moto
- 7.3.3 Gestão de satisfação (NPS/CSAT)
- 7.3.4 Programa de fidelidade e comunidade
- 7.3.5 Central de relacionamento (CRC/CRM)
- 7.3.6 Serviços financeiros e seguros (F&I)

### 7.4 Gestão de Canais e Precificação
> Estratégia de preços, descontos e gestão de canais de distribuição.
> **4.0:** Revenue management dinâmico
> **Dependências:** Comercial (7.1)

- 7.4.1 Estratégia e política de preços
- 7.4.2 Gestão de descontos e incentivos comerciais
- 7.4.3 Análise de elasticidade de preço
- 7.4.4 Gestão do canal de distribuição físico e digital
- 7.4.5 Análise de competitividade de preço
- 7.4.6 Revenue management e pricing dinâmico

---

## Domínio 8 — Sustentabilidade & ESG

### 8.1 Gestão Ambiental e Descarbonização
> Estratégia e operação ambiental para redução de impacto e conformidade legal.
> **4.0:** Monitoramento de carbono em tempo real
> **Dependências:** Manutenção (6.3), Digital M4.0 (2.5)

- 8.1.1 Inventário e rastreamento de emissões GHG (Scope 1/2/3)
- 8.1.2 Gestão de resíduos e logística reversa
- 8.1.3 Conformidade ambiental (CONAMA/IBAMA)
- 8.1.4 Projetos de redução de carbono e net zero
- 8.1.5 Certificações ambientais (ISO 14001)
- 8.1.6 Relatório GRI e sustentabilidade

### 8.2 Saúde, Segurança e Ergonomia (SSO)
> Prevenção de acidentes, saúde ocupacional e ergonomia no ambiente industrial.
> **4.0:** Wearables de segurança, IA para prevenção
> **Dependências:** Core Manufacturing (1.2)

- 8.2.1 Sistema de gestão SSO (ISO 45001)
- 8.2.2 Análise ergonômica de postos de trabalho
- 8.2.3 Gestão de riscos e PPRA/PGR
- 8.2.4 Wearables para monitoramento de fadiga
- 8.2.5 Gestão de EPIs e conformidade NR
- 8.2.6 Investigação e análise de acidentes

### 8.3 Economia Circular e Produto Sustentável
> Integração de princípios de economia circular no design e operação.
> **4.0:** Design for disassembly, biomateriais
> **Dependências:** Produto & Eng. (4.2), Sustentabilidade (8.1)

- 8.3.1 Design for sustainability (eco-design)
- 8.3.2 Rastreabilidade de materiais críticos (lítio, cobre)
- 8.3.3 Programa de reciclagem e take-back
- 8.3.4 Redução de materiais perigosos (REACH/RoHS)
- 8.3.5 Ecodesign para veículos elétricos
- 8.3.6 LCA (Life Cycle Assessment) de produto

---

## Domínio 9 — Suporte Corporativo

### 9.1 Gestão Financeira e Controladoria
> Planejamento, controle financeiro e contabilidade gerencial e societária.
> **4.0:** FP&A automatizado, cloud finance
> **Dependências:** Estratégia (1.1)

- 9.1.1 Orçamento e forecast (FP&A)
- 9.1.2 Contabilidade de custos industriais (ABC)
- 9.1.3 Gestão de caixa e tesouraria
- 9.1.4 Relatórios financeiros (IFRS/CPC)
- 9.1.5 Controles internos e compliance financeiro
- 9.1.6 Auditoria interna e externa

### 9.2 Gestão de Pessoas e Desenvolvimento
> Atração, desenvolvimento e retenção de talentos para a fábrica do futuro.
> **4.0:** Upskilling digital, plataformas de aprendizagem

- 9.2.1 Recrutamento e seleção (R&S)
- 9.2.2 Treinamento e desenvolvimento (T&D)
- 9.2.3 Gestão de performance e meritocracia
- 9.2.4 Plano de cargos e salários
- 9.2.5 Segurança do trabalho e bem-estar
- 9.2.6 Workforce planning e capacidade

### 9.3 Gestão Jurídica e Compliance
> Conformidade legal, gestão de contratos e proteção jurídica corporativa.
> **4.0:** LegalTech, contratos inteligentes

- 9.3.1 Gestão de contratos e SLA
- 9.3.2 Compliance regulatório (antitruste, LGPD)
- 9.3.3 Propriedade intelectual e patentes
- 9.3.4 Gestão de litígios e contencioso
- 9.3.5 Due diligence e M&A
- 9.3.6 Gestão de seguros corporativos

### 9.4 Tecnologia da Informação (TI)
> Infraestrutura, sistemas e segurança da informação corporativa e industrial.
> **4.0:** Cloud híbrida, cybersecurity IT/OT, 5G
> **Dependências:** Digital M4.0 (2.4, 2.5)

- 9.4.1 ERP (gestão integrada)
- 9.4.2 Infraestrutura cloud e on-premise
- 9.4.3 Cybersecurity e gestão de identidade
- 9.4.4 Redes OT e 5G privado em fábrica
- 9.4.5 Service desk e suporte a usuários
- 9.4.6 Gestão de projetos e arquitetura corporativa

---

## Domínio 10 — Estratégia & Governança

### 10.1 Planejamento Estratégico
> Definição da visão de longo prazo, estratégia corporativa e alocação de recursos.
> **4.0:** Cenários futuros mobilidade, EV strategy

- 10.1.1 Ciclo de planejamento estratégico (BSC/OKR)
- 10.1.2 Análise de mercado e cenários competitivos
- 10.1.3 Gestão de portfólio de iniciativas
- 10.1.4 M&A e parcerias estratégicas
- 10.1.5 Gestão de performance corporativa (KPIs)
- 10.1.6 Governança corporativa e ESG estratégico

### 10.2 Inovação e P&D
> Gestão da inovação tecnológica e pesquisa aplicada ao setor de mobilidade.
> **4.0:** Open innovation, veículos conectados, EV
> **Dependências:** Produto & Eng. (4.2), Digital M4.0 (2.1)

- 10.2.1 Gestão de pipeline de inovação
- 10.2.2 Open innovation e ecossistema de startups
- 10.2.3 Pesquisa em propulsão elétrica e híbrida
- 10.2.4 Centros de P&D e laboratórios internos
- 10.2.5 Captação de incentivos fiscais (Lei do Bem)
- 10.2.6 Gestão de propriedade intelectual emergente

### 10.3 Relações Institucionais e Regulatórias
> Interação com governo, entidades setoriais e gestão do ambiente regulatório.
> **4.0:** Regulação de veículos autônomos e conectados
> **Dependências:** Produto & Eng. (4.4)

- 10.3.1 Relacionamento com DENATRAN/SENATRAN/ABRACICLO
- 10.3.2 Monitoramento e influência de políticas públicas
- 10.3.3 Gestão de incentivos fiscais (IPI, Manaus)
- 10.3.4 Relações com entidades sindicais
- 10.3.5 Participação em comitês de normatização
- 10.3.6 Gestão de aprovações regulatórias internacionais

---

## Resumo Executivo

| # | Domínio | Capabilities L1 | Capabilities L3 (total) | Tipo |
|---|---------|:-:|:-:|---|
| 1 | Core Manufacturing | 6 | 36 | Core |
| 2 | Digital & Manufacturing 4.0 | 6 | 36 | Digital |
| 3 | Supply Chain & Logística | 6 | 36 | Core |
| 4 | Produto & Engenharia | 5 | 30 | Core |
| 5 | Qualidade & Conformidade | 5 | 30 | Core |
| 6 | Manutenção & Facilities | 3 | 18 | Core |
| 7 | Comercial, Marketing & CX | 4 | 24 | Support |
| 8 | Sustentabilidade & ESG | 3 | 18 | Strategic |
| 9 | Suporte Corporativo | 4 | 24 | Support |
| 10 | Estratégia & Governança | 3 | 18 | Strategic |
| **Total** | | **45** | **270** | |

---

*Gerado com foco em Manufacturing 4.0 para indústria de motocicletas. Compatível com LeanIX, Ardoq, BizzDesign e demais ferramentas de EA.*
