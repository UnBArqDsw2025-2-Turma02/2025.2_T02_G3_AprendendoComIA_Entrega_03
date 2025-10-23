# Ata 02

---

## Identificação

| Data       | Início | Término | Local           |
| ---------- | ------ | ------- | --------------- |
| 22/10/2025 | 20:00  | 21:00   | Microsoft Teams |

**Redator**: Letícia da Silva Monteiro  

**Participantes**:

* ✅  Letícia Monteiro  
* ✅  Luiz Henrique Guimarães Soares  

| Legenda | Presente | Ausente |
| ------- | -------- | ------- |
|         | ✅        | ❌       |

---

## Pauta

* Análise dos padrões de projeto **Mediator** e **Observer** para o sistema “Aprendendo com IA”.  
* Discussão sobre a viabilidade de implementação e substituição de padrões conforme o contexto do projeto.  
* Discursão sobre a representações em UML e documentação.  

---

## Assuntos Tratados

A reunião teve uma discussão sobre os padrões de projeto que o grupo está desenvolvendo. Foram analisadas as possibilidades de aplicação dos padrões **Mediator** ou **Observer** no contexto da plataforma de ensino **Aprendendo com IA**, com foco na clareza de implementação e adequação ao sistema.

### **1. Análise do Padrão Mediator**
- Luiz relatou dificuldades para compreender e aplicar o **Mediator** dentro do projeto, destacando a complexidade de integração entre componentes e a falta de clareza sobre os fluxos internos de comunicação da plataforma.  
- Letícia observou que o padrão é conceitualmente simples, mas difícil de aplicar sem pleno entendimento das interações entre classes e métodos do sistema.  
- Decisão inicial: reconsiderar o uso do **Mediator**, avaliando alternativas mais viáveis.

---

### **2. Avaliação do Padrão Observer**
- Letícia pesquisou sobre o **Observer**, confirmando que ele também é um padrão **comportamental** e, portanto, poderia substituir o **Mediator** sem afetar a estrutura geral do projeto.  
- O padrão foi considerado **mais simples de implementar**, pois trabalha com a ideia de “observar” mudanças de estado em objetos e reagir a elas.  
- Aplicação sugerida:
  - O **aluno** é o objeto observado (subject).  
  - O **sistema de gamificação** e o **dashboard** são observadores que reagem às mudanças no progresso do aluno.  
  - Quando o aluno conclui uma atividade, o sistema atualiza automaticamente **pontuação, medalhas** e **ranking**.  
  - Também poderá enviar **notificações** ou **feedbacks em tempo real** ao usuário.  
- Luiz sugeriu que o Observer também possa monitorar a **inatividade do aluno**, enviando notificações caso o usuário não realize atividades por um determinado tempo — comportamento semelhante ao **Duolingo**.  

---

### **3. Ajustes Técnicos e Documentação** 
- Ambos concordaram em adotar o **Observer** como substituto do **Mediator**, por ser mais aplicável e condizente com o tempo de desenvolvimento disponível.  
- Letícia reforçou que a equipe deve registrar essa alteração na documentação, atualizando os diagramas UML e explicações teóricas.  
- Luiz ficou responsável por testar e estruturar o UML do **Observer** 

---

<div align="center">

## Gravação da Reunião

**Reunião 02**

<iframe width="560" height="315" src="https://www.youtube.com/embed/qJMkPi1N4uM?si=enDVvo7N70LllYUN" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<p>Autor: <a href="https://github.com/LeticiaMonteiroo">Letícia Monteiro</a></p>
</div>

---

## Histórico de Versões

| Versão | Descrição                                                        | Autor(es)                                              | Data de Produção | Revisor(es) | Data de Revisão | Incremento do Revisor |
| :----: | ---------------------------------------------------------------- | ------------------------------------------------------ | :--------------: | ----------- | :-------------: | :-------------------: |
|  `1.0` | Criação da Ata e registro da substituição do padrão Mediator pelo padrão Observer | [Letícia Monteiro](https://github.com/LeticiaMonteiroo)  |    23/10/2025    |             |                 |                       |
