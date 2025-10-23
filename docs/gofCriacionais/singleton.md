# Padrão Criacional Singleton - Group

## Sumário

- [Introdução](#Introdução)
- [Objetivo](#Objetivo)
- [Metodologia](#Metodologia)
- [Desenvolvimento e Implementação](#Desenvolvimento-e-Implementação)
- [Modelagem do Singleton para o Group](#Modelagem-do-Singleton-para-o-Group)
    - [Implementação da Classe Group](#Implementação-da-Classe-Group)
    - [Classe de Teste GroupSingletonTest](#Classe-de-Teste-GroupSingletonTest)
- [Vídeo Explicação e Execução do Singleton para Group](#Vídeo-Explicação-e-Execução-do-Singleton-para-Group)
- [Discussão Vantagens e Desvantagens do Singleton](#Discussão-Vantagens-e-Desvantagens-do-Singleton)
- [Conclusão](#Conclusão)
- [Referências](#Referências)
- [Histórico de Versão](#Histórico-de-Versão)

## Introdução

Como explicado em [Singleton](https://refactoring.guru/pt-br/design-patterns/singleton), o padrão de projeto Singleton é uma solução criacional utilizada para garantir que uma determinada classe possua apenas uma instância ao longo da execução de um programa. Com isso, ele oferece um ponto de acesso global e controlado a essa instância, o que o torna especialmente útil para gerenciar recursos compartilhados, como conexões com banco de dados, arquivos ou componentes centrais do sistema.

No contexto do projeto AILinguo, o padrão Singleton foi aplicado à classe `Group`, responsável por centralizar o gerenciamento de grupos de usuários, criação de subgrupos e controle de membros. Assim, essa decisão arquitetural foi tomada para assegurar que haja apenas uma instância global do gerenciador de grupos no sistema e evitar a criação acidental de múltiplas instâncias, facilitando o controle centralizado dos grupos e relacionamentos entre usuários.

## Objetivo

Os principais objetivos ao aplicar o padrão Singleton à classe `Group` são:

* **Garantir Instância Única:** Assegurar que apenas um objeto `Group` seja criado e utilizado em todo o ciclo de vida da aplicação.
* **Ponto de Acesso Global:** Fornecer um método bem definido e acessível globalmente (`getInstance()`) para que outras partes do sistema possam obter a referência à instância única do `Group` sem a necessidade de passá-la como parâmetro por múltiplas camadas.
* **Controle sobre a Instanciação:** Centralizar o controle da criação da instância do `Group` dentro da própria classe e evitar instanciações acidentais ou múltiplas.
* **Gerenciamento Centralizado:** Garantir que todas as operações de grupos utilizem o mesmo estado global e controlem centralmente os relacionamentos entre usuários e grupos.

## Metodologia

O padrão de projeto criacional **Singleton** foi selecionado para a classe `Group` devido à necessidade de que exista apenas uma única instância do gerenciador de grupos principal. Assim, como foi descrito em [Padrão de design Singleton: Implementação e Exemplos de Uso](https://elemarjr.com/clube-de-estudos/artigos/padrao-de-design-singleton-implementacao-e-exemplos-de-uso/), esta abordagem garante um ponto de acesso global e controlado.

A implementação do padrão Singleton para a classe `Group` foi realizada em Java, utilizando o Spring Boot como framework de desenvolvimento, com o apoio das ferramentas e extensões para desenvolvimento Java. O processo de desenvolvimento seguiu as práticas padrão de codificação e organização em pacotes, conforme detalhado nas seções subsequentes.

**A concepção da classe `Group` foi guiada pela análise dos seguintes artefatos de modelagem previamente desenvolvidos:**

* **Análise de Gerenciamento de Grupos:** A classe `Group` é fundamental para o gerenciamento de grupos de usuários na aplicação AILinguo, sendo utilizada por serviços, controladores e componentes do sistema.
* **Gerenciamento de Estado Global:** A classe mantém informações críticas sobre grupos e relacionamentos que devem ser consistentes em toda a aplicação.
* **Centralização de Operações:** Múltiplas partes do sistema precisam acessar e gerenciar grupos, incluindo criação de subgrupos, adição/remoção de membros e controle de relacionamentos.

**O desenvolvimento e implementação da classe `Group` como um Singleton seguiram os seguintes passos:**

1.  **Identificação da Necessidade de Unicidade:** Foi constatado, a partir da análise dos requisitos de gerenciamento de grupos, que a plataforma "AILinguo" deveria possuir um único gerenciador de grupos. Este gerenciador centraliza todas as operações de grupos, e a existência de múltiplas instâncias seria conceitualmente incorreta e logisticamente problemática.
2.  **Escolha do Padrão Singleton:** Diante da exigência de uma única instância globalmente acessível, o padrão de projeto criacional Singleton foi escolhido como a solução de design mais direta e apropriada.
3.  **Definição da Estrutura da Classe `Group`:** Modificação do arquivo `Group.java` existente.
4.  **Implementação dos Elementos Característicos do Singleton**
5.  **Definição dos Métodos de Negócio do Group**
6.  **Integração e Teste na Aplicação (`GroupSingletonTest.java`):**

**Principais Componentes e Características da Implementação do Singleton:**

* **Construtor Privado:** Impede que a classe seja instanciada diretamente de fora dela mesma com o operador `new`.
* **Instância Estática Privada:** A classe mantém uma referência estática à sua própria instância única. Como a que se encontra abaixo:

    ```java
    private static Group instancia;
    ```
* **Método Estático Público `getInstance()`:** Este método é o único ponto de acesso para obter a instância da classe. Ele verifica se a instância já foi criada; se não, ele a cria e a armazena na variável estática. Se já existe, ele simplesmente a retorna. Para garantir segurança em ambientes com múltiplas threads (concorrência), este método é sincronizado.
    ```java
    public static synchronized Group getInstance() {
        if (instancia == null) {
            instancia = new Group(); // Criação na primeira chamada
        }
        return instancia;
    }
    ```

No contexto do "AILinguo", a classe `Group` deve encapsular suas funcionalidades (como gerenciamento de grupos, criação de subgrupos e controle de membros) e a lógica para gerenciá-los, enquanto o padrão Singleton controla sua criação e acesso.

## Desenvolvimento e Implementação

A seguir, são detalhadas a modelagem e a implementação da classe `Group` como um Singleton e sua interação com outras partes do sistema.

## Modelagem do Singleton para o Group

Abaixo o espaço para o seu diagrama UML para o Singleton do `Group`:

<div align="center">
    Figura 1 (Singleton): Modelagem UML do Padrão Singleton para a Classe Group
    <br>
    <img src="#" alt="Modelagem UML do Singleton para Group" width="800">
    <br>
    <b>Autor:</b> Gabriel Lima e Mateus Bastos.
    <br>
</div>

## Implementação da Classe Group

A classe `Group` foi implementada no pacote `com.ailinguo.model` para representar o gerenciador de grupos principal da plataforma. Ela contém um construtor privado para impedir a instanciação externa, um atributo estático privado para armazenar sua única instância, e um método estático público `getInstance()` que controla o acesso a essa instância, criando-a apenas na primeira chamada. A classe também gerencia subgrupos e fornece métodos para operações com grupos.

Abaixo o código para `Group.java`:

<details>
  <summary><strong>Código para `Group.java`:</strong></summary>

```java
package com.ailinguo.model;

import jakarta.persistence.*;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.atomic.AtomicInteger;

@Entity
@Table(name = "groups")
@EntityListeners(AuditingEntityListener.class)
public class Group {
    
    // Atributo estático privado para guardar a ÚNICA instância da classe
    private static Group instancia;
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(nullable = false)
    private String name;
    
    private String description;
    private Integer members;
    private Boolean open;
    
    // Atributos para gerenciamento centralizado
    private static AtomicInteger proximoGrupoId = new AtomicInteger(1);
    private static List<Group> gruposCriados = new ArrayList<>();
    
    @CreatedDate
    @Column(name = "created_at", nullable = false, updatable = false)
    private LocalDateTime createdAt;
    
    @LastModifiedDate
    @Column(name = "updated_at")
    private LocalDateTime updatedAt;
    
    // Construtor PRIVADO: impede a criação de instâncias com 'new Group()' de fora da classe
    private Group() {
        this.id = 1L;
        this.name = "Grupo Principal AILinguo";
        this.description = "O grupo principal para todos os usuários da plataforma";
        this.members = 0;
        this.open = true;
        this.createdAt = LocalDateTime.now();
        this.updatedAt = LocalDateTime.now();
        this.membersList = new ArrayList<>();
        System.out.println("👥 Instância ÚNICA do Grupo Principal criada! (Singleton) 👥");
    }
    
    // Método estático público para obter a instância única
    public static synchronized Group getInstance() {
        if (instancia == null) {
            instancia = new Group();
        }
        return instancia;
    }
    
    // Relacionamento many-to-many com User
    @ManyToMany(fetch = FetchType.LAZY)
    @JoinTable(
        name = "group_members",
        joinColumns = @JoinColumn(name = "group_id"),
        inverseJoinColumns = @JoinColumn(name = "user_id")
    )
    private List<User> membersList;
    
    // Métodos de negócio para gerenciamento de grupos
    public void adicionarMembro(User usuario) {
        if (usuario != null && !this.membersList.contains(usuario)) {
            this.membersList.add(usuario);
            this.members = this.membersList.size();
            this.updatedAt = LocalDateTime.now();
            System.out.println("[Group Singleton] Usuário '" + usuario.getName() + "' adicionado ao grupo principal. Total de membros: " + this.members);
        }
    }
    
    public void removerMembro(User usuario) {
        if (usuario != null && this.membersList.contains(usuario)) {
            this.membersList.remove(usuario);
            this.members = this.membersList.size();
            this.updatedAt = LocalDateTime.now();
            System.out.println("[Group Singleton] Usuário '" + usuario.getName() + "' removido do grupo principal. Total de membros: " + this.members);
        }
    }
    
    public void criarSubgrupo(String nome, String descricao) {
        int novoId = proximoGrupoId.getAndIncrement();
        Group subgrupo = new Group();
        subgrupo.setId((long) novoId);
        subgrupo.setName(nome);
        subgrupo.setDescription(descricao);
        subgrupo.setMembers(0);
        subgrupo.setOpen(true);
        subgrupo.setCreatedAt(LocalDateTime.now());
        subgrupo.setUpdatedAt(LocalDateTime.now());
        subgrupo.setMembersList(new ArrayList<>());
        
        gruposCriados.add(subgrupo);
        System.out.println("[Group Singleton] Subgrupo '" + nome + "' (ID: " + novoId + ") criado pelo grupo principal.");
    }
    
    public List<Group> listarSubgrupos() {
        System.out.println("[Group Singleton] Listando " + gruposCriados.size() + " subgrupo(s) criado(s)...");
        return new ArrayList<>(gruposCriados);
    }
    
    public void exibirDetalhes() {
        System.out.println("\n--- Detalhes do Grupo Principal (Singleton) ---");
        System.out.println("ID: " + id);
        System.out.println("Nome: " + name);
        System.out.println("Descrição: " + description);
        System.out.println("Membros: " + members);
        System.out.println("Aberto: " + (open ? "Sim" : "Não"));
        System.out.println("Criado em: " + createdAt);
        System.out.println("Atualizado em: " + updatedAt);
        if (membersList.isEmpty()) {
            System.out.println("Lista de Membros: Nenhum membro adicionado ainda.");
        } else {
            System.out.println("Lista de Membros (" + membersList.size() + "):");
            for (User member : membersList) {
                System.out.println("  - " + member.getName() + " (ID: " + member.getId() + ", Email: " + member.getEmail() + ")");
            }
        }
        System.out.println("Subgrupos criados: " + gruposCriados.size());
        System.out.println("HashCode da Instância: " + this.hashCode());
        System.out.println("----------------------------------------");
    }
}
```

<b> Autor: </b> Gabriel Lima e Mateus Bastos.

##### Imagem do código no VSCODE

As figuras 2 e 3 abaixo ilustram a estrutura da classe `Group.java` no ambiente de desenvolvimento VSCode.

<div align="center">
    Figura 2: Classe Group.java implementando Singleton
    <br>
    <img src="assets/gofcodes/singleton1.png" width="1000">
    <br>
    <b>Autor:</b> Gabriel Lima e Mateus Bastos.
    <br>
</div>

<div align="center">
    Figura 3: Classe Group.java implementando Singleton
    <br>
    <img src="assets/gofcodes/singleton2.png" width="1000">
    <br>
    <b>Autor:</b> Gabriel Lima e Mateus Bastos.
    <br>
</div>

</details>

## Classe de Teste GroupSingletonTest

A classe `GroupSingletonTest.java` foi criada para demonstrar e testar o funcionamento do padrão Singleton aplicado ao `Group`. Esta classe contém testes que verificam a unicidade da instância, funcionalidade básica do gerenciamento de grupos e thread-safety.

**Observação:** Embora a implementação completa de testes unitários não seja o foco deste documento sobre Singleton, sua existência é necessária para a validação da funcionalidade da classe Group.

Abaixo o código para `GroupSingletonTest.java`:

<details>
  <summary><strong>Código para `GroupSingletonTest.java`</strong></summary>

```java
package com.ailinguo.model;

import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

import static org.junit.jupiter.api.Assertions.*;

@SpringBootTest
public class GroupSingletonTest {

    @Test
    public void testSingletonPattern() {
        System.out.println("\n=== TESTANDO PADRÃO SINGLETON PARA GROUP ===");
        
        // Obter primeira instância
        Group group1 = Group.getInstance();
        System.out.println("Primeira instância obtida. HashCode: " + group1.hashCode());
        
        // Obter segunda instância
        Group group2 = Group.getInstance();
        System.out.println("Segunda instância obtida. HashCode: " + group2.hashCode());
        
        // Obter terceira instância
        Group group3 = Group.getInstance();
        System.out.println("Terceira instância obtida. HashCode: " + group3.hashCode());
        
        // Verificar se todas as instâncias são a mesma
        assertSame(group1, group2, "As duas primeiras instâncias devem ser a mesma");
        assertSame(group2, group3, "As duas últimas instâncias devem ser a mesma");
        assertSame(group1, group3, "A primeira e terceira instâncias devem ser a mesma");
        
        System.out.println("✅ Confirmado: Todas as instâncias são a MESMA! (Singleton funcionando)");
        
        // Testar funcionalidade básica
        assertNotNull(group1.getName(), "Nome do grupo não deve ser nulo");
        assertNotNull(group1.getDescription(), "Descrição do grupo não deve ser nula");
        assertTrue(group1.getOpen(), "Grupo deve estar aberto por padrão");
        assertEquals(0, group1.getMembers(), "Grupo deve começar com 0 membros");
        
        System.out.println("✅ Funcionalidade básica do Group testada com sucesso!");
        System.out.println("Nome do grupo: " + group1.getName());
        System.out.println("Descrição: " + group1.getDescription());
        System.out.println("===============================================\n");
    }
    
    @Test
    public void testGroupManagement() {
        System.out.println("=== TESTANDO GERENCIAMENTO DE GRUPOS ===");
        
        Group grupoPrincipal = Group.getInstance();
        
        // Criar usuários de teste
        User usuario1 = User.builder()
                .id(1L)
                .name("João Silva")
                .email("joao@example.com")
                .password("senha123")
                .cefrLevel(User.CefrLevel.A2)
                .build();
        
        User usuario2 = User.builder()
                .id(2L)
                .name("Maria Santos")
                .email("maria@example.com")
                .password("senha456")
                .cefrLevel(User.CefrLevel.B1)
                .build();
        
        // Testar adição de membros
        grupoPrincipal.adicionarMembro(usuario1);
        assertEquals(1, grupoPrincipal.getMembers(), "Grupo deve ter 1 membro após adicionar usuário1");
        
        grupoPrincipal.adicionarMembro(usuario2);
        assertEquals(2, grupoPrincipal.getMembers(), "Grupo deve ter 2 membros após adicionar usuário2");
        
        // Testar criação de subgrupos
        grupoPrincipal.criarSubgrupo("Grupo Iniciantes", "Grupo para usuários iniciantes");
        grupoPrincipal.criarSubgrupo("Grupo Avançados", "Grupo para usuários avançados");
        
        List<Group> subgrupos = grupoPrincipal.listarSubgrupos();
        assertEquals(2, subgrupos.size(), "Devem existir 2 subgrupos criados");
        
        // Testar remoção de membro
        grupoPrincipal.removerMembro(usuario1);
        assertEquals(1, grupoPrincipal.getMembers(), "Grupo deve ter 1 membro após remover usuário1");
        
        System.out.println("✅ Gerenciamento de grupos testado com sucesso!");
        grupoPrincipal.exibirDetalhes();
        System.out.println("===============================================\n");
    }
    
    @Test
    public void testMultipleThreadsSingleton() {
        System.out.println("=== TESTANDO SINGLETON COM MÚLTIPLAS THREADS ===");
        
        final int NUM_THREADS = 10;
        final Group[] instances = new Group[NUM_THREADS];
        final Thread[] threads = new Thread[NUM_THREADS];
        
        // Criar múltiplas threads que tentam obter a instância
        for (int i = 0; i < NUM_THREADS; i++) {
            final int index = i;
            threads[i] = new Thread(() -> {
                instances[index] = Group.getInstance();
                System.out.println("Thread " + index + " obteve instância. HashCode: " + instances[index].hashCode());
            });
        }
        
        // Iniciar todas as threads
        for (Thread thread : threads) {
            thread.start();
        }
        
        // Aguardar todas as threads terminarem
        for (Thread thread : threads) {
            try {
                thread.join();
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
        
        // Verificar se todas as instâncias são a mesma
        Group firstInstance = instances[0];
        for (int i = 1; i < NUM_THREADS; i++) {
            assertSame(firstInstance, instances[i], 
                "Todas as instâncias obtidas em threads diferentes devem ser a mesma");
        }
        
        System.out.println("✅ Singleton thread-safe funcionando corretamente!");
        System.out.println("===============================================\n");
    }
    
    @Test
    public void testGroupOperations() {
        System.out.println("=== TESTANDO OPERAÇÕES DO GRUPO ===");
        
        Group grupo = Group.getInstance();
        
        // Testar criação de múltiplos subgrupos
        grupo.criarSubgrupo("Grupo A1", "Iniciantes absolutos");
        grupo.criarSubgrupo("Grupo A2", "Iniciantes com conhecimento básico");
        grupo.criarSubgrupo("Grupo B1", "Intermediários");
        grupo.criarSubgrupo("Grupo B2", "Intermediários avançados");
        grupo.criarSubgrupo("Grupo C1", "Avançados");
        
        List<Group> subgrupos = grupo.listarSubgrupos();
        assertEquals(5, subgrupos.size(), "Devem existir 5 subgrupos");
        
        // Verificar se os subgrupos têm IDs únicos
        List<Long> ids = subgrupos.stream().map(Group::getId).toList();
        assertEquals(5, ids.stream().distinct().count(), "Todos os IDs devem ser únicos");
        
        // Testar adição de múltiplos usuários
        for (int i = 1; i <= 5; i++) {
            User usuario = User.builder()
                    .id((long) i)
                    .name("Usuário " + i)
                    .email("usuario" + i + "@example.com")
                    .password("senha" + i)
                    .cefrLevel(User.CefrLevel.A1)
                    .build();
            grupo.adicionarMembro(usuario);
        }
        
        assertEquals(5, grupo.getMembers(), "Grupo deve ter 5 membros");
        assertEquals(5, grupo.getMembersList().size(), "Lista de membros deve ter 5 usuários");
        
        System.out.println("✅ Operações do grupo testadas com sucesso!");
        grupo.exibirDetalhes();
        System.out.println("===============================================\n");
    }
}
```

<b> Autor: </b> Gabriel Lima e Mateus Bastos.

</details>

## Discussão Vantagens e Desvantagens do Singleton

A escolha de utilizar o padrão Singleton para a classe Group foi ponderada, considerando seus benefícios e também as críticas frequentemente associadas a este padrão.

**Vantagens Observadas:**

- **Instância Única Garantida:** O padrão assegura de forma robusta que apenas uma instância do Group principal exista, o que está alinhado com o requisito do sistema de ter um único gerenciador de grupos central.
- **Ponto de Acesso Global Controlado:** O método Group.getInstance() oferece um meio padronizado e global para acessar o gerenciador de grupos, sem a necessidade de passar a referência do objeto Group por múltiplas camadas da aplicação.
- **Inicialização Sob Demanda:** A instância do Group só é criada na primeira vez que getInstance() é chamado.
- **Gerenciamento Centralizado:** Garante que todas as operações de grupos utilizem o mesmo estado global e controlem centralmente os relacionamentos entre usuários e grupos.

**Desvantagens e Considerações (Críticas ao Singleton):**

É importante reconhecer as críticas comuns ao padrão Singleton, que foram consideradas:

- **Estado Global:** O Singleton pode introduzir um estado global, o que pode dificultar o rastreamento de dependências e o raciocínio sobre o comportamento do sistema.
- **Acoplamento:** Classes que utilizam o Singleton diretamente através de Group.getInstance() podem se tornar fortemente acopladas à classe Group o que dificulta a substituição ou a modificação do Singleton sem impactá-los.
- **Testabilidade:** Testar unitariamente classes que dependem de um Singleton global pode ser mais desafiador.
- **Integração com JPA:** O uso do padrão Singleton pode conflitar com o sistema de persistência JPA do Spring Boot, especialmente em relação ao gerenciamento de entidades.

**Observação:** No contexto deste projeto e para a entidade Group que é conceitualmente única e gerencia relacionamentos críticos entre usuários, os benefícios de garantir a unicidade e ter um ponto de acesso claro foram considerados preponderantes.

## Conclusão

A implementação da classe Group utilizando o padrão Singleton atendeu ao requisito de garantir uma única instância para o gerenciador de grupos principal da plataforma e fornecer um ponto de acesso global e controlado a ele. A aplicação do padrão Singleton na classe Group demonstra como este padrão pode ser efetivamente utilizado para gerenciar relacionamentos e operações centralizadas em aplicações Spring Boot, garantindo consistência e controle sobre operações de grupos e membros.

## Referências

[1] ELEMAR JR. Padrão de design Singleton: Implementação e Exemplos de Uso. Clube de Estudos, [s.d.]. Disponível em: https://elemarjr.com/clube-de-estudos/artigos/padrao-de-design-singleton-implementacao-e-exemplos-de-uso/. Acesso em: 26 maio 2025.

[2] DEVMEDIA. Padrão de Projeto Singleton em Java. DevMedia, [s.d.]. Disponível em: https://www.devmedia.com.br/padrao-de-projeto-singleton-em-java/26392. Acesso em: 27 maio 2025.

[3] REFACTORING GURU. Singleton. Refactoring.Guru, [s.d.]. Disponível em: https://refactoring.guru/pt-br/design-patterns/singleton. Acesso em: 26 maio 2025.

[4] THIENGO, Vinícius. Padrão de Projeto: Singleton. Thiengo, [s.d.]. Disponível em: https://www.thiengo.com.br/padrao-de-projeto-singleton. Acesso em: 27 maio 2025.

[5] SPRING FRAMEWORK. JPA and Hibernate. Spring.io, [s.d.]. Disponível em: https://docs.spring.io/spring-framework/docs/current/reference/html/data-access.html#orm-jpa. Acesso em: 27 maio 2025.

## Histórico de versão

| Versão | Alteração | Responsável | Data |
| - | - | - | - |
| 1.0 | Elaboração dos códigos e documentação | Gabriel Lima, Mateus Bastos | 22/10/2025 |
