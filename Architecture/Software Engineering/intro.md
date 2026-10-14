# Prompt (Adapta)
Com foco em Engenharia de Software, em específico sobre Arquitetura e Design de Software, desenvolva todos os tópicos para orientar o time de desenvolvimento de software, de como criar software de qualidade e seguro.

# Guia de Arquitetura e Design de Software

Este guia reúne os tópicos essenciais para orientar o time na criação de software de qualidade e seguro, do nível estratégico (arquitetura) ao nível tático (código). Ele incorpora as práticas já padronizadas nas skills do time (Clean Architecture, SOLID, Lean e testes), aplicáveis independentemente da stack.

---

## 1. Fundamentos: arquitetura, design e decisões

**Arquitetura** são as decisões de alto nível e difícil reversão: estrutura do sistema, fronteiras entre módulos, tecnologias e forma de comunicação entre componentes. **Design** são as decisões de nível de código: classes, interfaces, padrões e organização interna. Regra prática: se mudar isso depois dói muito, é decisão de arquitetura.

Toda decisão arquitetural é um **trade-off** — não existe solução "de graça". Por isso:

- **Requisitos não funcionais dirigem a arquitetura**: performance, escalabilidade, disponibilidade, segurança, manutenibilidade e observabilidade (use a **ISO/IEC 25010** como checklist de atributos de qualidade).
- **Registre decisões em ADRs** (Architecture Decision Records): contexto, decisão tomada e consequências. Isso evita relitigar decisões antigas sem contexto.
- **Arquitetura evolutiva**: revise decisões com métricas reais (acoplamento, complexidade, lead time de deploy) em vez de apostas iniciais.

## 2. Princípios de design — a base de tudo

Antes de qualquer padrão ou framework, o time deve dominar:

- **SOLID**:
  - **S**RP — uma única responsabilidade por classe/módulo; um motivo para mudar.
  - **O**CP — aberto a extensão, fechado a modificação: use portas (interfaces) para estender sem alterar o que já funciona.
  - **L**SP — subtipos devem ser substituíveis sem quebrar contratos; prefira **composição a herança**.
  - **I**SP — interfaces pequenas e específicas; evite interfaces genéricas "que fazem tudo".
  - **D**IP — dependa de abstrações, nunca de implementações concretas.
- **DRY, KISS, YAGNI**: não duplique conhecimento, mantenha simples, não construa o que não é necessário agora. ⚠️ Código especulativo é dívida futura.
- **Alta coesão e baixo acoplamento**: módulos com propósito único e dependências mínimas entre si.
- **Separação de responsabilidades (SoC)** e **Lei de Deméter**: um objeto não deve conhecer os detalhes internos de quem ele apenas usa.

## 3. Estilos e padrões arquiteturais

- **Em camadas (layered)**: apresentação → aplicação → domínio → infraestrutura. Simples e conhecido, mas exige disciplina para não virar um "funil" onde tudo depende de tudo.
- **Clean Architecture / Hexagonal (Ports & Adapters)**: o domínio no centro, com dependências apontando sempre **para dentro**. Regra rígida do time: **domain e application não podem importar nada de infrastructure** — regra de negócio não depende de framework, banco ou I/O.
- **Monolito modular vs. microsserviços**: comece com um **monolito modular** bem delimitado. Microsserviços só quando houver necessidade real (escala independente, times autônomos, deploy independente) — eles cobram caro em observabilidade, resiliência e consistência eventual.
- **Event-driven**: ótimo para desacoplamento, mas exige **idempotência** e rastreabilidade desde o primeiro dia.
- **CQRS e Event Sourcing**: apenas para domínios com necessidade real de modelos de leitura/escrita distintos ou histórico completo de eventos. Não use por modinha.

## 4. Design tático: do domínio ao código

- **DDD (Domain-Driven Design)**: linguagem onipresente (o código fala a língua do negócio), **entidades**, **Value Objects** (imutáveis — no Java, records), **agregados** com fronteiras claras, **bounded contexts** bem mapeados.
- **Design de APIs**: contratos explícitos e versionados, erros padronizados, paginação consistente, **idempotência** em operações críticas. A API é um compromisso público — mude com cuidado.
- **Tratamento de erros**: centralizado (ex.: `@ControllerAdvice` no Spring), separando erros de domínio de erros técnicos. **Nunca engolir exceções** nem vazar stack trace ao cliente.
- **Padrões de projeto (GoF)**: use para resolver problemas reais, não como enfeite. Padrão sem problema é complexidade gratuita.
- **Higiene de código**: injeção de dependência **exclusivamente via construtor**, nomes expressivos, métodos pequenos, sem código morto nem imports não utilizados.

