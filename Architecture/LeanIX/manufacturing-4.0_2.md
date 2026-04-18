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

# English
# Business Capability Map — Motorcycle Industry / Manufacturing 4.0

> Business Capability Map structured across 3 hierarchical levels, focused on Manufacturing 4.0, for use in LeanIX or equivalent EA (Enterprise Architecture) tooling.

---

## Domain 1 — Core Manufacturing

### 1.1 Production Planning & Scheduling
> Orchestration of all manufacturing output, from the master plan through to line sequencing.
> **4.0:** AI-powered APS, capacity digital twin
> **Dependencies:** Supply Chain (1.1), Digital & M4.0 (1.1), Quality (1.1)

- 1.1.1 Master Production Schedule (MPS)
- 1.1.2 Fine-grain Scheduling (APS/MRP)
- 1.1.3 AI-driven line sequencing
- 1.1.4 Machine load balancing
- 1.1.5 Production scenario simulation
- 1.1.6 Constraint management (Theory of Constraints — TOC)

### 1.2 Factory Operations Management
> Real-time shop-floor control and execution via MES and IoT.
> **4.0:** Integrated MES, real-time OEE, IoT
> **Dependencies:** Digital & M4.0 (1.2, 1.3)

- 1.2.1 Production order execution (MES)
- 1.2.2 Real-time OEE monitoring
- 1.2.3 Downtime and loss management
- 1.2.4 Lot and part traceability
- 1.2.5 Operator and workstation management
- 1.2.6 Work-in-process (WIP) control

### 1.3 Manufacturing Process Engineering
> Definition, standardisation, and continuous improvement of production processes.
> **4.0:** Digital simulation, process digital twin
> **Dependencies:** Product & Eng. (1.1), Quality (1.2)

- 1.3.1 Manufacturing routing definition
- 1.3.2 Time and motion studies (MTM)
- 1.3.3 Process FMEA
- 1.3.4 Standard Operating Procedures (SOP)
- 1.3.5 Line simulation (Discrete Event Simulation — DES)
- 1.3.6 Engineering change management

### 1.4 Automation & Robotics Management
> Operation and evolution of automated systems, cobots, and robotic cells.
> **4.0:** Collaborative cobots, AMR, offline programming
> **Dependencies:** Digital & M4.0 (1.4), Maintenance (1.1)

- 1.4.1 Industrial robot programming and operation
- 1.4.2 Collaborative robots (cobots)
- 1.4.3 Autonomous vehicles (AGV/AMR)
- 1.4.4 Machine vision systems
- 1.4.5 PLCs and SCADA systems
- 1.4.6 Offline robotic simulation

### 1.5 Final Assembly Management
> Control of motorcycle assembly lines, from the frame to the finished product.
> **4.0:** Pick-to-light, AR-assisted assembly
> **Dependencies:** Supply Chain (1.2), Quality (1.3)

- 1.5.1 Mixed-model sequencing
- 1.5.2 Kit and sub-assembly management
- 1.5.3 Traceable critical torques and fastenings
- 1.5.4 Assisted in-line inspection
- 1.5.5 Finished product functional testing
- 1.5.6 Finished product packaging and dispatch

### 1.6 Tooling & Mould Management
> Lifecycle control of tools, moulds, stampings, and fixtures.
> **4.0:** Additive manufacturing of tooling, RFID
> **Dependencies:** Maintenance (1.1)

- 1.6.1 Tool service life tracking
- 1.6.2 Injection mould management
- 1.6.3 Fixture and jig calibration
- 1.6.4 Additive manufacturing of inserts and prototypes
- 1.6.5 Sharpening and rework control
- 1.6.6 RFID tagging of critical tooling

---

## Domain 2 — Digital & Manufacturing 4.0

### 2.1 Digital Twin & Simulation
> Digital replica of products, processes, and the factory to support real-time decision-making.
> **4.0:** Industry 4.0 core enabler
> **Dependencies:** Core Manufacturing (1.1), Digital M4.0 (2.4)

- 2.1.1 Product digital twin (kinematic/FEA)
- 2.1.2 Production process digital twin
- 2.1.3 Factory digital twin (3D layout)
- 2.1.4 Material flow simulation
- 2.1.5 PLM–MES integration via digital twin
- 2.1.6 Real-time IoT-driven twin updates

