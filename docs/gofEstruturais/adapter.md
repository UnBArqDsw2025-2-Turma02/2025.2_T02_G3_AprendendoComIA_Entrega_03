# Padrão Estrutural Adapter - LLMProvider

## Sumário

- [Introdução](#Introdução)
- [Objetivo](#Objetivo)
- [Metodologia](#Metodologia)
- [Desenvolvimento e Implementação](#Desenvolvimento-e-Implementação)
- [Modelagem do Adapter para LLMProvider](#Modelagem-do-Adapter-para-LLMProvider)
    - [Implementação das Classes do Adapter](#Implementação-das-Classes-do-Adapter)
    - [Classe de Teste AdapterTest](#Classe-de-Teste-AdapterTest)
- [Vídeo Explicação e Execução do Adapter para LLMProvider](#Vídeo-Explicação-e-Execução-do-Adapter-para-LLMProvider)
- [Discussão Vantagens e Desvantagens do Adapter](#Discussão-Vantagens-e-Desvantagens-do-Adapter)
- [Conclusão](#Conclusão)
- [Referências](#Referências)
- [Histórico de Versão](#Histórico-de-Versão)

## Introdução

Como explicado em [Adapter](https://refactoring.guru/pt-br/design-patterns/adapter), o padrão de projeto Adapter é uma solução estrutural utilizada para permitir que objetos com interfaces incompatíveis trabalhem juntos. Com isso, ele oferece uma interface unificada para diferentes implementações, o que o torna especialmente útil para integrar sistemas legados, APIs externas ou diferentes provedores de serviços, como LLMs (Large Language Models), sem modificar o código cliente existente.

No contexto do projeto AILinguo, o padrão Adapter foi aplicado à interface LLMProvider, responsável por centralizar o acesso a diferentes provedores de LLM (OpenAI, Gemini, Mock), permitindo alternância entre eles sem modificar o código cliente. Assim, essa decisão arquitetural foi tomada para assegurar que haja uma interface estável para chamadas de LLM no sistema e evitar acoplamento entre o código cliente e as implementações específicas de cada provedor.

## Objetivo

Os principais objetivos ao aplicar o padrão Adapter à interface LLMProvider são:

* *Interface Unificada:* Fornecer um contrato estável (TutorResponse tutor(TutorRequest)) que o restante do domínio consome, independentemente do provedor LLM utilizado.
* *Flexibilidade de Provedores:* Permitir múltiplos provedores (OpenAI, Gemini, Mock) sem alterar o código cliente.
* *Configuração Dinâmica:* Centralizar a lógica de seleção do provedor através de configuração (app.llm.provider).
* *Testabilidade:* Facilitar testes unitários através do MockAdapter sem necessidade de chamadas externas.

## Metodologia

O padrão de projeto estrutural *Adapter* foi selecionado para a interface LLMProvider devido à necessidade de integrar diferentes provedores de LLM com interfaces incompatíveis. Assim, como foi descrito em [Padrão Adapter: Implementação e Exemplos de Uso](https://refactoring.guru/pt-br/design-patterns/adapter), esta abordagem garante uma interface unificada para diferentes implementações.

A implementação do padrão Adapter para a interface LLMProvider foi realizada em Java, utilizando o Spring Boot como framework de desenvolvimento, com o apoio das ferramentas e extensões para desenvolvimento Java. O processo de desenvolvimento seguiu as práticas padrão de codificação e organização em pacotes, conforme detalhado nas seções subsequentes.

*A concepção das classes do Adapter foi guiada pela análise dos seguintes artefatos de modelagem previamente desenvolvidos:*

* *Análise de Integração LLM:* As classes do Adapter são fundamentais para a integração com diferentes provedores de LLM na aplicação AILinguo, sendo utilizadas por serviços, controladores e componentes do sistema.
* *Gerenciamento de Configuração:* As classes mantêm configurações para seleção dinâmica do provedor e gerenciamento de dependências.
* *Encapsulamento de Implementação:* Múltiplas partes do sistema precisam acessar funcionalidades de LLM de forma consistente, incluindo integração com OpenAI, Gemini e testes com Mock.

*O desenvolvimento e implementação das classes do Adapter seguiram os seguintes passos:*

1.  *Identificação da Necessidade de Adaptação:* Foi constatado, a partir da análise dos requisitos de integração com LLMs, que a plataforma "AILinguo" deveria possuir uma interface unificada para diferentes provedores. Esta interface centraliza todas as operações de LLM, e a existência de múltiplas implementações diretas seria conceitualmente incorreta e logisticamente problemática.
2.  *Escolha do Padrão Adapter:* Diante da exigência de uma interface unificada para diferentes provedores, o padrão de projeto estrutural Adapter foi escolhido como a solução de design mais direta e apropriada.
3.  *Definição da Estrutura das Classes:* Criação dos arquivos LLMProvider.java, OpenAIAdapter.java, MockAdapter.java, GeminiAdapter.java e LLMConfiguration.java.
4.  *Implementação dos Elementos Característicos do Adapter*
5.  *Definição dos Métodos de Negócio da Adaptação*
6.  *Integração e Teste na Aplicação (AdapterTest.java):*

*Principais Componentes e Características da Implementação do Adapter:*

* *Interface LLMProvider:* Define o contrato estável para operações de LLM (tutor(TutorRequest)).
* *Classe OpenAIAdapter:* Adapta a interface OpenAIService existente para o contrato LLMProvider.
* *Classe MockAdapter:* Implementa um adapter de teste com respostas simuladas.
* *Classe GeminiAdapter:* Esqueleto para futura integração com o provedor Gemini.
* *Classe LLMConfiguration:* Factory que seleciona o adapter apropriado baseado em configuração.

No contexto do "AILinguo", as classes do Adapter devem encapsular suas funcionalidades (como adaptação de interfaces, delegação de chamadas e configuração de provedores) e a lógica para gerenciá-los, enquanto o padrão Adapter controla a unificação e a adaptação.

# Desenvolvimento e Implementação

A seguir, são detalhadas a modelagem e a implementação das classes do Adapter e sua interação com outras partes do sistema.

## Modelagem do Adapter para LLMProvider

Abaixo o espaço para o seu diagrama UML para o Adapter do LLMProvider:

<div align="center">
    Figura 1 (Adapter): Modelagem UML
    <br>
    <img src="assets/adapterUML.jpeg" alt="Modelagem UML do Adapter para LLMProvider" width="800">
    <br>
    <b>Autores:</b> Gabriel, Leonardo de Melo, Mateus Bastos, Vitor Bessa, Felipe das Neves.
    <br>
</div>
## Implementação das Classes do Adapter

As classes do Adapter foram implementadas no pacote com.ailinguo.llm para representar o sistema de adaptação de provedores LLM da plataforma. Elas contêm interfaces bem definidas para adaptação, implementações concretas para diferentes provedores, e configuração para seleção dinâmica.

Abaixo o código para as classes do Adapter:

<details>
  <summary><strong>Código para LLMProvider.java:</strong></summary>

java
package com.ailinguo.llm;

import com.ailinguo.dto.tutor.TutorRequest;
import com.ailinguo.dto.tutor.TutorResponse;

/** Target (GoF Adapter) – contrato estável do domínio. */
public interface LLMProvider {
    TutorResponse tutor(TutorRequest request);
}


<b> Autor: </b> Gabriel Lima e Mateus Bastos.

</details>

<details>
  <summary><strong>Código para OpenAIAdapter.java:</strong></summary>

java
package com.ailinguo.llm;

import com.ailinguo.dto.tutor.TutorRequest;
import com.ailinguo.dto.tutor.TutorResponse;
import com.ailinguo.service.OpenAIService;

/** Adapter concreto p/ OpenAI – delega ao serviço existente. */
public class OpenAIAdapter implements LLMProvider {
    private final OpenAIService openAIService;
    
    public OpenAIAdapter(OpenAIService openAIService) { 
        this.openAIService = openAIService; 
    }

    @Override
    public TutorResponse tutor(TutorRequest request) {
        return openAIService.generateTutorResponse(request);
    }
}


<b> Autor: </b> Gabriel Lima e Mateus Bastos.

</details>

<details>
  <summary><strong>Código para MockAdapter.java:</strong></summary>

java
package com.ailinguo.llm;

import com.ailinguo.dto.tutor.TutorRequest;
import com.ailinguo.dto.tutor.TutorResponse;
import java.util.Arrays;

/** Adapter mock */
public class MockAdapter implements LLMProvider {

    @Override
    public TutorResponse tutor(TutorRequest request) {
        TutorResponse res = new TutorResponse();
        String msg = request.getUserText() == null ? "" : request.getUserText();

        res.setReply("Mock: here is a friendly correction of your text.");
        TutorResponse.Correction c = TutorResponse.Correction.builder()
                .original(msg)
                .corrected(msg.replace("go to", "went to"))
                .explanation("Past tense correction (mock).")
                .rule("Simple Past of 'go' is 'went'.")
                .build();
        res.setCorrections(Arrays.asList(c));

        TutorResponse.MiniExercise ex = TutorResponse.MiniExercise.builder()
                .type("multiple_choice")
                .question("Choose the correct past of 'go'")
                .options(Arrays.asList("go", "went", "gone", "going"))
                .correct(1)
                .explanation("Past simple is 'went'.")
                .build();
        res.setMiniExercise(ex);
        return res;
    }
}


<b> Autor: </b> Gabriel Lima e Mateus Bastos.

</details>

<details>
  <summary><strong>Código para GeminiAdapter.java:</strong></summary>

java
package com.ailinguo.llm;

import com.ailinguo.dto.tutor.TutorRequest;
import com.ailinguo.dto.tutor.TutorResponse;

/** Adapter para Gemini (esqueleto). Implemente quando integrar o SDK/HTTP. */
public class GeminiAdapter implements LLMProvider {
    @Override
    public TutorResponse tutor(TutorRequest request) {
        TutorResponse res = new TutorResponse();
        res.setReply("[Gemini] Not yet implemented");
        return res;
    }
}


<b> Autor: </b> Gabriel Lima e Mateus Bastos.

</details>

<details>
  <summary><strong>Código para LLMConfiguration.java:</strong></summary>

java
package com.ailinguo.llm;

import com.ailinguo.service.OpenAIService;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/** Factory/Config do Adapter – escolhe o provider por propriedade. */
@Configuration
public class LLMConfiguration {

    @Value("${app.llm.provider:openai}")
    private String provider;

    @Bean
    public LLMProvider llmProvider(OpenAIService openAIService) {
        switch (provider.toLowerCase()) {
            case "mock": return new MockAdapter();
            case "gemini": return new GeminiAdapter();
            case "openai":
            default: return new OpenAIAdapter(openAIService);
        }
    }
}


<b> Autor: </b> Gabriel Lima e Mateus Bastos.

</details>

##### Imagem do código no VSCODE

As figuras 2, 3, 4, 5 e 6 abaixo ilustram a estrutura das classes do Adapter no ambiente de desenvolvimento VSCode.
<div align="center">
    Figura 2: Interface LLMProvider.java
    <br>
    <img src="assets/img.adapter/LLMProvider.png" width="1000">
    <br>
    <b>Autor:</b> Gabriel Lima e Mateus Bastos.
    <br>
</div>

---

<div align="center">
    Figura 3: Classe OpenAIAdapter.java
    <br>
    <img src="assets/img.adapter/OpenAIAdapter.png" width="1000">
    <br>
    <b>Autor:</b> Gabriel Lima e Mateus Bastos.
    <br>
</div>

---

<div align="center">
    Figura 4: Classe MockAdapter.java
    <br>
    <img src="assets/img.adapter/MockAdapter.png" width="1000">
    <br>
    <b>Autor:</b> Gabriel Lima e Mateus Bastos.
    <br>
</div>

---

<div align="center">
    Figura 5: Classe GeminiAdapter.java
    <br>
    <img src="assets/img.adapter/GeminiAdapter.png" width="1000">
    <br>
    <b>Autor:</b> Gabriel Lima e Mateus Bastos.
    <br>
</div>

---

<div align="center">
    Figura 6: Classe LLMConfiguration.java
    <br>
    <img src="assets/img.adapter/LLMConfiguration.png" width="1000">
    <br>
    <b>Autor:</b> Gabriel Lima e Mateus Bastos.
    <br>
</div>

## Classe de Teste AdapterTest

A classe AdapterTest.java foi criada para demonstrar e testar o funcionamento do padrão Adapter aplicado ao LLMProvider. Esta classe contém testes que verificam a funcionalidade de adaptação, delegação de chamadas e comportamento dos diferentes adapters.

*Observação:* Embora a implementação completa de testes unitários não seja o foco deste documento sobre Adapter, sua existência é necessária para a validação da funcionalidade das classes do Adapter.

Abaixo o código para AdapterTest.java:

<details>
  <summary><strong>Código para AdapterTest.java</strong></summary>

java
package com.ailinguo.llm;

import com.ailinguo.dto.tutor.TutorRequest;
import com.ailinguo.dto.tutor.TutorResponse;
import com.ailinguo.model.User;
import com.ailinguo.service.OpenAIService;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.DisplayName;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;

class AdapterTest {

    @Mock
    private OpenAIService openAIService;

    private TutorRequest sampleRequest;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
        sampleRequest = new TutorRequest();
        sampleRequest.setUserText("I go to school yesterday");
        sampleRequest.setUserLevel(User.CefrLevel.A2);
    }

    @Test
    @DisplayName("MockAdapter deve retornar resposta mock válida")
    void testMockAdapter() {
        MockAdapter adapter = new MockAdapter();
        
        TutorResponse response = adapter.tutor(sampleRequest);
        
        assertNotNull(response);
        assertNotNull(response.getReply());
        assertTrue(response.getReply().contains("Mock"));
        assertNotNull(response.getCorrections());
        assertFalse(response.getCorrections().isEmpty());
        assertNotNull(response.getMiniExercise());
    }

    @Test
    @DisplayName("MockAdapter deve fazer correção de texto")
    void testMockAdapterCorrection() {
        MockAdapter adapter = new MockAdapter();
        
        TutorResponse response = adapter.tutor(sampleRequest);
        
        TutorResponse.Correction correction = response.getCorrections().get(0);
        assertNotNull(correction);
        assertTrue(correction.getCorrected().contains("went to"));
        assertNotNull(correction.getExplanation());
        assertNotNull(correction.getRule());
    }

    @Test
    @DisplayName("GeminiAdapter deve retornar mensagem de não implementado")
    void testGeminiAdapter() {
        GeminiAdapter adapter = new GeminiAdapter();
        
        TutorResponse response = adapter.tutor(sampleRequest);
        
        assertNotNull(response);
        assertNotNull(response.getReply());
        assertTrue(response.getReply().contains("Gemini"));
        assertTrue(response.getReply().contains("Not yet implemented"));
    }

    @Test
    @DisplayName("OpenAIAdapter deve delegar para OpenAIService")
    void testOpenAIAdapterDelegation() {
        TutorResponse mockResponse = new TutorResponse();
        mockResponse.setReply("OpenAI response");
        
        when(openAIService.generateTutorResponse(any(TutorRequest.class)))
            .thenReturn(mockResponse);
        
        OpenAIAdapter adapter = new OpenAIAdapter(openAIService);
        TutorResponse response = adapter.tutor(sampleRequest);
        
        assertNotNull(response);
        assertEquals("OpenAI response", response.getReply());
        verify(openAIService, times(1)).generateTutorResponse(sampleRequest);
    }

    @Test
    @DisplayName("Todos os adapters devem implementar LLMProvider")
    void testAllAdaptersImplementInterface() {
        MockAdapter mockAdapter = new MockAdapter();
        GeminiAdapter geminiAdapter = new GeminiAdapter();
        OpenAIAdapter openAIAdapter = new OpenAIAdapter(openAIService);
        
        assertTrue(mockAdapter instanceof LLMProvider);
        assertTrue(geminiAdapter instanceof LLMProvider);
        assertTrue(openAIAdapter instanceof LLMProvider);
    }
}


<b> Autor: </b> Gabriel Lima e Mateus Bastos.
##### Imagem do código no VSCODE

A figura 7 abaixo ilustra a primeira parte da classe AdapterTest.java no ambiente de desenvolvimento VSCode, e a figura 8 a sua continuação.

<div align="center">
    Figura 7: Classe de Teste AdapterTest.java (Parte 1)
    <br>
    <img src="assets/img.adapter/adapterTest1.png" width="1000">
    <br>
    <b>Autor:</b> Gabriel Lima e Mateus Bastos.
    <br>
</div>

</details>

---

<div align="center">
    Figura 8: Classe de Teste AdapterTest.java (Parte 2/Continuação)
    <br>
    <img src="assets/img.adapter/adapterTest2.png" width="1000">
    <br>
    <b>Autor:</b> Gabriel Lima e Mateus Bastos.
    <br>
</div>

## Vídeo Explicação e Execução do Adapter para LLMProvider

O vídeo 1 abaixo mostra explicação e a execução do Adapter para LLMProvider

<div align="center">
    Vídeo 1: Adapter para LLMProvider
    <br>
    <iframe width="1321" height="743" src="https://www.youtube.com/embed/tYnxU_JCnv4?si=i2tmRaZbr4bVYgnY" title="AILinguo: Explicação e Execução do Adapter para LLMProvider" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
   <br>
    <b>Autor:</b> Felipe, Leonardo e Vítor.
    <br>
</div>

## Discussão Vantagens e Desvantagens do Adapter

A escolha de utilizar o padrão Adapter para a interface LLMProvider foi ponderada, considerando seus benefícios e também as críticas frequentemente associadas a este padrão.

*Vantagens Observadas:*

- *Interface Unificada:* O padrão fornece um contrato estável (TutorResponse tutor(TutorRequest)) que o restante do domínio consome, independentemente do provedor LLM utilizado.
- *Flexibilidade de Provedores:* Permite múltiplos provedores (OpenAI, Gemini, Mock) sem alterar o código cliente.
- *Configuração Dinâmica:* A LLMConfiguration permite trocar providers por configuração, útil para diferentes ambientes (dev/test/prod).
- *Testabilidade:* O MockAdapter facilita testes sem chamadas externas.

*Desvantagens e Considerações (Críticas ao Adapter):*

É importante reconhecer as críticas comuns ao padrão Adapter, que foram consideradas:

- *Complexidade Adicional:* O padrão pode adicionar complexidade desnecessária para integrações simples.
- *Overhead de Performance:* Pode haver overhead adicional devido às camadas de adaptação.
- *Manutenção:* Cada novo provedor requer implementação de um novo adapter.
- *Acoplamento:* Ainda existe acoplamento com as interfaces específicas dos provedores.

*Observação:* No contexto deste projeto e para a integração com diferentes provedores de LLM que requerem interfaces unificadas e configuração flexível, os benefícios de unificação e flexibilidade foram considerados preponderantes.

---

## Bibliogaria

> FREEMAN, Eric et al. Use A Cabeça Padrões e Projetos. Rio de Janeiro: Alta Books, 2007.

> GAMMA, E. et al. Padrões de projeto: soluções reutilizáveis de software orientado a objetos. Porto Alegre: Bookman, 2006.

> Refactoring.Guru – Adapter: https://refactoring.guru/pt-br/design-patterns/adapter

> DEVMEDIA. Padrão de Projeto Adapter em Java. DevMedia, [s.d.]. Disponível em: https://www.devmedia.com.br/padrao-de-projeto-adapter-em-java/26397.

---

## Histórico de Versões

## Histórico de versão


| Versão | Descrição | Autor(es) | Data de Produção | Revisor(es) | Data de Revisão | Incremento do Revisor |
| :----: | --------- | --------- | :--------------: | ----------- | :-------------: | :-------------------: |
| `1.0` | Elaboração dos códigos e documentação | [Gabriel Lima, Mateus Bastos, Vitor Bessa e Leonardo de Melo ]() | 23/10/2025 | | | |
| `1.1` | Complemento nos textos | [Felipe das Neves](FelipeFreire-gf) | 23/10/2025 | | | |
| `1.2` | Adição do video de explicação e execução | [Vítor Bessa](Bessaz), [Leonardo de Melo]() | 23/10/2025 | | | |
| `1.3` | Modelagem da UML |  [ Gabriel Lima, Mateus Bastos, Vitor Bessa, Leonardo de Melo  e Felipe das Neves]()| 23/10/2025 | | | |