## 5. Qualidade: testes, automação e manutenibilidade

- **Pirâmide de testes**: muitos testes unitários (rápidos e isolados), alguns de integração (com dependências reais via **Testcontainers**), poucos E2E.
- **Testes nascem com o código**: nunca entregue código sem teste. Estrutura **Given-When-Then** e nomes que descrevem comportamento (`shouldReturnXWhenY`). Meta de cobertura: **80–90% nas camadas centrais** (domain e application).
- **Code review obrigatório**: revisão é controle de qualidade **e** compartilhamento de conhecimento. Use um checklist objetivo (SOLID, Lean, testes, segurança) nos diffs.
- **Análise estática no pipeline**: linters, detecção de complexidade, vulnerabilidades e código duplicado como portão de qualidade no CI.
- **Refatoração contínua**: dívida técnica existe — registre-a, priorize-a e pague os juros conscientemente, em vez de deixá-la virar avalanche.
- **CI/CD**: integração frequente, pipeline com portões de qualidade, deploys pequenos e reversíveis.
- **Observabilidade**: logs estruturados em JSON com **Correlation ID**, métricas (ex.: Micrometer/Prometheus), health checks e tracing distribuído. Se não dá para observar, não dá para operar.

## 6. Segurança desde o design (secure by design)

⚠️ Segurança não é fase final do projeto — é requisito de design. Princípio do **shift-left**: trate segurança desde a modelagem.

- **OWASP Top 10 como checklist obrigatório**: injection (SQL/NoSQL), falhas de autenticação, exposição de dados sensíveis, controle de acesso quebrado, SSRF, entre outros. Sempre use **consultas parametrizadas** e valide/sanitize **toda entrada** nas fronteiras do sistema.
- **Autenticação e autorização**: OAuth 2.1/OIDC, JWT com validação adequada (assinatura, expiração, audience), autorização verificada **no servidor, sempre** — nunca confie apenas no frontend.
- **Menor privilégio e defesa em profundidade**: cada componente recebe só as permissões de que precisa; sobreponha camadas de proteção (rede, aplicação, dados).
- **Criptografia**: TLS em trânsito, criptografia em repouso para dados sensíveis, senhas com **bcrypt ou argon2** (nunca MD5/SHA puro).
- **Gestão de segredos**: vault ou cofre dedicado — **nunca** credenciais em código, configuração versionada ou repositório.
- **Cadeia de suprimentos**: dependências atualizadas, análise automática de vulnerabilidades (SCA) e SBOM para rastreabilidade.
- **Threat modeling** (ex.: STRIDE) para funcionalidades críticas, e **SAST/DAST/pentest** no ciclo de desenvolvimento.
- **LGPD e privacidade**: minimização de dados, base legal clara, atenção aos direitos do titular desde o modelagem do banco.

## 7. Governança e cultura do time

- **Documentação viva**: ADRs para decisões, **modelo C4** (contexto, contêiner, componente, código) para visualizar a arquitetura em diferentes níveis. Documentação desatualizada é pior que nenhuma — mantenha-a enxuta e no fluxo.
- **Definition of Done completa**: só está "pronto" se tiver testes, passou nos portões de segurança e tem observabilidade configurada.
- **Padrões compartilhados**: convenções de código e arquiteturais documentadas no repositório (ex.: arquivo de convenções lido por humanos e por assistentes de IA).
- **Papéis claros nas revisões**: quem decide arquitetura (valida contratos e fronteiras), quem implementa, quem valida qualidade e operação.
- **Melhoria contínua**: retrospectivas que discutam dívida técnica e incidentes de segurança como oportunidades de aprendizado, não de culpa.

---

**Resumindo**

- **Qualidade nasce do design**: SOLID, coesão, baixo acoplamento e domínio isolado (Clean Architecture) valem mais que qualquer ferramenta.
- **Testes e automação são inegociáveis**: código sem teste não está pronto; pipeline com portões de qualidade protege o padrão.
- **Segurança é requisito de design**: OWASP, menor privilégio, criptografia e gestão de segredos desde a primeira linha de código.
- **Decisões documentadas (ADRs + C4)** mantêm o alinhamento do time e evitam retrabalho.

Para facilitar a navegação do time, vou gerar um mapa mental com a estrutura completa dos tópicos:O mapa mental com os 7 eixos e seus subtópicos foi gerado — o time pode explorá-lo interativamente como referência rápida.