### 2.2 Industrial IoT (IIoT)
> Connectivity of machines, sensors, and plant assets for data collection and analysis.
> **4.0:** Edge computing, OPC-UA, MQTT
> **Dependencies:** Core Manufacturing (1.2), Digital M4.0 (2.5)

- 2.2.1 Machine connectivity (OPC-UA/MQTT)
- 2.2.2 Process parameter sensing
- 2.2.3 Edge computing and fog computing
- 2.2.4 Industrial gateways and legacy protocols
- 2.2.5 Energy and utilities monitoring
- 2.2.6 Real-time operational dashboards

### 2.3 Industrial Analytics & AI
> Transformation of operational data into intelligence for prediction and optimisation.
> **4.0:** ML for quality, computer vision
> **Dependencies:** Digital M4.0 (2.2), Quality (1.1)

- 2.3.1 ML-based predictive maintenance
- 2.3.2 Process anomaly detection
- 2.3.3 Computer vision for inspection
- 2.3.4 Process parameter optimisation (DOE/ML)
- 2.3.5 Automated root cause analysis
- 2.3.6 AI-driven demand forecasting

### 2.4 Intelligent Automation & Systems Integration
> Vertical and horizontal integration of IT and OT systems across the plant.
> **4.0:** IT/OT convergence, ISA-95
> **Dependencies:** Core Manufacturing (1.4), Digital M4.0 (2.2)

- 2.4.1 ERP–MES–SCADA integration (ISA-95)
- 2.4.2 Industrial data bus and APIs
- 2.4.3 Advanced SCADA and DCS
- 2.4.4 PID loop control and supervision
- 2.4.5 RPA for plant administrative processes
- 2.4.6 OT cybersecurity (ICS/SCADA)

### 2.5 Data Platform & Data Governance
> Integrated data infrastructure for analytical and regulatory support.
> **4.0:** Industrial data lake, DataOps
> **Dependencies:** Digital M4.0 (2.2, 2.3)

- 2.5.1 Industrial data lake
- 2.5.2 Data catalogue and data dictionary
- 2.5.3 Data quality and data lineage
- 2.5.4 Master Data Management (MDM)
- 2.5.5 Data consumption APIs
- 2.5.6 Data privacy and regulatory compliance (GDPR/LGPD)

### 2.6 Augmented Reality & Wearables
> Immersive technologies to support operations, training, and maintenance.
> **4.0:** Guided AR, smart glasses, XR
> **Dependencies:** Core Manufacturing (1.5), Maintenance (2.2)

- 2.6.1 AR-guided assembly
- 2.6.2 Interactive digital manuals in AR
- 2.6.3 Smart glasses for remote maintenance
- 2.6.4 Virtual reality (VR) training
- 2.6.5 Digital work instructions with video
- 2.6.6 Remote inspection by drone

---

## Domain 3 — Supply Chain & Logistics

### 3.1 Demand Planning & S&OP
> Integrated sales and operations planning process with predictive intelligence.
> **4.0:** AI-powered forecasting, digital S&OP
> **Dependencies:** Commercial (1.1), Core Manufacturing (1.1)

- 3.1.1 AI and ML-based forecasting
- 3.1.2 Demand consensus (S&OP)
- 3.1.3 Long-range capacity planning
- 3.1.4 Portfolio and product mix management
- 3.1.5 Plan sensitivity analysis
- 3.1.6 Integration with distributor sell-in/sell-out data

### 3.2 Procurement & Sourcing Management
> Strategy and operations for acquiring raw materials, components, and services.
> **4.0:** e-Procurement, digital SRM
> **Dependencies:** Supply Chain (3.3), Quality (1.2)

- 3.2.1 Sourcing strategy and dual sourcing
- 3.2.2 Supplier qualification and approval
- 3.2.3 Contract and SLA management
- 3.2.4 e-Procurement and digital quoting
- 3.2.5 Supply risk analysis
- 3.2.6 Commodity pricing and index management

### 3.3 Supplier Relationship Management (SRM)
> Strategic relationship and collaborative development of the supplier base.
> **4.0:** Supplier portal, digital VMI
> **Dependencies:** Supply Chain (3.2)

- 3.3.1 Integrated supplier portal
- 3.3.2 Performance evaluation (scorecard)
- 3.3.3 Local supplier development
- 3.3.4 Vendor Managed Inventory (VMI)
- 3.3.5 Supplier quality audits
- 3.3.6 Supply chain sustainability management

