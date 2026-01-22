
## Design Patterns

### **Design Patterns para Migração, Integração e Arquitetura Corporativa**

#### **I. Padrões de Transição e Coexistência**

Esses padrões são focados em gerenciar a mudança gradual de um sistema para outro, permitindo que ambos coexistam por um período.

1.  **Strangler Fig Pattern (O Estrangulador)**
    *   **Descrição:** Consiste em construir um novo sistema (ou partes dele) ao redor do sistema legado, interceptando suas funcionalidades e substituindo-as gradualmente até que o sistema antigo seja completamente "estrangulado" e desativado.
    *   **Utilização:** Ideal para migrações complexas de ERP, onde uma substituição "big bang" é inviável. Permite migrar funcionalidades de forma incremental, minimizando riscos e interrupções.

2.  **Anti-Corruption Layer (ACL - Camada Anti-Corrupção)**
    *   **Descrição:** Atua como uma camada de tradução entre um novo sistema (domínio *limpo*) e um sistema legado (domínio *sujo*), protegendo o novo sistema da complexidade, modelos de dados e semântica do sistema antigo.
    *   **Utilização:** Essencial em integrações de ERP para evitar que o modelo de dados e a lógica de negócio do S/4HANA sejam "contaminados" pelos conceitos do legado. Garante que o S/4H4NA mantenha sua pureza e aderência aos padrões SAP.

3.  **Parallel Run Pattern (Execução Paralela)**
    *   **Descrição:** Ambos os sistemas (legado e novo) rodam simultaneamente por um período, processando as mesmas transações. Os resultados de ambos são comparados e validados para garantir a precisão do novo sistema antes do *go-live* final.
    *   **Utilização:** Oferece máxima segurança antes do corte, pois permite a validação exaustiva do novo sistema sob carga real sem impactar a operação.

4.  **Feature Toggle / Feature Flag (Alternador de Funcionalidade)**
    *   **Descrição:** Permite ligar ou desligar funcionalidades específicas em um sistema em tempo de execução, sem a necessidade de um novo *deploy*. As *flags* podem ser controladas por usuários, grupos, ou ambientes.
    *   **Utilização:** No contexto do Strangler Fig, pode ser usado no API Gateway para controlar qual sistema (legado ou S/4HANA) uma funcionalidade específica está atendendo. Facilita *canary releases* e *A/B testing*.

#### **II. Padrões de Integração e Sincronização de Dados**

Focados em como os dados e as interações fluem entre os diferentes sistemas.

5.  **Change Data Capture (CDC)**
    *   **Descrição:** Uma técnica que captura e replica alterações (inserções, atualizações, exclusões) nos dados de um banco de dados de origem para um ou mais destinos em tempo real ou quase real.
    *   **Utilização:** Fundamental para sincronizar dados do ERP legado para o S/4HANA durante a fase de coexistência, minimizando a janela de indisponibilidade no *cutover* e garantindo consistência.

6.  **Outbox Pattern (Padrão da Caixa de Saída)**
    *   **Descrição:** Garante a atomicidade entre a gravação de dados em um banco de dados local e a publicação de um evento/mensagem correspondente. Em vez de publicar diretamente no barramento, o evento é primeiro gravado em uma tabela "outbox" *dentro da mesma transação de banco de dados*. Um processo separado, o "relay", lê e publica esses eventos.
    *   **Utilização:** Crucial para garantir que, quando uma transação de negócio ocorre no sistema legado (ou no S/4HANA), o evento correspondente seja publicado de forma confiável no barramento de eventos, mesmo que ocorra uma falha logo após o commit da transação.

7.  **Publish-Subscribe (Pub/Sub - Publicar/Assinar)**
    *   **Descrição:** Um padrão de mensageria onde os *publishers* (editores) enviam mensagens para um tópico ou fila sem conhecer os *subscribers* (assinantes), e os *subscribers* recebem todas as mensagens de tópicos que assinaram.
    *   **Utilização:** Base de muitas arquiteturas orientadas a eventos. Usado para desacoplar sistemas, onde o S/4HANA publica eventos (ex: `SalesOrder.Created`) e diversos sistemas (legado, BI, extensões) podem consumi-los sem acoplamento direto.

8.  **API Gateway**
    *   **Descrição:** Um único ponto de entrada para todas as requisições de clientes ou outros sistemas. O Gateway pode rotear requisições para diferentes serviços/sistemas, atuar como proxy reverso, aplicar segurança, *throttling*, cache, e transformar requisições/respostas.
    *   **Utilização:** É a espinha dorsal do Strangler Fig Pattern, mas também pode ser usado de forma independente para gerenciar e expor as APIs do S/4HANA de forma controlada, segura e performática.

