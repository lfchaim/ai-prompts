# Exemplos SOLID em Java/Spring Boot (Sem Lombok)

Este documento apresenta exemplos de código Java com Spring Boot, aplicando os princípios SOLID, seguindo a Clean Architecture e sem o uso de Lombok.

## 1. Princípio da Responsabilidade Única (SRP - Single Responsibility Principle)

**Definição:** Uma classe deve ter apenas um motivo para mudar. Isso significa que cada classe deve ter uma única responsabilidade bem definida.

**Exemplo:** Em vez de ter uma única classe `UserService` que lida com a criação de usuários, validação e envio de e-mails, separamos essas responsabilidades em classes distintas.

### Antes do SRP (Exemplo Ruim)

```java
// com.empresa.projeto.application.service
public class UserService {

    private final UserRepository userRepository;
    private final EmailService emailService;

    public UserService(UserRepository userRepository, EmailService emailService) {
        this.userRepository = userRepository;
        this.emailService = emailService;
    }

    public User createUser(String name, String email) {
        // 1. Validação (Responsabilidade 1)
        if (name == null || name.isBlank()) {
            throw new IllegalArgumentException("Nome inválido");
        }
        if (email == null || !email.contains("@")) {
            throw new IllegalArgumentException("Email inválido");
        }

        // 2. Persistência (Responsabilidade 2)
        User newUser = new User(name, email);
        userRepository.save(newUser);

        // 3. Notificação (Responsabilidade 3)
        emailService.sendWelcomeEmail(newUser.getEmail(), newUser.getName());

        return newUser;
    }
}

// com.empresa.projeto.domain.model
public class User {
    private String name;
    private String email;

    public User(String name, String email) {
        this.name = name;
        this.email = email;
    }

    public String getName() { return name; }
    public String getEmail() { return email; }
}

// com.empresa.projeto.domain.repository
public interface UserRepository {
    void save(User user);
}

// com.empresa.projeto.infrastructure.email
public class EmailService {
    public void sendWelcomeEmail(String to, String userName) {
        System.out.println("Enviando e-mail de boas-vindas para " + userName + " em " + to);
    }
}
```

### Depois do SRP (Exemplo Bom)

```java
// com.empresa.projeto.domain.model
public class User {
    private String name;
    private String email;

    public User(String name, String email) {
        if (name == null || name.isBlank()) {
            throw new IllegalArgumentException("Nome não pode ser vazio.");
        }
        if (email == null || !email.contains("@")) {
            throw new IllegalArgumentException("Email inválido.");
        }
        this.name = name;
        this.email = email;
    }

    public String getName() { return name; }
    public String getEmail() { return email; }
}

// com.empresa.projeto.domain.repository
public interface UserRepository {
    void save(User user);
}

// com.empresa.projeto.application.service
// Responsabilidade: Orquestrar a criação de usuário
public class UserCreationService {

    private final UserRepository userRepository;
    private final EmailNotificationService emailNotificationService;

    public UserCreationService(UserRepository userRepository, EmailNotificationService emailNotificationService) {
        this.userRepository = userRepository;
        this.emailNotificationService = emailNotificationService;
    }

    public User createUser(String name, String email) {
        User newUser = new User(name, email); // Validação agora no construtor do User
        userRepository.save(newUser);
        emailNotificationService.sendWelcomeEmail(newUser.getEmail(), newUser.getName());
        return newUser;
    }
}

// com.empresa.projeto.application.service
// Responsabilidade: Envio de notificações por e-mail
public class EmailNotificationService {
    public void sendWelcomeEmail(String to, String userName) {
        System.out.println("Enviando e-mail de boas-vindas para " + userName + " em " + to);
    }
}
```

## 2. Princípio Aberto/Fechado (OCP - Open/Closed Principle)

**Definição:** Entidades de software (classes, módulos, funções, etc.) devem ser abertas para extensão, mas fechadas para modificação. Isso significa que o comportamento de um módulo pode ser estendido sem alterar seu código fonte.

**Exemplo:** Um sistema de relatórios que pode gerar diferentes formatos (PDF, CSV) sem precisar modificar a classe principal que orquestra a geração.

### Antes do OCP (Exemplo Ruim)

