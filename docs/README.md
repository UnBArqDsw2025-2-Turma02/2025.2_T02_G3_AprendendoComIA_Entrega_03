# AprendendoComIA

**Código da Disciplina**: FGA0208<br>
**Número do Grupo**: 03<br>
**Entrega**: 03<br>

## Alunos

| Foto | Matrícula | Nome |
|------|-----------|------|
| <img src="https://avatars.githubusercontent.com/u/170873899?v=4" height="70"> | 22/2037595 | [Arthur Carvalho Leite](https://github.com/arthurlleite) |
| <img src="https://avatars.githubusercontent.com/u/138714054?v=4" height="70"> | 18/0100271 | [Emivalto Da Costa Tavares Junior](https://github.com/EmivaltoJrr) |
| <img src="https://avatars.githubusercontent.com/u/62055315?v=4" height="70"> | 20/2046102 | [Felipe das Neves Freire](https://github.com/FelipeFreire-gf) |
| <img src="https://avatars.githubusercontent.com/u/116119327?v=4" height="70"> | 22/2037610 | [Gabriel Lima](https://github.com/gabriel-lima258) |
| <img src="https://avatars.githubusercontent.com/u/105813929?v=4" height="70"> | 22/2037700 | [Leonardo de Melo Lima](https://github.com/leozinlima) |
| <img src="https://avatars.githubusercontent.com/u/99836497?v=4" height="70"> | 22/2022144 | [Luiz Henrique Soares ](https://github.com/luizh-gsoares) |
| <img src="https://avatars.githubusercontent.com/u/152661076?v=4" height="70"> | 23/102685 |[Letícia Monteiro ](https://github.com/leticiamonteiroo) |
| <img src="https://avatars.githubusercontent.com/u/164573233?v=4" height="70"> | 21/1062240 |[Mateus Bastos](https://github.com/MateuSansete) |
| <img src="https://avatars.githubusercontent.com/u/52254091?v=4" height="70"> | 21/2005444 | [Pedro Fonseca Cruz](https://github.com/pfc15) |
| <img src="https://avatars.githubusercontent.com/u/106821260?v=4" height="70"> | 21/1062473 | [Samuel Afonso da Silva Santos](https://github.com/SamuelAfonso) |
| <img src="https://avatars.githubusercontent.com/u/118318004?v=4" height="70"> | 18/0132466 | [Vitor Bessa](https://github.com/Bessazs) |

## Sobre

O aprendizado de inglês é uma das habilidades mais valorizadas globalmente, tanto no contexto acadêmico quanto profissional. De acordo com o relatório da [EF English Proficiency Index (2023)](https://www.ef.com.br/epi/), o Brasil ocupa a 58ª posição entre 113 países avaliados, sendo classificado com **nível baixo de proficiência**. Esse dado reforça a necessidade de soluções inovadoras que promovam maior engajamento e eficácia no processo de aprendizagem.  

Nos últimos anos, o uso de **Inteligência Artificial na educação (AI in Education)** tem ganhado destaque. Relatórios como o da [UNESCO (2022)](https://unesdoc.unesco.org/ark:/48223/pf0000376709) ressaltam que a IA pode personalizar o ritmo de aprendizado, identificar dificuldades específicas e oferecer feedback imediato, ampliando as oportunidades de acesso à educação de qualidade.  

Além disso, estudos sobre **gamificação aplicada ao ensino** mostram que estratégias baseadas em recompensas, rankings e desafios aumentam a motivação e a retenção de conhecimento. Segundo [Deterding et al. (2011)](https://dl.acm.org/doi/10.1145/2181037.2181040), a gamificação transforma atividades tradicionais em experiências mais atrativas e imersivas, com impacto positivo no engajamento dos estudantes.  

Diversos aplicativos já exploram esse potencial. O [Duolingo](https://www.duolingo.com/press), por exemplo, popularizou o aprendizado de idiomas com forte uso de gamificação, alcançando mais de 500 milhões de usuários. O [ELSA Speak](https://elsaspeak.com/), por outro lado, foca no aperfeiçoamento da pronúncia com tecnologia de reconhecimento de voz e feedback personalizado. Apesar disso, ainda há uma lacuna na integração entre **conteúdo estruturado, gamificação e prática de conversação em tempo real**.  

Nesse contexto, o projeto **Aprendendo com IA** busca preencher essa lacuna, oferecendo uma plataforma que combina **gamificação**, **conteúdo adaptativo** e um **tutor de conversação inteligente**, tornando o aprendizado de inglês mais **personalizado, acessível e motivador**.

## Screenshots da Terceira Entrega

### UML Adapter

![componentes](https://raw.githubusercontent.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_Entrega_03/refs/heads/main/docs/assets/adapterUML.png)

<center> Autor(a): <a href="https://github.com/gabriel-lima258" target = "_blank">Gabriel Lima</a>, <a href="https://github.com/FelipeFreire-gf" target = "_blank">Felipe das Neves</a>, <a href="https://github.com/MateuSansetef" target = "_blank">Mateus Bastos</a> e <a href="https://github.com/bessazs" target = "_blank">Vitor Bessa</a></center>
    
### UML Singleton

![atividades](https://raw.githubusercontent.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_Entrega_03/refs/heads/main/docs/assets/gofcodes/singleton.jpeg)

<center> Autor(a): <a href="https://github.com/gabriel-lima258" target = "_blank">Gabriel Lima</a>, <a href="https://github.com/FelipeFreire-gf" target = "_blank">Felipe das Neves</a>, <a href="https://github.com/MateuSansetef" target = "_blank">Mateus Bastos</a> e <a href="https://github.com/bessazs" target = "_blank">Vitor Bessa</a></center>


## Há algo a ser executado?

(x) SIM

( ) NÃO

Obs.: O projeto está no mesmo repositório: https://github.com/UnBArqDsw2025-2-Turma02/2025.2_T02_G3_AprendendoComIA_DEV2

## Início Rápido

### Pré-requisitos

- Docker e Docker Compose
- OU Java 21+ e Node.js 18+

### Executar com Docker (Recomendado)

```bash
# Clonar repositório
git clone <repo-url>
cd 2025.2_T02_G3_AprendendoComIA_DEV

# Iniciar todos os serviços
docker-compose -f docker-compose-new.yml up --build

# Acessar:
# - Frontend: http://localhost:3000
# - Backend:  http://localhost:8080
```

### Executar Localmente

#### Backend

```bash
cd backend-java
mvn clean install
mvn spring-boot:run
```

#### Frontend

```bash
cd frontend-react
npm install
npm run dev
```

---

## Estrutura do Projeto

```
.
├── backend-java/          # Backend Spring Boot
│   ├── src/main/java/
│   │   └── com/ailinguo/
│   │       ├── config/
│   │       ├── controller/
│   │       ├── dto/
│   │       ├── model/
│   │       ├── repository/
│   │       └── service/
│   └── pom.xml
│
├── frontend-react/        # Frontend React + Vite
│   ├── src/
│   │   ├── components/
│   │   ├── context/
│   │   ├── pages/
│   │   └── App.jsx
│   └── package.json
│
├── docker-compose-new.yml    # Docker Compose
├── BUSINESS_RULES.md         # Regras de negócio
├── MIGRATION_GUIDE.md        # Guia de migração
└── NEW_PROJECT_README.md     # Docs detalhadas
```

---

## Documentação

- **[NEW_PROJECT_README.md](./NEW_PROJECT_README.md)** - Documentação completa
- **[BUSINESS_RULES.md](./BUSINESS_RULES.md)** - Regras de negócio detalhadas
- **[MIGRATION_GUIDE.md](./MIGRATION_GUIDE.md)** - Guia de migração Next.js → Java/React
- **[backend-java/README.md](./backend-java/README.md)** - Docs do backend
- **[frontend-react/README.md](./frontend-react/README.md)** - Docs do frontend

---

## Funcionalidades

### Autenticação
- Registro e login de usuários
- JWT em cookie HTTP-only
- Níveis CEFR (A1-C1)

### Tutor IA
- Chat interativo com IA
- Correções gramaticais em tempo real
- Exercícios personalizados
- Adaptação ao nível do aluno

### Vocabulário (SRS)
- Sistema de repetição espaçada
- Algoritmo de revisão inteligente
- Cartões por nível CEFR

### Gamificação
- Ranking de usuários
- Sistema de XP e níveis
- Metas semanais
- Grupos/Times
- Streaks diários

### Exercícios
- Quizzes de gramática
- Feedback imediato
- Sistema de pontuação

---

## Segurança

- JWT com tokens HTTP-only
- Senhas com BCrypt
- Spring Security
- CORS configurável
- Validação de entrada

---

## API Endpoints

### Autenticação
```
POST   /api/auth/register
POST   /api/auth/login
GET    /api/auth/me
POST   /api/auth/logout
```

### Tutor
```
POST   /api/tutor
```

### Vocabulário
```
GET    /api/vocabulary/due
POST   /api/vocabulary/review
```

### Gamificação
```
GET    /api/gamification/leaderboard
GET    /api/gamification/goals
GET    /api/gamification/groups
POST   /api/gamification/groups
```

### Tarefas
```
GET    /api/tasks/quiz
```

---

## Informações Complementares 

Para executar a documentação em ambiente local siga os seguintes passos:

### Instale o docsify

Execute o comando:

```shell
npm i docsify-cli -g
```

### Executando localmente

Para iniciar o site localmente, utilize o comando::

```shell
docsify serve ./docs
```



