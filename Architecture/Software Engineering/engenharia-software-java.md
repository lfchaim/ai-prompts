# Guia Completo para Engenharia de Software com Java

## SOLID, Clean Code Clean Architecture e tipos arquiteturais

Este artigo explica os fundamentos de engenharia de software moderna: princípios SOLID, práticas de Clean Code, padrões de Clean Architecture e Tipos Arquiterurais. Faz um breve overview entre a filosofia de Clean Arch e os tipos de arquiteturas mais usadas,como: MVC (Model, View, Controller), Monolito, Microservices e Hexagonal para explicá-las e diferencia-las. Será abordado no decorrer deste artigo cada conceito com exemplos práticos em código Java e comparações entre esses diferentes padrões arquiteturais para melhor entendimento. Cada arquitetura, será abordada em um próximo artigo individualmente na série de Artigos sobre **_Arquiteturas_**.

* * *

## 📑 Navegue sobre os temas de interesse

### [PARTE 1: PRINCÍPIOS SOLID](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn\#parte-1-princ%C3%ADpios-solid)

- [O que é SOLID?](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#o-que-%C3%A9-solid)
- [Por que usar SOLID?](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#por-que-usar-solid)
- [Quando aplicar?](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#quando-aplicar)
- [Onde aplicar?](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#onde-aplicar)
- [Quem deve aplicar?](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#quem-deve-aplicar)
- [Como aplicar?](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#como-aplicar)  - [S - Single Responsibility Principle](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#s-single-responsibility-principle)
  - [O - Open/Closed Principle](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#o-openclosed-principle)
  - [L - Liskov Substitution Principle](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#l-liskov-substitution-principle)
  - [I - Interface Segregation Principle](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#i-interface-segregation-principle)
  - [D - Dependency Inversion Principle](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#d-dependency-inversion-principle)
- [Quanto custa não aplicar?)](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#quanto-custa-n%C3%A3o-aplicar)

### [PARTE 2: CLEAN CODE](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn\#parte-2-clean-code)

- [O que é Clean Code?](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#o-que-%C3%A9-clean-code)
- [Por que escrever Clean Code?](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#por-que-escrever-clean-code)
- [Quando aplicar?](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#quando-aplicar)
- [Como aplicar?](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#como-aplicar) 1

  - [Nomes Significativos](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#nomes-significativos)
  - [Funções Pequenas](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#fun%C3%A7%C3%B5es-pequenas)
  - [Comentários Mínimos](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#coment%C3%A1rios-m%C3%ADnimos)
  - [Tratamento de Erros](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#tratamento-de-erros)
  - [Evitar Magic Numbers](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#evitar-magic-numbers)
- [Quanto custa?](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#quanto-custa)

### [PARTE 3: CLEAN ARCHITECTURE](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn\#parte-3-clean-architecture)

- [O que é Clean Architecture?](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#o-que-%C3%A9-clean-architecture)
- [Por que usar Clean Architecture?](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#por-que-usar-clean-architecture)
- [Quando aplicar?](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#quando-aplicar)
- [Onde aplicar?](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#onde-aplicar)
- [Como aplicar?](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#como-aplicar)  - [Camada de Domínio (Core)](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#camada-de-dom%C3%ADnio-core)
  - [Camada de Aplicação (Use Cases)](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#camada-de-aplica%C3%A7%C3%A3o-use-cases)
  - [Camada de Infraestrutura](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#camada-de-infraestrutura)
  - [Camada de Apresentação](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#camada-de-apresenta%C3%A7%C3%A3o)
- [Quanto custa?](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#quanto-custa)

### [PARTE 4: DIFERENÇAS ENTRE CLEAN ARCHITECTURE E PADRÕES ARQUITETURAIS](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn\#parte-4-diferen%C3%A7as-entre-clean-architecture-e-padr%C3%B5es-arquiteturais)

- [Clean Architecture vs Padrões de Estruturação](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#clean-architecture-vs-padr%C3%B5es-de-estrutura%C3%A7%C3%A3o)
- [MVC (Model-View-Controller)](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#mvc-model-view-controller)
- [Monolito](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#monolito)
- [Microserviços](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#microservi%C3%A7os)
- [Arquitetura Hexagonal (Ports & Adapters)](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#arquitetura-hexagonal-ports--adapters)
- [Arquitetura Orientada a Eventos](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#arquitetura-orientada-a-eventos)
- [Tabela Comparativa Resumida](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#tabela-comparativa-resumida)

### [CONCLUSÃO](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn\#conclus%C3%A3o)

- [Resumo dos Conceitos](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#resumo-dos-conceitos)
- [Relação Entre os Conceitos](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#rela%C3%A7%C3%A3o-entre-os-conceitos)
- [Recomendações de Aplicação](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#recomenda%C3%A7%C3%B5es-de-aplica%C3%A7%C3%A3o)
- [Próximos Passos](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#pr%C3%B3ximos-passos)
- [Recursos Adicionais](https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn#recursos-adicionais)

* * *

## PARTE 1: PRINCÍPIOS SOLID

### O que é SOLID?

SOLID é um acrônimo criado por Michael Feathers em 2004 para cinco princípios fundamentais de design orientado a objetos propostos por Robert C. Martin (Uncle Bob) em 2002-2005, depois de 20 anos de amadurecimento sobre estes. Sendo estes princípios:

- **S** ingle Responsibility Principle (Princípio da Responsabilidade Única)
- **O** pen/Closed Principle (Princípio Aberto/Fechado)
- **L** iskov Substitution Principle (Princípio da Substituição de Liskov)
- **I** nterface Segregation Principle (Princípio da Segregação de Interface)
- **D** ependency Inversion Principle (Princípio da Inversão de Dependência)

### Fluxo de Decisão: Aplicado os 5 princípios SOLID

![Fluxo de Decisão: SOLID](https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Fb3x9np21n2dfnn7cni78.png)

### Por que usar SOLID?

- **Manutenibilidade**: código mais fácil de modificar e estender
- **Testabilidade**: classes desacopladas facilitam testes unitários
- **Escalabilidade**: sistemas que crescem sem acumular débito técnico
- **Redução de bugs**: mudanças localizadas diminuem efeitos colaterais
- **Reusabilidade**: componentes independentes podem ser reutilizados

### Quando aplicar?

- Durante o design de novas funcionalidades
- Em refatorações de código legado
- Ao identificar classes com múltiplas responsabilidades
- Quando perceber alto acoplamento entre módulos
- Em code reviews e melhorias contínuas

### Onde aplicar?

- Camada de domínio (regras de negócio)
- Camada de aplicação (casos de uso)
- Camada de infraestrutura (repositórios, adaptadores)
- DTOs e entidades
- Services e controllers

### Quem deve aplicar?

- Desenvolvedores (em qualquer linguagem) de todos os níveis
- Arquitetos de software
- Tech Leads
- Engenheiros de qualidade
- Qualquer profissional que escreve código orientado a objetos

### Como aplicar?

#### S - Single Responsibility Principle

**Conceito**: Uma classe deve ter apenas uma única responsabilidade (Robert C. MARTIN em Clean Arch, p.61).

> - Uma classe deve ter apenas uma razão para mudar.
> - Uma classe deve ser responsável por um, e apenas um usuário ou stakeholder.
> - Uma classe deve ser responsável por um, e apenas um, ator.

**Violação do SRP:**

```
// ❌ Classe com múltiplas responsabilidades
public class Usuario {
    private String nome;
    private String email;

    public void salvarNoBancoDeDados() {
        // lógica de persistência
    }

    public void enviarEmailBoasVindas() {
        // lógica de envio de email
    }

    public void gerarRelatorioUsuario() {
        // lógica de geração de relatório
    }
}
```

Enter fullscreen modeExit fullscreen mode

**Aplicando SRP:**

```
// ✅ Cada classe com uma responsabilidade
public class Usuario {
    private String nome;
    private String email;

    // Getters e setters
}

public class UsuarioRepository {
    public void salvar(Usuario usuario) {
        // lógica de persistência
    }
}

public class EmailService {
    public void enviarBoasVindas(Usuario usuario) {
        // lógica de envio de email
    }
}

public class RelatorioUsuarioService {
    public void gerar(Usuario usuario) {
        // lógica de geração de relatório
    }
}
```

Enter fullscreen modeExit fullscreen mode

#### O - Open/Closed Principle

**Conceito**: Entidades devem estar abertas para extensão, mas fechadas para modificação.

**Violação do OCP:**

```
// ❌ Precisa modificar a classe para adicionar novos tipos
public class CalculadoraDesconto {
    public double calcular(String tipoCliente, double valor) {
        if (tipoCliente.equals("BRONZE")) {
            return valor * 0.05;
        } else if (tipoCliente.equals("PRATA")) {
            return valor * 0.10;
        } else if (tipoCliente.equals("OURO")) {
            return valor * 0.15;
        }
        return 0;
    }
}
```

Enter fullscreen modeExit fullscreen mode

**Aplicando OCP:**

```
// ✅ Extensível sem modificação
public interface EstrategiaDesconto {
    double calcular(double valor);
}

public class DescontoBronze implements EstrategiaDesconto {
    @Override
    public double calcular(double valor) {
        return valor * 0.05;
    }
}

public class DescontoPrata implements EstrategiaDesconto {
    @Override
    public double calcular(double valor) {
        return valor * 0.10;
    }
}

public class DescontoOuro implements EstrategiaDesconto {
    @Override
    public double calcular(double valor) {
        return valor * 0.15;
    }
}

public class CalculadoraDesconto {
    private final EstrategiaDesconto estrategia;

    public CalculadoraDesconto(EstrategiaDesconto estrategia) {
        this.estrategia = estrategia;
    }

    public double calcular(double valor) {
        return estrategia.calcular(valor);
    }
}
```

Enter fullscreen modeExit fullscreen mode

#### L - Liskov Substitution Principle

**Conceito**: Subtipos devem ser substituíveis por seus tipos base sem alterar o comportamento esperado.

**Violação do LSP:**

```
// ❌ Quadrado viola LSP como subtipo de Retângulo
public class Retangulo {
    protected int largura;
    protected int altura;

    public void setLargura(int largura) {
        this.largura = largura;
    }

    public void setAltura(int altura) {
        this.altura = altura;
    }

    public int getArea() {
        return largura * altura;
    }
}

public class Quadrado extends Retangulo {
    @Override
    public void setLargura(int largura) {
        this.largura = largura;
        this.altura = largura; // quebra a expectativa
    }

    @Override
    public void setAltura(int altura) {
        this.largura = altura; // quebra a expectativa
        this.altura = altura;
    }
}
```

Enter fullscreen modeExit fullscreen mode

**Aplicando LSP:**

```
// ✅ Usando composição e interfaces apropriadas
public interface Forma {
    int getArea();
}

public class Retangulo implements Forma {
    private final int largura;
    private final int altura;

    public Retangulo(int largura, int altura) {
        this.largura = largura;
        this.altura = altura;
    }

    @Override
    public int getArea() {
        return largura * altura;
    }
}

public class Quadrado implements Forma {
    private final int lado;

    public Quadrado(int lado) {
        this.lado = lado;
    }

    @Override
    public int getArea() {
        return lado * lado;
    }
}
```

Enter fullscreen modeExit fullscreen mode

#### I - Interface Segregation Principle

**Conceito**: Clientes não devem ser forçados a depender de interfaces que não usam.

**Violação do ISP:**

```
// ❌ Interface muito ampla
public interface Trabalhador {
    void trabalhar();
    void comer();
    void receberSalario();
    void dormir();
}

public class Robo implements Trabalhador {
    @Override
    public void trabalhar() {
        // implementação
    }

    @Override
    public void comer() {
        throw new UnsupportedOperationException(); // robô não come
    }

    @Override
    public void receberSalario() {
        throw new UnsupportedOperationException(); // robô não recebe salário
    }

    @Override
    public void dormir() {
        throw new UnsupportedOperationException(); // robô não dorme
    }
}
```

Enter fullscreen modeExit fullscreen mode

**Aplicando ISP:**

```
// ✅ Interfaces segregadas
public interface Trabalhavel {
    void trabalhar();
}

public interface Alimentavel {
    void comer();
}

public interface Assalariado {
    void receberSalario();
}

public interface Descansavel {
    void dormir();
}

public class Humano implements Trabalhavel, Alimentavel, Assalariado, Descansavel {
    @Override
    public void trabalhar() { /* implementação */ }

    @Override
    public void comer() { /* implementação */ }

    @Override
    public void receberSalario() { /* implementação */ }

    @Override
    public void dormir() { /* implementação */ }
}

public class Robo implements Trabalhavel {
    @Override
    public void trabalhar() { /* implementação */ }
}
```

Enter fullscreen modeExit fullscreen mode

#### D - Dependency Inversion Principle

**Conceito**: Módulos de alto nível não devem depender de módulos de baixo nível. Ambos devem depender de abstrações.

**Violação do DIP:**

```
// ❌ Alto nível depende de implementação concreta
public class MySQLDatabase {
    public void save(String dados) {
        // salva no MySQL
    }
}

public class UsuarioService {
    private final MySQLDatabase database = new MySQLDatabase();

    public void criarUsuario(String dados) {
        database.save(dados);
    }
}
```

Enter fullscreen modeExit fullscreen mode

**Aplicando DIP:**

```
// ✅ Ambos dependem de abstração
public interface Database {
    void save(String dados);
}

public class MySQLDatabase implements Database {
    @Override
    public void save(String dados) {
        // salva no MySQL
    }
}

public class PostgreSQLDatabase implements Database {
    @Override
    public void save(String dados) {
        // salva no PostgreSQL
    }
}

public class UsuarioService {
    private final Database database;

    public UsuarioService(Database database) {
        this.database = database;
    }

    public void criarUsuario(String dados) {
        database.save(dados);
    }
}
```

Enter fullscreen modeExit fullscreen mode

### Quanto custa não aplicar?

**Débito Técnico:**

- Aumento de 40-60% no tempo de manutenção
- Redução de 30-50% na velocidade de desenvolvimento de novas features
- Incremento de 25-35% na taxa de bugs em produção
- Dificuldade em testes (cobertura < 60%)
- Rotatividade de equipe por frustração

**Benefícios de aplicar:**

- Redução de 50% no tempo de onboarding
- Aumento de 70% na cobertura de testes
- Diminuição de 40% no tempo de code review
- Redução de 60% em bugs relacionados a acoplamento

* * *

## PARTE 2: CLEAN CODE

### O que é Clean Code?

Clean Code (Código Limpo) é um conjunto de práticas e princípios (também usa SOLID) para escrever código que seja:

- **Legível**: fácil de ler e entender
- **Simples**: sem complexidade desnecessária
- **Expressivo**: comunica a intenção claramente
- **Bem testado**: alta cobertura e testes confiáveis
- **Manutenível**: fácil de modificar

### Por que escrever Clean Code?

- **Comunicação**: código é lido 10x mais do que escrito
- **Produtividade**: menos tempo debugando, mais tempo criando
- **Qualidade**: menos bugs e problemas em produção
- **Colaboração**: facilita trabalho em equipe
- **Sustentabilidade**: código que sobrevive ao tempo

### Quando aplicar?

**Sempre!** Especialmente:

- Ao escrever novo código
- Durante refatoração
- Em code reviews
- Ao corrigir bugs
- Antes de commits

### Como aplicar?

#### Nomes Significativos

**Ruim:**

```
// ❌ Nomes genéricos e confusos
public class Dados {
    private String s;
    private int d1;
    private List<String> lst;

    public void fazer() {
        for (String x : lst) {
            if (x.length() > d1) {
                s += x;
            }
        }
    }
}
```

Enter fullscreen modeExit fullscreen mode

**Bom:**

```
// ✅ Nomes que revelam intenção
public class FiltroTexto {
    private String textoFiltrado;
    private int tamanhoMinimo;
    private List<String> palavras;

    public void filtrarPalavrasLongas() {
        for (String palavra : palavras) {
            if (palavra.length() > tamanhoMinimo) {
                textoFiltrado += palavra;
            }
        }
    }
}
```

Enter fullscreen modeExit fullscreen mode

#### Funções Pequenas

**Ruim:**

```
// ❌ Função longa com múltiplas responsabilidades
public void processarPedido(Pedido pedido) {
    // validação
    if (pedido == null || pedido.getItens().isEmpty()) {
        throw new IllegalArgumentException();
    }

    // cálculo
    double total = 0;
    for (Item item : pedido.getItens()) {
        total += item.getPreco() * item.getQuantidade();
    }

    // desconto
    if (pedido.getCliente().getTipo().equals("VIP")) {
        total *= 0.9;
    }

    // persistência
    pedidoRepository.save(pedido);

    // notificação
    emailService.enviar(pedido.getCliente().getEmail(), "Pedido confirmado");
}
```

Enter fullscreen modeExit fullscreen mode

**Bom:**

```
// ✅ Funções pequenas e focadas
public void processarPedido(Pedido pedido) {
    validarPedido(pedido);
    double total = calcularTotal(pedido);
    total = aplicarDesconto(total, pedido.getCliente());
    salvarPedido(pedido);
    notificarCliente(pedido.getCliente());
}

private void validarPedido(Pedido pedido) {
    if (pedido == null || pedido.getItens().isEmpty()) {
        throw new IllegalArgumentException("Pedido inválido");
    }
}

private double calcularTotal(Pedido pedido) {
    return pedido.getItens().stream()
        .mapToDouble(item -> item.getPreco() * item.getQuantidade())
        .sum();
}

private double aplicarDesconto(double total, Cliente cliente) {
    return cliente.isVIP() ? total * 0.9 : total;
}

private void salvarPedido(Pedido pedido) {
    pedidoRepository.save(pedido);
}

private void notificarCliente(Cliente cliente) {
    emailService.enviar(cliente.getEmail(), "Pedido confirmado");
}
```

Enter fullscreen modeExit fullscreen mode

#### Comentários Mínimos

**Ruim:**

```
// ❌ Código que precisa de comentários para ser entendido
public class Calc {
    // calcula o salário
    public double calc(int h, double v) {
        // multiplica horas por valor
        double t = h * v;
        // desconta imposto de 15%
        return t * 0.85;
    }
}
```

Enter fullscreen modeExit fullscreen mode

**Bom:**

```
// ✅ Código auto-explicativo
public class CalculadoraSalario {
    private static final double ALIQUOTA_IMPOSTO = 0.15;

    public double calcularSalarioLiquido(int horasTrabalhadas, double valorHora) {
        double salarioBruto = horasTrabalhadas * valorHora;
        return aplicarDesconto(salarioBruto, ALIQUOTA_IMPOSTO);
    }

    private double aplicarDesconto(double valor, double aliquota) {
        return valor * (1 - aliquota);
    }
}
```

Enter fullscreen modeExit fullscreen mode

#### Tratamento de Erros

**Ruim:**

```
// ❌ Retorno de código de erro
public int criarUsuario(Usuario usuario) {
    if (usuario == null) {
        return -1;
    }
    if (usuario.getEmail() == null) {
        return -2;
    }
    if (usuarioRepository.existePorEmail(usuario.getEmail())) {
        return -3;
    }
    usuarioRepository.save(usuario);
    return 0;
}
```

Enter fullscreen modeExit fullscreen mode

**Bom:**

```
// ✅ Uso de exceções
public void criarUsuario(Usuario usuario) {
    validarUsuario(usuario);
    verificarDuplicidade(usuario);
    usuarioRepository.save(usuario);
}

private void validarUsuario(Usuario usuario) {
    if (usuario == null) {
        throw new IllegalArgumentException("Usuário não pode ser nulo");
    }
    if (usuario.getEmail() == null || usuario.getEmail().isBlank()) {
        throw new IllegalArgumentException("Email é obrigatório");
    }
}

private void verificarDuplicidade(Usuario usuario) {
    if (usuarioRepository.existePorEmail(usuario.getEmail())) {
        throw new UsuarioDuplicadoException(
            "Já existe usuário com email: " + usuario.getEmail()
        );
    }
}
```

Enter fullscreen modeExit fullscreen mode

#### Evitar Magic Numbers

**Ruim:**

```
// ❌ Números mágicos
public class CarrinhoCompras {
    public double calcularFrete(double peso) {
        if (peso < 5) {
            return 10.0;
        } else if (peso < 20) {
            return 25.0;
        } else {
            return 50.0;
        }
    }
}
```

Enter fullscreen modeExit fullscreen mode

**Bom:**

```
// ✅ Constantes nomeadas
public class CarrinhoCompras {
    private static final double PESO_LEVE_KG = 5.0;
    private static final double PESO_MEDIO_KG = 20.0;
    private static final double FRETE_LEVE = 10.0;
    private static final double FRETE_MEDIO = 25.0;
    private static final double FRETE_PESADO = 50.0;

    public double calcularFrete(double peso) {
        if (peso < PESO_LEVE_KG) {
            return FRETE_LEVE;
        } else if (peso < PESO_MEDIO_KG) {
            return FRETE_MEDIO;
        } else {
            return FRETE_PESADO;
        }
    }
}
```

Enter fullscreen modeExit fullscreen mode

### Quanto custa?

**Custo de código sujo (Code Smell):**

- 80% do custo de software está em manutenção
- Desenvolvedores gastam 60% do tempo tentando entender código
- Cada hora economizada em escrita pode custar 10 horas em manutenção

**ROI de Clean Code:**

- Redução de 30-50% no tempo de code review
- Aumento de 40% na velocidade de onboarding
- Diminuição de 35% em bugs relacionados a legibilidade
- Incremento de 60% na satisfação da equipe

* * *

## PARTE 3: CLEAN ARCHITECTURE

### O que é Clean Architecture?

Clean Architecture é uma filosofia de design proposta por Robert C. Martin que organiza o código em camadas concêntricas, onde:

- **Camadas internas** contêm regras de negócio (domínio)
- **Camadas externas** contêm detalhes de implementação (frameworks, UI, DB)
- **Dependências** sempre apontam de fora para dentro
- **Negócio** é independente de frameworks e infraestrutura

### Por que usar Clean Architecture?

É uma filosofia ou um método arquitetural de programação, que é agnóstico a linguagem de programação e tipo de arquitetura, ou seja, deve (altamente recomendada rs) ser usada em qualquer tipo de arquitetura.

- **Independência de frameworks**: negócio não depende de Spring, Hibernate, etc.
- **Testabilidade**: regras de negócio podem ser testadas sem UI ou banco
- **Independência de UI**: pode trocar web por CLI sem afetar negócio
- **Independência de banco**: pode trocar MySQL por MongoDB sem afetar negócio
- **Manutenibilidade**: mudanças são localizadas e controladas

> Segundo Robert C. Martin em o Código Limpo (Clean Code), organizar o código em camadas concêntricas (Entidades, Casos de Uso, Adaptadores, Frameworks), onde a dependência aponta sempre para o centro, garantindo que as regras de negócio não dependam de detalhes técnicos. Separa portanto a lógica de negócio da camada / Interface externa. Como está sendo explicado na imagem a seguir.

![Filosófia da Arch Limpa](https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Fslz7gy5aqjnf2z3xj74y.png)

### Quando aplicar?

- Projetos de médio a grande porte
- Sistemas com ciclo de vida longo (> 2 anos)
- Aplicações com requisitos de negócio complexos
- Quando testabilidade é crítica
- Sistemas que precisam suportar múltiplas interfaces (web, mobile, API)

### Onde aplicar?

**Estrutura de pacotes:**

```
com.empresa.projeto
├── domain
│   ├── entities
│   ├── valueobjects
│   └── repositories (interfaces)
├── application
│   ├── usecases
│   ├── ports (interfaces)
│   └── dto
├── infrastructure
│   ├── persistence
│   ├── messaging
│   └── external
└── presentation
    ├── controllers
    └── config
```

Enter fullscreen modeExit fullscreen mode

### Como aplicar?

#### Camada de Domínio (Core)

```
// Entidade de domínio (não depende de nada externo)
public class Produto {
    private final UUID id;
    private String nome;
    private Money preco;
    private int quantidadeEstoque;

    public Produto(UUID id, String nome, Money preco, int quantidadeEstoque) {
        this.id = Objects.requireNonNull(id);
        this.nome = validarNome(nome);
        this.preco = Objects.requireNonNull(preco);
        this.quantidadeEstoque = validarEstoque(quantidadeEstoque);
    }

    public void atualizarPreco(Money novoPreco) {
        if (novoPreco.compareTo(Money.ZERO) <= 0) {
            throw new IllegalArgumentException("Preço deve ser positivo");
        }
        this.preco = novoPreco;
    }

    public void decrementarEstoque(int quantidade) {
        if (quantidade > quantidadeEstoque) {
            throw new EstoqueInsuficienteException(
                "Estoque disponível: " + quantidadeEstoque
            );
        }
        this.quantidadeEstoque -= quantidade;
    }

    private String validarNome(String nome) {
        if (nome == null || nome.isBlank()) {
            throw new IllegalArgumentException("Nome é obrigatório");
        }
        return nome;
    }

    private int validarEstoque(int estoque) {
        if (estoque < 0) {
            throw new IllegalArgumentException("Estoque não pode ser negativo");
        }
        return estoque;
    }

    // Getters
}

// Interface de repositório (porta)
public interface ProdutoRepository {
    Produto buscarPorId(UUID id);
    List<Produto> buscarTodos();
    void salvar(Produto produto);
    void deletar(UUID id);
}
```

Enter fullscreen modeExit fullscreen mode

#### Camada de Aplicação (Use Cases)

```
// Caso de uso
public class CriarProdutoUseCase {
    private final ProdutoRepository produtoRepository;
    private final EventPublisher eventPublisher;

    public CriarProdutoUseCase(
        ProdutoRepository produtoRepository,
        EventPublisher eventPublisher
    ) {
        this.produtoRepository = produtoRepository;
        this.eventPublisher = eventPublisher;
    }

    public ProdutoDTO executar(CriarProdutoCommand command) {
        Money preco = Money.of(command.preco(), "BRL");

        Produto produto = new Produto(
            UUID.randomUUID(),
            command.nome(),
            preco,
            command.quantidadeEstoque()
        );

        produtoRepository.salvar(produto);
        eventPublisher.publicar(new ProdutoCriadoEvent(produto.getId()));

        return ProdutoDTO.from(produto);
    }
}

// Command (entrada)
public record CriarProdutoCommand(
    String nome,
    double preco,
    int quantidadeEstoque
) {}

// DTO (saída)
public record ProdutoDTO(
    UUID id,
    String nome,
    double preco,
    int quantidadeEstoque
) {
    public static ProdutoDTO from(Produto produto) {
        return new ProdutoDTO(
            produto.getId(),
            produto.getNome(),
            produto.getPreco().getAmount(),
            produto.getQuantidadeEstoque()
        );
    }
}
```

Enter fullscreen modeExit fullscreen mode

#### Camada de Infraestrutura

```
// Implementação do repositório (adaptador)
@Repository
public class ProdutoRepositoryJpa implements ProdutoRepository {
    private final JpaProdutoRepository jpaRepository;
    private final ProdutoMapper mapper;

    public ProdutoRepositoryJpa(
        JpaProdutoRepository jpaRepository,
        ProdutoMapper mapper
    ) {
        this.jpaRepository = jpaRepository;
        this.mapper = mapper;
    }

    @Override
    public Produto buscarPorId(UUID id) {
        return jpaRepository.findById(id)
            .map(mapper::toDomain)
            .orElseThrow(() -> new ProdutoNaoEncontradoException(id));
    }

    @Override
    public List<Produto> buscarTodos() {
        return jpaRepository.findAll().stream()
            .map(mapper::toDomain)
            .toList();
    }

    @Override
    public void salvar(Produto produto) {
        ProdutoEntity entity = mapper.toEntity(produto);
        jpaRepository.save(entity);
    }

    @Override
    public void deletar(UUID id) {
        jpaRepository.deleteById(id);
    }
}

// Entidade JPA (detalhe de infraestrutura)
@Entity
@Table(name = "produtos")
class ProdutoEntity {
    @Id
    private UUID id;

    @Column(nullable = false)
    private String nome;

    @Column(nullable = false)
    private double preco;

    @Column(nullable = false)
    private int quantidadeEstoque;

    // Getters, setters, constructors
}
```

Enter fullscreen modeExit fullscreen mode

#### Camada de Apresentação

```
@RestController
@RequestMapping("/api/produtos")
public class ProdutoController {
    private final CriarProdutoUseCase criarProdutoUseCase;

    public ProdutoController(CriarProdutoUseCase criarProdutoUseCase) {
        this.criarProdutoUseCase = criarProdutoUseCase;
    }

    @PostMapping
    public ResponseEntity<ProdutoDTO> criar(@RequestBody @Valid CriarProdutoRequest request) {
        CriarProdutoCommand command = new CriarProdutoCommand(
            request.nome(),
            request.preco(),
            request.quantidadeEstoque()
        );

        ProdutoDTO produto = criarProdutoUseCase.executar(command);

        return ResponseEntity.status(HttpStatus.CREATED).body(produto);
    }
}

// Request (específico da camada de apresentação)
public record CriarProdutoRequest(
    @NotBlank(message = "Nome é obrigatório")
    String nome,

    @Positive(message = "Preço deve ser positivo")
    double preco,

    @PositiveOrZero(message = "Estoque não pode ser negativo")
    int quantidadeEstoque
) {}
```

Enter fullscreen modeExit fullscreen mode

### Quanto custa?

**Investimento inicial:**

- 20-30% mais tempo no setup inicial
- Curva de aprendizado de 2-4 semanas
- Mais código boilerplate (mappers, adapters)

**Retorno:**

- Redução de 70% no tempo de testes
- Aumento de 80% na flexibilidade de mudanças
- Diminuição de 60% no acoplamento entre camadas
- Facilita migração de tecnologias (troca de DB, framework)

* * *

## PARTE 4: DIFERENÇAS ENTRE CLEAN ARCHITECTURE E PADRÕES ARQUITETURAIS

### Clean Architecture vs Padrões de Estruturação

Clean Architecture **não é** um padrão arquitetural como MVC ou microserviços. É uma **filosofia de organização** que pode ser aplicada **dentro** de qualquer padrão arquitetural.

![Filosofia Clean Arch vs Tipos Arquiteturais](https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Fb08q0uxv5fde2tie7w9k.png)

| Aspecto | Clean Architecture | Padrões Arquiteturais |
| --- | --- | --- |
| **Natureza** | Filosofia de organização | Padrão de estruturação |
| **Escopo** | Organização interna de componentes | Estrutura do sistema completo |
| **Objetivo** | Independência e testabilidade | Divisão de responsabilidades |
| **Aplicação** | Dentro de cada serviço/módulo | Sistema como um todo |

### MVC (Model-View-Controller)

**O que é:** Padrão que divide a aplicação em três camadas:

- **Model**: dados e lógica de negócio
- **View**: interface com usuário
- **Controller**: intermediário entre View e Model

**Diferença da Clean Architecture:**

- MVC organiza por **tipo de componente** (UI, lógica, dados)
- Clean Architecture organiza por **camadas de abstração** (domínio, aplicação, infraestrutura)

**Podem coexistir:**

```
Aplicação MVC com Clean Architecture:
├── Controller (Presentation Layer - Clean Arch)
├── Service (Application Layer - Clean Arch)
└── Model
    ├── Domain (Entities)
    └── Repository (Infrastructure)
```

Enter fullscreen modeExit fullscreen mode

### Monolito

**O que é:** Toda aplicação em um único processo deployável.

**Diferença da Clean Architecture:**

- Monolito define **estratégia de deployment** (um artefato)
- Clean Architecture define **organização interna** (camadas)

**Podem coexistir:**

```
Monolito bem estruturado com Clean Architecture:
my-app.jar
├── domain/
├── application/
├── infrastructure/
└── presentation/
```

Enter fullscreen modeExit fullscreen mode

**Quando usar Monolito:**

- Equipes pequenas (< 10 pessoas)
- Domínio coeso e bem definido
- Baixa necessidade de escala independente
- Projeto em estágio inicial

### Microserviços

**O que é:** Sistema dividido em serviços independentes que se comunicam via rede.

**Diferença da Clean Architecture:**

- Microserviços definem **distribuição física** (múltiplos processos)
- Clean Architecture define **estrutura lógica interna** de cada serviço

**Podem coexistir:**

```
Cada microserviço usa Clean Architecture internamente:

produto-service/
├── domain/
├── application/
├── infrastructure/
└── presentation/

pedido-service/
├── domain/
├── application/
├── infrastructure/
└── presentation/
```

Enter fullscreen modeExit fullscreen mode

**Quando usar Microserviços:**

- Equipes grandes (> 20 pessoas)
- Necessidade de escala independente
- Domínios bem separados (bounded contexts)
- Requisitos de disponibilidade alta

### Arquitetura Hexagonal (Ports & Adapters)

**O que é:** Padrão que isola a lógica de negócio do mundo externo através de portas (interfaces) e adaptadores (implementações).

**Similaridade com Clean Architecture:**

- **Muito similares!**Ambos focam em:

  - Independência de frameworks
  - Testabilidade
  - Inversão de dependência

**Diferença sutil:**

- Hexagonal enfatiza **portas e adaptadores** (interfaces de entrada/saída)
- Clean Architecture enfatiza **camadas concêntricas** (níveis de abstração)

**Na prática, são quase idênticos:**

```
// Hexagonal
interface ProdutoPort {
    Produto buscar(UUID id);
}

class ProdutoAdapter implements ProdutoPort {
    // implementação
}

// Clean Architecture (mesma coisa com nomes diferentes)
interface ProdutoRepository {
    Produto buscar(UUID id);
}

class ProdutoRepositoryJpa implements ProdutoRepository {
    // implementação
}
```

Enter fullscreen modeExit fullscreen mode

### Arquitetura Orientada a Eventos

**O que é:** Sistema onde componentes se comunicam através de eventos assíncronos.

**Diferença da Clean Architecture:**

- Eventos definem **mecanismo de comunicação** entre componentes
- Clean Architecture define **organização interna** dos componentes

**Podem coexistir:**

```
// Use Case em Clean Architecture que publica evento
public class ProcessarPedidoUseCase {
    private final PedidoRepository repository;
    private final EventPublisher eventPublisher;

    public void executar(ProcessarPedidoCommand command) {
        Pedido pedido = repository.buscar(command.pedidoId());
        pedido.processar();
        repository.salvar(pedido);

        // Publica evento
        eventPublisher.publicar(
            new PedidoProcessadoEvent(pedido.getId())
        );
    }
}
```

Enter fullscreen modeExit fullscreen mode

**Quando usar:**

- Necessidade de desacoplamento temporal
- Sistemas distribuídos
- Processamento assíncrono
- Múltiplos consumidores de mesma informação

### Tabela Comparativa Resumida

| Padrão | Foco | Escopo | Compatível com Clean Arch? |
| --- | --- | --- | --- |
| **MVC** | Separação UI/Lógica/Dados | Organização de camadas | ✅ Sim |
| **Monolito** | Deployment único | Estrutura de deploy | ✅ Sim |
| **Microserviços** | Serviços independentes | Distribuição física | ✅ Sim (dentro de cada serviço) |
| **Hexagonal** | Portas e Adaptadores | Isolamento de negócio | ✅ Sim (praticamente o mesmo) |
| **Eventos** | Comunicação assíncrona | Integração entre componentes | ✅ Sim |

* * *

## CONCLUSÃO

### Resumo dos Conceitos

1. **SOLID** são princípios de design orientado a objetos para classes e interfaces
2. **Clean Code** são práticas de escrita de código legível e manutenível
3. **Clean Architecture** é uma filosofia de organização em camadas independentes

### Relação Entre os Conceitos

```
Clean Architecture (macro)
    ↓
Camadas organizadas seguindo SOLID (médio)
    ↓
Código escrito com Clean Code (micro)
```

Enter fullscreen modeExit fullscreen mode

### Recomendações de Aplicação

**Projeto Pequeno (1-3 devs, < 6 meses):**

✅ Clean Code: sempre

✅ SOLID: princípios básicos (SRP, DIP)

⚠️ Clean Architecture: pode ser overkill

✅ Tipos de Arquitetura do seu sistema: Complexidade e escalabilidade (Tende a ser monolito)

**Projeto Médio (3-10 devs, 6 meses - 2 anos):**

✅ Clean Code: sempre

✅ SOLID: todos os princípios

✅ Clean Architecture: recomendado

✅ Tipos de Arquitetura do seu sistema: Complexidade e escalabilidade (Recomendo Microservices, tendo em vista que tudo está mudando e escalando o tempo todo, porém vai depender da sua regra de negócio e tipo de serviço, se for um serviço estático para um número de requisições não compensa, escalar muito a aplicação, mas casos bem específicos, na maioria escala).

**Projeto Grande (10+ devs, > 2 anos):**

✅ Clean Code: obrigatório

✅ SOLID: obrigatório

✅ Clean Architecture: obrigatório

✅ Considerar microserviços (Hexagonal se houver muita integração direta e indireta e em diferentes níveis do negócio)

### Próximos Passos

1. Pratique refatorando código existente
2. Aplique em pequenos projetos pessoais
3. Estude casos reais (GitHub, open source)
4. Leia os livros clássicos, que foram as referências deste artigo:

   - " [Clean Code](https://www.amazon.com.br/C%C3%B3digo-limpo-Robert-C-Martin/dp/8576082675/ref=asc_df_8576082675?mcid=a77be42894233d01be3730fd59f1df16&tag=googleshopp00-20&linkCode=df0&hvadid=709884550309&hvpos=&hvnetw=g&hvrand=14957170933421257544&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9197956&hvtargid=pla-398225630878&psc=1&hvocijid=14957170933421257544-8576082675-&hvexpln=0&language=pt_BR)" \- Robert C. Martin ( [Uncle Bob](http://butunclebob.com/) )
   - "Clean Architecture" - Robert C. Martin
   - "Refactoring" - Martin Fowler
   - "Design Patterns" - Gang of Four

### Recursos Adicionais

- **Comunidades**: SouJava, @EngineersGirls
- **Eventos**: Coders.jar Tech Summit
- **Práticas**: Code Katas, Pair Programming, Grupo de Estudos
- **Ferramentas**: SonarQube, Checkstyle, SpotBugs

* * *

**Autor:** Simone Pinheiro - @apinheira-tech

Engenheira de Software Java

**Quer ajuda no seu PROJETO?** Entre em contato comigo.

**Última atualização:** 10 Abril 2026

**Licença:** Creative Commons BY-SA 4.0

Fonte: https://dev.to/lady-java/solid-vs-clean-code-vs-clean-architecture-13pn
