# Prompt
Criar um artigo completo sobre IAM (Identity and Access Management), organizado por tópicos, vantagens e exemplos complexos.

# Merlin (Magic)

# IAM (Identity and Access Management): guia completo

## 1. O que é IAM?

**Identity and Access Management (IAM)**, ou **Gestão de Identidade e Acesso**, é o conjunto de políticas, processos e tecnologias usado para controlar **quem** pode acessar **quais recursos**, **em que condições** e **com quais permissões**.

IAM abrange pessoas, aplicações, serviços, dispositivos e cargas de trabalho. Seu objetivo é assegurar que cada identidade seja reconhecida corretamente e receba apenas o acesso necessário, pelo tempo necessário.

Uma forma simples de resumir IAM é:

> **Identidade + autenticação + autorização + auditoria = acesso governado.**

IAM não é apenas um produto ou um diretório de usuários. É uma disciplina de segurança e governança que conecta o ciclo de vida das identidades às regras de acesso em sistemas locais, aplicações SaaS, plataformas em nuvem e ambientes híbridos.

## 2. Conceitos fundamentais

### 2.1 Identidade

Uma identidade é uma representação digital de uma entidade que precisa interagir com um sistema. Pode representar:

- Uma pessoa: funcionário, prestador de serviços, cliente ou parceiro.
- Uma identidade não humana: aplicação, máquina virtual, contêiner, robô de automação, API ou dispositivo.

Uma identidade pode ter atributos como identificador, departamento, função, localização, vínculo empregatício, nível de risco e estado da conta.

### 2.2 Autenticação

Autenticação é a verificação de que a identidade é quem afirma ser. Os fatores mais comuns são:

- **Conhecimento:** senha, PIN ou frase secreta.
- **Posse:** chave de segurança, telefone ou token.
- **Inerência:** característica biométrica, como impressão digital.

A **autenticação multifator (MFA)** exige mais de um fator. Uma senha combinada com uma chave física, por exemplo, dificulta o uso de credenciais roubadas.

### 2.3 Autorização

Autorização determina o que uma identidade autenticada pode fazer. Uma política pode permitir a leitura de um arquivo, negar a alteração de dados financeiros e exigir aprovação para executar uma operação privilegiada.

Autenticação responde **“quem é você?”**; autorização responde **“o que você pode fazer?”**.

### 2.4 Conta, papel, grupo e permissão

- **Conta:** registro usado para representar uma identidade em determinado sistema.
- **Permissão:** autorização específica, como ler, criar ou excluir um recurso.
- **Papel (role):** conjunto de permissões associado a uma função ou contexto.
- **Grupo:** coleção de identidades à qual podem ser atribuídas permissões ou políticas comuns.

A mesma pessoa pode ter várias contas em sistemas diferentes. Um programa de IAM procura governar essas contas de forma coerente, reduzindo contas órfãs, permissões excessivas e inconsistências.

### 2.5 Federação e SSO

A **federação de identidade** permite que um sistema confie na autenticação realizada por outro provedor de identidade, dentro de uma relação configurada entre as partes.

O **Single Sign-On (SSO)** permite que o usuário acesse várias aplicações com uma sessão autenticada comum, sem digitar as credenciais repetidamente. SSO melhora a experiência e facilita a aplicação centralizada de políticas, mas exige proteção robusta da conta e da sessão principal.

### 2.6 Provisionamento e desprovisionamento

Provisionamento cria contas e concede acessos quando alguém entra, muda de função ou passa a usar um serviço. Desprovisionamento remove ou bloqueia contas e acessos quando deixam de ser necessários — por exemplo, após o fim do vínculo profissional.

## 3. Componentes de uma estratégia IAM

### 3.1 Diretório e provedor de identidade

O diretório mantém identidades e atributos; o provedor de identidade (IdP) autentica usuários, emite declarações de identidade e pode aplicar políticas de acesso. Em organizações grandes, fontes distintas — como RH, diretórios corporativos e sistemas de parceiros — podem alimentar o ciclo de identidade.

