# Prompt
Aja como um consultor sênior de produto e tecnologia.

Gere um Documento de Inception completo para uma solução de software de Tracking de Container, com foco em importação de peças de motos para uma fábrica localizada no Brasil.

Considere o contexto de negócio, operação logística e necessidades de rastreabilidade da cadeia de suprimentos.

O documento deve ser estruturado de forma clara e profissional, cobrindo no mínimo:

1. Visão geral do produto
2. Problema de negócio
3. Objetivos da solução
4. Escopo do produto
5. Principais stakeholders
6. Jornada atual e jornada desejada
7. Requisitos funcionais
8. Requisitos não funcionais
9. Integrações com sistemas externos
10. Premissas e restrições
11. Riscos e mitigação
12. Indicadores de sucesso
13. MVP proposto
14. Roadmap de alto nível
15. Critérios de aceite

Detalhe também:
- Perfis de usuários envolvidos
- Principais fluxos de uso
- Necessidades de monitoramento e alertas
- Regras de negócio relevantes para importação e rastreamento
- Possíveis exceções e tratamento de falhas

Apresente o conteúdo em português, com linguagem objetiva, técnica e executiva, adequada para alinhamento entre negócio, operação e tecnologia.

# Adapta
Departamento de Supply Chain & Tecnologia

DOCUMENTO DE INCEPTION: SOLUÇÃO TRACKING DE CONTAINERS

Sistema de Monitoramento Logístico Ponta a Ponta para Importação de Peças de Motocicletas

04 de agosto de 2026

---

### 1. CONTROLE DE VERSÃO




Versão
Data
Descrição
Autor




1.0
04/08/2026
Emissão inicial para alinhamento executivo.
[NOME/DEPARTAMENTO]




### 2. GLOSSÁRIO DE TERMOS LOGÍSTICOS

Para garantir o alinhamento entre as áreas de Negócio, Operação e Tecnologia, definem-se os seguintes termos técnicos utilizados neste documento:

*   **BL (Bill of Lading):** Conhecimento de embarque; documento que titula a posse da carga e descreve a operação de transporte.
*   **DUIMP (Declaração Única de Importação):** Novo documento eletrônico que centraliza os dados de natureza aduaneira, administrativa e fiscal na importação.
*   **ETA (Estimated Time of Arrival):** Data e hora estimada de chegada do navio ou da carga no destino.
*   **ETD (Estimated Time of Departure):** Data e hora estimada de partida do navio do porto de origem.
*   **Incoterms:** Termos internacionais de comércio que definem responsabilidades entre comprador e vendedor (ex: FOB, CIF, EXW).
*   **Free Time:** Período acordado em que o container pode permanecer no porto ou em posse do importador sem cobrança de taxas extras.
*   **Demurrage:** Multa paga pelo importador pelo uso do container além do free time enquanto ele está no porto (cheio).
*   **Detention:** Multa paga pelo importador pelo atraso na devolução do container vazio ao armador após a retirada do porto.
*   **FCL (Full Container Load):** Modalidade onde o container é utilizado exclusivamente por um único importador.

### 3. VISÃO GERAL DO PRODUTO

A solução consiste em uma plataforma centralizada de visibilidade em tempo real para o fluxo de importação de componentes destinados à produção de motocicletas. O sistema monitora o ciclo de vida completo da carga: desde a coleta no fornecedor internacional, trânsito internacional (marítimo), chegada e desembaraço nos portos brasileiros, até o transporte rodoviário final e recebimento na planta fabril. O foco primordial é a antecipação de desvios logísticos para permitir ajustes dinâmicos no planejamento de produção (PCP), mitigando riscos de ruptura de estoque.

### 4. PROBLEMA DE NEGÓCIO

Atualmente, a fábrica enfrenta uma "zona silenciosa" durante o trânsito internacional das peças. A gestão é baseada em processos manuais (planilhas e e-mails), resultando em:

