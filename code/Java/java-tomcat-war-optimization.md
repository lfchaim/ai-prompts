# Prompt
Avaliar a descrição do incidente e propor estratégias de engenharia de software para otimizar o uso de memória em uma aplicação Java War, rodando no servidor Tomcat.
Incidente:
O incidente ocorreu no servidor Janus, causando indisponibilidade do ERP e de outras aplicações Java hospedadas no ambiente. A causa imediata foi a atuação do OOM Killer do Linux, que encerrou o processo Java/Tomcat devido ao esgotamento total da memória RAM do servidor

# Gemini
O incidente no servidor **Janus** indica um esgotamento crítico de recursos onde o OOM Killer (*Out-Of-Memory Killer*) do Linux encerrou o processo do Tomcat por falta absoluta de memória RAM física. O fato de múltiplas aplicações Java compartilharem a mesma instância ou o mesmo servidor agrava o cenário, pois um vazamento ou pico de consumo em uma aplicação (como o ERP) compromete todo o ecossistema.

Abaixo estão as estratégias de engenharia de software e infraestrutura para otimizar o uso de memória e evitar novas indisponibilidades.

---

### 1. Estratégias de Código e Arquitetura (Software Engineering)

* **Evite o carregamento em massa de dados (*Data Fetching*):**
* *Problema:* Consultas SQL sem paginação (`SELECT * FROM...`) ou uso excessivo de mapeamentos `EAGER` no JPA/Hibernate que trazem grafos de objetos gigantescos para a memória.
* *Solução:* Implemente paginação rigorosa em consultas de relatórios e listagens, utilize projeções (DTOs) para buscar apenas os campos necessários e prefira `LAZY` nos relacionamentos.


* **Investigação de Vazamentos de Memória (*Memory Leaks*):**
* *Problema:* Coleções estáticas (`static List/Map`) que acumulam dados indefinidamente, `ThreadLocals` não removidas após o uso, ou conexões e streams não fechados corretamente.
* *Solução:* Capture um **Heap Dump** durante um pico de uso ou simulação de carga e utilize ferramentas como **Eclipse Memory Analyzer (MAT)** ou **VisualVM** para identificar classes ou objetos que estão retendo o heap.


* **Isolamento de Aplicações (*Multi-tenancy* vs. Instâncias Dedicadas):**
* *Problema:* Hospedar o ERP e outras aplicações críticas no mesmo Tomcat/JVM faz com que um problema em uma aplicação derrube as demais.
* *Solução:* Separe as aplicações em instâncias de Tomcat distintas ou utilize conteinerização (Docker/Kubernetes) com limites rígidos de memória (`cgroups`) para cada serviço.



### 2. Configuração e Ajuste Fino da JVM (Tomcat)

* **Dimensionamento Correto de Heap e Non-Heap:**
* Defina limites claros para o Heap (`-Xms` e `-Xmx`) e para o Metaspace (`-XX:MaxMetaspaceSize`).
* Garanta que a soma do consumo máximo da JVM (Heap + Metaspace + Off-Heap/Threads) deixe uma margem de segurança para o sistema operacional e outras aplicações no servidor.



* **Otimização do Garbage Collector (GC):**
* Avalie a migração para o coletor **G1GC** (ou ZGC, dependendo da versão do Java) para aplicações com grande volume de heap, pois ele oferece melhor previsibilidade de pausas e eficiência de compactação de memória.



### 3. Observabilidade e Monitoramento Proativo

* **Implementação de APM e Métricas:**
* Integre ferramentas de monitoramento (como Prometheus/Grafana, New Relic ou Dynatrace) para acompanhar em tempo real o uso de Heap, Non-Heap e a frequência de execuções do *Garbage Collector* (Full GC).


* **Configuração de Alertas Antecipados:**
* Configure alertas para disparar quando o uso de memória RAM ou Heap ultrapassar limites seguros (ex: 85%), permitindo intervenções manuais ou reinicializações programadas antes da atuação do OOM Killer.



---

# Prompt
Pode me explicar o passo a passo de como extrair e analisar um Heap Dump de uma aplicação Java no Tomcat?