### 3.2 Gestão do ciclo de vida

Um processo de ciclo de vida bem definido cobre:

1. **Entrada:** criação da identidade e concessão inicial de acessos conforme função e contexto.
2. **Mudança:** revisão de permissões quando mudam cargo, equipe, localização ou responsabilidades.
3. **Saída:** revogação rápida de sessões, credenciais, contas e acessos relacionados.

### 3.3 Governança de identidade (IGA)

A **Identity Governance and Administration (IGA)** trata da governança: quem aprovou um acesso, por que ele existe, se continua necessário e se está de acordo com políticas internas e exigências regulatórias. Inclui revisões periódicas, certificações de acesso, segregação de funções e trilhas de auditoria.

### 3.4 Gestão de acesso privilegiado (PAM)

A **Privileged Access Management (PAM)** protege contas e sessões com poderes elevados, como administradores de sistemas, bancos de dados e nuvens. Práticas comuns incluem credenciais guardadas em cofres, acesso temporário, aprovação, gravação de sessões e uso de contas administrativas separadas das contas cotidianas.

### 3.5 Acesso de clientes (CIAM)

O **Customer IAM (CIAM)** administra identidades de clientes e usuários externos. Dá atenção especial a cadastro, login, recuperação de conta, consentimento, privacidade, escalabilidade e experiência do usuário.

### 3.6 Identidades de máquinas e cargas de trabalho

Aplicações e serviços também precisam autenticar-se e receber permissões. Segredos estáticos compartilhados podem ser difíceis de rotacionar e rastrear. Estratégias mais seguras favorecem credenciais de curta duração, identidades vinculadas à carga de trabalho e permissões restritas ao serviço e à tarefa necessários.

## 4. Modelos de controle de acesso

### 4.1 RBAC — controle baseado em papéis

No **Role-Based Access Control**, as permissões são associadas a papéis e os usuários recebem papéis. Exemplo: o papel “analista financeiro” permite consultar relatórios financeiros, enquanto “aprovador financeiro” permite autorizar pagamentos dentro de limites definidos.

**Ponto forte:** é relativamente simples de administrar quando as funções são estáveis e bem definidas.

**Limitação:** papéis numerosos ou genéricos demais podem gerar complexidade ou conceder acesso além do necessário.

### 4.2 ABAC — controle baseado em atributos

No **Attribute-Based Access Control**, uma política avalia atributos da identidade, do recurso, da ação e do contexto. Uma regra pode permitir acesso a um registro se o usuário pertencer à equipe responsável, estiver em uma região autorizada e usar um dispositivo gerenciado.

**Ponto forte:** expressa regras contextuais e granulares.

**Limitação:** exige atributos confiáveis, políticas compreensíveis e boa capacidade de teste e auditoria.

### 4.3 ReBAC — controle baseado em relações

No **Relationship-Based Access Control**, o acesso depende das relações entre a identidade e o recurso. Por exemplo, um médico pode consultar pacientes sob sua responsabilidade, ou um colaborador pode editar documentos compartilhados com sua equipe.

É útil em aplicações com estruturas de compartilhamento dinâmicas, como plataformas colaborativas e serviços multiusuário.

### 4.4 Princípio do menor privilégio e Zero Trust

O **menor privilégio** significa conceder somente as permissões necessárias para uma tarefa, evitando acessos permanentes e amplos sem justificativa.

Uma abordagem **Zero Trust** não presume confiança automática por causa da rede ou da localização. A decisão de acesso considera identidade, recurso, contexto e risco, podendo exigir verificação contínua, MFA ou controles adicionais para ações sensíveis.

## 5. Fluxo típico de uma decisão de acesso

Uma decisão de acesso pode seguir esta sequência:

1. A identidade solicita uma ação sobre um recurso.
2. O sistema verifica a identidade e a validade da sessão.
3. O mecanismo de política avalia permissões, atributos, relações e contexto.
4. Controles adicionais são aplicados quando necessário, como MFA, aprovação ou restrição por dispositivo.
5. A decisão é permitir, negar ou exigir uma condição adicional.
6. O evento é registrado para monitoramento e auditoria.