9.  **Message Broker / Event Broker**
    *   **Descrição:** Um software que permite que aplicações se comuniquem trocando mensagens, atuando como intermediário. Ele garante que as mensagens sejam entregues aos destinos corretos e oferece funcionalidades como persistência, *queues* e *topics*.
    *   **Utilização:** Essencial para a implementação de CDC, Outbox, Pub/Sub e Event Sourcing, fornecendo a infraestrutura para o fluxo de eventos e mensagens entre o legado, o S/4HANA e sistemas satélites (ex: Apache Kafka, SAP Event Mesh, RabbitMQ).

#### **III. Padrões Arquiteturais e de Gerenciamento de Dados**

Esses padrões abordam a estrutura geral do sistema e como os dados são gerenciados.

10. **Command Query Responsibility Segregation (CQRS)**
    *   **Descrição:** Separa o modelo de dados e as operações para escrita (Comandos) do modelo de dados e operações para leitura (Consultas). O sistema pode ter modelos de dados e bancos de dados separados e otimizados para cada propósito.
    *   **Utilização:** Embora o S/4HANA seja um sistema transacional complexo, o CQRS pode ser aplicado nas camadas de integração ou em extensões. Por exemplo, os eventos do S/4HANA podem alimentar modelos de leitura otimizados para dashboards de BI ou para a experiência do usuário em um portal, desacoplando o desempenho das consultas do sistema transacional principal.

11. **Event Sourcing**
    *   **Descrição:** Em vez de armazenar apenas o estado atual de uma entidade, o Event Sourcing armazena todas as mudanças de estado como uma sequência imutável de eventos ordenados. O estado atual é reconstruído ao "reproduzir" esses eventos.
    *   **Utilização:** Cria um log de auditoria completo e imutável de todas as transações, facilitando a depuração, a análise temporal e a integração com outros sistemas. Pode ser usado em conjunto com CDC para transformar as alterações de dados em eventos de negócio.

12. **Event-Driven Architecture (EDA - Arquitetura Orientada a Eventos)**
    *   **Descrição:** Um estilo arquitetural onde os componentes do sistema se comunicam reagindo a eventos. Os serviços são desacoplados e interagem de forma assíncrona.
    *   **Utilização:** Adoção de EDA com S/4HANA é uma tendência. O S/4HANA pode publicar eventos de negócio (via SAP Event Mesh), e sistemas externos ou extensões podem reagir a eles. Promove escalabilidade, resiliência e agilidade.

#### **IV. Padrões de Resiliência e Transações Distribuídas**

Esses padrões ajudam a construir sistemas que podem se recuperar de falhas e manter a consistência em ambientes distribuídos.

13. **Saga Pattern (Padrão Saga)**
    *   **Descrição:** Um padrão para gerenciar transações distribuídas em sistemas baseados em microserviços ou em arquiteturas com múltiplos serviços acoplados. Uma saga é uma sequência de transações locais, onde cada transação publica um evento para acionar a próxima etapa. Se uma etapa falhar, transações de compensação são executadas para desfazer as etapas anteriores.
    *   **Utilização:** Essencial para processos de negócio complexos que atravessam o ERP legado, o S/4HANA e outros sistemas. Por exemplo, um processo de "pedido a pagamento" que inicia no legado, passa pelo S/4HANA e termina em um sistema bancário, pode usar uma Saga para garantir que, se o pagamento falhar, o pedido seja cancelado em todos os sistemas envolvidos.

14. **Circuit Breaker (Disjuntor)**
    *   **Descrição:** Um padrão de resiliência que previne que um sistema tente repetidamente chamar um serviço que está falhando, permitindo que o serviço se recupere e economizando recursos do chamador. Ele "abre" (interrompe chamadas) se o número de falhas atingir um limite, e "fecha" novamente após um período de *timeout*.
    *   **Utilização:** Em integrações entre o S/4HANA e sistemas externos (ou até mesmo entre o S/4HANA e o legado), previne que uma falha em um dos sistemas cause uma cascata de falhas.

15. **Retry Pattern (Padrão de Retentativa)**
    *   **Descrição:** Permite que um sistema tente novamente uma operação que falhou, presumindo que a falha pode ser temporária (ex: falha de rede transitória, serviço ocupado). Pode incluir *backoff* exponencial para evitar sobrecarregar o serviço falho.
    *   **Utilização:** Em todas as integrações assíncronas e síncronas que podem sofrer de falhas transitórias, garantindo que as mensagens ou chamadas de API eventualmente sejam processadas.

16. **Idempotent Consumer / Idempotent Receiver (Consumidor Idempotente)**
    *   **Descrição:** Garante que o processamento de uma mensagem ou evento mais de uma vez não produza efeitos colaterais indesejados. Ou seja, aplicar a mesma operação várias vezes tem o mesmo efeito que aplicá-la uma única vez.
    *   **Utilização:** Crítico em sistemas de mensageria onde a entrega "at least once" (pelo menos uma vez) é comum. Garante que se um evento for entregue duas vezes, o S/4HANA ou outro sistema receptor não criará dados duplicados ou processará a mesma lógica de negócio duas vezes.
