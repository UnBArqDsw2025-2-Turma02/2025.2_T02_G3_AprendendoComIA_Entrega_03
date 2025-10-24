
# Facade (Padrão Estrutural) – Fachada Única no AILinguo

---
## Sumário
- [Introdução](#introdução)
- [Objetivo](#objetivo)
- [Implementação no Projeto AILinguo](#implementação-no-projeto-ailinguo)
- [Diagrama UML](#diagrama-uml)
  - [Figura e Crédito](#figura-e-crédito)
  - [Figuras (RAW) e Explicações](#figuras-raw-e-explicações)
- [Participantes e Mapeamento para o Código](#participantes-e-mapeamento-para-o-código)
- [Benefícios e Considerações](#benefícios-e-considerações)
- [Vídeo de Apresentação](#vídeo-de-apresentação)
- [Anexos](#anexos)
- [Referências Bibliográficas](#referências-bibliográficas)
- [Histórico de Versões](#histórico-de-versões)

---

## Introdução

Segundo Eric Freeman, no livro **“Use a Cabeça! Padrões e Projetos”**, o padrão **Facade** fornece uma **interface unificada e simplificada** para um conjunto de interfaces em um subsistema. A Facade atua como uma “fachada” que **esconde a complexidade interna** e **reduz o acoplamento** entre clientes (ex.: controllers) e serviços.

## Objetivo

- Expor **um único ponto de entrada** para fluxos funcionais do sistema.
- **Orquestrar** serviços internos sem que o cliente precise conhecê-los diretamente.
- **Simplificar** controllers e **aumentar a coesão** de regras de alto nível.

---

## Implementação no Projeto AILinguo

No AILinguo, adotamos **uma Facade única**: `AILinguoFacade`, que implementa a interface `IAILinguoFacade`.  
Ela orquestra os seguintes subsistemas:

1. **Autenticação** — `AuthService` (registro, login, logout, usuário autenticado)
2. **Perfil/Preferências** — `UserSettingsService` (perfil, preferências, troca de senha)
3. **Aprendizado** — `TaskService`, `VocabularyController*`, `ExerciseController*`  
   > *Observação:* idealmente os *controllers* seriam refatorados para `VocabularyService` e `ExerciseService`.
4. **Progresso/Gamificação** — `DashboardService`, `GamificationService` (resumos, histórico, conquistas)

Controllers (`AuthController`, `UserProfileController`, `TaskController`, `ProgressController`) **dependem primariamente da Facade**, assim ficam focados em HTTP ↔ DTO, delegando a lógica de negócio à `AILinguoFacade`.

> Commits de referência (exemplo):  
> <https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2/commit/47c42d2203c5f6d595257934d04e602a7994d19b#diff-76f9c77ea2d3ec797247eebaa6c3b90117cdb99caafa38c8e57d857e601bed3e>

---

## Diagrama UML

### Figura e Crédito

> **Fonte/Autoria do UML:** [Arthur Carvalho Leite](https://github.com/arthurlleite).
---
# Fluxo de como a Tarefa funciona

![UML Fluxo de como a Tarefa funciona](../assets/img.facade/Fluxo%20de%20como%20a%20Tarefa%20funciona%20entrada%20e%20saída.png)

---

# Classes explicada do facade

![UML Classes explicada do facade](../assets/img.facade/uml%20classes%20explicada%20do%20facade.png)

---
# Fluxo do usuário facade

![UML com o fluxo do usuário facade](../assets/img.facade/Uml%20com%20o%20fluxo%20do%20usuário%20facade.png)

---

[Drive](https://drive.google.com/drive/folders/1a6CunBsFcQG4bItTI4N0fUOloQrCj4aP?usp=drive_link)

### Figuras (RAW) e Explicações

> **Nota:** URLs RAW do GitHub evitam que as imagens quebrem em _preview_ e no GitHub Pages.

#### Figura 1 — Fluxo de como a tarefa funciona (entrada → processamento → saída)

<img
  width="1000"
  alt="UML — Fluxo de como a Tarefa funciona (entrada e saída)"
  src="https://raw.githubusercontent.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_Entrega_03/main/docs/assets/img.facade/Fluxo%20de%20como%20a%20Tarefa%20funciona%20entrada%20e%20sa%C3%ADda.png"
/>

**Explicação:** o controller chama a **`AILinguoFacade`**, que orquestra `TaskService` (avaliação/submissão), atualiza progresso em `DashboardService/GamificationService` e retorna um **resultado unificado** ao cliente (HTTP/DTO). O cliente não conversa diretamente com cada serviço — somente com a **Fachada**.

---

#### Figura 2 — Classes explicadas do Facade (visão estática)

<img
  width="1000"
  alt="UML — Classes explicadas do Facade"
  src="https://raw.githubusercontent.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_Entrega_03/main/docs/assets/img.facade/uml%20classes%20explicada%20do%20facade.png"
/>

**Explicação:** `IAILinguoFacade` define a **interface pública**; `AILinguoFacade` a **implementa** e **compõe/usa** os serviços: `AuthService`, `UserSettingsService`, `TaskService`, `DashboardService`, `GamificationService` e (por enquanto) `VocabularyController`/`ExerciseController`. Os controllers (`Auth`, `UserProfile`, `Task`, `Progress`) **dependem de `IAILinguoFacade`** (baixo acoplamento).

---

#### Figura 3 — Fluxo do usuário pela Facade

<img
  width="1000"
  alt="UML — Fluxo do usuário pela Facade"
  src="https://raw.githubusercontent.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_Entrega_03/main/docs/assets/img.facade/Uml%20com%20o%20fluxo%20do%20usu%C3%A1rio%20facade.png"
/>

**Explicação:** o usuário executa ações (registrar/login, iniciar lição, enviar exercício). Cada ação **passa pela Facade**, que orquestra os serviços correspondentes e devolve **respostas agregadas** (ex.: token + resumo do usuário; _dashboard snapshot_; resultado da submissão).

---

## Participantes e Mapeamento para o Código

## Benefícios e Considerações

- **`IAILinguoFacade`** → Interface pública que define as operações expostas.
- **`AILinguoFacade`** → Implementação concreta; injeta e orquestra os serviços.
- **Serviços** → `AuthService`, `UserSettingsService`, `TaskService`, `DashboardService`, `GamificationService` e, por ora, `VocabularyController`, `ExerciseController`.
- **Controllers** → dependem de `IAILinguoFacade` para executar os fluxos.

---

## Benefícios e Considerações

### Benefícios
- **Simplificação**: controllers interagem com uma **única interface** coesa.
- **Desacoplamento**: mudanças internas impactam a Facade, **não** os clientes.
- **Orquestração central**: facilita visualizar e manter fluxos de negócio que envolvem múltiplos serviços.
- **Testabilidade**: controllers podem ser testados mockando `IAILinguoFacade`.

### Considerações
- **Risco de “God Class”**: uma única fachada pode ficar grande demais. Quando notar perda de coesão, **quebre por domínio**.
- **Acoplamento interno**: gerencie bem a dependência entre serviços dentro da Facade (SRP, limites claros).
- **Granularidade**: avalie fachadas específicas (Account/Learning/Progress) se a interface crescer.

---

## Vídeo de Apresentação

> **Link direto (SharePoint/Stream)** — pode exigir login institucional da UnB:  
> https://unbbr-my.sharepoint.com/personal/231026859_aluno_unb_br/_layouts/15/stream.aspx?id=%2Fpersonal%2F231026859%5Faluno%5Funb%5Fbr%2FDocuments%2FGrava%C3%A7%C3%B5es%2FReuni%C3%A3o%20com%20Leticia%20Da%20Silva%20Monteiro%2D20251024%5F095116%2DGrava%C3%A7%C3%A3o%20de%20Reuni%C3%A3o%2Emp4&referrer=StreamWebApp%2EWeb&referrerScenario=AddressBarCopied%2Eview%2E416c77bb%2Dbaea%2D44b8%2Db830%2Dfff32e90f23c&isDarkMode=true

**Botão clicável:**  
[▶️ Assistir ao vídeo (Facade)](https://unbbr-my.sharepoint.com/personal/231026859_aluno_unb_br/_layouts/15/stream.aspx?id=%2Fpersonal%2F231026859%5Faluno%5Funb%5Fbr%2FDocuments%2FGrava%C3%A7%C3%B5es%2FReuni%C3%A3o%20com%20Leticia%20Da%20Silva%20Monteiro%2D20251024%5F095116%2DGrava%C3%A7%C3%A3o%20de%20Reuni%C3%A3o%2Emp4&referrer=StreamWebApp%2EWeb&referrerScenario=AddressBarCopied%2Eview%2E416c77bb%2Dbaea%2D44b8%2Db830%2Dfff32e90f23c&isDarkMode=true)

> *Observação:* Players externos (ex.: `<iframe>`) podem ser bloqueados pelo SharePoint/Stream. Recomendamos usar o link acima.
---

## Anexos

- **Repositório de Documentação**: <https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_Entrega_03>  
- **Repositório de Desenvolvimento**: <https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2>  
- **Materiais de referência**: [Pasta no Drive](https://drive.google.com/drive/folders/1a6CunBsFcQG4bItTI4N0fUOloQrCj4aP?usp=drive_link)

---

## Referências Bibliográficas

- FREEMAN, Eric et al. **Use A Cabeça! Padrões e Projetos.** Rio de Janeiro: Alta Books, 2007.  
- GAMMA, E. et al. **Padrões de Projeto: soluções reutilizáveis de software orientado a objetos.** Porto Alegre: Bookman, 2006.

---

## Histórico de Versões

| Versão | Descrição | Autor(es) | Data de Produção | Revisor(es) | Data de Revisão | Incremento do Revisor |
| :----: | --------- | --------- | :--------------: | ----------- | :-------------: | :-------------------: |
| `1.0` | Documentação e formatação da aba | [Samuel Afonso](https://github.com/SamuelAfonso) | 22/10/2025 | | | |
| `1.1` | Adição da implementação específica (AILinguoFacade única), diagrama UML atualizado e seção de Benefícios/Considerações. |  [Emivalto Da Costa Tavares Junior](https://github.com/EmivaltoJrr) | 23/10/2025 | | | |
| `1.2` | Adição do diagrama UML | [Emivalto Da Costa Tavares Junior](https://github.com/EmivaltoJrr)  | 23/10/2025 | | | |

