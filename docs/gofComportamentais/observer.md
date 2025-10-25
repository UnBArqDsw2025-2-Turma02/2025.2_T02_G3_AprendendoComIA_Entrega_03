# Observer (Padrão Comportamental) – Notificações de Progresso no AILinguo

---
## Sumário
- [Introdução](#introdução)
- [Objetivo](#objetivo)
- [Contexto no AILinguo](#contexto-no-ailinguo)
- [Diagrama UML](#diagrama-uml)
- [Participantes e Mapeamento para o Código](#participantes-e-mapeamento-para-o-código)
- [Desenvolvimento e Implementação](#desenvolvimento-e-implementação)
  - [Implementação nas Classes](#implementação-nas-classes)
  - [Pontos de Atenção](#pontos-de-atenção)
- [Código completo](#código-completo)
  - [Observer.java](#observerjava)
  - [Subject.java](#subjectjava)
  - [UserProgressSubject.java](#userprogresssubjectjava)
  - [AchievementObserver.java](#achievementobserverjava)
  - [NotificationObserver.java](#notificationobserverjava)
  - [ObserverConfig.java](#observerconfigjava)
  - [ObserverDemo.java (opcional)](#observerdemojava-opcional)
  - [Uso em serviços](#uso-em-serviços)
- [Como usar](#como-usar)
- [Testes](#testes)
- [Discussão – Vantagens e Desvantagens](#discussão--vantagens-e-desvantagens)
- [Vídeo de Apresentação](#vídeo-de-apresentação)
- [Referências Bibliográficas](#referências-bibliográficas)
- [Histórico de Versões](#histórico-de-versões)

---

## Introdução

Segundo Eric Freeman, no livro **“Use a Cabeça! Padrões e Projetos”**, o padrão **Observer** define uma **dependência um-para-muitos** entre objetos. Quando o **Subject** muda seu estado, **notifica automaticamente** todos os **Observers** registrados, que reagem à mudança.

No AILinguo, aplicamos Observer para **gamificação e telemetria de progresso**: quando o usuário ganha XP, sobe de nível ou conclui tarefas, o `UserProgressSubject` notifica observadores que **desacoplam** efeitos como **logs de conquistas** e **envio de notificações**.

---

## Objetivo

- **Desacoplar** regras de notificação/efeitos colaterais das regras de negócio de progresso.
- Permitir **vários observadores** reagindo aos mesmos eventos (ex.: *Achievements*, *Notifications*, *Analytics*).
- **Facilitar testes** e evolução incremental (adicionar/remover observers sem tocar no subject/serviços).

---

## Contexto no AILinguo

- Eventos disparados no fluxo de estudo:
  - **`XP_GAINED`**: ganho de XP por interação (chat, revisão de vocabulário, etc.).
  - **`LEVEL_UP`**: mudança de nível do aluno.
  - **`TASK_COMPLETED`**: conclusão de uma tarefa (ex.: *VOCABULARY_REVIEW*, *CHAT_INTERACTION*).
- **Ambiente DEV (código do Observer – neste commit):**  
  <https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2/tree/690d6fd813e36566e6659da26d52200093c59037/backend-java/src/main/java/com/ailinguo/observer>

- **Commit base:**  
  <https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2/commit/690d6fd813e36566e6659da26d52200093c59037>

---

## Diagrama UML

> **Figura 1 — Observer (classes principais)**  
> Diagrama de classes do padrão aplicado ao progresso do usuário.

<img src="https://raw.githubusercontent.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_Entrega_03/main/docs/assets/UMLObserver.jpg"
     width="1000" alt="Diagrama UML do Observer no AILinguo" />

**Autor do UML:** [Luiz Henrique Soares](https://github.com/luizh-gsoares).  
**Revisão:** [Arthur Carvalho Leite](https://github.com/arthurlleite).

---

## Participantes e Mapeamento para o Código

- **Subject (Interface)** → [`Subject.java`](https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2/blob/690d6fd813e36566e6659da26d52200093c59037/backend-java/src/main/java/com/ailinguo/observer/Subject.java)  
  Contrato para gerenciar observers e **notificar** eventos.

- **Observer (Interface)** → [`Observer.java`](https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2/blob/690d6fd813e36566e6659da26d52200093c59037/backend-java/src/main/java/com/ailinguo/observer/Observer.java)  
  Contrato para **receber** eventos do subject.

- **Concrete Subject** → [`UserProgressSubject.java`](https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2/blob/690d6fd813e36566e6659da26d52200093c59037/backend-java/src/main/java/com/ailinguo/observer/UserProgressSubject.java)  
  Gera e **notifica** eventos como `XP_GAINED`, `LEVEL_UP`, `TASK_COMPLETED`.

- **Concrete Observers**  
  - **Achievements** → [`AchievementObserver.java`](https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2/blob/690d6fd813e36566e6659da26d52200093c59037/backend-java/src/main/java/com/ailinguo/observer/AchievementObserver.java) – Regras de **conquistas** (ex.: “Primeiros 100 XP”, “Nível 5”).  
  - **Notifications** → [`NotificationObserver.java`](https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2/blob/690d6fd813e36566e6659da26d52200093c59037/backend-java/src/main/java/com/ailinguo/observer/NotificationObserver.java) – **Avisos**/mensagens ao usuário (logs; no futuro, e‑mail/push).

- **Configuração** → [`ObserverConfig.java`](https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2/blob/690d6fd813e36566e6659da26d52200093c59037/backend-java/src/main/java/com/ailinguo/observer/ObserverConfig.java)  
  Registra (`attach`) observers ao subject (Spring `@Component` + `@PostConstruct`).

- **Exemplo CLI (opcional)** → [`ObserverDemo.java`](https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2/blob/690d6fd813e36566e6659da26d52200093c59037/backend-java/src/main/java/com/ailinguo/observer/ObserverDemo.java)

- **Pontos de uso (serviços)**  
  - [`TutorService.java`](https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2/blob/690d6fd813e36566e6659da26d52200093c59037/backend-java/src/main/java/com/ailinguo/service/TutorService.java) – notifica `XP_GAINED` e `TASK_COMPLETED` após interação.  
  - [`VocabularyService.java`](https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2/blob/690d6fd813e36566e6659da26d52200093c59037/backend-java/src/main/java/com/ailinguo/service/VocabularyService.java) – notifica `XP_GAINED` e `TASK_COMPLETED` após revisão.

**Créditos (Observer):**  
- **Código:** [Letícia Monteiro](https://github.com/leticiamonteiroo) e [Luiz Henrique Soares](https://github.com/luizh-gsoares)  
- **UML:** [Luiz Henrique Soares](https://github.com/luizh-gsoares)  
- **Documentação:** [Arthur Carvalho Leite](https://github.com/arthurlleite)

---

## Desenvolvimento e Implementação

### Implementação nas Classes

1) **Interfaces base** – `Observer` e `Subject` definem os contratos.  
2) **`UserProgressSubject`** – mantém a lista de observers e emite eventos de progresso.  
3) **Observers concretos** – `AchievementObserver` (regras de conquistas) e `NotificationObserver` (mensagens/logs).  
4) **`ObserverConfig`** – registra automaticamente os observers ao iniciar a aplicação.  
5) **Serviços** – invocam métodos de negócio do subject para publicar eventos (ex.: ganhar XP).

### Pontos de Atenção

- **Strings de evento** compartilhadas entre subject/observers (`XP_GAINED`, `LEVEL_UP`, `TASK_COMPLETED`).  
  (Uma alternativa é usar `enum` para maior segurança de tipos.)
- **Ordem dos observers** – sem garantias; cada um deve ser **independente**.
- **Evolução** – adicione novos observers sem alterar `UserProgressSubject` (princípio Open/Closed).

---

## Código completo

> Os trechos abaixo refletem o **estado do commit** `690d6fd` (links diretos em cada seção). Use as setas para expandir cada bloco.

### `Observer.java`

[Ver no commit](https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2/blob/690d6fd813e36566e6659da26d52200093c59037/backend-java/src/main/java/com/ailinguo/observer/Observer.java)

<details>
<summary><strong>Mostrar código</strong></summary>

```java
package com.ailinguo.observer;

/**
 * Observer interface - recebe notificações do Subject.
 */
public interface Observer {
    void update(String event, Object data);
}
```
</details>

---

### `Subject.java`

[Ver no commit](https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2/blob/690d6fd813e36566e6659da26d52200093c59037/backend-java/src/main/java/com/ailinguo/observer/Subject.java)

<details>
<summary><strong>Mostrar código</strong></summary>

```java
package com.ailinguo.observer;

/**
 * Subject interface - gerencia observers e notifica eventos.
 */
public interface Subject {

    void attach(Observer observer);

    void detach(Observer observer);

    void notifyObservers(String event, Object data);
}
```
</details>

---

### `UserProgressSubject.java`

[Ver no commit](https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2/blob/690d6fd813e36566e6659da26d52200093c59037/backend-java/src/main/java/com/ailinguo/observer/UserProgressSubject.java)

<details>
<summary><strong>Mostrar código</strong></summary>

```java
package com.ailinguo.observer;

import org.springframework.stereotype.Component;
import java.util.ArrayList;
import java.util.List;

/**
 * Concrete Subject - notifica observers sobre mudanças de progresso do usuário.
 */
@Component
public class UserProgressSubject implements Subject {

    private final List<Observer> observers = new ArrayList<>();

    @Override
    public void attach(Observer observer) {
        if (!observers.contains(observer)) {
            observers.add(observer);
        }
    }

    @Override
    public void detach(Observer observer) {
        observers.remove(observer);
    }

    @Override
    public void notifyObservers(String event, Object data) {
        for (Observer observer : observers) {
            observer.update(event, data);
        }
    }

    // ===== Business events (helpers) =====

    public void userGainedXp(Long userId, Integer xpGained, Integer totalXp) {
        notifyObservers("XP_GAINED", new XpData(userId, xpGained, totalXp));
    }

    public void userLeveledUp(Long userId, Integer newLevel) {
        notifyObservers("LEVEL_UP", new LevelData(userId, newLevel));
    }

    public void userCompletedTask(Long userId, String taskType) {
        notifyObservers("TASK_COMPLETED", new TaskData(userId, taskType));
    }

    // Payloads (records)
    public record XpData(Long userId, Integer xpGained, Integer totalXp) {}
    public record LevelData(Long userId, Integer newLevel) {}
    public record TaskData(Long userId, String taskType) {}
}
```
</details>

---

### `AchievementObserver.java`

[Ver no commit](https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2/blob/690d6fd813e36566e6659da26d52200093c59037/backend-java/src/main/java/com/ailinguo/observer/AchievementObserver.java)

<details>
<summary><strong>Mostrar código</strong></summary>

```java
package com.ailinguo.observer;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;

/**
 * Concrete Observer - regras de achievements.
 */
@Component
public class AchievementObserver implements Observer {

    private static final Logger logger =
            LoggerFactory.getLogger(AchievementObserver.class);

    @Override
    public void update(String event, Object data) {
        switch (event) {
            case "XP_GAINED" -> handleXpGained((UserProgressSubject.XpData) data);
            case "LEVEL_UP" -> handleLevelUp((UserProgressSubject.LevelData) data);
            case "TASK_COMPLETED" -> handleTaskCompleted((UserProgressSubject.TaskData) data);
        }
    }

    private void handleXpGained(UserProgressSubject.XpData data) {
        logger.info("🏅 Achievement: User {} gained {} XP (total: {}).",
                data.userId(), data.xpGained(), data.totalXp());

        // Milestone: primeiros 100 XP (bateu 100 agora)
        if (data.totalXp() >= 100 && data.totalXp() - data.xpGained() < 100) {
            logger.info("🥇 Achievement Unlocked: First 100 XP!");
        }
    }

    private void handleLevelUp(UserProgressSubject.LevelData data) {
        logger.info("🏅 Achievement: User {} reached level {}.",
                data.userId(), data.newLevel());

        if (data.newLevel() == 5) {
            logger.info("🥈 Achievement Unlocked: Intermediate Learner!");
        }
    }

    private void handleTaskCompleted(UserProgressSubject.TaskData data) {
        logger.info("🏅 Achievement: User {} completed task '{}'.",
                data.userId(), data.taskType());
    }
}
```
</details>

---

### `NotificationObserver.java`

[Ver no commit](https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2/blob/690d6fd813e36566e6659da26d52200093c59037/backend-java/src/main/java/com/ailinguo/observer/NotificationObserver.java)

<details>
<summary><strong>Mostrar código</strong></summary>

```java
package com.ailinguo.observer;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;

/**
 * Concrete Observer - notifica usuário (logs; no futuro e-mail/push).
 */
@Component
public class NotificationObserver implements Observer {

    private static final Logger logger =
            LoggerFactory.getLogger(NotificationObserver.class);

    @Override
    public void update(String event, Object data) {
        switch (event) {
            case "XP_GAINED" -> notifyXpGained((UserProgressSubject.XpData) data);
            case "LEVEL_UP" -> notifyLevelUp((UserProgressSubject.LevelData) data);
            case "TASK_COMPLETED" -> notifyTaskCompleted((UserProgressSubject.TaskData) data);
        }
    }

    private void notifyXpGained(UserProgressSubject.XpData data) {
        logger.info("🔔 Notification: Sending XP notice to user {}: +{} XP.",
                data.userId(), data.xpGained());
        // Produção: e-mail, push, etc.
    }

    private void notifyLevelUp(UserProgressSubject.LevelData data) {
        logger.info("🔔 Notification: Congratulations! User {} is now level {}.",
                data.userId(), data.newLevel());
    }

    private void notifyTaskCompleted(UserProgressSubject.TaskData data) {
        logger.info("🔔 Notification: User {} completed '{}'.",
                data.userId(), data.taskType());
    }
}
```
</details>

---

### `ObserverConfig.java`

[Ver no commit](https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2/blob/690d6fd813e36566e6659da26d52200093c59037/backend-java/src/main/java/com/ailinguo/observer/ObserverConfig.java)

<details>
<summary><strong>Mostrar código</strong></summary>

```java
package com.ailinguo.observer;

import jakarta.annotation.PostConstruct;
import org.springframework.stereotype.Component;

/**
 * Configuration - registra os observers com o subject.
 */
@Component
public class ObserverConfig {

    private final UserProgressSubject userProgressSubject;
    private final AchievementObserver achievementObserver;
    private final NotificationObserver notificationObserver;

    public ObserverConfig(UserProgressSubject userProgressSubject,
                          AchievementObserver achievementObserver,
                          NotificationObserver notificationObserver) {
        this.userProgressSubject = userProgressSubject;
        this.achievementObserver = achievementObserver;
        this.notificationObserver = notificationObserver;
    }

    @PostConstruct
    public void init() {
        userProgressSubject.attach(achievementObserver);
        userProgressSubject.attach(notificationObserver);
    }
}
```
</details>

---

### `ObserverDemo.java` (opcional)

[Ver no commit](https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2/blob/690d6fd813e36566e6659da26d52200093c59037/backend-java/src/main/java/com/ailinguo/observer/ObserverDemo.java)

<details>
<summary><strong>Mostrar código</strong></summary>

```java
package com.ailinguo.observer;

/**
 * Exemplo simples (CLI) demonstrando o padrão Observer sem Spring.
 */
public class ObserverDemo {

    public static void main(String[] args) {
        UserProgressSubject subject = new UserProgressSubject();

        AchievementObserver achievement = new AchievementObserver();
        NotificationObserver notification = new NotificationObserver();

        subject.attach(achievement);
        subject.attach(notification);

        System.out.println("=== Simulando progresso do usuário ===");

        subject.userGainedXp(1L, 10, 10);
        subject.userGainedXp(1L, 90, 100);
        subject.userLeveledUp(1L, 5);
        subject.userCompletedTask(1L, "VOCABULARY_REVIEW");

        subject.detach(achievement);
        subject.userGainedXp(1L, 15, 115);
    }
}
```
</details>

---

### Uso em serviços

**TutorService** – notifica ganho de XP por interação e conclusão de tarefa:

[Ver no commit](https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2/blob/690d6fd813e36566e6659da26d52200093c59037/backend-java/src/main/java/com/ailinguo/service/TutorService.java)

<details>
<summary><strong>Mostrar código (trecho)</strong></summary>

```java
// Dentro de com.ailinguo.service.TutorService
private final UserProgressSubject userProgressSubject;

public TutorService(OpenAIService openAIService,
                    ChatTurnRepository chatTurnRepository,
                    UserProgressSubject userProgressSubject) {
    this.openAIService = openAIService;
    this.chatTurnRepository = chatTurnRepository;
    this.userProgressSubject = userProgressSubject;
}

public TutorResponse processTutorRequest(TutorRequest request, Long userId) {
    // ... lógica ...
    userProgressSubject.userGainedXp(userId, 10, 10); // 10 XP por interação
    userProgressSubject.userCompletedTask(userId, "CHAT_INTERACTION");
    return response;
}
```
</details>

**VocabularyService** – notifica ganho de XP e conclusão da revisão:

[Ver no commit](https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2/blob/690d6fd813e36566e6659da26d52200093c59037/backend-java/src/main/java/com/ailinguo/service/VocabularyService.java)

<details>
<summary><strong>Mostrar código (trecho)</strong></summary>

```java
// Dentro de com.ailinguo.service.VocabularyService
private final UserProgressSubject userProgressSubject;

public VocabularyService(VocabularyCardRepository vocabularyCardRepository,
                         SrsReviewRepository srsReviewRepository,
                         UserProgressSubject userProgressSubject) {
    this.vocabularyCardRepository = vocabularyCardRepository;
    this.srsReviewRepository = srsReviewRepository;
    this.userProgressSubject = userProgressSubject;
}

public VocabularyReviewResponse reviewCard(VocabularyReviewRequest request) {
    // ... lógica de salvar review ...
    Long userIdLong = Long.parseLong(request.getUserId());
    Integer xpGained = request.getEase() == 5 ? 5 : 2;
    userProgressSubject.userGainedXp(userIdLong, xpGained, 100);
    userProgressSubject.userCompletedTask(userIdLong, "VOCABULARY_REVIEW");
    // ...
    return response;
}
```
</details>

---

## Como usar

- Injetar `UserProgressSubject` nos serviços que disparam eventos de progresso.  
- Chamar os **helpers** do subject (ex.: `userGainedXp`, `userLeveledUp`, `userCompletedTask`).  
- Cadastrar `Observer`s no `ObserverConfig` (ou dinamicamente) – nenhum serviço conhece observadores **concretos**.

---

## Testes

- **Unitário (Subject):** verificar que `attach/detach` funcionam e que `notifyObservers` chama `update` em todos os observers.  
- **Unitário (Observers):** validar regras de `AchievementObserver` (ex.: log em 100 XP, nível 5).  
- **Integração (Serviços):** ao processar interação/review, os eventos corretos são disparados e capturados pelos observers.

---

## Discussão – Vantagens e Desvantagens

**Vantagens**
- **Baixo acoplamento** entre a lógica de progresso e efeitos (notificações, conquistas).
- **Extensível**: novos observers entram sem tocar no subject/serviços.
- **Reuso**: o mesmo evento abastece vários consumidores.

**Desvantagens / Cuidados**
- **Ordem de execução** não garantida entre observers.
- **Debugging**: muitos observers podem dificultar rastreamento; centralize logs.
- **Strings de evento**: considerar `enum`/constantes para evitar typos.

---

## Vídeo de Apresentação

<iframe width="560" height="315" src="https://www.youtube.com/embed/EVhuY7rqSyw?si=Qk_ZsYS7erm5JekR" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

---

## Referências Bibliográficas

- FREEMAN, Eric et al. **Use A Cabeça! Padrões e Projetos.** Rio de Janeiro: Alta Books, 2007.  
- GAMMA, E. et al. **Padrões de Projeto: soluções reutilizáveis de software orientado a objetos.** Porto Alegre: Bookman, 2006.

---

## Histórico de Versões

| Versão | Descrição | Autor(es) | Data de Produção | Revisor(es) | Data de Revisão | Incremento do Revisor |
| :----: | --------- | --------- | :--------------: | ----------- | :-------------: | :-------------------: |
| `1.0` | Implementação do padrão Observer no backend + documentação inicial | [Arthur Carvalho Leite](https://github.com/arthurlleite) (docs), [Letícia Monteiro](https://github.com/leticiamonteiroo) e [Luiz Henrique Soares](https://github.com/luizh-gsoares) (código), [Luiz Henrique Soares](https://github.com/luizh-gsoares) (UML) | 24/10/2025 | — | — | — |
| `1.1` | Ajustes finais, links para o commit e seção de vídeo | [Arthur Carvalho Leite](https://github.com/arthurlleite) | 24/10/2025 | — | — | — |
| `1.2` | Vídeo de Explicação | [Letícia Monteiro](https://github.com/leticiamonteiroo) | 24/10/2025 | — | — | — |