*   **Falta de Visibilidade:** O status real da carga só é confirmado após o contato ativo com despachantes ou agentes de carga.
*   **Reatividade:** Atrasos marítimos ou retenções aduaneiras são identificados tardiamente, impossibilitando manobras no plano de produção.
*   **Custos Extras:** Elevados gastos com demurrage e detention devido à falha na gestão de prazos de devolução.
*   **Risco de Parada de Linha:** A incerteza sobre a chegada de itens críticos força a manutenção de estoques de segurança elevados ou causa paradas onerosas na montagem.

### 5. OBJETIVOS DA SOLUÇÃO

#### 5.1 Objetivos de Negócio
*   Reduzir em [VALOR]% as paradas de linha por falta de componentes importados.
*   Diminuir custos com demurrage e armazenagem portuária em [VALOR]%.
*   Otimizar o capital de giro através da redução planejada de estoques de segurança.

#### 5.2 Objetivos Operacionais
*   Centralizar o rastreamento multi-carrier em uma única interface.
*   Automatizar alertas de desvios de ETA superiores a [DEFINIR] horas.
*   Eliminar o uso de planilhas manuais para controle de follow-up de importação.

#### 5.3 Objetivos de Tecnologia
*   Implementar integração via API com os principais armadores e sistemas aduaneiros.
*   Garantir integridade e atualização de dados com latência máxima de [DEFINIR] minutos.

### 6. ESCOPO DO PRODUTO

#### 6.1 Dentro do Escopo
*   Rastreamento de containers FCL (origem ao destino).
*   Monitoramento de eventos portuários (atracação, descarga, gate-out).
*   Gestão de documentos digitais (BL, Invoice, DUIMP).
*   Cálculo automático de janelas de free time.
*   Dashboard de KPIs e alertas proativos.

#### 6.2 Fora do Escopo
*   Gestão de pagamentos e fechamento de câmbio.
*   Negociação de fretes e booking.
*   Rastreamento de peças individuais dentro do armazém da fábrica (WMS).
*   Gestão de frota própria de caminhões.

### 7. JORNADA DO USUÁRIO




Etapa
Jornada Atual (As-Is)
Jornada Desejada (To-Be)




Monitoramento Marítimo
Consulta manual nos sites de cada armador.
Atualização automática via API multi-carrier.


Gestão de Atrasos
Identificados apenas quando o navio não atraca.
Alertas preditivos baseados em desvios de rota.


Desembaraço
Dependência de e-mails do despachante.
Integração direta com status da DUIMP/Siscomex.


Custos de Demurrage
Controlados em planilhas sujeitas a erros.
Contagem regressiva automática com alertas críticos.




### 8. REQUISITOS FUNCIONAIS (RF)

1.  **RF-01:** Cadastro de processos de importação vinculados a números de container e BL.
2.  **RF-02:** Ingestão automática de eventos de rastreamento de múltiplos armadores (Carriers).
3.  **RF-03:** Comparação em tempo real entre ETD/ETA original vs. Realizado.
4.  **RF-04:** Painel de controle (Dashboard) com visão geográfica das cargas em trânsito.
5.  **RF-05:** Sistema de alertas configuráveis para atrasos, mudanças de navio (transbordo) e retenções.
6.  **RF-06:** Módulo de gestão de prazos de *free time* com alertas de proximidade de vencimento.
7.  **RF-07:** Repositório centralizado para documentos da carga (Packing List, Fatura, DUIMP).
8.  **RF-08:** Integração de saída para o ERP (status de "Carga em Trânsito" e "Disponível para Recebimento").

### 9. REQUISITOS NÃO FUNCIONAIS (RNF)

