# Prompt
SpecOps (Metodologia de Modernização de Software)

# Gemini
A metodologia SpecOps (Specification-Driven Legacy Modernization) propõe uma quebra de paradigma na modernização de sistemas legados complexos (especialmente no setor governamental). Idealizada e documentada por especialistas como [Mark Headd](https://spec-ops.ai/), o cerne da metodologia dita que o conhecimento institucional embutido no sistema legado é muito mais valioso do que o código em si. [1, 2, 3, 4, 5] 
Enquanto abordagens tradicionais de IA tentam apenas traduzir linhas de código diretamente (como converter COBOL para Java) — o que costuma propagar erros, redundâncias e funções obsoletas — a SpecOps foca na extração do comportamento do sistema em especificações legíveis por humanos antes de qualquer codificação. [1, 2, 3, 6, 7] 

------------------------------

## 💡 Os Princípios Fundamentais (The Manifesto)

* 
* A Especificação é a única Fonte de Verdade: Inspirado na filosofia GitOps de infraestrutura, as especificações comportamentais do software são controladas por versão em repositórios Git. Qualquer alteração futura no sistema começa alterando a especificação primeiro, e nunca o código de forma isolada. [1, 3] 
* Preservação do Conhecimento: Regras de elegibilidade, políticas públicas e casos de borda documentados no código antigo são traduzidos em linguagem natural estruturada. [1, 5] 
* Especialistas de Domínio como Árbitros: Analistas de negócios e especialistas em políticas públicas (que não entendem de código) conseguem revisar, validar e auditar as especificações geradas, garantindo que a IA extraiu a lógica correta do negócio. [3, 8] 
* IA Auxilia, Humanos Verificam: Modelos de linguagem (LLMs) são excelentes para analisar bases massivas de código legado e criar rascunhos de documentação detalhada. O julgamento humano valida o resultado. [7, 8] 
* 

------------------------------
## 🔄 Como Funciona a Conversão: As 6 Fases do Método
O fluxo de transformação da metodologia é dividido de forma estrita em seis etapas sequenciais: [9] 

[Descoberta] ➔ [Geração da Spec por IA] ➔ [Verificação Humana] ➔ [Implementação] ➔ [Testes] ➔ [Implantação]


   1. Descoberta e Avaliação (Discovery and Assessment): Mapeamento do inventário legado e definição do escopo da modernização.
   2. Geração de Especificações (Specification Generation): Agentes de IA consomem o código antigo e geram artefatos estruturados em formatos universais, como Markdown, contendo os modelos conceituais, fluxos lógicos e regras de negócio.
   3. Verificação da Especificação (Specification Verification): Especialistas de domínio revisam o documento gerado em linguagem natural. Erros de interpretação da IA são corrigidos diretamente no texto da especificação.
   4. Implementação Moderna (Modern Implementation): Com a especificação aprovada e versionada em Git, novas ferramentas ou agentes de codificação em IA geram a aplicação moderna (em arquiteturas de nuvem, microsserviços, etc.) guiando-se estritamente pela documentação aprovada.
   5. Testes e Validação (Testing and Validation): Testes automatizados validam se o novo código reflete exatamente o comportamento detalhado na especificação.
   6. Implantação e Transferência (Deployment & Knowledge Transfer): O sistema vai para produção e o conhecimento institucional fica perpetuado de forma limpa no repositório. [1, 2, 3, 5, 7, 9, 10] 

## ⚖️ Comparativo Direto de Abordagens

| Critério | Abordagem Tradicional / Transpilação Direta | Metodologia SpecOps |
|---|---|---|
| Foco principal | Converter sintaxe antiga em moderna (Ex: COBOL ➔ Java) | Extrair e preservar a lógica institucional antes de codificar |
| Fonte de Verdade | O código-fonte gerado na nova linguagem | A especificação versionada em Git |
| Papel dos Analistas | Testam o software apenas no final do ciclo de desenvolvimento | Validam o comportamento do sistema no início através do texto |
| Funções Obsoletas | São traduzidas e mantidas sem necessidade no novo código | São descartadas no processo de revisão da especificação |
| Documentação | Geralmente desatualizada ou inexistente | Sempre sincronizada com o comportamento atual do sistema |

Gostaria de se aprofundar em alguma etapa específica? Se desejar, posso ajudar fornecendo:

* 
* Um exemplo prático de prompt/instrução para agentes de IA extraírem regras de negócio seguindo o padrão SpecOps.
* Detalhes sobre a estrutura ideal da equipe necessária para tocar um projeto sob essa metodologia.
* Como criar e organizar o repositório de especificações em Git.
* 


[1] https://spec-ops.ai
[2] [https://github.com](https://github.com/mheadd/spec-ops)
[3] [https://www.linkedin.com](https://www.linkedin.com/pulse/specops-new-approach-legacy-system-modernization-mark-headd-82die)
[4] [https://www.linkedin.com](https://www.linkedin.com/pulse/proving-out-new-approach-legacy-system-modernization-mark-headd-zmyze)
[5] [https://civic.io](https://civic.io/2025/12/04/proving-out-a-new-approach-to-legacy-system-modernization/)
[6] [https://www.linkedin.com](https://www.linkedin.com/posts/markheadd_specops-specification-driven-legacy-modernization-activity-7499949833290502144-BunP)
[7] [https://spec-ops.ai](https://spec-ops.ai/book)
[8] [https://github.com](https://github.com/mheadd/spec-ops)
[9] [https://github.com](https://github.com/spec-ops-method/spec-ops/blob/main/public/METHODOLOGY.md)
[10] [https://www.infoq.com](https://translate.google.com/translate?u=https://www.infoq.com/articles/spec-driven-development/&hl=pt&sl=en&tl=pt&client=sge)


# Prompt
Exemplo prático para migrar projeto Delphi para Java

# Gemini
Schatz, um exemplo prático de como aplicar a metodologia SpecOps para migrar um módulo legado em Delphi (Object Pascal) para uma arquitetura moderna em Java (Spring Boot).
Em vez de colarmos o código Delphi no ChatGPT e pedir "converta para Java", vamos seguir o ecossistema SpecOps: extrair a lógica institucional em uma especificação agnóstica de linguagem, revisá-la e depois gerar o código Java limpo.

------------------------------
## 🧱 O Cenário Exemplo
Imagine que temos uma função legada em Delphi que calcula o desconto de um cliente com base em regras governamentais ou regras de negócio antigas de uma distribuidora.
## O Código Legado (Delphi)

```Delphi
function CalcularDescontoEspecial(ValorTotal: Double; CategoriaCliente: Integer; Regiao: string): Double;
begin
  Result := 0.0;
  if (ValorTotal > 5000.00) then
  begin
    if (CategoriaCliente = 1) then // Cliente VIP antigo
      Result := ValorTotal * 0.10
    else if (CategoriaCliente = 2) then // Cliente Corporativo
      Result := ValorTotal * 0.05;
  end;
  
  // Regra de subsídio regional adicionada em 2014 (ZFM)
  if (Regiao = 'NORTE') and (CategoriaCliente = 1) then
    Result := Result + (ValorTotal * 0.02);
end;
```

------------------------------
## 🔄 Passo 1: O Prompt de Extração (Fase de Geração da Spec)
Como engenheiro SpecOps, você usará um LLM/Agente de IA para documentar o comportamento, não a sintaxe. Você envia o código Delphi com o seguinte prompt:

``` Prompt
Prompt SpecOps:
"Atue como um Analista de Sistemas Sênior especializado em Engenharia Reversa de Sistemas Legados.
Analise o código Delphi fornecido e extraia o comportamento lógico do negócio.
Escreva o resultado em formato Markdown estruturado (SpecOps Specification), focando inteiramente nas regras de negócio e ignorando termos técnicos específicos do Delphi.
O público-alvo deste documento são especialistas de negócio e auditores que não sabem programar."
```
------------------------------
## 📄 Passo 2: O Artefato Gerado (A Especificação SpecOps)
A IA gerará o arquivo abaixo (que você salvará como docs/specs/calculo-desconto.md no seu repositório Git corporativo):

``` M̀arkdown
# Especificação Funcional: Cálculo de Desconto Especial

## 1. Objetivo Comercial
Calcular o percentual e valor de desconto aplicados a pedidos com base no volume de vendas, perfil de classificação do cliente e localização geográfica de entrega.

## 2. Regras de Negócio Fundamentais
* **RN01 - Elegibilidade por Volume:** O desconto só é ativado se o valor total do pedido for estritamente superior a R$ 5.000,00.
* **RN02 - Desconto por Perfil (VIP):** Clientes com o perfil de classificação antigo "1" (equivalente a clientes VIP) recebem 10% de desconto sobre o valor total.
* **RN03 - Desconto por Perfil (Corporativo):** Clientes com o perfil de classificação "2" (equivalente a clientes Corporativos) recebem 5% de desconto sobre o valor total.
* **RN04 - Subsídio Geográfico (Região Norte):** Caso o cliente possua o perfil "1" (VIP) E a entrega ocorra na Região Norte, concede-se um desconto adicional acumulativo de 2% sobre o valor total do pedido.

## 3. Casos de Borda e Validações
* Pedidos com valores iguais ou inferiores a R$ 5.000,00 possuem desconto padrão zero, a menos que se enquadrem em futuras regras de exceção.
* Se o cliente for da Região Norte, mas não for perfil 1 (VIP), o subsídio geográfico de 2% não se aplica.
```

⚠️ Fase de Verificação Humana: Neste momento, o analista de negócios lê o Markdown acima. Ele pode notar, por exemplo, que o termo "perfil de classificação antigo 1" deve ser atualizado para o termo moderno "Categoria DIAMANTE". Ele altera o Markdown no Git. A especificação agora é a fonte da verdade.

------------------------------
## 🚀 Passo 3: Geração do Novo Código (Java / Spring Boot)
Agora que a especificação em Markdown está revisada, aprovada e versionada no Git, você abre uma nova sessão de IA (ou usa seu agente de codificação) e fornece apenas o Markdown aprovado, orientando a arquitetura moderna que deseja:

``` Prompt
Prompt de Implementação:
"Atue como um Arquiteto de Software Java especialista em Spring Boot 3 e boas práticas (Clean Code, SOLID).
Leia a especificação SpecOps em Markdown anexada. Implemente a lógica descrita criando um serviço Java estruturado.
Use boas práticas como Enums para categorias de clientes e garanta que o código seja testável unitariamente usando JUnit 5."
```

## O Código Moderno Gerado (Java)
A IA lerá a especificação e gerará um código limpo, desacoplado do fantasma estrutural do Delphi:

package com.empresa.modernizacao.service;
import java.math.BigDecimal;import com.empresa.modernizacao.model.CategoriaCliente; // Enum: DIAMANTE, CORPORATIVO, REGULAR
public class DescontoService {

    private static final BigDecimal VALOR_CORTE_ELEGIBILIDADE = new BigDecimal("5000.00");
    private static final String REGIAO_BENEFICIADA = "NORTE";

    public BigDecimal calcularDescontoEspecial(BigDecimal valorTotal, CategoriaCliente categoria, String regiao) {
        if (valorTotal == null || valorTotal.compareTo(VALOR_CORTE_ELEGIBILIDADE) <= 0) {
            return BigDecimal.ZERO;
        }

        BigDecimal percentualDesconto = BigDecimal.ZERO;

        // Aplicação das regras RN02 e RN03 (Traduzidas para Enums modernos)
        if (categoria == CategoriaCliente.DIAMANTE) {
            percentualDesconto = percentualDesconto.add(new BigDecimal("0.10")); // 10%
        } else if (categoria == CategoriaCliente.CORPORATIVO) {
            percentualDesconto = percentualDesconto.add(new BigDecimal("0.05")); // 5%
        }

        // Aplicação da RN04 (Subsídio Geográfico)
        if (REGIAO_BENEFICIADA.equalsIgnoreCase(regiao) && categoria == CategoriaCliente.DIAMANTE) {
            percentualDesconto = percentualDesconto.add(new BigDecimal("0.02")); // +2%
        }

        return valorTotal.multiply(percentualDesconto);
    }
}

## 🎯 Por que isso é superior à tradução direta?

   1. Adequação Arquitetural: O Delphi usava inteiros mágicos (1, 2) e tipos primitivos Double (perigosos para dinheiro). O Java gerado usou BigDecimal (padrão de mercado para finanças) e Enums, sem herdar os vícios de escrita do programador Delphi de 15 anos atrás.
   2. Documentação Eterna: Se amanhã o time precisar mudar para C# ou Python, ninguém precisa ler o Java ou o Delphi. A especificação em Markdown na pasta docs/ explica exatamente o que o sistema faz.

Para avançarmos no seu laboratório de migração, me diga:

* O seu projeto Delphi utiliza muito acesso direto ao banco de dados nas telas (Data Modules, ClientDataSets) ou a lógica é isolada em Units?
* Qual é o tamanho aproximado desse sistema (quantas tabelas ou telas)?
* Você deseja que eu monte um script/pipeline que automatize a leitura em lote de múltiplos arquivos .pas?