### 3.4 Inventory & Warehouse Management
> Intelligent stock control across the chain, from the warehouse to WIP.
> **4.0:** WMS with RFID/RTLS, just-in-sequence
> **Dependencies:** Core Manufacturing (1.2), Digital M4.0 (2.2)

- 3.4.1 Advanced WMS with RFID/barcode
- 3.4.2 Dynamic safety stock management
- 3.4.3 Drone-automated cycle counting
- 3.4.4 Supermarkets and electronic kanban
- 3.4.5 Hazardous materials management
- 3.4.6 Lot and expiry date control

### 3.5 Inbound & Outbound Logistics
> Material flow within the plant and distribution to market.
> **4.0:** AGV/AMR, digital milk run
> **Dependencies:** Core Manufacturing (1.5), Supply Chain (3.4)

- 3.5.1 Internal milk run with AGV/AMR
- 3.5.2 Line replenishment sequencing
- 3.5.3 Returnable packaging management
- 3.5.4 Cross-docking and transit points
- 3.5.5 Fleet management and TMS
- 3.5.6 Spare parts last-mile delivery

### 3.6 Supply Chain Risk & Resilience Management
> Identification, monitoring, and mitigation of supply chain risks.
> **4.0:** Digital control tower, AI-driven risk management
> **Dependencies:** Supply Chain (3.1), Digital M4.0 (2.5)

- 3.6.1 Supply chain control tower
- 3.6.2 Supply risk mapping
- 3.6.3 Contingency plans and BCP
- 3.6.4 ESG monitoring across the supply chain
- 3.6.5 Origin management and traceability
- 3.6.6 Geographic diversification of sourcing

---

## Domain 4 — Product & Engineering

### 4.1 Product Lifecycle Management (PLM)
> Integrated product governance from concept through to end-of-life.
> **4.0:** PLM with digital twin, MBSE
> **Dependencies:** Digital M4.0 (2.1), Core Manufacturing (1.3)

- 4.1.1 BOM management (Engineering and Manufacturing BoM)
- 4.1.2 Revision control and ECN/ECO management
- 4.1.3 Technical documentation management
- 4.1.4 PLM–ERP–MES integration
- 4.1.5 Product configuration and variant management
- 4.1.6 End-of-life and replacement management

### 4.2 Product Development (R&D)
> Innovation and development process for new motorcycle models.
> **4.0:** Integrated CAD/CAE/CAM, 3D printing
> **Dependencies:** Product & Eng. (4.1), Quality (1.2)

- 4.2.1 Conceptualisation and styling (industrial design)
- 4.2.2 Parametric 3D CAD (CATIA/NX)
- 4.2.3 CAE and structural/CFD/crash simulation
- 4.2.4 Rapid prototyping and additive manufacturing
- 4.2.5 Validation and homologation testing
- 4.2.6 Virtual Product Development (VPD)

### 4.3 Portfolio & Roadmap Management
> Strategic decisions on model portfolio and future technologies.
> **4.0:** Electric vehicles, connectivity, motorcycle ADAS
> **Dependencies:** Strategy (1.1), Commercial (1.1)

- 4.3.1 Development stage-gate process
- 4.3.2 Technical and commercial feasibility analysis
- 4.3.3 Product and technology roadmap (EV/hybrid)
- 4.3.4 Intellectual property management (IP/patents)
- 4.3.5 Competitive product benchmarking
- 4.3.6 Regional and regulatory model management

### 4.4 Homologation & Regulatory Compliance
> Product certification with national and international regulatory bodies.
> **4.0:** Virtual homologation simulation
> **Dependencies:** Product & Eng. (4.2), Quality (1.3)

- 4.4.1 Type approval (DENATRAN/SENATRAN equivalent)
- 4.4.2 Emissions compliance (PROCONVE/Euro 5)
- 4.4.3 Active/passive safety (ABNT/ECE standards)
- 4.4.4 Electric motorcycle certification
- 4.4.5 Post-approval change management
- 4.4.6 Export compliance documentation

### 4.5 Spare Parts Management (Aftermarket)
> Strategy and management of the genuine parts portfolio in the after-sales channel.
> **4.0:** Demand sensing, interactive digital catalogue
> **Dependencies:** Supply Chain (3.5), Commercial (1.3)

- 4.5.1 Parts demand planning (POS-driven)
- 4.5.2 Obsolescence and supersession management
- 4.5.3 Digital parts catalogue (EPC)
- 4.5.4 Genuine parts programme
- 4.5.5 Aftermarket pricing and margin management
- 4.5.6 Legacy parts supply

