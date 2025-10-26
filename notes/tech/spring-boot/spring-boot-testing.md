---
date: 2025-10-16
---

# Testing in Spring Boot

## Setup

Spring Boot Starter Test includes JUnit, Mockito, Hamcrest, and more.

- **JUnit 5 (Jupiter)** - writing and running tests.
- **Mockito** - mocking dependencies.
- **Spring Test** - utilities for bootstrapping the test context.

Gradle

```bash
testImplementation 'org.springframework.boot:spring-boot-starter-test'
```

Maven

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

## Unit Test

**TODO: Rewrite this example. Not good enough**

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    // Mocks
    @Mock
    private UserRepository userRepository;
    @InjectMocks
    private UserService userService;

    @Test
    void whenSaveUser() {

        // Arrange
        User user = new User();
        user.setName("John Doe");
        user.setEmail("johndoe@example.com");

        when(userRepository.save(any(User.class))).thenReturn(user);

        // Act
        User savedUser = userService.saveUser(user);

        // Assert
        assertNotNull(savedUser);
        assertEquals("John Doe", savedUser.getName());
        verify(userRepository, times(1)).save(user);
    }

    @Test
    void testGetUserByEmail() {
        // Arrange
        String email = "test@example.com";
        User user = new User();
        user.setEmail(email);

        when(userRepository.findByEmail(email))
            .thenReturn(Optional.of(user));

        // Act
        Optional<User> result = userService.getUserByEmail(email);        assertTrue(result.isPresent());

        // Assert
        assertEquals(email, result.get().getEmail());
        verify(userRepository, times(1)).findByEmail(email);
    }
}
```

### Parameterised Tests

## Integration Test

**TODO: Rewrite this example. Autowired not desirable anymore**

```java

@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
@AutoConfigureMockMvc
class UserControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private ObjectMapper objectMapper;

    @Test
    void testCreateUser() throws Exception {
        User user = new User();
        user.setName("John Doe");
        user.setEmail("johndoe@example.com");
        mockMvc.perform(post("/api/users")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(objectMapper.writeValueAsString(user)))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.name").value("John Doe"))
            .andExpect(jsonPath("$.email").value("johndoe@example.com"));
    }
}
```

## Repository Test

You can test the repository using `@DataJpaTest` and an im-memory database.

```java

@DataJpaTest
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    void testFindByEmail() {
        User user = new User();
        user.setName("John");
        user.setEmail("john@example.com");

        userRepository.save(user);

        // Act
        // it is important to retrieve the data for verifying
        Optional<User> result = userRepository.findByEmail("john@example.com");
        assertTrue(result.isPresent());
        assertEquals("john@example.com", result.get().getEmail());
    }
}
```

## Other Spring Boot Annotations

- `@SpringBootTest`: Loads the full application context (avoid unless necessarily)
- `@WebMvcTest`: Tests only the controller layer
- `@DataJpaTest`: Tests repository layer with in-memory DB
- `@MockBean`: Injects mock beans into the Spring context
- `@AutoConfigureMockMvc`: Auto-configures `MockMvc` for controller testing
- `@TestConfiguration`: Defines additional beans only for the test context

Sometimes you want to test a controller **without hitting the actual service layer**. You can achieve this using `@MockBean`.

```java
@WebMvcTest(UserController.class)
class UserControllerWithMockTest {

    private MockMvc mockMvc;

    @MockBean
    private UserService userService;

    @Test
    void testGetUser() throws Exception {
        User user = new User();
        user.setEmail("mock@example.com");
        user.setName("Mock User");

        when(userService.getUserByEmail("mock@example.com"))
            .thenReturn(Optional.of(user));

        mockMvc.perform(get("/api/users/mock@example.com"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.name").value("Mock User"));
    }
}
```

## Verify vs Assert

## Best Practices

**1. Keep Unit and Integration Tests Separate**
Use packages or naming conventions (`*Test` vs `*IT`) to distinguish them.

**2. Use Mocking Wisely**
Only mock dependencies, not the class under test.

**3. Use In-Memory Databases for Repositories**
H2 is perfect for fast and isolated repository testing.

**4. Test Real Scenarios, Not Just Happy Paths**
Include negative tests (e.g., invalid inputs, missing data).

**5. Automate Testing**
Integrate tests into your CI/CD pipeline (GitHub Actions, Jenkins, etc.).

**6. Avoid Overusing `@SpringBootTest`**
It’s slow; use `@WebMvcTest` or `@DataJpaTest` when possible.