```java
// com.empresa.projeto.application.service
public class ReportGenerator {

    public void generateReport(List<String> data, String format) {
        if ("PDF".equalsIgnoreCase(format)) {
            // Lógica para gerar PDF
            System.out.println("Gerando relatório em PDF...");
            for (String item : data) {
                System.out.println("PDF Content: " + item);
            }
        } else if ("CSV".equalsIgnoreCase(format)) {
            // Lógica para gerar CSV
            System.out.println("Gerando relatório em CSV...");
            for (String item : data) {
                System.out.println("CSV Content: " + item + ",");
            }
        } else {
            throw new IllegalArgumentException("Formato de relatório não suportado: " + format);
        }
    }
}
```

### Depois do OCP (Exemplo Bom)

```java
import java.util.List;

// com.empresa.projeto.domain.service
public interface ReportFormatter {
    void format(List<String> data);
}

// com.empresa.projeto.infrastructure.report
public class PdfReportFormatter implements ReportFormatter {
    @Override
    public void format(List<String> data) {
        System.out.println("Gerando relatório em PDF...");
        for (String item : data) {
            System.out.println("PDF Content: " + item);
        }
    }
}

// com.empresa.projeto.infrastructure.report
public class CsvReportFormatter implements ReportFormatter {
    @Override
    public void format(List<String> data) {
        System.out.println("Gerando relatório em CSV...");
        for (String item : data) {
            System.out.println("CSV Content: " + item + ",");
        }
    }
}

// com.empresa.projeto.application.service
public class ReportGenerator {

    private final ReportFormatter reportFormatter;

    // Injeção de dependência via construtor
    public ReportGenerator(ReportFormatter reportFormatter) {
        this.reportFormatter = reportFormatter;
    }

    public void generateReport(List<String> data) {
        reportFormatter.format(data);
    }
}

// Exemplo de uso em um controlador Spring Boot
/*
@RestController
@RequestMapping("/reports")
public class ReportController {

    private final ReportGenerator pdfReportGenerator;
    private final ReportGenerator csvReportGenerator;

    public ReportController(
        @Qualifier("pdfReportGenerator") ReportGenerator pdfReportGenerator,
        @Qualifier("csvReportGenerator") ReportGenerator csvReportGenerator) {
        this.pdfReportGenerator = pdfReportGenerator;
        this.csvReportGenerator = csvReportGenerator;
    }

    @GetMapping("/pdf")
    public ResponseEntity<Void> getPdfReport() {
        List<String> data = List.of("Item 1", "Item 2", "Item 3");
        pdfReportGenerator.generateReport(data);
        return ResponseEntity.ok().build();
    }

    @GetMapping("/csv")
    public ResponseEntity<Void> getCsvReport() {
        List<String> data = List.of("Item A", "Item B", "Item C");
        csvReportGenerator.generateReport(data);
        return ResponseEntity.ok().build();
    }
}

// Configuração Spring para os beans (exemplo)
@Configuration
public class ReportConfig {

    @Bean
    public ReportFormatter pdfReportFormatter() {
        return new PdfReportFormatter();
    }

    @Bean
    public ReportFormatter csvReportFormatter() {
        return new CsvReportFormatter();
    }

    @Bean("pdfReportGenerator")
    public ReportGenerator pdfReportGenerator(ReportFormatter pdfReportFormatter) {
        return new ReportGenerator(pdfReportFormatter);
    }

    @Bean("csvReportGenerator")
    public ReportGenerator csvReportGenerator(ReportFormatter csvReportFormatter) {
        return new ReportGenerator(csvReportFormatter);
    }
}
*/


## 3. Princípio da Substituição de Liskov (LSP - Liskov Substitution Principle)

**Definição:** Objetos de um programa devem ser substituíveis por instâncias de seus subtipos sem alterar a correção do programa. Em outras palavras, se `S` é um subtipo de `T`, então objetos do tipo `T` podem ser substituídos por objetos do tipo `S` sem quebrar o programa.

**Exemplo:** Um processador de pagamentos que pode lidar com diferentes tipos de pagamentos (crédito, débito) de forma polimórfica.

### Antes do LSP (Exemplo Ruim)

```java
// com.empresa.projeto.domain.model
public class Payment {
    protected double amount;