1.  **RNF-01 (Disponibilidade):** O sistema deve operar com SLA de 99,5% em regime 24/7.
2.  **RNF-02 (Segurança):** Autenticação via Single Sign-On (SSO) e criptografia de dados em repouso.
3.  **RNF-03 (Desempenho):** Dashboards devem carregar em menos de [VALOR] segundos.
4.  **RNF-04 (Escalabilidade):** Suporte para monitoramento simultâneo de até [VALOR] containers/mês.
5.  **RNF-05 (Conformidade):** Aderência total à LGPD para tratamento de dados de contatos comerciais.

### 10. INTEGRAÇÕES COM SISTEMAS EXTERNOS

A solução atuará como um hub de dados, consumindo e enviando informações para os seguintes ecossistemas:

*   **Carriers (Maersk, MSC, CMA CGM, etc.):** Integração via API/EDI para eventos de rastreamento (Pull/Frequência: [DEFINIR]).
*   **ERP Corporativo (SAP/TOTVS):** Sincronização de Pedidos de Compra (PO) e confirmação de recebimento físico.
*   **Portal Único Siscomex:** Monitoramento do status de parametrização (Canais Verde, Amarelo, Vermelho, Cinza).
*   **Terminais Portuários:** Dados de gate-in e gate-out para precisão na última milha.

### 11. RISCOS E MITIGAÇÃO




Risco
Impacto
Mitigação




Baixa qualidade de dados dos armadores
Alto
Utilização de múltiplas fontes (Carrier + Terminal + AIS).


Indisponibilidade de APIs governamentais
Médio
Implementação de cache e reprocessamento automático.


Resistência dos despachantes externos
Médio
Treinamento e inclusão dos parceiros como usuários do sistema.




### 12. MVP PROPOSTO (MÍNIMO PRODUTO VIÁVEL)

O MVP focará na visibilidade do trecho marítimo e portuário para os 3 principais armadores utilizados pela fábrica. Incluirá:

*   Cadastro manual/CSV de containers.
*   Tracking automático de eventos marítimos básicos.
*   Dashboard de status (Em trânsito, No porto, Desembaraçado).
*   Alertas de atraso de ETA via e-mail.

### 13. ROADMAP DE ALTO NÍVEL

*   **Fase 1 (Mês 1):** Discovery, Design de Interface e Definição de Arquitetura.
*   **Fase 2 (Mês 2-3):** Desenvolvimento do MVP e Integração com 1º Carrier.
*   **Fase 3 (Mês 4-5):** Integração com ERP e Siscomex; Expansão para todos os Carriers.
*   **Fase 4 (Mês 6+):** Analytics preditivo e Machine Learning para estimativa real de chegada.

### 14. PERFIS DE USUÁRIOS

*   **Analista de Importação:** Operação diária, gestão de documentos e alertas.
*   **Planejador PCP:** Consulta de ETA para ajuste de cronograma de montagem.
*   **Gerente de Supply Chain:** Visão executiva de KPIs e custos de estadia.
*   **Despachante (Externo):** Atualização de status aduaneiro e upload de guias.

---

### PRÓXIMOS PASSOS

1.  Validação técnica das APIs dos armadores prioritários.
2.  Aprovação orçamentária para contratação de provedor de dados marítimos.
3.  Kickoff oficial com os stakeholders de Negócio e TI.

---

          
PATROCINADOR EXECUTIVO                                        DIRETORIA DE TI

Local e data: _________________________, 04 de agosto de 2026

*Documento elaborado em 04 de agosto de 2026. As informações contidas são de responsabilidade do solicitante.*

# Prompt
Gerar um Documento de Inception completo, para uma solução de software de Tracking de Container, com foco em importação de peças de motos, para uma fábrica, localizada no Brasil.

# MyHUB
# 📋 Documento de Inception

## Solução de Software: Tracking de Containers para Importação de Peças de Motos

---

## 1. 🎯 Visão Geral do Projeto

### 1.1 Nome do Projeto
**MotoTrack Container** — Sistema de Rastreamento e Gestão de Containers para Importação de Peças de Motocicletas

### 1.2 Descrição Executiva