Uma política deve ser clara tanto na concessão quanto na negação. “Acesso negado” também precisa gerar informações úteis para investigação, sem expor dados sensíveis.

## 6. Vantagens do IAM

### Segurança e redução de risco

- Reduz contas órfãs, credenciais compartilhadas e acessos esquecidos.
- Limita o impacto de uma conta comprometida por meio do menor privilégio e de acessos temporários.
- Facilita MFA, políticas adaptativas e proteção de contas privilegiadas.
- Ajuda a reduzir movimentação lateral após uma invasão, se os acessos estiverem segmentados.

### Eficiência operacional

- Automatiza criação, alteração e remoção de contas.
- Diminui solicitações manuais repetitivas e inconsistências entre sistemas.
- Simplifica o acesso a aplicações por meio de SSO e provisionamento integrado.
- Ajuda equipes de TI e segurança a identificar rapidamente quem tem acesso a quê.

### Governança e conformidade

- Mantém registros de concessões, aprovações, revisões e alterações.
- Apoia auditorias e demonstração de controles internos.
- Facilita revisões periódicas e a identificação de conflitos de função.
- Ajuda a aplicar políticas de retenção, privacidade e segregação de funções.

IAM não garante conformidade por si só: os controles precisam estar corretamente desenhados, operados e comprovados.

### Experiência do usuário e agilidade

- Reduz a quantidade de senhas e logins repetidos com SSO.
- Permite oferecer acesso mais rápido a funcionários, parceiros e clientes.
- Evita que políticas de segurança dependam apenas de processos manuais.

## 7. Exemplos complexos

### Exemplo 1: entrada, mudança de função e saída em uma empresa global

Uma empresa possui equipes nos Estados Unidos, Brasil e Alemanha, com sistemas de RH, suporte ao cliente, finanças e engenharia distribuídos entre nuvem e infraestrutura local.

**Entrada:** ao registrar uma nova contratação no sistema de RH, um fluxo cria a identidade corporativa. A função, a unidade, a localização e o tipo de vínculo determinam um conjunto inicial de acessos. A pessoa recebe SSO e MFA; acessos a dados regulados exigem aprovação adicional.

**Mudança:** a funcionária passa do suporte para finanças. O fluxo não deve simplesmente acrescentar os novos acessos aos antigos. Ele remove permissões incompatíveis, solicita aprovação para sistemas financeiros e verifica segregação de funções — por exemplo, impedir que a mesma pessoa cadastre um fornecedor e aprove seu pagamento.

**Saída:** quando o RH registra a data de desligamento, o IAM bloqueia a autenticação, revoga sessões e tokens, remove grupos e inicia a desativação das contas em aplicações integradas. Contas não integradas entram em uma fila de confirmação. Registros de auditoria são preservados de acordo com as políticas aplicáveis.

**Desafio real:** uma aplicação legada não aceita provisionamento automatizado. A organização precisa manter um controle compensatório, atribuir um responsável, revisar a conta e comprovar o encerramento. A automação não substitui a verificação dos sistemas que ela não alcança.

### Exemplo 2: acesso temporário de fornecedor a um ambiente de produção

Um fornecedor precisa diagnosticar uma falha em um serviço de produção por até duas horas.

1. O responsável interno abre uma solicitação com sistema, motivo, escopo e duração.
2. O gestor do serviço aprova; uma política verifica contrato vigente, treinamento e janela de manutenção.
3. É criada uma identidade externa individual, sem conta compartilhada.
4. O acesso exige MFA resistente a phishing quando disponível e uma estação gerenciada ou ambiente remoto controlado.
5. O fornecedor recebe permissões temporárias apenas para os recursos envolvidos, sem acesso amplo ao restante da nuvem.
6. A sessão privilegiada é registrada; comandos sensíveis podem exigir aprovação em tempo real.
7. Ao fim do prazo, o acesso expira automaticamente. A equipe revisa os registros e confirma a remoção de credenciais temporárias.

