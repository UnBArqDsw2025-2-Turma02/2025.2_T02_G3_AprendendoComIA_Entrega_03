# Facade

---
## Sumário
- [Introdução](#introdução)
- [Implementação no Projeto AILinguo](#implementação-no-projeto-ailinguo)
- [Diagrama UML](#diagrama-uml)
- [Benefícios e Considerações](#benefícios-e-considerações)
- [Referências Bibliográficas](#referencias-bibliográficas)
- [Histórico de Versões](#histórico-de-versões)

---

## Introdução

Segundo Eric Freeman, no livro __"Use a Cabeça! Padrões E Projetos"__, o padrão __Facade__ é descrito como uma entidade que fornece uma interface unificada e simplificada para um conjunto de interfaces em um subsistema, facilitando o uso do sistema ao esconder sua complexidade. <br>Basicamente, o __Facade__ atua como uma fachada que oferece uma interface mais fácil de usar permitindo que os clientes interajam com o sistema através de um ponto único de acesso, sem precisar conhecer detalhes internos ou múltiplas interfaces complexas.

O objetivo principal é reduzir o acoplamento entre os clientes (como controllers) e os subsistemas internos, promovendo uma arquitetura mais organizada e de fácil manutenção.

---

## Implementação no Projeto AILinguo

No projeto AILinguo, optamos por implementar uma **Facade única**, chamada `AILinguoFacade`, que serve como a principal "porta de entrada" para as funcionalidades essenciais da aplicação. Esta abordagem encapsula a complexidade dos seguintes subsistemas:

1.  **Autenticação (`AuthService`)**: Gerencia registro, login, logout e informações do usuário autenticado.
2.  **Perfil e Configurações (`UserSettingsService`)**: Controla dados do perfil, preferências e segurança da conta.
3.  **Conteúdo de Aprendizado (`TaskService`, `VocabularyController`, `ExerciseController`)**: Oferece acesso a tarefas, vocabulário e exercícios, incluindo recomendações e submissão de respostas. (Idealmente, `VocabularyController` e `ExerciseController` seriam refatorados para `VocabularyService` e `ExerciseService` para melhor separação de camadas).
4.  **Progresso e Gamificação (`DashboardService`, `GamificationService`)**: Agrega estatísticas de progresso, ranking, metas e conquistas. (`XpService` é usado internamente por esses serviços).

A `AILinguoFacade` (e sua interface opcional `IAILinguoFacade`) expõe métodos simplificados que orquestram chamadas aos serviços subjacentes. Por exemplo, `getUserOverallProgressSummary(userId)` chama `DashboardService`, `GamificationService` e `TaskService` para compilar um resumo completo para o cliente (como um controller).

Os controllers da aplicação (`AuthController`, `UserProfileController`, `TaskController`, etc.) agora dependem primariamente da `AILinguoFacade`, reduzindo suas dependências diretas e simplificando sua lógica interna. Isso torna os controllers mais focados em receber requisições HTTP e retornar respostas, delegando a lógica de negócio para a Facade.

---
## Diagrama UML


---

## Benefícios e Considerações

# Benefícios:

- Simplificação: Reduz drasticamente a complexidade para os clientes (controllers), que interagem com uma única interface coesa para a maioria das operações.

- Desacoplamento Forte: Isola os controllers das implementações específicas e da quantidade de serviços. Mudanças internas nos serviços (ex: adicionar um StreakService) impactam primariamente a Facade, não os controllers.

- Ponto Central de Orquestração: Facilita a visualização e manutenção da lógica de fluxo de alto nível que envolve múltiplos serviços.

- Melhora a Testabilidade dos Controllers: Controllers podem ser testados mais facilmente mockando apenas a Facade.

# Considerações:

- Potencial "God Class": A AILinguoFacade única pode se tornar muito grande e complexa, centralizando responsabilidades demais e dificultando a manutenção dentro da própria Facade.

- Risco de Acoplamento Interno: Embora desacople os clientes, pode aumentar o acoplamento entre os serviços dentro da Facade se não for bem gerenciada.

- Granularidade: Se a Facade ficar muito grande, pode ser um sinal de que a divisão em Facades menores e mais focadas (por domínio, como UserAccountFacade, LearningActivityFacade, ProgressReportingFacade) seria mais apropriada para manter a coesão e o SRP.

---



## Referências Bibliográficas

- FREEMAN, Eric et al. __Use A Cabeça Padrões E Projetos.__ Rio de Janeiro: Alta Books, 2007.
- GAMMA, E. et al. __Padrões de projeto : soluções reutilizáveis de software orientado a objetos.__ Porto Alegre: Bookman, 2006.

---

## Histórico de Versões

| Versão | Descrição | Autor(es) | Data de Produção | Revisor(es) | Data de Revisão | Incremento do Revisor |
| :----: | --------- | --------- | :--------------: | ----------- | :-------------: | :-------------------: |
| `1.0` | Documentação e formatação da aba | [Samuel Afonso](https://github.com/SamuelAfonso) | 22/10/2025 | | | |
| `1.1` | Adição da implementação específica (AILinguoFacade única), diagrama UML atualizado e seção de Benefícios/Considerações. |  [Emivalto Da Costa Tavares Junior](https://github.com/EmivaltoJrr) | 23/10/2025 | | | |