---

## Domain 5 — Quality & Compliance

### 5.1 Quality Management System (QMS)
> Integrated quality management framework aligned with international standards.
> **4.0:** eQMS, digitalised IATF 16949
> **Dependencies:** Core Manufacturing (1.2), Digital M4.0 (2.5)

- 5.1.1 IATF 16949 / ISO 9001 certification
- 5.1.2 Digital APQP and PPAP
- 5.1.3 Business process management (BPM)
- 5.1.4 Internal and external audits
- 5.1.5 Non-conformance management (NCRs)
- 5.1.6 Quality KPIs and QMS indicators

### 5.2 Quality by Design (QbD)
> Quality engineering integrated into the product development process.
> **4.0:** FEA/reliability simulation
> **Dependencies:** Product & Eng. (4.2), Core Manufacturing (1.3)

- 5.2.1 Design FMEA (DFMEA)
- 5.2.2 Process FMEA (PFMEA)
- 5.2.3 Control plans and Measurement System Analysis (MSA)
- 5.2.4 Reliability engineering (DRBFM)
- 5.2.5 Design Verification Plan and Report (DVP&R)
- 5.2.6 Lessons learned management

### 5.3 In-Process Quality Inspection & Control
> Statistical control and automated inspection at every production stage.
> **4.0:** Computer vision, automated CMM, SPC
> **Dependencies:** Core Manufacturing (1.2), Digital M4.0 (2.3)

- 5.3.1 Statistical Process Control (SPC)
- 5.3.2 Computer vision inspection
- 5.3.3 Coordinate Measuring Machine (CMM) and 3D scanning
- 5.3.4 Weld inspection by X-ray/ultrasound
- 5.3.5 Non-Destructive Testing (NDT)
- 5.3.6 Incoming material inspection

### 5.4 Warranty & Field Quality Management
> Monitoring and resolution of quality issues reported from the market.
> **4.0:** Connected telematics, field predictive analytics
> **Dependencies:** Commercial (1.3), Digital M4.0 (2.3)

- 5.4.1 Warranty and recall management
- 5.4.2 Field failure analysis (8D/MASP)
- 5.4.3 Telematics-based quality monitoring
- 5.4.4 Customer complaint management
- 5.4.5 Field quality benchmarking
- 5.4.6 Early warning system for field failures

### 5.5 Metrology & Laboratory
> Metrological traceability and laboratory analysis of materials and products.
> **4.0:** Digital metrology, Laboratory 4.0
> **Dependencies:** Quality (5.3)

- 5.5.1 Measurement instrument management
- 5.5.2 Calibration and traceability (national standards body)
- 5.5.3 Materials and metallurgy laboratory
- 5.5.4 Emissions and noise testing
- 5.5.5 Reliability laboratory (accelerated life testing)
- 5.5.6 Sample and results management

---

## Domain 6 — Maintenance & Facilities

### 6.1 Maintenance Management (CMMS/EAM)
> Maintenance strategies and systems to maximise asset availability.
> **4.0:** ML-based predictive maintenance, connected CMMS
> **Dependencies:** Digital M4.0 (2.3, 2.2)

- 6.1.1 Preventive and predictive maintenance
- 6.1.2 CMMS/EAM integrated with the shop floor
- 6.1.3 Total Productive Maintenance (TPM)
- 6.1.4 Critical spare parts management
- 6.1.5 Equipment reliability analysis (RCM)
- 6.1.6 Maintenance KPIs (MTBF/MTTR/OEE)

### 6.2 Industrial Asset Management
> Full lifecycle management of productive assets, from acquisition to disposal.
> **4.0:** Asset Performance Management (APM)
> **Dependencies:** Maintenance (6.1), Digital M4.0 (2.4)

- 6.2.1 Asset registry and records (EAM)
- 6.2.2 Financial asset management (depreciation)
- 6.2.3 Asset renewal planning
- 6.2.4 Asset Performance Management (APM)
- 6.2.5 Maintenance contract management
- 6.2.6 Sustainability and asset disposal

### 6.3 Utilities & Energy Management
> Efficient management of electricity, gas, water, and building systems.
> **4.0:** ISO 50001, intelligent monitoring
> **Dependencies:** Digital M4.0 (2.2), Sustainability (1.1)