# Gemini
Para diagnosticar vazamentos de memória (*memory leaks*) no Tomcat, siga o procedimento abaixo para extrair e analisar o Heap Dump da aplicação.

1. **Localizar o PID do processo Tomcat:** Ferramenta: jps ou ps.
Abra o terminal do servidor e identifique o ID do processo (PID) da JVM do Tomcat executando o comando: `jps -v` ou `ps -ef | grep tomcat`.
*Verificação:* O comando deve retornar uma linha contendo o número do PID e os parâmetros de inicialização do Tomcat.


2. **Extrair o Heap Dump manualmente:** Ferramenta: jcmd.
Execute o comando nativo do Java para gerar o arquivo de dump: `jcmd <PID> GC.heap_dump /caminho/absoluto/para/o/arquivo.hprof`. Substitua `<PID>` pelo número obtido no passo anterior.
*Verificação:* Confirme que o arquivo `.hprof` foi criado no diretório informado e que o seu tamanho de arquivo está aumentando/estável.


3. **Configurar geração automática para o futuro:** Parâmetro JVM.
Adicione as flags `-XX:+HeapDumpOnOutOfMemoryError` e `-XX:HeapDumpPath=/caminho/para/logs/` no script de inicialização do Tomcat (`setenv.sh` ou serviço systemd).
*Verificação:* Reinicie o Tomcat e valide via linha de comando se os parâmetros aparecem nos argumentos da JVM (`jps -v`).


4. **Analisar o arquivo com o Eclipse MAT:** Ferramenta: Eclipse Memory Analyzer (MAT).
Baixe e abra o **Eclipse MAT**, clique em *File > Open Heap Dump*, selecione o arquivo `.hprof` gerado e execute o assistente **Leak Suspects Report**.
*Verificação:* O relatório abrirá gerando um gráfico de pizza e uma lista detalhada indicando quais classes ou componentes estão retendo a maior porcentagem de memória.


---

# Prompt
Quais são os erros mais comuns de código Java que causam memory leaks no Tomcat?

# Gemini
Em aplicações Java rodando no Tomcat, os vazamentos de memória (*memory leaks*) geralmente ocorrem porque objetos continuam referenciados por componentes de longa duração (como o pool de threads do servidor ou o ClassLoader), impedindo que o Garbage Collector os remova.

Os erros de código mais comuns que causam esse comportamento incluem:

* **1. Uso incorreto de `ThreadLocal`:**
* *O problema:* O Tomcat reutiliza as threads de atendimento a partir de um pool. Se o seu código armazena dados em um `ThreadLocal` (muito comum em frameworks de segurança, contextos de transação ou log) e **esquece de chamar `.remove()**` ao final da requisição, aquele dado permanece preso na thread para sempre.
* *Como evitar:* Sempre invoque `threadLocalVariable.remove()` em um bloco `finally` ao término do processamento da requisição (ou utilize interceptores/filtros para limpá-los).


* **2. Coleções estáticas (`static`) sem controle:**
* *O problema:* Declaração de listas, mapas ou caches globais (`public static Map<String, Object> cache = new HashMap<>();`) que acumulam dados indefinidamente sem uma política de expiração (como LRU) ou limite máximo de tamanho.
* *Como evitar:* Prefira soluções de cache dedicadas (como Ehcache, Caffeine ou Redis) que possuem políticas nativas de *eviction* (expiração por tempo ou uso).


* **3. Vazamentos de ClassLoader em Redeploy (*Classloader Leaks*):**
* *O problema:* Quando você faz o *redeploy* ou *undeploy* de um arquivo War, o Tomcat descarta o ClassLoader antigo e cria um novo. Se a sua aplicação iniciou threads em segundo plano, registrou drivers JDBC manualmente no `DriverManager`, ou utilizou certas bibliotecas de logging sem encerrá-las adequadamente no evento de destruição (`contextDestroyed`), o ClassLoader antigo inteiro fica preso na memória junto com todas as classes da aplicação.
* *Como evitar:* Utilize ouvintes de contexto (`ServletContextListener`) para encerrar explicitamente threads e desregistrar drivers ao desligar a aplicação.