    public Payment(double amount) {
        this.amount = amount;
    }

    public double getAmount() {
        return amount;
    }

    // Método que pode ser problemático se subtipos tiverem lógicas muito diferentes
    public void processPayment() {
        System.out.println("Processando pagamento genérico de: " + amount);
    }
}

// com.empresa.projeto.domain.model
public class CreditCardPayment extends Payment {
    public CreditCardPayment(double amount) {
        super(amount);
    }

    @Override
    public void processPayment() {
        System.out.println("Processando pagamento com Cartão de Crédito de: " + amount);
        // Lógica específica de cartão de crédito
    }
}

// com.empresa.projeto.domain.model
public class DebitCardPayment extends Payment {
    public DebitCardPayment(double amount) {
        super(amount);
    }

    @Override
    public void processPayment() {
        System.out.println("Processando pagamento com Cartão de Débito de: " + amount);
        // Lógica específica de cartão de débito
    }
}

// com.empresa.projeto.application.service
public class PaymentProcessor {
    public void process(List<Payment> payments) {
        for (Payment payment : payments) {
            // Se um subtipo de Payment não puder ser processado aqui, o LSP é violado
            payment.processPayment();
        }
    }
}
```

### Depois do LSP (Exemplo Bom)

O exemplo anterior já é razoavelmente bom para LSP, pois os subtipos `CreditCardPayment` e `DebitCardPayment` podem ser substituídos por `Payment` sem quebrar o `PaymentProcessor`. A chave é garantir que os métodos sobrescritos mantenham o contrato da superclasse.

Para ilustrar melhor, vamos considerar um cenário onde a violação seria mais evidente, como um método que não faz sentido para um subtipo.

**Exemplo de Violação (se `processPayment` tivesse uma lógica que só funcionasse para crédito, por exemplo, e débito precisasse de algo totalmente diferente que não pudesse ser implementado no `processPayment` ou que gerasse um erro inesperado).**

No nosso exemplo, a estrutura já é LSP-compliant. A interface `Payment` ou uma classe abstrata `Payment` com um método `process()` bem definido seria a base.

```java
import java.util.List;

// com.empresa.projeto.domain.model
public interface Payable {
    void pay();
    double getAmount();
}

// com.empresa.projeto.domain.model
public class CreditCardPayment implements Payable {
    private final double amount;

    public CreditCardPayment(double amount) {
        this.amount = amount;
    }

    @Override
    public void pay() {
        System.out.println("Processando pagamento com Cartão de Crédito de: " + amount);
        // Lógica específica de cartão de crédito
    }

    @Override
    public double getAmount() {
        return amount;
    }
}

// com.empresa.projeto.domain.model
public class DebitCardPayment implements Payable {
    private final double amount;

    public DebitCardPayment(double amount) {
        this.amount = amount;
    }

    @Override
    public void pay() {
        System.out.println("Processando pagamento com Cartão de Débito de: " + amount);
        // Lógica específica de cartão de débito
    }

    @Override
    public double getAmount() {
        return amount;
    }
}

// com.empresa.projeto.application.service
public class UniversalPaymentProcessor {
    public void processPayments(List<Payable> payables) {
        for (Payable payable : payables) {
            // Qualquer tipo de Payable pode ser processado aqui sem problemas
            payable.pay();
        }
    }
}
```

## 4. Princípio da Segregação de Interfaces (ISP - Interface Segregation Principle)

**Definição:** Clientes não devem ser forçados a depender de interfaces que não utilizam. É melhor ter muitas interfaces pequenas e específicas do que uma única interface grande e genérica.

**Exemplo:** Uma interface `Worker` que tem métodos para `work()` e `eat()`. Se tivermos um `Robot` que não come, ele seria forçado a implementar um método `eat()` inútil. Separar em interfaces menores resolve isso.

### Antes do ISP (Exemplo Ruim)

```java
// com.empresa.projeto.domain.model
public interface Worker {
    void work();
    void eat();
}

// com.empresa.projeto.domain.model
public class HumanWorker implements Worker {
    @Override
    public void work() {
        System.out.println("Humano trabalhando...");
    }

    @Override
    public void eat() {
        System.out.println("Humano comendo...");
    }
}

