
# Prototype (Padrão Criacional) – Perfil Padrão e Clonagem no AILinguo

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
  - [Backend (Java)](#backend-java)
  - [Frontend (React) – opcional](#frontend-react--opcional)
- [Como usar](#como-usar)
- [Testes](#testes)
- [Discussão Vantagens e Desvantagens do Prototype](#discussão-vantagens-e-desvantagens-do-prototype)
- [Conclusão](#conclusão)
- [Vídeo de Apresentação](#vídeo-de-apresentação)
- [Referências Bibliográficas](#referências-bibliográficas)
- [Histórico de Versões](#histórico-de-versões)

---

## Introdução

Segundo Eric Freeman, no livro **“Use a Cabeça! Padrões e Projetos”**, o padrão **Prototype** especifica os tipos de objetos a serem criados por meio de uma instância-protótipo e cria novos objetos **pela cópia desse protótipo**.  
No AILinguo, usamos Prototype para **padronizar** a criação de **perfis de usuário**: em vez de montar o objeto do zero a cada cadastro, **clonamos** um *perfil padrão* e apenas preenchemos os campos específicos do usuário (nome, e‑mail, senha, CEFR).

---

## Objetivo

- Centralizar os **valores padrão** (meta diária, XP inicial, nível, etc.) em um protótipo único.
- **Clonar** rapidamente esse perfil padrão para cada novo usuário (consistência e performance).
- Permitir **variações** futuras sem quebra (ex.: perfis *Admin*/*Professor*) apenas registrando **novos protótipos**.

---

## Contexto no AILinguo

- O fluxo de registro (`/api/auth/register` ou equivalente) precisa montar um **perfil inicial** para cada usuário.
- Com **Prototype**, o serviço de autenticação/fachada apenas **clona** o perfil padrão e injeta as informações do novo usuário, evitando espalhar valores mágicos pelo código.
- O protótipo fica em `com.ailinguo.prototype` e pode ser reutilizado em qualquer caso de uso que precise de um **“fresh user profile”**.

- **Ambiente DEV (código do Prototype):**  
  <https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2/tree/main/backend-java/src/main/java/com/ailinguo/prototype>

---

## Diagrama UML

> **Figura 1 — Prototype (classes principais)**  
> Diagrama  das classes do Prototype e seu uso no Auth/Facade.

<img src="https://raw.githubusercontent.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_Entrega_03/main/docs/assets/img.prototype/prototype.png"
     width="1000" alt="Visão geral Prototype" />

**Autores do UML:** [Luiz Henrique Soares](https://github.com/luizh-gsoares) (UML), com revisão de [Arthur Carvalho Leite](https://github.com/arthurlleite).

---

## Participantes e Mapeamento para o Código

- **Prototype (Interface)** → `backend-java/src/main/java/com/ailinguo/prototype/Prototype.java`  
  Define o **contrato** de clonagem.
- **UserProfilePrototype (Concreto)** → `backend-java/src/main/java/com/ailinguo/prototype/UserProfilePrototype.java`  
  Guarda **defaults** (goal/streak/XP/level) e expõe `clonePrototype()` e `cloneWith(...)`.
- **PrototypeRegistry** → `backend-java/src/main/java/com/ailinguo/prototype/PrototypeRegistry.java`  
  Ponto único para obter o **protótipo padrão** do usuário.
- **AuthService (uso do Prototype)** → método de registro chama o **clone** para montar o usuário inicial.

**Distribuição (GOFs Criacionais / Prototype):**  
- **UML:** [Luiz Henrique Soares](https://github.com/luizh-gsoares)  
- **Código:** [Arthur Carvalho Leite](https://github.com/arthurlleite) e [Letícia Monteiro](https://github.com/leticiamonteiroo)  
- **Documentação:** [Emivalto Tavares Junior](https://github.com/EmivaltoJrr) e [Samuel Afonso da Silva Santos](https://github.com/SamuelAfonso)

---

## Desenvolvimento e Implementação

### Implementação nas Classes

1) **`Prototype<T>`** – Interface do padrão (contrato para `clonePrototype`).  
2) **`UserProfilePrototype`** – Protótipo **concreto** que sabe criar um User **padrão** e, opcionalmente, clonar com identidade (`cloneWith`).  
3) **`PrototypeRegistry`** – *Registry* simples para recuperar o **protótipo padrão** (facilita extensão: registrar Admin/Professor, etc.).  
4) **`AuthService`** – No registro, **clona** o protótipo para montar o novo usuário sem “valores mágicos” no serviço.

### Pontos de Atenção

- **Clonagem “limpa”**: o protótipo não deve carregar dados pessoais; apenas **defaults**.
- **Evolução**: para novos perfis padrão, **adicione** protótipos ao `PrototypeRegistry` (não altere o Auth).
- **Testabilidade**: mocks/stubs do `PrototypeRegistry` permitem testar o registro sem acoplar a defaults concretos.
- **Fronteira Frontend**: manter um **espelho** simples dos defaults ajuda UX (pré-preencher forms), mas o **backend é a fonte da verdade**.

---

## Código completo

### Backend (Java)

<details>
<summary><strong>Prototype.java</strong> — <em>Interface do padrão</em></summary>

```java
package com.ailinguo.prototype;

/**
 * Interface do padrão Prototype.
 * @param <T> tipo do objeto a ser clonado
 */
public interface Prototype<T> {
    T clonePrototype();
}
```
<div align="center">
  <br>Figura 2: <code>Prototype.java</code><br>
  <img src="https://raw.githubusercontent.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_Entrega_03/main/docs/assets/img.prototype/PrototypeIMG.png"
     width="1000" alt="Prototype.java" /><br>

  <b>Autores:</b> <a href="https://github.com/leticiamonteiroo">Letícia Monteiro</a>, <a href="https://github.com/arthurlleite">Arthur Carvalho Leite</a>, <a href="https://github.com/luizh-gsoares">Luiz Henrique Soares</a>, <a href="https://github.com/SamuelAfonso">Samuel Afonso</a>, <a href="https://github.com/EmivaltoJrr">Emivalto Da Costa</a>.
</div>
```
</details>

<details>
<summary><strong>UserProfilePrototype.java</strong> — <em>Protótipo concreto do "perfil padrão"</em></summary>

```java
package com.ailinguo.prototype;

import com.ailinguo.model.User;
import java.time.LocalDateTime;

/**
 * Protótipo concreto para criação de perfis padrão de usuário.
 * Centraliza valores default e expõe clonagem "limpa".
 */
public class UserProfilePrototype implements Prototype<User> {

    // Defaults de um usuário "fresh"
    private final int defaultDailyGoalMinutes;
    private final int defaultStreakDays;
    private final int defaultTotalMinutes;
    private final int defaultTotalXp;
    private final int defaultLevel;

    public UserProfilePrototype() {
        this.defaultDailyGoalMinutes = 15;
        this.defaultStreakDays = 0;
        this.defaultTotalMinutes = 0;
        this.defaultTotalXp = 0;
        this.defaultLevel = 1;
    }

    @Override
    public User clonePrototype() {
        // Clona um usuário "limpo" (sem identidade pessoal)
        return User.builder()
                .dailyGoalMinutes(defaultDailyGoalMinutes)
                .streakDays(defaultStreakDays)
                .totalMinutes(defaultTotalMinutes)
                .totalXp(defaultTotalXp)
                .level(defaultLevel)
                .createdAt(LocalDateTime.now())
                .build();
    }

    /**
     * Clona e preenche campos de identidade.
     */
    public User cloneWith(String email, String name, String passwordHash, User.CefrLevel cefrLevel) {
        User base = clonePrototype();
        base.setEmail(email);
        base.setName(name);
        base.setPassword(passwordHash);
        base.setCefrLevel(cefrLevel);
        return base;
    }
}
```
<div align="center">
  <br>Figura 3: <code>UserProfilePrototype.java</code><br>
  <img src="https://raw.githubusercontent.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_Entrega_03/main/docs/assets/img.prototype/UserProfilePrototypeIMG.png"
     width="1000" alt="UserProfilePrototype.java" /><br>

  <b>Autores:</b> <a href="https://github.com/arthurlleite">Arthur Carvalho Leite</a> e <a href="https://github.com/leticiamonteiroo">Letícia Monteiro</a> (código);
  revisão: <a href="https://github.com/luizh-gsoares">Luiz Henrique Soares</a> (UML).
</div>
```
</details>

<details>
<summary><strong>PrototypeRegistry.java</strong> — <em>Registro dos protótipos</em></summary>

```java
package com.ailinguo.prototype;

/**
 * Registro simples de protótipos.
 * Permite evoluir para múltiplos perfis padrão (ex.: Admin/Professor).
 */
public class PrototypeRegistry {

    private static final UserProfilePrototype DEFAULT_USER = new UserProfilePrototype();

    private PrototypeRegistry() {}

    public static UserProfilePrototype defaultUserProfile() {
        return DEFAULT_USER;
    }
}
```
<div align="center">
  <br>Figura 4: <code>PrototypeRegistry.java</code><br>
  <img src="https://raw.githubusercontent.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_Entrega_03/main/docs/assets/img.prototype/PrototypeRegistryIMG.png"
     width="1000" alt="PrototypeRegistry.java" /><br>

  <b>Autores:</b> <a href="https://github.com/arthurlleite">Arthur Carvalho Leite</a> e <a href="https://github.com/leticiamonteiroo">Letícia Monteiro</a> (código);
  revisão: <a href="https://github.com/luizh-gsoares">Luiz Henrique Soares</a> (UML).
</div>
```
</details>

<details>
<summary><strong>AuthService.java</strong> (trecho) — <em>Usando o Prototype no registro</em></summary>

```java
// Dentro de com.ailinguo.service.AuthService (método register)
User user = com.ailinguo.prototype.PrototypeRegistry
        .defaultUserProfile()
        .cloneWith(
            email,
            name,
            passwordEncoder.encode(password),
            cefrLevel
        );

userRepository.save(user);
```
<div align="center">
  <br>Figura 5: <code>AuthService.java</code> (uso do Prototype)<br>
  <img src="https://raw.githubusercontent.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_Entrega_03/main/docs/assets/img.prototype/AuthServiceIMG.png"
     width="1000" alt="AuthService.java" /><br>

  <b>Autores:</b> <a href="https://github.com/arthurlleite">Arthur Carvalho Leite</a> e <a href="https://github.com/leticiamonteiroo">Letícia Monteiro</a> (código);
  documentação: <a href="https://github.com/EmivaltoJrr">Emivalto Jr.</a> e <a href="https://github.com/SamuelAfonso">Samuel Afonso</a>.
</div>
```
</details>

### Frontend (React) – opcional

<details>
<summary><strong>profilePrototype.js</strong> — <em>Espelho simples dos defaults no cliente</em></summary>

```javascript
// File: frontend-react/src/prototype/profilePrototype.js
// Pequeno helper para inicializar estados do form com os mesmos defaults do backend.

export const defaultProfilePrototype = Object.freeze({
  dailyGoalMinutes: 15,
  streakDays: 0,
  totalMinutes: 0,
  totalXp: 0,
  level: 1,
  cefrLevel: 'A2'
});

export function cloneProfile(overrides = {}) {
  return { ...defaultProfilePrototype, ...overrides };
}
```
<div align="center">
  <br>Figura 6: <code>profilePrototype.js</code><br>
  <img src="https://raw.githubusercontent.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_Entrega_03/main/docs/assets/img.prototype/profilePrototypeIMG.png"
     width="1000" alt="profilePrototype.js" /><br>

  <b>Autores:</b> <a href="https://github.com/arthurlleite">Arthur Carvalho Leite</a> (apoio front) e <a href="https://github.com/leticiamonteiroo">Letícia Monteiro</a>.
</div>
```
</details>

---

## Como usar

- No **registro** de usuário, chame:
```java
User novo = PrototypeRegistry.defaultUserProfile()
               .cloneWith(email, name, passwordHash, cefrLevel);
userRepository.save(novo);
```
- Para **novos perfis padrão** (ex.: *Admin*, *Teacher*), crie outra classe *Prototype* concreta e exponha um getter no `PrototypeRegistry`.

---

## Testes

- **Smoke test** do registro: `POST /api/auth/register` com `{ email, name, password, cefrLevel }` deve criar um usuário com `dailyGoalMinutes=15`, `streakDays=0`, `totalXp=0`, `level=1` etc.  
- **Unit**: mock do `PrototypeRegistry` para verificar se o `AuthService` chama **clone** (e não constrói manualmente o `User`).

---

## Discussão Vantagens e Desvantagens do Prototype

**Vantagens**
- **Consistência**: todos começam com os mesmos defaults (goal, XP, nível…).
- **Flexível**: novos perfis padrão entram só registrando outro protótipo.
- **Baixo acoplamento**: o serviço não conhece valores mágicos; apenas **clona**.

**Desvantagens / Cuidados**
- **Clonagem profunda** pode ser complexa em objetos com grafos/referências cíclicas.
- **Encapsulamento**: cuidado ao acessar campos privados no clone.
- **Sincronismo de defaults** entre front e back: manter o **back como fonte da verdade**.

---

## Conclusão

O **Prototype** no AILinguo simplifica e padroniza a criação do **perfil inicial de usuários**, favorecendo **coerência**, **testabilidade** e **evolução** (novos perfis por registro, sem refatorações amplas). O padrão se integra naturalmente com a **fachada** e os serviços de autenticação.

---

## Vídeo de Apresentação
O vídeo 1 abaixo mostra explicação e a execução do Prototype para o AprendendocomIA

<div align="center">
  Vídeo 1: Prototype para AprendendocomIA
  <br>
  <iframe width="960" height="540"
          src="https://www.youtube.com/embed/Kbd-NaKFBSQ"
          title="Vídeo — Prototype no AILinguo"
          frameborder="0"
          allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
          allowfullscreen>
  </iframe>
  <br>
  <b>Autor:</b> <a href="https://github.com/arthurlleite">Arthur Carvalho Leite</a>.
</div>


---

## Referências Bibliográficas

- FREEMAN, Eric et al. **Use A Cabeça! Padrões e Projetos.** Rio de Janeiro: Alta Books, 2007.  
- GAMMA, E. et al. **Padrões de Projeto: soluções reutilizáveis de software orientado a objetos.** Porto Alegre: Bookman, 2006.

---

## Histórico de Versões

| Versão | Descrição | Autor(es) | Data de Produção | Revisor(es) | Data de Revisão | Incremento do Revisor |
| :----: | --------- | --------- | :--------------: | ----------- | :-------------: | :-------------------: |
| `1.0` | Estrutura inicial e códigos Prototype | [Arthur Carvalho Leite](https://github.com/arthurlleite), [Letícia Monteiro](https://github.com/leticiamonteiroo) | 24/10/2025 |  | |  |
| `1.1` | Ajustes e Confecção da  UML | [Luiz Henrique Soares](https://github.com/luizh-gsoares) | 24/10/2025 | [Arthur Carvalho Leite](https://github.com/arthurlleite) | 24/10/2025 | Ajustes UML |
| `1.2` | Documentação (texto e organização) | [Emivalto Tavares Junior](https://github.com/EmivaltoJrr), [Samuel Afonso da Silva Santos](https://github.com/SamuelAfonso) | 24/10/2025 | [Arthur Carvalho Leite](https://github.com/arthurlleite) | 24/10/2025 | Revisão técnica |