## Mapa Mental 
```mindmap
{
  "colorOverridesByNodeId": {},
  "mindmap": {
    "version": "1",
    "title": "Arquitetura e Design de Software",
    "layoutMode": "distributed",
    "tree": {
      "id": "root",
      "label": "Arquitetura e Design de Software",
      "children": [
        {
          "id": "fundamentos",
          "label": "1. Fundamentos",
          "children": [
            {
              "id": "fund-arq-design",
              "label": "Arquitetura vs Design"
            },
            {
              "id": "fund-tradeoffs",
              "label": "Trade-offs e ADRs"
            },
            {
              "id": "fund-iso",
              "label": "Atributos de Qualidade ISO/IEC 25010"
            },
            {
              "id": "fund-evolutiva",
              "label": "Arquitetura Evolutiva"
            }
          ]
        },
        {
          "id": "principios",
          "label": "2. Princípios de Design",
          "children": [
            {
              "id": "princ-solid",
              "label": "SOLID: SRP, OCP, LSP, ISP, DIP"
            },
            {
              "id": "princ-kisses",
              "label": "DRY, KISS e YAGNI"
            },
            {
              "id": "princ-coeso",
              "label": "Alta coesão e baixo acoplamento"
            },
            {
              "id": "princ-demeter",
              "label": "Separação de responsabilidades e Lei de Deméter"
            },
            {
              "id": "princ-composicao",
              "label": "Composição sobre herança"
            }
          ]
        },
        {
          "id": "padroes",
          "label": "3. Padrões Arquiteturais",
          "children": [
            {
              "id": "pad-camadas",
              "label": "Arquitetura em camadas"
            },
            {
              "id": "pad-clean",
              "label": "Clean Architecture e Hexagonal"
            },
            {
              "id": "pad-monolito",
              "label": "Monolito modular vs Microsserviços"
            },
            {
              "id": "pad-event",
              "label": "Event-driven com idempotência"
            },
            {
              "id": "pad-cqrs",
              "label": "CQRS e Event Sourcing com parcimônia"
            }
          ]
        },
        {
          "id": "tatico",
          "label": "4. Design Tático",
          "children": [
            {
              "id": "tatico-ddd",
              "label": "DDD: Linguagem, Entidades, VOs, Agregados, Contextos"
            },
            {
              "id": "tatico-api",
              "label": "Design de APIs: versionamento e idempotência"
            },
            {
              "id": "tatico-erro",
              "label": "Tratamento de erros centralizado"
            },
            {
              "id": "tatico-gof",
              "label": "Padrões GoF com critério"
            },
            {
              "id": "tatico-higiene",
              "label": "Higiene de código e injeção via construtor"
            }
          ]
        },
        {
          "id": "qualidade",
          "label": "5. Qualidade",
          "children": [
            {
              "id": "qual-testes",
              "label": "Pirâmide de testes, TDD e Given-When-Then"
            },
            {
              "id": "qual-integracao",
              "label": "Testcontainers e cobertura 80-90%"
            },
            {
              "id": "qual-ci",
              "label": "Code review, análise estática e CI/CD"
            },
            {
              "id": "qual-divida",
              "label": "Refatoração e gestão de dívida técnica"
            },
            {
              "id": "qual-obs",
              "label": "Observabilidade: logs, métricas e tracing"
            }
          ]
        },
        {
          "id": "seguranca",
          "label": "6. Segurança",
          "children": [
            {
              "id": "seg-design",
              "label": "Secure by design e shift-left"
            },
            {
              "id": "seg-owasp",
              "label": "OWASP Top 10 e Threat Modeling STRIDE"
            },
            {
              "id": "seg-auth",
              "label": "OAuth2, OIDC, JWT e autorização"
            },
            {
              "id": "seg-cripto",
              "label": "Criptografia, TLS, bcrypt e gestão de segredos"
            },
            {
              "id": "seg-supply",
              "label": "SCA, SBOM, SAST, DAST e LGPD"
            }
          ]
        },
        {
          "id": "governanca",
          "label": "7. Governança e Cultura",
          "children": [
            {
              "id": "gov-doc",
              "label": "Documentação viva: ADRs e modelo C4"
            },
            {
              "id": "gov-dod",
              "label": "Definition of Done com testes e segurança"
            },
            {
              "id": "gov-convencoes",
              "label": "Convenções compartilhadas e papéis"
            },
            {
              "id": "gov-melhoria",
              "label": "Melhoria contínua e retrospectivas"
            }
          ]
        }
      ]
    }
  }
}
```

**Se quiser, posso:**
- Gerar este guia como **documento Word ou PDF** para distribuir ao time
- **Detalhar um tópico específico** (ex.: segurança ou Clean Architecture) com exemplos de código
- Criar um **checklist prático de code review** baseado nesses princípios
