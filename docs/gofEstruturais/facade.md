

# Facade (Padrão Estrutural) – Fachada Única no AILinguo

---
## Sumário
- [Introdução](#introdução)
- [Objetivo](#objetivo)
- [Implementação no Projeto AILinguo](#implementação-no-projeto-ailinguo)
- [Diagrama UML](#diagrama-uml)
  - [Figura e Crédito](#figura-e-crédito)
  - [Figuras (RAW) e Explicações](#figuras-raw-e-explicações)
- [Códigos (com explicação)](#códigos-com-explicação)
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

## Códigos (com explicação)

Abaixo estão os principais arquivos relacionados ao **Facade**, cada um com seu **código** e uma **explicação** do papel no padrão.

<details>
<summary><strong>backend-java/src/main/java/com/ailinguo/controller/AuthControllerIntegrationTest.java</strong> — <em>Teste de integração do fluxo de autenticação</em></summary>

```java

package com.ailinguo.controller;

import com.ailinguo.dto.UserDto;
import com.ailinguo.dto.auth.AuthResponse;
import com.ailinguo.dto.auth.LoginRequest;
import com.ailinguo.facade.IAILinguoFacade; // Importe a Facade
import com.ailinguo.model.User;
import com.ailinguo.repository.UserRepository;
import com.fasterxml.jackson.databind.ObjectMapper;
import jakarta.servlet.http.Cookie;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.MediaType;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.MvcResult;
import org.springframework.transaction.annotation.Transactional; // Para limpar o banco após cada teste, se necessário

import java.time.LocalDateTime;
import java.util.HashMap;
import java.util.Map;
import java.util.Optional;

// Importações estáticas para facilitar
import static org.hamcrest.Matchers.*;
import static org.junit.jupiter.api.Assertions.*;
import org.springframework.security.test.web.servlet.request.SecurityMockMvcRequestPostProcessors; // Para simular autenticação
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

@SpringBootTest // Carrega o contexto completo da aplicação Spring Boot
@AutoConfigureMockMvc // Configura o MockMvc para simular requisições HTTP
// @ActiveProfiles("test") // Opcional: Ativa um perfil de teste (ex: application-test.yml) se você tiver um
@Transactional // Opcional: Faz rollback das transações do banco após cada teste
class AuthControllerIntegrationTest {

    @Autowired
    private MockMvc mockMvc; // Ferramenta para simular requisições HTTP

    @Autowired
    private ObjectMapper objectMapper; // Para converter objetos Java <-> JSON

    @Autowired
    private UserRepository userRepository; // Acesso direto ao repositório para setup/cleanup

    @Autowired
    private PasswordEncoder passwordEncoder; // Para codificar senhas no setup

    // NÃO mockamos a Facade aqui para um teste de integração mais completo
    // @Autowired
    // private IAILinguoFacade aiLinguoFacade;

    // Limpa o repositório antes de cada teste para garantir isolamento
    @BeforeEach
    void setUpDatabase() {
        userRepository.deleteAll(); // Cuidado: Apaga todos os usuários! Use um banco de testes.
    }

    @Test
    void register_shouldReturnOkAndUserData_whenRequestIsValid() throws Exception {
        // Arrange: Prepara os dados da requisição de registro
        Map<String, Object> registrationRequest = new HashMap<>();
        registrationRequest.put("email", "register.test@example.com");
        registrationRequest.put("name", "Register User");
        registrationRequest.put("password", "password123");
        registrationRequest.put("cefrLevel", "A2");

        // Act & Assert: Simula a requisição POST para /api/auth/register
        mockMvc.perform(post("/api/auth/register")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(objectMapper.writeValueAsString(registrationRequest)))
                // Verifica o Status HTTP 200 OK
                .andExpect(status().isOk())
                // Verifica o conteúdo do JSON de resposta
                .andExpect(jsonPath("$.user", notNullValue()))
                .andExpect(jsonPath("$.user.name", is("Register User")))
                .andExpect(jsonPath("$.user.email", is("register.test@example.com")))
                .andExpect(jsonPath("$.user.cefrLevel", is("A2")))
                // Verifica se o cookie 'auth_token' foi setado
                .andExpect(cookie().exists("auth_token"))
                .andExpect(cookie().httpOnly("auth_token", true)) // Verifica HttpOnly
                .andExpect(cookie().path("auth_token", is("/"))); // Verifica o Path

        // Assert (Opcional): Verifica se o usuário foi realmente salvo no banco
        Optional<User> savedUser = userRepository.findByEmail("register.test@example.com");
        assertTrue(savedUser.isPresent(), "Usuário deveria ter sido salvo no banco de dados.");
        assertEquals("Register User", savedUser.get().getName());
    }

    @Test
    void register_shouldReturnBadRequest_whenEmailAlreadyExists() throws Exception {
        // Arrange: Cria um usuário existente no banco
        userRepository.save(User.builder()
                .email("existing@example.com")
                .name("Existing User")
                .password(passwordEncoder.encode("password123"))
                .cefrLevel(User.CefrLevel.A1)
                .createdAt(LocalDateTime.now())
                .build());

        // Prepara a requisição com o mesmo email
        Map<String, Object> registrationRequest = new HashMap<>();
        registrationRequest.put("email", "existing@example.com");
        registrationRequest.put("name", "New User");
        registrationRequest.put("password", "newpassword123");

        // Act & Assert: Espera um status 400 Bad Request
        mockMvc.perform(post("/api/auth/register")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(objectMapper.writeValueAsString(registrationRequest)))
                .andExpect(status().isBadRequest())
                .andExpect(jsonPath("$.error", containsString("User already exists"))); // Verifica a mensagem de erro
    }

    @Test
    void register_shouldReturnBadRequest_whenInputIsInvalid() throws Exception {
        // Arrange: Prepara dados inválidos (ex: email sem formato correto)
        Map<String, Object> invalidRequest = new HashMap<>();
        invalidRequest.put("email", "invalid-email");
        invalidRequest.put("name", "Test");
        invalidRequest.put("password", "short"); // Senha curta (se houver validação)

        // Act & Assert: Espera um 400 Bad Request
        mockMvc.perform(post("/api/auth/register")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(objectMapper.writeValueAsString(invalidRequest)))
                .andExpect(status().isBadRequest())
                .andExpect(jsonPath("$.error", notNullValue())); // Verifica se há uma mensagem de erro
    }


    @Test
    void login_shouldReturnOkAndUserData_whenCredentialsAreValid() throws Exception {
        // Arrange: Cria um usuário no banco para o teste de login
        String userEmail = "login.test@example.com";
        String userPassword = "password123";
        User user = userRepository.save(User.builder()
                .email(userEmail)
                .name("Login User")
                .password(passwordEncoder.encode(userPassword)) // Senha codificada!
                .cefrLevel(User.CefrLevel.B1)
                .createdAt(LocalDateTime.now())
                .build());

        LoginRequest loginRequest = new LoginRequest();
        loginRequest.setEmail(userEmail);
        loginRequest.setPassword(userPassword); // Senha em texto plano

        // Act & Assert: Simula a requisição POST para /api/auth/login
        mockMvc.perform(post("/api/auth/login")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(objectMapper.writeValueAsString(loginRequest)))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.user", notNullValue()))
                .andExpect(jsonPath("$.user.id", is(user.getId().toString()))) // Verifica o ID
                .andExpect(jsonPath("$.user.name", is("Login User")))
                .andExpect(jsonPath("$.user.email", is(userEmail)))
                .andExpect(cookie().exists("auth_token")); // Verifica o cookie
    }

    @Test
    void login_shouldReturnUnauthorized_whenPasswordIsInvalid() throws Exception {
        // Arrange: Cria um usuário
        String userEmail = "login.fail@example.com";
        userRepository.save(User.builder()
                .email(userEmail)
                .name("Login Fail User")
                .password(passwordEncoder.encode("correctPassword"))
                .cefrLevel(User.CefrLevel.A2)
                .createdAt(LocalDateTime.now())
                .build());

        LoginRequest loginRequest = new LoginRequest();
        loginRequest.setEmail(userEmail);
        loginRequest.setPassword("wrongPassword"); // Senha incorreta

        // Act & Assert: Espera um status 401 Unauthorized
        mockMvc.perform(post("/api/auth/login")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(objectMapper.writeValueAsString(loginRequest)))
                .andExpect(status().isUnauthorized()) // Status 401
                .andExpect(jsonPath("$.error", containsString("Invalid password")));
    }

    @Test
    void login_shouldReturnUnauthorized_whenUserNotFound() throws Exception {
        // Arrange: Prepara requisição para um usuário que não existe
        LoginRequest loginRequest = new LoginRequest();
        loginRequest.setEmail("nonexistent@example.com");
        loginRequest.setPassword("password123");

        // Act & Assert: Espera um status 401 Unauthorized
        mockMvc.perform(post("/api/auth/login")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(objectMapper.writeValueAsString(loginRequest)))
                .andExpect(status().isUnauthorized())
                .andExpect(jsonPath("$.error", containsString("User not found")));
    }

    @Test
    // @WithMockUser(username="2") // Alternativa mais simples se só precisar do ID como String
    void getCurrentUser_shouldReturnUserData_whenAuthenticated() throws Exception {
        // Arrange: Cria um usuário para simular a autenticação
        User user = userRepository.save(User.builder()
                .email("me@example.com")
                .name("Current User")
                .password(passwordEncoder.encode("password"))
                .cefrLevel(User.CefrLevel.B2)
                .createdAt(LocalDateTime.now())
                .build());

        // Cria um objeto Authentication simulado (necessário importar SecurityMockMvcRequestPostProcessors)
        // O 'principal' deve ser o ID do usuário como String, pois é isso que o JwtAuthenticationFilter coloca
        org.springframework.security.authentication.UsernamePasswordAuthenticationToken authToken =
                new org.springframework.security.authentication.UsernamePasswordAuthenticationToken(
                        user.getId().toString(), // Principal é o ID do usuário como String
                        null,
                        List.of() // Autoridades (pode deixar vazio se não usar roles)
                );

        // Act & Assert: Simula a requisição GET para /api/auth/me COM autenticação
        mockMvc.perform(get("/api/auth/me")
                        .with(SecurityMockMvcRequestPostProcessors.authentication(authToken))) // Adiciona a autenticação simulada à requisição
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.user", notNullValue()))
                .andExpect(jsonPath("$.user.id", is(user.getId().toString())))
                .andExpect(jsonPath("$.user.name", is("Current User")))
                .andExpect(jsonPath("$.user.email", is("me@example.com")));
    }

    @Test
    void getCurrentUser_shouldReturnEmptyUser_whenNotAuthenticated() throws Exception {
        // Act & Assert: Simula a requisição GET para /api/auth/me SEM autenticação
        mockMvc.perform(get("/api/auth/me"))
                .andExpect(status().isOk())
                // Espera um objeto 'user' presente, mas com campos nulos ou vazios (conforme sua lógica no controller)
                .andExpect(jsonPath("$.user", notNullValue()))
                .andExpect(jsonPath("$.user.id", nullValue()))
                .andExpect(jsonPath("$.user.name", nullValue()));
    }

    @Test
    void logout_shouldClearAuthCookie() throws Exception {
        // Arrange: Simula uma requisição com um cookie existente
        Cookie existingCookie = new Cookie("auth_token", "some-dummy-token-value");
        existingCookie.setPath("/");
        existingCookie.setHttpOnly(true);

        // Act & Assert: Simula a requisição POST para /api/auth/logout
        mockMvc.perform(post("/api/auth/logout")
                        .cookie(existingCookie)) // Envia o cookie na requisição
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.success", is(true)))
                // Verifica se o cookie 'auth_token' foi removido (Max-Age=0)
                .andExpect(cookie().exists("auth_token"))
                .andExpect(cookie().maxAge("auth_token", is(0)));
    }
}

```

**Explicação:** cobre cenários de **registro**, **login**, **/me** e **logout** contra a API HTTP, validando **status**, **payload** e **cookies** (auth_token). Teste de integração com contexto real do Spring.
</details>

---

<details>
<summary><strong>backend-java/src/main/java/com/ailinguo/facade/AILinguoFacadeTest.java</strong> — <em>Teste unitário da Facade</em></summary>

```java

package com.ailinguo.facade;

import com.ailinguo.dto.UserDto;
import com.ailinguo.dto.auth.AuthResponse;
import com.ailinguo.dto.auth.LoginRequest;
import com.ailinguo.service.*; // Importe todos os seus serviços
import com.ailinguo.controller.VocabularyController; // Temporário, idealmente usar VocabularyService
import com.ailinguo.controller.ExerciseController;   // Temporário, idealmente usar ExerciseService
import com.ailinguo.model.User; // Importe o modelo User se necessário para DTOs ou setup
import com.ailinguo.model.UserVocabularyProgress; // Importe se usado em retornos mockados
import jakarta.servlet.http.HttpServletResponse;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.http.ResponseEntity; // Necessário para mockar VocabularyController
import org.springframework.security.core.Authentication;


import java.util.Collections;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

// Importações estáticas para facilitar a leitura dos asserts e mocks
import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.ArgumentMatchers.eq; // Para verificações exatas de argumentos
import static org.mockito.Mockito.*;

// Habilita o uso das anotações do Mockito (@Mock, @InjectMocks) com JUnit 5
@ExtendWith(MockitoExtension.class)
class AILinguoFacadeTest {

    // --- Mocks para TODAS as dependências da AILinguoFacade ---
    @Mock private AuthService authService;
    @Mock private UserSettingsService userSettingsService;
    @Mock private TaskService taskService;
    @Mock private ExerciseController exerciseController; // Mock do controller (idealmente seria Service)
    @Mock private VocabularyController vocabularyController; // Mock do controller (idealmente seria Service)
    @Mock private DashboardService dashboardService;
    @Mock private GamificationService gamificationService;
    // XpService não é injetado diretamente na Facade neste exemplo

    // Mocks para objetos HttpServletResponse e Authentication
    @Mock private HttpServletResponse httpServletResponse;
    @Mock private Authentication authentication;

    // --- A classe que estamos testando ---
    // Injeta os mocks declarados acima na instância da Facade
    @InjectMocks
    private AILinguoFacade aiLinguoFacade;

    // Campos para guardar dados de teste reutilizáveis
    private Map<String, Object> registrationRequest;
    private LoginRequest loginRequest;
    private UserDto sampleUserDto;
    private AuthResponse sampleAuthResponse;
    private final Long sampleUserId = 1L;

    // Configura dados de teste comuns antes de cada teste
    @BeforeEach
    void setUp() {
        registrationRequest = new HashMap<>();
        registrationRequest.put("email", "test@example.com");
        registrationRequest.put("name", "Test User");
        registrationRequest.put("password", "password123");
        registrationRequest.put("cefrLevel", "A2");

        loginRequest = new LoginRequest();
        loginRequest.setEmail("test@example.com");
        loginRequest.setPassword("password123");

        sampleUserDto = UserDto.builder()
                .id(sampleUserId.toString()) // ID como String, conforme seu DTO
                .name("Test User")
                .email("test@example.com")
                .cefrLevel(User.CefrLevel.A2) // Adicionando nível para consistência
                .build();

        sampleAuthResponse = AuthResponse.builder().user(sampleUserDto).build();
    }

    // --- Testes de Autenticação ---

    @Test
    @DisplayName("registerUser: Deve chamar AuthService.register e retornar AuthResponse")
    void registerUser_shouldCallAuthServiceRegister_andReturnAuthResponse() {
        // Arrange
        when(authService.register(any(Map.class), any(HttpServletResponse.class)))
                .thenReturn(sampleAuthResponse);

        // Act
        AuthResponse actualResponse = aiLinguoFacade.registerUser(registrationRequest, httpServletResponse);

        // Assert
        assertNotNull(actualResponse, "A resposta não deveria ser nula.");
        assertEquals(sampleAuthResponse, actualResponse, "A resposta retornada deve ser a mesma que o mock retornou.");
        assertEquals("Test User", actualResponse.getUser().getName());

        verify(authService, times(1)).register(eq(registrationRequest), eq(httpServletResponse));
        verifyNoInteractions(userSettingsService, taskService, dashboardService, gamificationService);
        verifyNoMoreInteractions(authService);
    }

    @Test
    @DisplayName("registerUser: Deve propagar exceção se AuthService falhar")
    void registerUser_shouldPropagateException_whenAuthServiceThrowsException() {
        // Arrange
        String errorMessage = "Email already exists";
        when(authService.register(any(Map.class), any(HttpServletResponse.class)))
                .thenThrow(new RuntimeException(errorMessage));

        // Act & Assert
        RuntimeException exception = assertThrows(RuntimeException.class, () -> {
            aiLinguoFacade.registerUser(registrationRequest, httpServletResponse);
        });
        assertEquals(errorMessage, exception.getMessage());

        verify(authService, times(1)).register(any(Map.class), any(HttpServletResponse.class));
        verifyNoMoreInteractions(authService);
        verifyNoInteractions(userSettingsService, taskService);
    }

    @Test
    @DisplayName("loginUser: Deve chamar AuthService.login e retornar AuthResponse")
    void loginUser_shouldCallAuthServiceLogin_andReturnAuthResponse() {
        // Arrange
        when(authService.login(any(LoginRequest.class), any(HttpServletResponse.class)))
                .thenReturn(sampleAuthResponse);

        // Act
        AuthResponse result = aiLinguoFacade.loginUser(loginRequest, httpServletResponse);

        // Assert
        assertNotNull(result);
        assertEquals(sampleUserDto, result.getUser());
        verify(authService, times(1)).login(loginRequest, httpServletResponse);
        verifyNoMoreInteractions(authService);
    }

    @Test
    @DisplayName("getCurrentAuthenticatedUser: Deve chamar AuthService.getCurrentUser com ID correto")
    void getCurrentAuthenticatedUser_shouldCallAuthService() {
        // Arrange
        String userIdString = sampleUserId.toString();
        when(authentication.getPrincipal()).thenReturn(userIdString); // Simula um usuário autenticado
        when(authService.getCurrentUser(userIdString)).thenReturn(sampleUserDto);

        // Act
        UserDto result = aiLinguoFacade.getCurrentAuthenticatedUser(authentication);

        // Assert
        assertNotNull(result);
        assertEquals("Test User", result.getName());
        verify(authService, times(1)).getCurrentUser(userIdString);
    }

    @Test
    @DisplayName("getCurrentAuthenticatedUser: Deve retornar null se não autenticado")
    void getCurrentAuthenticatedUser_shouldReturnNull_whenNotAuthenticated() {
        // Arrange
        when(authentication.getPrincipal()).thenReturn(null); // Simula não autenticado
        when(authService.getCurrentUser(null)).thenReturn(null); // Configura o mock para retornar null

        // Act
        UserDto result = aiLinguoFacade.getCurrentAuthenticatedUser(authentication);

        // Assert
        assertNull(result);
        verify(authService, times(1)).getCurrentUser(null);
    }


    @Test
    @DisplayName("logoutUser: Deve chamar AuthService.logout")
    void logoutUser_shouldCallAuthServiceLogout() {
        // Arrange
        doNothing().when(authService).logout(any(HttpServletResponse.class)); // Configura mock para método void

        // Act
        aiLinguoFacade.logoutUser(httpServletResponse);

        // Assert
        verify(authService, times(1)).logout(httpServletResponse);
    }

    // --- Testes de Perfil ---

    @Test
    @DisplayName("getUserProfile: Deve chamar UserSettingsService.getUserProfile")
    void getUserProfile_shouldCallUserSettingsService() {
        // Arrange
        Map<String, Object> profileMap = Map.of("id", sampleUserId, "name", "Test User", "email", "test@example.com");
        when(userSettingsService.getUserProfile(sampleUserId)).thenReturn(profileMap);

        // Act
        Map<String, Object> result = aiLinguoFacade.getUserProfile(sampleUserId);

        // Assert
        assertNotNull(result);
        assertEquals("Test User", result.get("name"));
        verify(userSettingsService, times(1)).getUserProfile(sampleUserId);
    }

    @Test
    @DisplayName("updateUserProfile: Deve chamar UserSettingsService.updateUserProfile")
    void updateUserProfile_shouldCallUserSettingsService() {
        // Arrange
        Map<String, Object> profileUpdateData = Map.of("name", "Updated Name");
        Map<String, Object> updatedProfileMap = Map.of("id", sampleUserId, "name", "Updated Name", "email", "test@example.com");
        when(userSettingsService.updateUserProfile(sampleUserId, profileUpdateData)).thenReturn(updatedProfileMap);

        // Act
        Map<String, Object> result = aiLinguoFacade.updateUserProfile(sampleUserId, profileUpdateData);

        // Assert
        assertNotNull(result);
        assertEquals("Updated Name", result.get("name"));
        verify(userSettingsService, times(1)).updateUserProfile(sampleUserId, profileUpdateData);
    }

     @Test
    @DisplayName("changeUserPassword: Deve chamar UserSettingsService.changePassword")
    void changeUserPassword_shouldCallUserSettingsService() {
        // Arrange
        String currentPassword = "oldPassword";
        String newPassword = "newPasswordStrong";
        Map<String, Object> successResponse = Map.of("success", true);
        when(userSettingsService.changePassword(sampleUserId, currentPassword, newPassword)).thenReturn(successResponse);

        // Act
        Map<String, Object> result = aiLinguoFacade.changeUserPassword(sampleUserId, currentPassword, newPassword);

        // Assert
        assertNotNull(result);
        assertTrue((Boolean) result.get("success"));
        verify(userSettingsService, times(1)).changePassword(sampleUserId, currentPassword, newPassword);
    }


    // --- Testes de Conteúdo/Exercícios ---

    @Test
    @DisplayName("getRecommendedTasks: Deve chamar TaskService.getRandomTasks")
    void getRecommendedTasks_shouldCallTaskService() {
        // Arrange
        int limit = 5;
        Map<String, Object> tasksMap = Map.of("tasks", List.of(Map.of("id", 1L, "title", "Task 1")));
        when(taskService.getRandomTasks(sampleUserId, limit, null, null)).thenReturn(tasksMap);

        // Act
        Map<String, Object> result = aiLinguoFacade.getRecommendedTasks(sampleUserId, limit);

        // Assert
        assertNotNull(result);
        assertTrue(result.containsKey("tasks"));
        verify(taskService, times(1)).getRandomTasks(sampleUserId, limit, null, null);
    }

    @Test
    @DisplayName("submitTaskAttempt: Deve chamar TaskService.submitTaskAttempt")
    void submitTaskAttempt_shouldCallTaskService() {
        // Arrange
        Long taskId = 10L;
        Integer selectedAnswerIndex = 1;
        Integer timeSpent = 30;
        Map<String, Object> attemptResult = Map.of("isCorrect", true, "xpEarned", 15);
        when(taskService.submitTaskAttempt(sampleUserId, taskId, selectedAnswerIndex, timeSpent)).thenReturn(attemptResult);

        // Act
        Map<String, Object> result = aiLinguoFacade.submitTaskAttempt(sampleUserId, taskId, selectedAnswerIndex, timeSpent);

        // Assert
        assertNotNull(result);
        assertTrue((Boolean) result.get("isCorrect"));
        assertEquals(15, result.get("xpEarned"));
        verify(taskService, times(1)).submitTaskAttempt(sampleUserId, taskId, selectedAnswerIndex, timeSpent);
    }

    @Test
    @DisplayName("getTaskAndVocabularySummary: Deve chamar TaskService e VocabularyController")
    void getTaskAndVocabularySummary_shouldCallServices() {
        // Arrange
        Map<String, Object> taskStats = Map.of("totalAttempts", 5, "accuracy", 60.0);
        // Simula o retorno do VocabularyController (ResponseEntity)
        ResponseEntity<List<UserVocabularyProgress>> vocabResponseEntity = ResponseEntity.ok(Collections.emptyList());
        List<UserVocabularyProgress> vocabProgressBody = Collections.emptyList(); // O corpo da ResponseEntity

        when(taskService.getUserTaskStats(sampleUserId)).thenReturn(taskStats);
        when(vocabularyController.getUserProgress(sampleUserId)).thenReturn(vocabResponseEntity);

        // Act
        Map<String, Object> summary = aiLinguoFacade.getTaskAndVocabularySummary(sampleUserId);

        // Assert
        assertNotNull(summary);
        assertTrue((Boolean) summary.get("success"));
        assertEquals(taskStats, summary.get("taskStats"));
        // Verifica se o corpo da resposta do controller foi colocado no sumário
        assertEquals(vocabProgressBody, summary.get("vocabularyProgress"));

        verify(taskService, times(1)).getUserTaskStats(sampleUserId);
        verify(vocabularyController, times(1)).getUserProgress(sampleUserId);
    }

    // --- Testes de Progresso ---

    @Test
    @DisplayName("getUserOverallProgressSummary: Deve chamar serviços de progresso e combinar")
    void getUserOverallProgressSummary_shouldCallAllRelevantServicesAndCombineResults() {
        // Arrange
        Map<String, Object> dashboardStats = Map.of("totalXp", 100, "level", 2);
        Map<String, Object> ranking = Map.of("rank", 5, "totalUsers", 50);
        Map<String, Object> goals = Map.of("completedGoals", 1, "totalGoals", 3);
        Map<String, Object> taskStats = Map.of("totalAttempts", 10, "accuracy", 80.0);

        when(dashboardService.getUserStats(sampleUserId)).thenReturn(dashboardStats);
        when(gamificationService.getUserRanking(sampleUserId)).thenReturn(ranking);
        when(gamificationService.getUserGoals(sampleUserId)).thenReturn(goals);
        when(taskService.getUserTaskStats(sampleUserId)).thenReturn(taskStats);

        // Act
        Map<String, Object> summary = aiLinguoFacade.getUserOverallProgressSummary(sampleUserId);

        // Assert
        assertNotNull(summary);
        assertTrue((Boolean) summary.get("success"));
        assertEquals(dashboardStats, summary.get("dashboardStats"));
        assertEquals(ranking, summary.get("ranking"));
        assertEquals(goals, summary.get("goals"));
        assertEquals(taskStats, summary.get("taskStats"));

        verify(dashboardService, times(1)).getUserStats(sampleUserId);
        verify(gamificationService, times(1)).getUserRanking(sampleUserId);
        verify(gamificationService, times(1)).getUserGoals(sampleUserId);
        verify(taskService, times(1)).getUserTaskStats(sampleUserId);
        verifyNoInteractions(authService, userSettingsService);
    }

    @Test
    @DisplayName("getUserOverallProgressSummary: Deve tratar erro em um dos serviços")
    void getUserOverallProgressSummary_shouldHandleServiceError() {
        // Arrange: Simula um erro ao buscar o ranking
        Map<String, Object> dashboardStats = Map.of("totalXp", 100, "level", 2);
        when(dashboardService.getUserStats(sampleUserId)).thenReturn(dashboardStats);
        when(gamificationService.getUserRanking(sampleUserId)).thenThrow(new RuntimeException("Ranking service unavailable"));

        // Act
        Map<String, Object> summary = aiLinguoFacade.getUserOverallProgressSummary(sampleUserId);

        // Assert
        assertNotNull(summary);
        assertFalse((Boolean) summary.get("success"));
        assertTrue(((String) summary.get("error")).contains("Ranking service unavailable"));

        // Verifica que os serviços até o ponto do erro foram chamados
        verify(dashboardService, times(1)).getUserStats(sampleUserId);
        verify(gamificationService, times(1)).getUserRanking(sampleUserId);
        // Garante que os serviços *depois* do erro não foram chamados
        verifyNoInteractions(taskService);
    }

    @Test
    @DisplayName("getUserActivityHistory: Deve chamar TaskService.getUserTaskHistory")
    void getUserActivityHistory_shouldCallTaskService() {
        // Arrange
        Map<String, Object> historyMap = Map.of("recentAttempts", List.of(Map.of("id", 1L)));
        when(taskService.getUserTaskHistory(sampleUserId)).thenReturn(historyMap);

        // Act
        Map<String, Object> result = aiLinguoFacade.getUserActivityHistory(sampleUserId);

        // Assert
        assertNotNull(result);
        assertTrue((Boolean) result.get("success"));
        assertEquals(historyMap, result.get("taskHistory"));
        verify(taskService, times(1)).getUserTaskHistory(sampleUserId);
        // Adicionar verificações para histórico de vocabulário/chat se implementado
    }

    @Test
    @DisplayName("getUserAchievements: Deve chamar DashboardService.getUserAchievements")
    void getUserAchievements_shouldCallDashboardService() {
        // Arrange
        Map<String, Object> achievementsMap = Map.of("achievements", List.of(Map.of("title", "First Step")));
        when(dashboardService.getUserAchievements(sampleUserId)).thenReturn(achievementsMap);

        // Act
        Map<String, Object> result = aiLinguoFacade.getUserAchievements(sampleUserId);

        // Assert
        assertNotNull(result);
        // Verifica se o resultado é o mesmo mapa retornado pelo serviço
        assertEquals(achievementsMap, result);
        verify(dashboardService, times(1)).getUserAchievements(sampleUserId);
    }
}

```

**Explicação:** usa **Mockito** para verificar que a `AILinguoFacade` **orquestra** corretamente os serviços: chama o método certo, combina resultados e trata **erros** (ex.: ranking indisponível).
</details>

---

<details>
<summary><strong>backend-java/src/main/java/com/ailinguo/controller/AuthController.java</strong> — <em>Controller delegando para a Facade</em></summary>

```java

import com.ailinguo.dto.UserDto;
import com.ailinguo.dto.auth.AuthResponse;
import com.ailinguo.dto.auth.LoginRequest;
import com.ailinguo.service.AuthService;
import com.ailinguo.facade.IAILinguoFacade; // Importe a interface da Facade
import jakarta.servlet.http.HttpServletResponse;
import jakarta.validation.Valid;
import org.springframework.http.ResponseEntity;
import org.springframework.security.core.Authentication;
import org.springframework.web.bind.annotation.*;

import java.util.Map;

@RestController
@RequestMapping("/api/auth")
public class AuthController {

    private final AuthService authService;

    public AuthController(AuthService authService) {
        this.authService = authService;

    // A única dependência agora é a Facade
    private final IAILinguoFacade aiLinguoFacade;

    // Injeta a Facade através do construtor
    public AuthController(IAILinguoFacade aiLinguoFacade) {
        this.aiLinguoFacade = aiLinguoFacade;
    }


    @PostMapping("/register")
    public ResponseEntity<?> register(
            @Valid @RequestBody Map<String, Object> request,
            HttpServletResponse response
    ) {
        try {
            AuthResponse authResponse = authService.register(request, response);
            // Delega a chamada de registro para a Facade
            AuthResponse authResponse = aiLinguoFacade.registerUser(request, response);
            return ResponseEntity.ok(authResponse);
        } catch (Exception e) {
            // Mantém o tratamento de erro (ou centraliza na Facade)
            return ResponseEntity.badRequest()
                    .body(Map.of("error", e.getMessage()));
        }
    }


    @PostMapping("/login")
    public ResponseEntity<?> login(
            @Valid @RequestBody LoginRequest request,
            HttpServletResponse response
    ) {
        try {
            AuthResponse authResponse = authService.login(request, response);
            // Delega a chamada de login para a Facade
            AuthResponse authResponse = aiLinguoFacade.loginUser(request, response);
            return ResponseEntity.ok(authResponse);
        } catch (Exception e) {
            // Mantém o tratamento de erro
            return ResponseEntity.status(401)
                    .body(Map.of("error", e.getMessage()));
        }
    }


    @GetMapping("/me")
    public ResponseEntity<Map<String, UserDto>> getCurrentUser(Authentication authentication) {
        String userId = authentication != null ? (String) authentication.getPrincipal() : null;
        UserDto user = authService.getCurrentUser(userId);
        return ResponseEntity.ok(Map.of("user", user != null ? user : new UserDto()));
        // Delega a busca do usuário atual para a Facade
        UserDto user = aiLinguoFacade.getCurrentAuthenticatedUser(authentication);
        // Retorna um DTO vazio se não autenticado, para consistência
        UserDto responseUser = user != null ? user : new UserDto();
        return ResponseEntity.ok(Map.of("user", responseUser));
    }


    @PostMapping("/logout")
    public ResponseEntity<Map<String, Boolean>> logout(HttpServletResponse response) {
        authService.logout(response);
        // Delega a chamada de logout para a Facade
        aiLinguoFacade.logoutUser(response);
        return ResponseEntity.ok(Map.of("success", true));
    }
}

```

**Explicação:** expõe endpoints `/register`, `/login`, `/me`, `/logout`.  
> **Observação técnica:** este trecho mostra **duas injeções** (por `AuthService` e por `IAILinguoFacade`) e dupla chamada dentro dos métodos. Para adotar **somente a Facade**, remova o `AuthService` do construtor e use apenas `IAILinguoFacade` nas ações.
</details>

---

<details>
<summary><strong>backend-java/src/main/java/com/ailinguo/facade/IAILinguoFacade.java</strong> — <em>Interface + (trecho) implementação da Facade</em></summary>

```java

package com.ailinguo.facade;import java.util.HashMap;import com.ailinguo.dto.UserDto;import com.ailinguo.dto.auth.AuthResponse;import com.ailinguo.dto.auth.LoginRequest;import jakarta.servlet.http.HttpServletResponse;import org.springframework.security.core.Authentication;import com.ailinguo.service.*; // Importa todos os serviços necessáriosimport com.ailinguo.controller.VocabularyController; // Temporário, idealmente usar VocabularyServiceimport com.ailinguo.controller.ExerciseController; // Temporário, idealmente usar ExerciseServiceimport com.ailinguo.controller.ExerciseController;import com.ailinguo.controller.VocabularyController;import java.util.List;import java.util.Map;import java.util.HashMap;import jakarta.servlet.http.HttpServletResponse;import org.springframework.security.core.Authentication;import org.slf4j.Logger;import org.slf4j.LoggerFactory;import org.springframework.stereotype.Service;import java.util.Map;import com.ailinguo.service.AuthService;import com.ailinguo.service.DashboardService;import com.ailinguo.service.GamificationService;import com.ailinguo.service.TaskService;import com.ailinguo.service.UserSettingsService;public interface IAILinguoFacade {    // --- Autenticação ---    AuthResponse registerUser(Map<String, Object> requestData, HttpServletResponse response);    AuthResponse loginUser(LoginRequest loginRequest, HttpServletResponse response);    UserDto getCurrentAuthenticatedUser(Authentication authentication);    void logoutUser(HttpServletResponse response);    // --- Perfil ---    Map<String, Object> getUserProfile(Long userId);    Map<String, Object> updateUserProfile(Long userId, Map<String, Object> profileData);    Map<String, Object> getUserPreferences(Long userId);    Map<String, Object> updateUserPreferences(Long userId, Map<String, Object> preferencesData);    Map<String, Object> changeUserPassword(Long userId, String currentPassword, String newPassword);    // --- Aulas / Exercícios / Vocabulário ---    Map<String, Object> getRecommendedTasks(Long userId, int limit);    Map<String, Object> submitTaskAttempt(Long userId, Long taskId, Integer selectedAnswerIndex, Integer timeSpent);    Map<String, Object> getTaskAndVocabularySummary(Long userId); // Exemplo combinado    // --- Progresso ---    Map<String, Object> getUserOverallProgressSummary(Long userId); // Usará DashboardService, GamificationService, etc.    Map<String, Object> getUserActivityHistory(Long userId); // Combina histórico de tarefas, vocabulário, etc.    Map<String, Object> getUserAchievements(Long userId); // Poderia vir do DashboardService ou GamificationService}@Servicepublic class AILinguoFacade implements IAILinguoFacade {class AILinguoFacade implements IAILinguoFacade {    private static final Logger logger = LoggerFactory.getLogger(AILinguoFacade.class);@@ -65,13 +74,13 @@ public class AILinguoFacade implements IAILinguoFacade {    // private final XpService xpService; // XpService é provavelmente usado internamente pelos outros serviços    public AILinguoFacade(AuthService authService,                          UserSettingsService userSettingsService,                          TaskService taskService,                          ExerciseController exerciseController,                          VocabularyController vocabularyController,                          DashboardService dashboardService,                          GamificationService gamificationService                          /*XpService xpService*/) { //            UserSettingsService userSettingsService,            TaskService taskService,            ExerciseController exerciseController,            VocabularyController vocabularyController,            DashboardService dashboardService,            GamificationService gamificationService    /*XpService xpService*/) { //        this.authService = authService;        this.userSettingsService = userSettingsService;        this.taskService = taskService;@@ -121,15 +130,15 @@ public Map<String, Object> updateUserProfile(Long userId, Map<String, Object> pr        return userSettingsService.updateUserProfile(userId, profileData); //    }     @Override    @Override    public Map<String, Object> getUserPreferences(Long userId) {        logger.info("Facade: Buscando preferências para usuário ID: {}", userId);        return userSettingsService.getUserPreferences(userId); //    }    @Override    public Map<String, Object> updateUserPreferences(Long userId, Map<String, Object> preferencesData) {         logger.info("Facade: Atualizando preferências para usuário ID: {}", userId);        logger.info("Facade: Atualizando preferências para usuário ID: {}", userId);        return userSettingsService.updateUserPreferences(userId, preferencesData); //    }@@ -154,57 +163,56 @@ public Map<String, Object> submitTaskAttempt(Long userId, Long taskId, Integer s        return taskService.submitTaskAttempt(userId, taskId, selectedAnswerIndex, timeSpent); //    }     @Override    @Override    public Map<String, Object> getTaskAndVocabularySummary(Long userId) {        logger.info("Facade: Buscando resumo de tarefas e vocabulário para usuário ID: {}", userId);        Map<String, Object> summary = new HashMap<>();        try {             summary.put("taskStats", taskService.getUserTaskStats(userId)); //             // Supondo que VocabularyController tem um método para progresso             summary.put("vocabularyProgress", vocabularyController.getUserProgress(userId).getBody()); //             summary.put("success", true);        } catch(Exception e) {             logger.error("Erro ao buscar resumo de atividades para {}: {}", userId, e.getMessage());             summary.put("success", false);             summary.put("error", "Erro ao buscar resumo de atividades.");            summary.put("taskStats", taskService.getUserTaskStats(userId)); //            // Supondo que VocabularyController tem um método para progresso            summary.put("vocabularyProgress", vocabularyController.getUserProgress(userId).getBody()); //            summary.put("success", true);        } catch (Exception e) {            logger.error("Erro ao buscar resumo de atividades para {}: {}", userId, e.getMessage());            summary.put("success", false);            summary.put("error", "Erro ao buscar resumo de atividades.");        }        return summary;    }    // --- Progresso ---    @Override    public Map<String, Object> getUserOverallProgressSummary(Long userId) {        logger.info("Facade: Buscando resumo geral de progresso para usuário ID: {}", userId);        Map<String, Object> summary = new HashMap<>();         try {        try {            summary.put("dashboardStats", dashboardService.getUserStats(userId)); //            summary.put("ranking", gamificationService.getUserRanking(userId)); //            summary.put("goals", gamificationService.getUserGoals(userId)); //            summary.put("taskStats", taskService.getUserTaskStats(userId)); //            summary.put("success", true);         } catch (Exception e) {             logger.error("Erro ao buscar resumo geral para {}: {}", userId, e.getMessage());             summary.put("success", false);             summary.put("error", "Erro ao buscar resumo geral.");         }        } catch (Exception e) {            logger.error("Erro ao buscar resumo geral para {}: {}", userId, e.getMessage());            summary.put("success", false);            summary.put("error", "Erro ao buscar resumo geral.");        }        return summary;    }    @Override    public Map<String, Object> getUserActivityHistory(Long userId) {        logger.info("Facade: Buscando histórico de atividades para usuário ID: {}", userId);        Map<String, Object> history = new HashMap<>();         try {             history.put("taskHistory", taskService.getUserTaskHistory(userId)); //             // Adicionar histórico de vocabulário, chat, etc. se necessário             // history.put("vocabularyHistory", vocabularyController.getUserProgress(userId).getBody()); // Exemplo             history.put("success", true);         } catch (Exception e) {             logger.error("Erro ao buscar histórico para {}: {}", userId, e.getMessage());             history.put("success", false);             history.put("error", "Erro ao buscar histórico.");         }        try {            history.put("taskHistory", taskService.getUserTaskHistory(userId)); //            // Adicionar histórico de vocabulário, chat, etc. se necessário            // history.put("vocabularyHistory", vocabularyController.getUserProgress(userId).getBody()); // Exemplo            history.put("success", true);        } catch (Exception e) {            logger.error("Erro ao buscar histórico para {}: {}", userId, e.getMessage());            history.put("success", false);            history.put("error", "Erro ao buscar histórico.");        }        return history;    }@@ -213,10 +221,10 @@ public Map<String, Object> getUserAchievements(Long userId) {        logger.info("Facade: Buscando conquistas para usuário ID: {}", userId);        // Supondo que DashboardService tem o método        try {             return dashboardService.getUserAchievements(userId); //            return dashboardService.getUserAchievements(userId); //        } catch (Exception e) {             logger.error("Erro ao buscar conquistas para {}: {}", userId, e.getMessage());             return Map.of("success", false, "error", "Erro ao buscar conquistas.");            logger.error("Erro ao buscar conquistas para {}: {}", userId, e.getMessage());            return Map.of("success", false, "error", "Erro ao buscar conquistas.");        }    }}}

```

**Explicação:** a **interface** define as operações expostas pela fachada. O trecho incluído também mostra a **implementação** (`AILinguoFacade`) com injeção dos serviços e métodos de orquestração.  
> **Boas práticas:** preferir **separar** a interface e a implementação em arquivos distintos (`IAILinguoFacade.java` e `AILinguoFacade.java`) e **limpar imports duplicados**.
</details>

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
| `1.0` | Documentação inicial e formatação da aba | [Samuel Afonso](https://github.com/SamuelAfonso) | 22/10/2025 |  |  |  |
| `1.1` | Definição da **Facade única** (AILinguoFacade) e seção de benefícios/considerações | [Emivalto Da Costa Tavares Junior](https://github.com/EmivaltoJrr) | 23/10/2025 |  |  |  |
| `1.2` | **Criação do UML do Facade** (Figuras 1–3) | [Arthur Carvalho Leite](https://github.com/arthurlleite) | 24/10/2025 |  |  |  |
| `1.3` | **Atualização do documento**: seção “Figuras (RAW) e Explicações)”, vídeo (SharePoint) e ajustes (padrão Prototype) | [Arthur Carvalho Leite](https://github.com/arthurlleite) | 24/10/2025 |  |  |  |
| `1.4` | **Inclusão dos códigos do Facade** (controller, interface/implementação, testes) com explicações e _details_ | [Arthur Carvalho Leite](https://github.com/arthurlleite) | 24/10/2025 |  |  |  |
