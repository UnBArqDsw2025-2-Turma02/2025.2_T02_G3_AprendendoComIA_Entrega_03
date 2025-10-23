# Singleton (Padrão Criacional) – Escopo de Beans no AILinguo

---
## Sumário
- [Introdução](#introdução)
- [Objetivo](#objetivo)
- [Contexto no AILinguo](#contexto-no-ailinguo)
- [Diagrama UML](#diagrama-uml)
- [Participantes e Mapeamento para o Código](#participantes-e-mapeamento-para-o-código)
- [Desenvolvimento e Implementação](#desenvolvimento-e-implementação)
  - [Singleton gerenciado por framework (Spring)](#singleton-gerenciado-por-framework-spring)
  - [Exemplos representativos](#exemplos-representativos)
- [Como usar (escopos de bean)](#como-usar-escopos-de-bean)
- [Testes](#testes)
- [Vantagens e Desvantagens](#vantagens-e-desvantagens)
- [Conclusão](#conclusão)
- [Referências Bibliográficas](#referências-bibliográficas)
- [Histórico de Versões](#histórico-de-versões)

---

## Introdução

O padrão criacional Singleton garante que uma classe tenha apenas uma instância e fornece um ponto global de acesso a ela. Em aplicações modernas Java com Spring, o Singleton costuma ser aplicado de forma declarativa: o contêiner IoC mantém um único objeto por tipo de bean com escopo `singleton` (padrão), evitando a necessidade de gerenciamento manual com `static` e `getInstance()`.

---

## Objetivo

- Garantir uma única instância por tipo de componente (quando apropriado) para economia de recursos e consistência.
- Centralizar configurações e dependências em um único ponto.
- Simplificar o ciclo de vida, delegando-o ao contêiner IoC (Spring).

---

## Contexto no AILinguo

No backend do AILinguo, serviços, configurações e fábricas de componentes são registrados como beans Spring. Por padrão, esses beans têm escopo `singleton`, isto é, uma instância por ApplicationContext. Exemplos incluem serviços de domínio (como `OpenAIService`) e a fábrica do Adapter de LLM (`LLMConfiguration`), que expõe um `LLMProvider` único para todo o aplicativo.

---

## Diagrama UML

<div align="center">
  Figura 1 (Singleton): Modelagem UML do padrão Singleton com gerenciamento por contêiner
  <br>
  <em>Client</em> → <strong>Spring Container</strong> (IoC) → <strong>Bean X</strong> (escopo: singleton)
  <br>
  <br>
  ** >>>>>>>>>>>>>>>>>>>>>>>>> FALTA O UML <<<<<<<<<<<<<<<<<<<<<<<**
  <br>
</div>

Observação: Diferentemente do Singleton “clássico” (com construtor privado + `getInstance()` estático), o AILinguo utiliza o Singleton gerenciado pelo Spring, que provê instância única por tipo de bean.

---

## Participantes e Mapeamento para o Código

- Contêiner IoC (Spring): responsável por criar e manter a única instância de cada bean `singleton`.
- Beans com escopo `singleton` (representativos):
  - `OpenAIService` → `backend-java/src/main/java/com/ailinguo/service/OpenAIService.java` (anotado com `@Service`).
  - `LLMConfiguration` → `backend-java/src/main/java/com/ailinguo/llm/LLMConfiguration.java` (anotado com `@Configuration`), que expõe o bean `LLMProvider` via `@Bean` – também `singleton` por padrão.
  - Outros serviços anotados com `@Service` (e.g., `TutorService`, `VocabularyService`, etc.) também são `singleton`, salvo mudança explícita de escopo.

---

## Desenvolvimento e Implementação

### Singleton gerenciado por framework (Spring)

No Spring, o escopo padrão de beans é `singleton`. Isso significa que, para uma aplicação, haverá uma instância única de cada bean mantida pelo ApplicationContext. Não é necessário escrever código com `getInstance()`; basta anotar classes com `@Service`, `@Component`, `@Configuration`, ou expor métodos `@Bean`.



## Bibliografia

> FREEMAN, Eric et al. Use A Cabeça Padrões e Projetos. Rio de Janeiro: Alta Books, 2007.

> GAMMA, E. et al. Padrões de projeto: soluções reutilizáveis de software orientado a objetos. Porto Alegre: Bookman, 2006.

> Refactoring.Guru – Singleton: https://refactoring.guru/pt-br/design-patterns/singleton

> Spring Framework Docs – Bean Scopes: https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html

---

## Histórico de Versões

| Versão | Descrição | Autor(es) | Data de Produção | Revisor(es) | Data de Revisão | Incremento do Revisor |
| :----: | --------- | --------- | :--------------: | ----------- | :-------------: | :-------------------: |
| `1.0` | Visão geral do Singleton no AILinguo: IoC do Spring, mapeamento de beans e contexto | [Leonardo de Melo Lima](https://github.com/leozinlima) | 23/10/2025 | | | |