// com.empresa.projeto.domain.model
public class RobotWorker implements Worker {
    @Override
    public void work() {
        System.out.println("Robô trabalhando...");
    }

    @Override
    public void eat() {
        // Robôs não comem, mas são forçados a implementar este método inútil
        System.out.println("Robô não come, mas implementa o método eat()...");
    }
}
```

### Depois do ISP (Exemplo Bom)

```java
// com.empresa.projeto.domain.model
public interface Workable {
    void work();
}

// com.empresa.projeto.domain.model
public interface Feedable {
    void eat();
}

// com.empresa.projeto.domain.model
public class HumanWorker implements Workable, Feedable {
    @Override
    public void work() {
        System.out.println("Humano trabalhando...");
    }

    @Override
    public void eat() {
        System.out.println("Humano comendo...");
    }
}

// com.empresa.projeto.domain.model
public class RobotWorker implements Workable {
    @Override
    public void work() {
        System.out.println("Robô trabalhando...");
    }
    // Não precisa implementar Feedable, pois robôs não comem
}

// com.empresa.projeto.application.service
public class WorkManager {
    public void manageWork(List<Workable> workers) {
        for (Workable worker : workers) {
            worker.work();
        }
    }
}

// com.empresa.projeto.application.service
public class FeedingManager {
    public void manageFeeding(List<Feedable> feedables) {
        for (Feedable feedable : feedables) {
            feedable.eat();
        }
    }
}
```

## 5. Princípio da Inversão de Dependência (DIP - Dependency Inversion Principle)

**Definição:**
1. Módulos de alto nível não devem depender de módulos de baixo nível. Ambos devem depender de abstrações.
2. Abstrações não devem depender de detalhes. Detalhes devem depender de abstrações.

**Exemplo:** Um `UserService` (módulo de alto nível) não deve depender diretamente de uma implementação concreta de `UserRepository` (módulo de baixo nível, como `JpaUserRepository`). Ambos devem depender de uma abstração `UserRepository` (interface).

### Antes do DIP (Exemplo Ruim)

```java
// com.empresa.projeto.infrastructure.persistence
public class JpaUserRepository {
    public void save(User user) {
        System.out.println("Salvando usuário no banco de dados via JPA: " + user.getName());
    }
}

// com.empresa.projeto.application.service
public class UserService {
    // Dependência direta de uma implementação concreta de baixo nível
    private final JpaUserRepository userRepository = new JpaUserRepository();

    public void createUser(String name, String email) {
        User user = new User(name, email);
        userRepository.save(user);
    }
}

// com.empresa.projeto.domain.model
public class User {
    private String name;
    private String email;

    public User(String name, String email) {
        this.name = name;
        this.email = email;
    }

    public String getName() { return name; }
    public String getEmail() { return email; }
}
```

### Depois do DIP (Exemplo Bom)

```java
import org.springframework.stereotype.Service;
import org.springframework.stereotype.Repository;

// com.empresa.projeto.domain.model
public class User {
    private String name;
    private String email;

    public User(String name, String email) {
        this.name = name;
        this.email = email;
    }

    public String getName() { return name; }
    public String getEmail() { return email; }
}

// com.empresa.projeto.domain.repository (Abstração)
public interface UserRepository {
    void save(User user);
}

// com.empresa.projeto.infrastructure.persistence (Detalhe/Implementação de baixo nível)
@Repository
public class JpaUserRepository implements UserRepository {
    @Override
    public void save(User user) {
        System.out.println("Salvando usuário no banco de dados via JPA: " + user.getName());
        // Lógica real de persistência com JPA
    }
}

// com.empresa.projeto.application.service (Módulo de alto nível)
@Service
public class UserService {
    // Depende da abstração, não da implementação concreta
    private final UserRepository userRepository;

    // Injeção de dependência via construtor (Spring resolve a implementação)
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public void createUser(String name, String email) {
        User user = new User(name, email);
        userRepository.save(user);
    }
}
```

Neste exemplo, o `UserService` (módulo de alto nível) não conhece os detalhes de implementação do `UserRepository`. Ele depende da abstração `UserRepository`. O Spring Boot, através da Injeção de Dependência, fornece a implementação concreta (`JpaUserRepository`) em tempo de execução, invertendo o controle da dependência.