O **MotoTrack Container** é uma plataforma web integrada para o monitoramento completo da cadeia logística de importação de peças de motocicletas, desde a saída do fornecedor internacional (principalmente China, Índia e Tailândia) até a entrada na fábrica no Brasil. A solução visa eliminar a dependência de planilhas e e-mails descentralizados, centralizando todas as informações de rastreamento marítimo, documentação aduaneira e gestão de inventário em trânsito.

### 1.3 Problema de Negócio

Atualmente, a fábrica enfrenta os seguintes desafios:

- **Falta de visibilidade** sobre a localização exata dos containers em trânsito
- **Atrasos na produção** por falta de previsibilidade na chegada de insumos
- **Processos manuais** de consulta a sites de armadores (MSC, Maersk, CMA CGM, etc.)
- **Documentação fragmentada** entre setores (Compras, Logística, Fiscal, Produção)
- **Perda de prazos** de desembaraço aduaneiro (Siscomex)
- **Multas por armazenagem** (demurrage/detention) por falta de acompanhamento proativo

---

## 2. 🎯 Objetivos

| # | Objetivo | Indicador de Sucesso |
|---|----------|---------------------|
| O1 | Visibilidade em tempo real de 100% dos containers em trânsito | Dashboard com status atualizado a cada 4h |
| O2 | Reduzir em 40% os atrasos de produção por falta de peças | Comparativo mensal pré e pós-implantação |
| O3 | Zerar multas por demurrage/detention | Relatório mensal de ocorrências |
| O4 | Automatizar 90% das consultas de rastreamento | Integração com APIs de armadores e Siscomex |
| O5 | Centralizar toda documentação de importação | Repositório único com acesso por perfil |

---

## 3. 📦 Escopo

### 3.1 Dentro do Escopo

✅ Cadastro de fornecedores internacionais e rotas de origem  
✅ Integração com APIs de tracking de armadores (MSC, Maersk, CMA CGM, Hapag-Lloyd, Cosco)  
✅ Integração com Siscomex (API Receita Federal / Portal Único Siscomex)  
✅ Rastreamento de containers por número (BILL OF LADING / Container Number)  
✅ Dashboard com mapa-múndi interativo e status de cada embarque  
✅ Alertas automáticos por e-mail e WhatsApp para eventos críticos  
✅ Gestão de documentos (BL, Invoice, Packing List, Conhecimento de Embarque, DI, LI)  
✅ Módulo de previsão de chegada (ETA) com impacto na linha de produção  
✅ Cálculo automático de custos logísticos (frete, seguro, taxas portuárias, armazenagem)  
✅ Relatórios gerenciais (lead time médio, custo por container, OTIF)  
✅ Controle de demurrage/detention com alertas de vencimento  
✅ Perfis de acesso: Administrador, Logística, Fiscal, Compras, Produção, Diretoria  

### 3.2 Fora do Escopo

❌ Rastreamento de cargas aéreas (fase futura)  
❌ Integração direta com ERP da fábrica (interface via API REST a ser consumida pelo ERP)  
❌ Gestão de transporte rodoviário interno no Brasil (apenas container até o porto de destino)  
❌ Cotação de frete internacional  
❌ Módulo de pagamentos internacionais  
❌ Aplicativo mobile nativo (versão web responsiva como MVP)  

---

## 4. 👥 Stakeholders

| Papel | Nome/Área | Responsabilidade |
|-------|-----------|-----------------|
| **Product Owner** | Gerente de Logística | Priorização de funcionalidades e validação do negócio |
| **Usuário-Chave** | Analista de Importação | Operação diária, cadastro de embarques e consulta de status |
| **Usuário-Chave** | Analista Fiscal | Acompanhamento de documentação aduaneira (Siscomex) |
| **Usuário-Chave** | PCP (Planejamento) | Previsibilidade de chegada para linha de produção |
| **Stakeholder** | Gerente de Compras | Visão dos pedidos internacionais e lead time |
| **Stakeholder** | Diretor Industrial | Dashboard gerencial e indicadores de desempenho |
| **TI** | Coordenador de TI | Integração com infraestrutura e ERP existentes |
| **Externo** | Despachante Aduaneiro | Acesso limitado para upload de documentos de desembaraço |