- 6.3.1 Energy Management System (EMS / ISO 50001)
- 6.3.2 Real-time consumption monitoring
- 6.3.3 Energy efficiency projects
- 6.3.4 Boiler and steam utilities management
- 6.3.5 Effluent and industrial waste management
- 6.3.6 Renewable energy (solar/biomass)

---

## Domain 7 — Commercial, Marketing & CX

### 7.1 Commercial & Sales Management
> Sales strategy and execution across dealers, distributors, and direct channels.
> **4.0:** Integrated CRM, sell-out analytics
> **Dependencies:** Supply Chain (3.1), Product & Eng. (4.3)

- 7.1.1 Dealer network management
- 7.1.2 Sales planning and quota setting
- 7.1.3 Sell-in / sell-out analytics
- 7.1.4 Contract and consortium management
- 7.1.5 Trade marketing and network incentives
- 7.1.6 Motorcycle e-commerce (D2C)

### 7.2 Marketing & Brand Management
> Brand positioning, communication, and management in the two-wheeler market.
> **4.0:** Digital marketing, influence and community
> **Dependencies:** Product & Eng. (4.3)

- 7.2.1 Brand management (brand equity)
- 7.2.2 Digital marketing, SEO/SEM
- 7.2.3 Riding community and enthusiast management
- 7.2.4 Product launches and events
- 7.2.5 Market research and Voice of Customer (VOC)
- 7.2.6 Partnerships and co-branding

### 7.3 After-Sales Services & CX
> Customer experience throughout the motorcycle's lifecycle.
> **4.0:** Connected app, telematics, digital services
> **Dependencies:** Product & Eng. (4.5), Quality (5.4)

- 7.3.1 Authorised service network management
- 7.3.2 Motorcycle connectivity and telematics app
- 7.3.3 Customer satisfaction management (NPS/CSAT)
- 7.3.4 Loyalty programme and community
- 7.3.5 Customer relations centre (CRM)
- 7.3.6 Financial services and insurance (F&I)

### 7.4 Channel & Pricing Management
> Pricing strategy, discount management, and distribution channel governance.
> **4.0:** Dynamic revenue management
> **Dependencies:** Commercial (7.1)

- 7.4.1 Pricing strategy and policy
- 7.4.2 Discount and commercial incentive management
- 7.4.3 Price elasticity analysis
- 7.4.4 Physical and digital distribution channel management
- 7.4.5 Competitive price analysis
- 7.4.6 Revenue management and dynamic pricing

---

## Domain 8 — Sustainability & ESG

### 8.1 Environmental Management & Decarbonisation
> Environmental strategy and operations to reduce impact and ensure legal compliance.
> **4.0:** Real-time carbon monitoring
> **Dependencies:** Maintenance (6.3), Digital M4.0 (2.5)

- 8.1.1 GHG emissions inventory and tracking (Scope 1/2/3)
- 8.1.2 Waste management and reverse logistics
- 8.1.3 Environmental compliance (national regulations)
- 8.1.4 Carbon reduction and net zero projects
- 8.1.5 Environmental certifications (ISO 14001)
- 8.1.6 GRI reporting and sustainability disclosures

### 8.2 Health, Safety & Ergonomics (HSE)
> Accident prevention, occupational health, and ergonomics in the industrial environment.
> **4.0:** Safety wearables, AI-driven prevention
> **Dependencies:** Core Manufacturing (1.2)

- 8.2.1 HSE management system (ISO 45001)
- 8.2.2 Ergonomic workstation analysis
- 8.2.3 Risk management and hazard assessment plans
- 8.2.4 Wearables for fatigue monitoring
- 8.2.5 PPE management and regulatory compliance
- 8.2.6 Accident investigation and analysis

### 8.3 Circular Economy & Sustainable Product
> Integration of circular economy principles into design and operations.
> **4.0:** Design for disassembly, bio-materials
> **Dependencies:** Product & Eng. (4.2), Sustainability (8.1)

- 8.3.1 Design for sustainability (eco-design)
- 8.3.2 Critical material traceability (lithium, copper)
- 8.3.3 Recycling and take-back programme
- 8.3.4 Hazardous material reduction (REACH/RoHS)
- 8.3.5 Eco-design for electric vehicles
- 8.3.6 Product Life Cycle Assessment (LCA)

---

## Domain 9 — Corporate Support

### 9.1 Financial Management & Controlling
> Financial planning, control, and managerial and statutory accounting.
> **4.0:** Automated FP&A, cloud finance
> **Dependencies:** Strategy (1.1)