**Risco que o desenho reduz:** credenciais permanentes de terceiros, sem responsável claro, podem permanecer ativas depois do término do trabalho. O prazo curto e a atribuição individual melhoram a contenção e a rastreabilidade.

### Exemplo 3: autorização contextual em uma plataforma de saúde

Uma plataforma conecta pacientes, médicos, enfermagem, faturamento e laboratórios externos. A autorização não pode depender apenas do papel do usuário, porque informações clínicas são sensíveis e a relação assistencial pode mudar.

Uma regra possível combina:

- Papel profissional e estado válido da licença.
- Relação atual entre profissional e paciente.
- Finalidade da ação: tratamento, faturamento ou auditoria.
- Sensibilidade do dado solicitado.
- Local, dispositivo e nível de autenticação da sessão.

Um médico com vínculo assistencial ativo pode consultar dados necessários ao tratamento. Um profissional de faturamento pode acessar informações administrativas e códigos relevantes, mas não necessariamente notas clínicas completas. Uma consulta de emergência pode usar um procedimento excepcional de “acesso de emergência”, com justificativa obrigatória, alerta e revisão posterior.

**Complexidade:** um usuário pode mudar de equipe durante o turno, um paciente pode revogar uma autorização e um laboratório pode precisar apenas de resultados específicos. Por isso, atributos e relações precisam ser atualizados com rapidez, e cada decisão deve ser auditável.

### Exemplo 4: aplicação SaaS multiempresa com isolamento de clientes

Uma plataforma SaaS atende milhares de organizações. Cada cliente possui administradores, funcionários, convidados e integrações próprias.

A autorização verifica simultaneamente a identidade, a organização ativa, o papel dentro daquela organização, a relação com o objeto solicitado e a política do recurso. Um administrador de uma empresa não deve acessar dados de outra simplesmente porque conhece o identificador de um registro.

Medidas importantes incluem:

- Vincular cada solicitação a um contexto de organização validado no servidor.
- Aplicar autorização em cada operação de leitura e escrita, não apenas na interface.
- Usar políticas explícitas para convites, compartilhamentos e contas de serviço.
- Registrar alterações administrativas e acessos a dados sensíveis.
- Testar tentativas de trocar identificadores e atravessar limites entre clientes.

**Falha ilustrativa:** esconder um botão na interface não impede que alguém chame diretamente uma API. A autorização precisa existir no serviço que protege o dado.

### Exemplo 5: identidade de aplicação em uma arquitetura de microsserviços

Um serviço de pedidos precisa consultar o estoque e solicitar uma cobrança. Se usar uma única chave estática com acesso amplo a todos os serviços, o comprometimento de um componente pode expor toda a plataforma.

Uma arquitetura mais restrita atribui identidade própria a cada serviço. O serviço de pedidos recebe autorização para consultar o estoque e iniciar uma cobrança limitada, mas não para exportar a base de clientes nem administrar a infraestrutura. Credenciais de curta duração reduzem a exposição; políticas de rede e autorização no serviço receptor acrescentam camadas de controle.

A equipe monitora uso anômalo, mantém responsável por cada identidade não humana e remove identidades ligadas a componentes descontinuados. Segredos inevitáveis devem ser guardados e rotacionados, não embutidos no código-fonte ou em imagens de contêiner.

## 8. Riscos e erros comuns

- **Acesso excessivo:** permissões amplas concedidas “por precaução” e nunca removidas.
- **Contas órfãs:** identidades que continuam ativas após o fim do vínculo ou do serviço.
- **Papéis mal definidos:** papéis genéricos, sobrepostos ou difíceis de revisar.
- **Exceções permanentes:** acessos emergenciais que não expiram nem passam por revisão.
- **Dependência de senha:** ausência de MFA ou proteção insuficiente contra phishing e reutilização de credenciais.
- **Contas compartilhadas:** dificultam atribuir ações a uma pessoa e revogar o acesso individualmente.
- **Autorização apenas na interface:** APIs ou serviços deixam de verificar as permissões no servidor.
- **Identidades de máquina esquecidas:** chaves e contas de serviço permanecem ativas sem proprietário.
- **Atributos incorretos:** políticas contextuais tomam decisões erradas quando dados de origem estão desatualizados.
- **Automação sem reconciliação:** um fluxo reporta sucesso, mas uma aplicação não integrada continua com a conta ativa.