---

## 5. 📊 Requisitos Funcionais

### Módulo 1: Cadastros Básicos

| ID | Requisito | Prioridade |
|----|-----------|-----------|
| **RF-01** | Cadastro de fornecedores internacionais com CNPJ/ID estrangeiro, país de origem, porto de embarque padrão | Alta |
| **RF-02** | Cadastro de rotas marítimas (origem → destino) com lead time médio e armadores disponíveis | Alta |
| **RF-03** | Cadastro de peças/SKUs com código, descrição, fornecedor padrão e lead time esperado | Alta |
| **RF-04** | Cadastro de portos de destino no Brasil (Santos, Paranaguá, Itajaí, Rio de Janeiro, Suape) | Média |
| **RF-05** | Cadastro de usuários com perfis de acesso e permissões granulares | Alta |

### Módulo 2: Gestão de Embarques

| ID | Requisito | Prioridade |
|----|-----------|-----------|
| **RF-06** | Criação de embarque com Order Number, fornecedor, data de saída da fábrica de origem | Alta |
| **RF-07** | Associação de containers ao embarque (1:N — um embarque pode ter múltiplos containers) | Alta |
| **RF-08** | Upload de documentos comerciais (Invoice, Packing List, Certificado de Origem) por embarque | Alta |
| **RF-09** | Registro de Bill of Lading (BL) number, armador, navio, ETA e ETD iniciais | Alta |
| **RF-10** | Vinculação dos itens do pedido de compra (PO) ao container/embarque | Média |

### Módulo 3: Tracking Automatizado

| ID | Requisito | Prioridade |
|----|-----------|-----------|
| **RF-11** | Integração com API de tracking de pelo menos 5 armadores principais | Alta |
| **RF-12** | Consulta automática a cada 4 horas do status dos containers ativos | Alta |
| **RF-13** | Atualização manual de status (fallback quando API indisponível) | Alta |
| **RF-14** | Histórico de eventos do container (saída origem, transbordo, chegada destino, descarga) | Alta |
| **RF-15** | Captura de ETA atualizado automaticamente via API | Alta |

### Módulo 4: Módulo Aduaneiro (Siscomex)

| ID | Requisito | Prioridade |
|----|-----------|-----------|
| **RF-16** | Registro de números de LI (Licença de Importação) e DI (Declaração de Importação) | Alta |
| **RF-17** | Acompanhamento de status aduaneiro: parametrização, em conferência, desembaraçado | Alta |
| **RF-18** | Alertas de pendências fiscais e prazos de desembaraço | Média |
| **RF-19** | Upload e vinculação de documentos fiscais: DI, LI, Comprovante de Importação | Alta |
| **RF-20** | Integração com API do Portal Único Siscomex para consulta de status | Média |

### Módulo 5: Alertas e Notificações

| ID | Requisito | Prioridade |
|----|-----------|-----------|
| **RF-21** | Alerta de atraso de ETA superior a 48h em relação ao planejado | Alta |
| **RF-22** | Alerta de demurrage/detention — 5 dias antes e 2 dias antes do vencimento do free time | Alta |
| **RF-23** | Notificação de chegada do container ao porto de destino | Alta |
| **RF-24** | Alerta de pendência documental para desembaraço | Média |
| **RF-25** | Canais: e-mail e WhatsApp (via API WhatsApp Business) | Alta |

### Módulo 6: Dashboard e Relatórios

