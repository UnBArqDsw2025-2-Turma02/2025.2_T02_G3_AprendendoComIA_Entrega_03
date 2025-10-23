# Ata 01

---

## Identificação

| Data       | Início | Término | Local           |
| ---------- | ------ | ------- | --------------- |
| 22/10/2025 | 19:00  | 19:45   | Microsoft Teams |

**Redator**: Letícia da Silva Monteiro  

**Participantes**:

* ✅  Arthur Carvalho Leite  
* ✅  Emivalto da Costa Tavares Junior  
* ✅  Luiz Henrique Guimarães Soares  
* ✅  Samuel Afonso da Silva Santos
* ✅  Letícia da Silva Monteiro  

| Legenda | Presente | Ausente |
| ------- | -------- | ------- |
|         | ✅        | ❌       |

---

## Pauta

* Definição das aplicações práticas dos padrões de projeto (Prototype, Facade e Mediator) na plataforma “Aprendendo com IA”.
* Organização das tarefas entre os membros do grupo (OML, código e documentação).
* Planejamento da documentação e divisão final das duplas.

---

## Assuntos Tratados

Durante a reunião, o grupo discutiu os três tipos de padrões de projeto (criacional, estrutural e comportamental) que serão aplicados no sistema.

### **Padrão Criacional — Prototype**
- Proposta 1: clonar estruturas de exercícios e respostas para evitar recriações complexas.
- Proposta 2: uma aplicação no **perfil de usuário**, onde haveria um perfil base (modelo padrão) e novos perfis seriam clones desse modelo, com possibilidade de pequenas edições (nome, foto etc.).
- O grupo decidiu adotar a 2, por ser mais simples e consistente com o funcionamento da plataforma.

### **Padrão Estrutural — Facade**
- O grupo discutiu o uso do **LearningFacade**, que centralizaria a comunicação entre subsistemas internos (como ChatService, VocabularyService, GamificationService e UserProgressService).  
- Decisão: utilizar o **LearningFacade** para melhorar a organização interna e facilitar a manutenção do código.

### **Padrão Comportamental — Mediator**
- Foi levantada a possibilidade de aplicar o **Mediator** em um sistema de **notificações** (por exemplo, quando um aluno termina uma atividade e o sistema atualiza o progresso e o ranking).
- Outra ideia seria que o Mediator coordenasse a comunicação entre os serviços de **Gamificação**, **Progresso** e **Usuário**, garantindo atualizações simultâneas (XP, tempo de estudo, status das tarefas).
- Foi acordado que Letícia e Luiz desenvolveriam o **UML e código do Mediator**, enquanto Emivalto e Samuel ficariam responsáveis pelo código do **Facade**.

### **Documentação**
- Arthur reforçou que o grupo deve manter agilidade na criação do UML e código para permitir que a documentação avance sem atrasos.
- Samuel se voluntariou para iniciar a estrutura básica das páginas de documentação no GitHub Pages, incluindo introduções e histórico de versionamento.
- Letícia ressaltou que, com a estrutura pronta, os responsáveis por cada parte (UML e código) poderão apenas inserir os conteúdos finais, agilizando a finalização.

---

<div align="center">

## Gravação da Reunião

**Reunião 01**

<iframe width="560" height="315" src="https://www.youtube.com/embed/Bcy-081nDhA?si=yeWO6Ei8JWImS03h" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<p>Autor: <a href="https://github.com/LeticiaMonteiroo">Letícia Monteiro</a></p>
</div>

---

## Histórico de Versões

| Versão | Descrição                                                        | Autor(es)                                              | Data de Produção | Revisor(es) | Data de Revisão | Incremento do Revisor |
| :----: | ---------------------------------------------------------------- | ------------------------------------------------------ | :--------------: | ----------- | :-------------: | :-------------------: |
|  `1.0` | Criação da Ata | [Letícia Monteiro](https://github.com/LeticiaMonteiroo)  |    23/10/2025    |             |                 |                       |