## 9. Práticas recomendadas para implantação

1. **Inventariar identidades e recursos:** incluir pessoas, terceiros, aplicações, dispositivos, contas privilegiadas e serviços.
2. **Definir fontes confiáveis de atributos:** esclarecer qual sistema mantém cargo, vínculo, departamento e estado da conta.
3. **Priorizar sistemas críticos:** começar por dados sensíveis, administração de nuvem, finanças e aplicações expostas à internet.
4. **Aplicar MFA e proteger contas privilegiadas:** dar prioridade a controles fortes para administradores e acessos remotos.
5. **Automatizar o ciclo de vida:** ligar eventos de contratação, transferência e desligamento aos sistemas de destino, com monitoramento de falhas.
6. **Usar acesso temporário quando possível:** especialmente para tarefas administrativas e acesso de terceiros.
7. **Revisar permissões periodicamente:** direcionar revisões por risco e importância, em vez de depender de aprovações automáticas.
8. **Testar políticas antes de aplicá-las:** usar ambientes de teste, registros de decisão e cenários de negação, não apenas os casos permitidos.
9. **Registrar e monitorar:** detectar concessões incomuns, uso fora de contexto e tentativas repetidas de acesso negado.
10. **Planejar exceções e recuperação:** documentar acesso de emergência, responsáveis, expiração e revisão posterior.
11. **Medir resultados:** acompanhar tempo de provisionamento, contas sem proprietário, acessos privilegiados permanentes, falhas de desprovisionamento e exceções vencidas.
12. **Tratar IAM como responsabilidade compartilhada:** RH, segurança, TI, donos de aplicações, jurídico e áreas de negócio precisam colaborar.

## 10. IAM, autenticação e segurança: distinções importantes

IAM é mais amplo do que login. Um login protegido por MFA pode autenticar corretamente um usuário, mas ainda permitir que ele veja dados que não deveria. Da mesma forma, uma política de autorização detalhada não ajuda se as identidades estiverem desatualizadas ou se contas antigas continuarem ativas.

Uma implementação eficaz combina identidade confiável, autenticação apropriada ao risco, autorização consistente, ciclo de vida automatizado, governança e monitoramento. Cada camada resolve uma parte diferente do problema.

## 11. Indicadores para avaliar maturidade

Os indicadores devem orientar decisões, não apenas produzir números. Exemplos úteis:

- Percentual de aplicações críticas integradas ao provisionamento central.
- Tempo médio entre desligamento e revogação efetiva dos acessos.
- Número de contas privilegiadas permanentes e sem responsável identificado.
- Quantidade de contas de serviço sem uso ou sem proprietário.
- Percentual de acessos sensíveis revisados dentro do prazo.
- Taxa de exceções vencidas ainda ativas.
- Incidentes ou alertas associados a credenciais comprometidas.
- Volume de concessões manuais e tempo necessário para aprovações.

Uma métrica precisa de definição consistente. Por exemplo, “revogado” deve significar que a sessão e os tokens relevantes foram invalidados, e não apenas que o usuário foi removido de um grupo em um único sistema.

## 12. Conclusão

IAM é a base operacional para administrar identidades e acessos em organizações modernas. Quando bem desenhado, ajuda a reduzir privilégios desnecessários, acelera o acesso legítimo, melhora a resposta a mudanças de pessoal e fornece evidências para governança e auditoria.

O desafio está em manter as decisões corretas ao longo do tempo: pessoas mudam de função, fornecedores encerram contratos, aplicações são substituídas e serviços automatizados surgem e desaparecem. Uma estratégia sustentável combina menor privilégio, autenticação forte, políticas claras, automação supervisionada, revisões periódicas e responsabilidade definida para cada identidade e acesso.