| ID | Requisito | Prioridade |
|----|-----------|-----------|
| **RF-26** | Dashboard com mapa-múndi exibindo posição dos containers em trânsito | Alta |
| **RF-27** | Painel de status: Em Trânsito, No Porto, Em Desembaraço, Liberado, Entregue | Alta |
| **RF-28** | Relatório de lead time por rota, fornecedor e período | Média |
| **RF-29** | Relatório de custos logísticos consolidados (frete, seguro, armazenagem, taxas) | Média |
| **RF-30** | Indicador OTIF (On Time In Full) por fornecedor e período | Média |
| **RF-31** | Previsão de impacto na produção: alerta quando ETA compromete linha de montagem | Alta |

---

## 6. 🔧 Requisitos Não Funcionais

| ID | Requisito | Especificação |
|----|-----------|--------------|
| **RNF-01** | Disponibilidade | 99,5% (24/7), com janelas de manutenção programadas |
| **RNF-02** | Tempo de resposta | Máximo 3 segundos para operações CRUD; 5 segundos para relatórios |
| **RNF-03** | Segurança | HTTPS/TLS 1.3, OAuth 2.0 + JWT, senhas com bcrypt, proteção OWASP Top 10 |
| **RNF-04** | Escalabilidade | Suporte a 500 containers ativos simultâneos e 50 usuários concorrentes |
| **RNF-05** | Backup | Backup diário automático com retenção de 90 dias (banco + documentos) |
| **RNF-06** | LGPD | Adequação à Lei 13.709/2018 — criptografia de dados sensíveis, direito a exclusão, termo de privacidade |
| **RNF-07** | Responsividade | Acesso completo via desktop, tablet e smartphone (PWA opcional) |
| **RNF-08** | Logs | Auditoria completa de ações: quem fez, o quê, quando, IP de origem |
| **RNF-09** | Idiomas | Interface em Português (Brasil) — internacionalização preparada para Inglês (fase 2) |
| **RNF-10** | Navegadores | Compatível com Chrome, Edge, Firefox e Safari (últimas 2 versões) |

---

## 7. 🏗️ Arquitetura Sugerida

### 7.1 Stack Tecnológica

```
┌─────────────────────────────────────────────────────┐
│                   FRONTEND (SPA)                     │
│    React 18 + TypeScript + Vite                      │
│    TailwindCSS + Shadcn/ui + Leaflet (Mapas)         │
│    PWA - Progressive Web App                         │
└──────────────────────┬──────────────────────────────┘
                       │ REST API / WebSocket
┌──────────────────────▼──────────────────────────────┐
│                   BACKEND (API)                      │
│    Node.js + NestJS / ou Django REST Framework       │
│    Autenticação: OAuth 2.0 + JWT                     │
│    Cache: Redis                                       │
│    Fila: BullMQ (Redis) - processamento assíncrono   │
└──────────────────────┬──────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────┐
│                BANCO DE DADOS                        │
│    PostgreSQL 15 (principal)                         │
│    AWS S3 / MinIO (documentos)                       │
│    Elasticsearch (logs e busca avançada)             │
└──────────────────────┬──────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────┐
│            INTEGRAÇÕES EXTERNAS                      │
│    • APIs de armadores (MSC, Maersk, CMA CGM...)     │
│    • Siscomex API (Portal Único)                     │
│    • WhatsApp Business API                           │
│    • SMTP (e-mail transacional)                      │
│    • MarineTraffic / VesselFinder (AIS - opcional)   │
└─────────────────────────────────────────────────────┘
```

### 7.2 Infraestrutura (Sugestão AWS)

| Serviço | Finalidade |
|---------|-----------|
| **ECS Fargate** | Execução da aplicação (containers Docker) |
| **RDS PostgreSQL** | Banco de dados relacional |
| **ElastiCache (Redis)** | Cache e filas de processamento |
| **S3** | Armazenamento de documentos de importação |
| **CloudFront** | CDN para distribuição do frontend |
| **Route 53** | DNS e gerenciamento de domínio |
| **CloudWatch** | Monitoramento, logs e alertas operacionais |
| **SES** | Envio de e-mails transacionais |

---