* **4. Excesso de dados na `HttpSession`:**
* *O problema:* Armazenar objetos grandes, listas extensas de entidades JPA ou arquivos binários diretamente na sessão HTTP do usuário (`request.getSession().setAttribute(...)`) sem uma estratégia de limpeza.
* *Como evitar:* Guarde apenas identificadores leves (como IDs) na sessão e busque os dados sob demanda no banco de dados quando necessário.


* **5. Conexões, Cursors e Streams não fechados:**
* *O problema:* Omissão do fechamento de Statements, ResultSets ou Streams de arquivos em blocos `finally` ou na ausência da estrutura `try-with-resources`. Embora muitos frameworks gerenciem isso, código legado ou consultas JDBC puras mal escritas acumulam cursores abertos que consomem memória nativa e referências no Heap.



---

# Prompt
Como posso identificar vazamentos causados por ThreadLocal usando o Eclipse MAT?

# Gemini
Para identificar vazamentos de memória causados por variáveis `ThreadLocal` utilizando o **Eclipse Memory Analyzer (MAT)**, siga o procedimento abaixo. Como as threads no Tomcat são reutilizadas em um pool, os objetos presos em `ThreadLocal` acabam impedindo que o Garbage Collector limpe o classloader ou as instâncias da sua aplicação.

1. **Abrir o relatório de suspeitas de vazamento:** Ferramenta: Eclipse MAT.
Abra o arquivo `.hprof` no Eclipse MAT e execute o assistente padrão **Leak Suspects** caso ele não abra automaticamente.
*Verificação:* O MAT exibirá um gráfico de pizza indicando quais caminhos retêm a maior quantidade de memória (*Retained Heap*).


2. **Executar uma consulta OQL para ThreadLocal:** Ferramenta: OQL (Object Query Language).
Clique no ícone de lupa amarela (**OQL Studio**) na barra de ferramentas superior do MAT para abrir o console de consultas.
*Verificação:* Uma aba de texto será aberta para você digitar comandos de consulta baseados em SQL voltados para objetos Java.


3. **Filtrar instâncias de ThreadLocal e valores retidos:** Consulta OQL.
Cole a seguinte consulta OQL para listar as instâncias de `ThreadLocal` e inspecionar seus valores associados:
`SELECT t, t.threadLocals.table FROM java.lang.ThreadLocal t`
Clique no botão vermelho de execução (Play) ou pressione `F5`.
*Verificação:* O painel inferior exibirá uma tabela contendo as instâncias de `ThreadLocal` encontradas no Heap Dump.


4. **Analisar as referências e a origem do vazamento:** Ferramenta: Path to GC Roots.
Na tabela resultante da consulta, clique com o botão direito sobre a linha suspeita, vá em **Path to GC Roots** e selecione **exclude phantom/weak/soft references**.
*Verificação:* O MAT abrirá a árvore de referências mostrando qual classe ou thread do Tomcat está segurando o objeto, permitindo identificar exatamente qual serviço ou código gerou o `ThreadLocal`.


---

# Prompt
Quais são as melhores práticas e fórmulas para calcular os valores ideais de -Xms e -Xmx para o Tomcat?

# Gemini
O dimensionamento correto dos parâmetros `-Xms` (memória inicial do Heap) e `-Xmx` (memória máxima do Heap) é fundamental para evitar que a JVM consuma todos os recursos do servidor e acione o OOM Killer do Linux (como ocorreu no incidente do servidor Janus).

As principais práticas e fórmulas recomendadas para calcular esses valores com segurança incluem:

---

### 1. Melhores Práticas de Configuração

* **Iguale `-Xms` e `-Xmx` em Produção:**
* Defina o valor inicial igual ao valor máximo (ex: `-Xms4g -Xmx4g`).
* *Por quê?* Isso evita que a JVM precise redimensionar o Heap dinamicamente durante os picos de acesso, o que causa custos desnecessários de CPU e pausas prolongadas do Garbage Collector.


* **Não aloque 100% da RAM para o Heap:**
* Lembre-se de que a JVM consome memória **além do Heap** (*Non-Heap / Off-Heap*), incluindo o Metaspace, as pilhas de threads (*Thread Stacks*) e buffers diretos. Além disso, o sistema operacional e outros serviços precisam de folga.



