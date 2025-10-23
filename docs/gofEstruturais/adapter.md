<<<<<<< Updated upstream
# Adapter

---
## Sumário
- [Introdução](#introdução)
- [Diagrama UML](#diagrama)
- [Referências Bibliográficas](#referencias-bibliográficas)
- [Histórico de Versões](#histórico-de-versões)
=======
## Sumário
>>>>>>> Stashed changes

- [Introdução](#introdução)
- [Objetivo](#objetivo)
- [Visão do código existente](#visão-do-código-existente)
- [Implementação encontrada no projeto](#implementação-encontrada-no-projeto)
  - [Interface Target — `LLMProvider`](#interface-target----llmprovider)
  - [Adapters concretos (`OpenAIAdapter`, `MockAdapter`, `GeminiAdapter`)](#adapters-concretos-openaiadapter-mockadapter-geminiadapter)
  - [Configuração / Factory — `LLMConfiguration`](#configuração--factory----llmconfiguration)
- [Exemplo de uso](#exemplo-de-uso)
- [Testes e verificação](#testes-e-verificação)
- [Vantagens e desvantagens na implementação atual](#vantagens-e-desvantagens-na-implementação-atual)
- [Próximos passos sugeridos](#próximos-passos-sugeridos)
- [Referências](#referências)
- [Histórico de versão](#histórico-de-versão)

## Introdução

<<<<<<< Updated upstream
Segundo Eric Freeman, no livro __"Use a Cabeça! Padrões E Projetos"__, o padrão __Adapter__ é definido como uma maneira de fazer duas interfaces incompatíveis trabalharem juntas, ou seja, ele atua como um tradutor entre duas interfaces diferentes. O __Adapter__ envolve o objeto existente e fornece uma interface compatível com o cliente facilitando a integração de classes incompatíveis sem modificar seu código original.
=======
O padrão Adapter é usado aqui para oferecer um contrato estável (`LLMProvider`) que o restante do domínio consome, enquanto a implementação concreta (OpenAI, Gemini, mock, etc.) pode variar. A `LLMConfiguration` escolhe qual adapter injetar com base em propriedade (`app.llm.provider`).

## Objetivo

- Criar um contrato estável para chamadas LLM no domínio (`TutorResponse tutor(TutorRequest)`).
- Permitir múltiplos provedores (OpenAI, Gemini, mocks) sem alterar o código cliente.
- Centralizar a lógica de adaptação e a seleção do provider.

## Visão do código existente

Arquivos relevantes (existentes no projeto):

- `com.ailinguo.llm.LLMProvider` — interface Target que define `tutor(TutorRequest)`.
- `com.ailinguo.llm.OpenAIAdapter` — adapter concreto que delega para `OpenAIService`.
- `com.ailinguo.llm.MockAdapter` — adapter de teste que responde com dados simulados.
- `com.ailinguo.llm.GeminiAdapter` — esqueleto de adapter para futura integração com Gemini.
- `com.ailinguo.llm.LLMConfiguration` — classe `@Configuration` que cria o bean `LLMProvider` selecionando o provider por propriedade.

Esses arquivos já implementam a estrutura clássica do Adapter (Target + ConcreteAdapters) e uma fábrica simples.

## Implementação encontrada no projeto

### Interface Target — `LLMProvider`

Trecho principal:

```java
package com.ailinguo.llm;

import com.ailinguo.dto.tutor.TutorRequest;
import com.ailinguo.dto.tutor.TutorResponse;

public interface LLMProvider {
    TutorResponse tutor(TutorRequest request);
}
```

Esta interface é o contrato estável esperado pelos serviços/controles do domínio.

### Adapters concretos (`OpenAIAdapter`, `MockAdapter`, `GeminiAdapter`)

OpenAIAdapter (delegação ao serviço existente):

```java
package com.ailinguo.llm;

import com.ailinguo.dto.tutor.TutorRequest;
import com.ailinguo.dto.tutor.TutorResponse;
import com.ailinguo.service.OpenAIService;

public class OpenAIAdapter implements LLMProvider {
    private final OpenAIService openAIService;
    public OpenAIAdapter(OpenAIService openAIService) { this.openAIService = openAIService; }

    @Override
    public TutorResponse tutor(TutorRequest request) {
        return openAIService.generateTutorResponse(request);
    }
}
```

MockAdapter (adaptador de teste com resposta estática/formatada):

```java
package com.ailinguo.llm;

import com.ailinguo.dto.tutor.TutorRequest;
import com.ailinguo.dto.tutor.TutorResponse;
import java.util.Arrays;

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
```

GeminiAdapter (esqueleto — ainda não implementado):

```java
package com.ailinguo.llm;

import com.ailinguo.dto.tutor.TutorRequest;
import com.ailinguo.dto.tutor.TutorResponse;

public class GeminiAdapter implements LLMProvider {
    @Override
    public TutorResponse tutor(TutorRequest request) {
        TutorResponse res = new TutorResponse();
        res.setReply("[Gemini] Not yet implemented");
        return res;
    }
}
```

### Configuração / Factory — `LLMConfiguration`

Classe de configuração que fornece o bean `LLMProvider` com base na propriedade `app.llm.provider` (valor padrão `openai`):

```java
package com.ailinguo.llm;

import com.ailinguo.service.OpenAIService;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

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
```

Essa configuração torna fácil alternar providers por configuração (ex.: `-Dapp.llm.provider=mock`).

## Exemplo de uso

Um serviço ou controller que precise de um LLM deve depender da interface `LLMProvider` e receber o bean via injeção do Spring:

```java
@Service
public class TutorService {
    private final LLMProvider llmProvider;

    public TutorService(LLMProvider llmProvider) {
        this.llmProvider = llmProvider;
    }

    public TutorResponse askTutor(TutorRequest req) {
        return llmProvider.tutor(req);
    }
}
```

Com isso, trocar o provider não exige alteração do `TutorService`.

## Testes e verificação

- Já existe um `MockAdapter` que facilita testes unitários (injetando o provider mock via `LLMConfiguration` com `app.llm.provider=mock` ou instanciando `new MockAdapter()` diretamente nos testes).
- Sugestão de casos de teste:
  - Verificar que `MockAdapter` retorna respostas com `corrections` e `miniExercise` conforme o corpo do `TutorRequest` de exemplo.
  - Testar `TutorService` com um `MockAdapter` injetado (ou com um mock do `LLMProvider`).
  - Testar a delegação do `OpenAIAdapter` (aqui pode ser necessário mockar `OpenAIService` para validar chamadas e tratativas de erros).

Exemplo simples de teste usando o `MockAdapter`:

```java
@Test
public void testTutorServiceWithMockAdapter() {
    MockAdapter mock = new MockAdapter();
    TutorRequest req = new TutorRequest();
    req.setUserText("I go to school yesterday");
    TutorResponse resp = mock.tutor(req);
    assertNotNull(resp.getReply());
    assertFalse(resp.getCorrections().isEmpty());
}
```

## Vantagens e desvantagens na implementação atual

Vantagens:

- Implementação simples e clara do padrão Adapter; `LLMProvider` é o contrato único.
- `LLMConfiguration` permite trocar providers por configuração, útil para ambientes (dev/test/prod).
- `MockAdapter` facilita testes sem chamadas externas.

Desvantagens / pontos a melhorar:

- `GeminiAdapter` está como esqueleto — falta implementar integração real e tratamento de erros/timeouts.
- `LLMConfiguration` instancia adapters diretamente; se houver mais dependências (ex.: clientes HTTP diferentes), pode ser interessante usar `@ConditionalOnProperty` e `@ConfigurationProperties` ou providers separados para cada cliente.
- Falta tratamento centralizado de erros e de timeouts/retries na camada de adapters (ex.: circuit-breaker, retry, fallback).

## Próximos passos sugeridos

1. Implementar `GeminiAdapter` conectando o SDK/HTTP do provedor Gemini (tratamento de autenticação/erros).
2. Adicionar testes que mockem `OpenAIService` para validar comportamento do `OpenAIAdapter` sem chamadas reais.
3. Externalizar configuração de providers para `application.yml` com exemplos e documentação (ex.: `app.llm.provider: openai`).
4. Considerar uso de adaptadores mais ricos: mapeamentos DTO<->domínio separados, validação, e retry/backoff.

## Referências

- REFACTORING GURU. Adapter. Disponível em: https://refactoring.guru/pt-br/design-patterns/adapter
- Documentação do Spring Boot: https://spring.io/projects/spring-boot

## Histórico de versão

| Versão | Alteração | Responsável | Data |
| - | - | - | - |
| 1.1 | Revisão do README para refletir os adapters reais (`llm`), exemplos e recomendações | Mateus Bastos | 23/10/2025 |






>>>>>>> Stashed changes


## Diagrama UML


---
## Referências Bibliográficas

- FREEMAN, Eric et al. __Use A Cabeça Padrões E Projetos.__ Rio de Janeiro: Alta Books, 2007.
- GAMMA, E. et al. __Padrões de projeto : soluções reutilizáveis de software orientado a objetos.__ Porto Alegre: Bookman, 2006.

<<<<<<< Updated upstream
---

## Histórico de Versões

| Versão | Descrição | Autor(es) | Data de Produção | Revisor(es) | Data de Revisão | Incremento do Revisor |
| :----: | --------- | --------- | :--------------: | ----------- | :-------------: | :-------------------: |
| `1.0` | Documentação e formatação da aba | [Samuel Afonso](https://github.com/SamuelAfonso) | 22/10/2025 | | | |


=======
>>>>>>> Stashed changes