## 8. 🗃️ Modelagem de Dados (Conceitual)

### 8.1 Entidades Principais

```
┌──────────────┐       ┌─────────────────┐       ┌──────────────┐
│  FORNECEDOR   │1─────N│    EMBARQUE     │1─────N│  CONTAINER   │
│               │       │                 │       │              │
│ • id          │       │ • id            │       │ • id         │
│ • nome        │       │ • order_number  │       │ • numero     │
│ • pais        │       │ • data_saida    │       │ • bl_number  │
│ • porto_padrao│       │ • incoterm      │       │ • armador    │
│ • contato     │       │ • valor_total   │       │ • navio      │
└──────────────┘       │ • moeda         │       │ • eta        │
                       │ • status        │       │ • etd        │
                       └────────┬────────┘       │ • status     │
                                │                └──────┬───────┘
                                │1                      │
                                │                       │N
                       ┌────────▼────────┐       ┌──────▼───────┐
                       │    DOCUMENTO    │       │  EVENTO      │
                       │                 │       │              │
                       │ • id            │       │ • id         │
                       │ • tipo (BL,     │       │ • data_hora  │
                       │   Invoice, DI)  │       │ • tipo       │
                       │ • arquivo_url   │       │ • descricao  │
                       │ • data_upload   │       │ • localizacao│
                       └─────────────────┘       └──────────────┘

┌──────────────┐       ┌─────────────────┐
│   ITEM_PO    │       │   PROCESSO      │
│              │       │   ADUANEIRO     │
│ • id         │       │                 │
│ • sku        │       │ • id            │
│ • descricao  │       │ • li_number     │
│ • quantidade │       │ • di_number     │
│ • ncm        │       │ • status        │
│ • peso       │       │ • canal         │
└──────────────┘       │ • data_registro │
                       └─────────────────┘
```

---

## 9. 🔄 Fluxos de Processo

### 9.1 Fluxo Principal — Do Pedido à Entrega

```
[PEDIDO EMITIDO] → [EMBARQUE CRIADO] → [SAÍDA FORNECEDOR] → [TRÂNSITO MARÍTIMO]
                                                                       │
                                                          ┌────────────┘
                                                          ▼
                                                  [CHEGADA NO BRASIL]
                                                          │
                                                          ▼
                                                  [DESEMBARAÇO ADUANEIRO]
                                                          │
                                              ┌───────────┴───────────┐
                                              ▼                       ▼
                                      [LIBERADO]               [PENDÊNCIA FISCAL]
                                              │                       │
                                              ▼                       ▼
                                    [ENTREGA NA FÁBRICA]      [ALERTA → AÇÃO CORRETIVA]
                                              │
                                              ▼
                                    [CONFERÊNCIA E ESTOQUE]
```

### 9.2 Fluxo de Tracking Automatizado

```
[CRON JOB a cada 4h]
        │
        ▼
[Consulta containers ativos]
        │
        ▼
[Para cada container → chama API do armador]
        │
        ├── Status alterado? ──SIM──→ [Atualiza DB + registra evento]
        │                                   │
        │                                   ▼
        │                            [Verifica regras de alerta]
        │                                   │
        │                         ┌─────────┴─────────┐
        │                         ▼                   ▼
        │                  [Dispara alerta]    [Sem ação necessária]
        │
        └── Status igual? ──NÃO──→ [Registra heartbeat da consulta]
```

---

## 10. 🚨 Mapeamento de Riscos

| Risco | Probabilidade | Impacto | Mitigação |
|-------|:---:|:---:|-----------|
| APIs de armadores instáveis/indisponíveis | Alta | Alto | Fallback com entrada manual + cache da última posição conhecida |
| Mudanças no Siscomex quebrando integração | Média | Alto | Monitoramento contínuo dos portais oficiais + desenho flexível de integração |
| Resistência dos analistas (apego a planilhas) | Média | Médio | Treinamento intensivo + período de transição (rodar em paralelo 2 meses) |
| Baixa qualidade dos dados de entrada | Média | Alto | Validações rigorosas de formulário + campos obrigatórios + onboarding guiado |
| Atraso na entrega do projeto | Média | Médio | Metodologia ágil com entregas parciais a cada 2 semanas |
| Vazamento de dados comerciais sensíveis | Baixa | Crítico | Criptografia em repouso e trânsito + pentest pré-implantação + LGPD compliance |