---

### 2. A Fórmula de Dimensionamento

Para calcular o valor ideal de `-Xmx`, utilize a seguinte lógica baseada na memória total disponível no servidor ou container:

$$\text{Xmx} = \text{Memória Total} - (\text{Reserva do Sistema Operacional}) - (\text{Memória Non-Heap da JVM})$$

Passo a passo para estimar cada componente:

1. **Reserva do Sistema Operacional:** Reserve de **15% a 20%** da RAM total (ou pelo menos 1GB a 2GB fixos) para o kernel do Linux, cache de arquivos do sistema e outras ferramentas de monitoramento.
2. **Memória Non-Heap:**
* **Metaspace:** Defina um limite seguro (ex: `-XX:MaxMetaspaceSize=256m` ou `512m`) para evitar crescimento descontrolado.
* **Thread Stacks:** Cada thread consome cerca de `1MB` (controlado por `-Xss`). Se o Tomcat estiver configurado para suportar até 200 threads simultâneas, adicione cerca de `200MB` a essa conta.


3. **Heap Restante (`-Xmx`):** O que sobrar do cálculo acima será o teto seguro para o Heap.

---

### 3. Exemplo Prático (Servidor com 8 GB de RAM)

Imagine um servidor dedicado exclusivamente ao Tomcat com **8 GB de RAM física**:

* **RAM Total:** 8192 MB
* **Reserva do SO:** ~15% (aprox. 1228 MB)
* **Non-Heap (Metaspace + Threads):** ~500 MB
* **Cálculo do `-Xmx`:**

$$8192 - 1228 - 500 = 6464 \text{ MB}$$



**Configuração resultante recomendada no Tomcat (`setenv.sh`):**

```bash
CATALINA_OPTS="$CATALINA_OPTS -Xms6g -Xmx6g -XX:MaxMetaspaceSize=256m"

```

*(Nota: 6GB arredondados para baixo para garantir uma margem de segurança confortável).*

---

# Prompt
Quais flags da JVM devo habilitar para logar e monitorar o comportamento do Garbage Collector?

# Gemini
Para monitorar o comportamento do *Garbage Collector* (GC) de forma eficiente em versões modernas do Java (Java 9 em diante, como Java 11, 17 ou 21), deve-se utilizar o framework unificado de logs da JVM (`-Xlog`), que substitui as flags antigas e obsoletas (como `-XX:+PrintGCDetails`).

### Flags Recomendadas para o `CATALINA_OPTS`

Adicione a seguinte linha no arquivo de inicialização do Tomcat (`setenv.sh`):

```bash
CATALINA_OPTS="$CATALINA_OPTS -Xlog:gc*,gc+phases=debug,gc+age=trace:file=/caminho/para/logs/gc.log:time,uptime,pid,level,tags:filecount=5,filesize=50M"

```

### Explicação dos Parâmetros

* `-Xlog:gc*,gc+phases=debug`: Ativa todos os logs relacionados ao Garbage Collector (`gc*`), incluindo os detalhes das fases de limpeza em nível de depuração (`debug`), permitindo identificar pausas longas (*Stop-the-World*).
* `:file=/caminho/para/logs/gc.log`: Direciona a saída dos logs para um arquivo dedicado em vez da saída padrão do console.
* `:time,uptime,pid,level,tags`: Adiciona metadados cruciais a cada linha de log (horário real, tempo de atividade da JVM, PID do processo, nível e tags).
* `:filecount=5,filesize=50M`: Configura uma política de rotação de arquivos, mantendo no máximo 5 arquivos de 50MB cada, evitando que o log de GC esgote o espaço em disco do servidor.

### Como Analisar os Logs de GC

Após coletar os dados por alguns dias (especialmente durante picos de uso), você pode utilizar ferramentas gráficas gratuitas e leves para visualizar o comportamento do coletor:

* **GCViewer** (projeto open-source em Java para ler arquivos de log de GC).
* **GCEasy** (`gceasy.io`), uma ferramenta online baseada em IA que analisa o arquivo `gc.log` gerado e aponta gargalos de memória e vazamentos potenciais.

---