- 9.1.1 Budgeting and forecasting (FP&A)
- 9.1.2 Industrial cost accounting (ABC)
- 9.1.3 Cash and treasury management
- 9.1.4 Financial reporting (IFRS)
- 9.1.5 Internal controls and financial compliance
- 9.1.6 Internal and external audit

### 9.2 People Management & Development
> Attracting, developing, and retaining talent for the factory of the future.
> **4.0:** Digital upskilling, learning management platforms

- 9.2.1 Recruitment and selection
- 9.2.2 Training and development (L&D)
- 9.2.3 Performance management and meritocracy
- 9.2.4 Job grading and compensation structure
- 9.2.5 Occupational safety and employee wellbeing
- 9.2.6 Workforce planning and capacity management

### 9.3 Legal & Compliance Management
> Legal conformance, contract management, and corporate legal protection.
> **4.0:** LegalTech, smart contracts

- 9.3.1 Contract and SLA management
- 9.3.2 Regulatory compliance (antitrust, GDPR/LGPD)
- 9.3.3 Intellectual property and patents
- 9.3.4 Litigation and dispute management
- 9.3.5 Due diligence and M&A
- 9.3.6 Corporate insurance management

### 9.4 Information Technology (IT)
> Corporate and industrial IT infrastructure, systems, and information security.
> **4.0:** Hybrid cloud, IT/OT cybersecurity, private 5G
> **Dependencies:** Digital M4.0 (2.4, 2.5)

- 9.4.1 ERP (integrated management system)
- 9.4.2 Cloud and on-premise infrastructure
- 9.4.3 Cybersecurity and identity management
- 9.4.4 OT networks and private 5G in-plant
- 9.4.5 Service desk and end-user support
- 9.4.6 Project management and enterprise architecture

---

## Domain 10 — Strategy & Governance

### 10.1 Strategic Planning
> Defining the long-term vision, corporate strategy, and resource allocation.
> **4.0:** Future mobility scenarios, EV strategy

- 10.1.1 Strategic planning cycle (BSC/OKR)
- 10.1.2 Market analysis and competitive scenario planning
- 10.1.3 Initiative portfolio management
- 10.1.4 M&A and strategic partnerships
- 10.1.5 Corporate performance management (KPIs)
- 10.1.6 Corporate governance and strategic ESG

### 10.2 Innovation & R&D
> Management of technological innovation and applied research in the mobility sector.
> **4.0:** Open innovation, connected vehicles, EV
> **Dependencies:** Product & Eng. (4.2), Digital M4.0 (2.1)

- 10.2.1 Innovation pipeline management
- 10.2.2 Open innovation and startup ecosystem
- 10.2.3 Electric and hybrid propulsion research
- 10.2.4 R&D centres and internal laboratories
- 10.2.5 Government R&D tax incentive capture
- 10.2.6 Emerging intellectual property management

### 10.3 Government Affairs & Regulatory Relations
> Engagement with government, industry bodies, and management of the regulatory environment.
> **4.0:** Regulation of autonomous and connected vehicles
> **Dependencies:** Product & Eng. (4.4)

- 10.3.1 Engagement with transport authorities and industry associations
- 10.3.2 Public policy monitoring and influence
- 10.3.3 Tax incentive management (IPI, free trade zones)
- 10.3.4 Trade union and labour relations
- 10.3.5 Participation in standardisation committees
- 10.3.6 International regulatory approval management

---

## Executive Summary

| # | Domain | L1 Capabilities | L3 Capabilities (total) | Type |
|---|--------|:-:|:-:|---|
| 1 | Core Manufacturing | 6 | 36 | Core |
| 2 | Digital & Manufacturing 4.0 | 6 | 36 | Digital |
| 3 | Supply Chain & Logistics | 6 | 36 | Core |
| 4 | Product & Engineering | 5 | 30 | Core |
| 5 | Quality & Compliance | 5 | 30 | Core |
| 6 | Maintenance & Facilities | 3 | 18 | Core |
| 7 | Commercial, Marketing & CX | 4 | 24 | Support |
| 8 | Sustainability & ESG | 3 | 18 | Strategic |
| 9 | Corporate Support | 4 | 24 | Support |
| 10 | Strategy & Governance | 3 | 18 | Strategic |
| **Total** | | **45** | **270** | |

---

*Generated with a Manufacturing 4.0 focus for the motorcycle industry. Compatible with LeanIX, Ardoq, BizzDesign, and other EA tooling.*