---

## 11. 📅 Cronograma Preliminar (8 Meses)

| Fase | Mês | Entregas Principais |
|------|:--:|---------------------|
| **Inception & Setup** | Mês 1 | Documento de inception aprovado, ambiente de desenvolvimento, repositórios |
| **MVP — Core** | Mês 2-3 | Cadastros básicos, criação de embarques e containers, upload de documentos, dashboard simples |
| **MVP — Tracking** | Mês 4 | Integração com APIs de 3 armadores principais, tracking automatizado, linha do tempo de eventos |
| **MVP — Alertas** | Mês 5 | Sistema de notificações (e-mail + WhatsApp), regras de alerta configuráveis |
| **Módulo Aduaneiro** | Mês 6 | Registro de LI/DI, status aduaneiro, integração inicial com Siscomex |
| **Dashboards & BI** | Mês 7 | Mapa interativo, relatórios gerenciais, indicadores OTIF, previsão de impacto |
| **Testes & Implantação** | Mês 8 | Testes integrados, treinamento de usuários, implantação em produção, hypercare |

---

## 12. 🇧🇷 Considerações Específicas — Brasil

### 12.1 Legislação Aduaneira

- **Siscomex (Sistema de Comércio Exterior)**: Integração obrigatória com o Portal Único para consulta de status de DI (Declaração de Importação)
- **NCM (Nomenclatura Comum do Mercosul)**: Cada peça deve ter NCM cadastrado para validação fiscal
- **Canais de Parametrização**: Verde (liberado), Amarelo (conferência documental), Vermelho (conferência física), Cinza (auditoria)
- **IN 1.600/2015** e atualizações da RFB

### 12.2 Regimes Especiais

- **Ex-Tarifário**: Redução de imposto de importação para bens sem similar nacional — relevante para peças especializadas
- **Drawback**: Suspensão/isencção de tributos para peças destinadas à produção e posterior exportação
- **Entreposto Aduaneiro**: Armazenamento com suspensão de tributos

### 12.3 Impostos Incidentes

O sistema deve permitir o registro dos seguintes tributos para cálculo de custo total:

| Tributo | Alíquota Aproximada |
|--------|:---:|
| II (Imposto de Importação) | Variável por NCM |
| IPI (Imposto sobre Produtos Industrializados) | Variável |
| PIS/COFINS Importação | ~9,25% |
| ICMS | 17-18% (SP) |
| AFRMM (Adicional ao Frete) | 25% sobre o frete |

### 12.4 Portos Brasileiros

Configuração padrão para os principais portos de entrada:

- **Santos (SP)** — Principal porta de entrada, ~40% do fluxo nacional
- **Paranaguá (PR)** — Segundo maior, forte em carga geral
- **Itajaí / Navegantes (SC)** — Alternativa ao sul
- **Rio de Janeiro (RJ)** — Opção para região sudeste
- **Suape (PE)** — Opção para região nordeste

---

## 13. 📝 Glossário

| Termo | Definição |
|-------|-----------|
| **BL** | Bill of Lading — Conhecimento de Embarque Marítimo, documento de transporte |
| **ETA** | Estimated Time of Arrival — data prevista de chegada |
| **ETD** | Estimated Time of Departure — data prevista de saída |
| **DI** | Declaração de Importação — documento fiscal eletrônico registrado no Siscomex |
| **LI** | Licença de Importação — autorização prévia para importar |
| **NCM** | Nomenclatura Comum do 

