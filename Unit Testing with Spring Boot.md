# Unit Testing with Spring Boot

1. Create Spring Beans
2. The usage of Mockito & AssertJ



## Dependencies

```xml
dependencies{
  compileOnly('org.projectlombok:lombok')
  testCompile('org.springframework.boot:spring-boot-starter-test')
  testCompile 'org.junit.jupiter:junit-jupiter-engine:5.2.0'
  testCompile('org.mockito:mockito-junit-jupiter:2.23.0')
}
```

JUnit Jupiter (JUnit 5), Mockito, and AssertJ.

Mockito and AssertJ are automatically imported with the `spring-boot-starter-test` dependency.



## Don’t Use Spring in Unit Tests

A good unit test only takes **milliseconds**. Otherwise it hinders the “test / code / test” flow promoted by the idea of Test-Driven Development (TDD).

Is due to the `@SpringBootRun` telling Spring Boot to **set up a whole Spring Boot application context**.



## Creating a Testable Spring Bean

### Field Injection is Evil

```java
@Service
public class RegisterUseCase {

  @Autowired
  private UserRepository userRepository;

  public User registerUser(User user) {
    return userRepository.save(user);
  }
}
```

This class **cannot be unit tested without Spring** because it provides no way to pass in a `UserRepository` instance.



## Providing a Constructor

**Actually let’s not use the `@Autowired` annotation at all:**

```java
@Service
public class UserService {

    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public User registerUser(User user){
        user.setRegisterDate(LocalDateTime.now());
        return userRepository.save(user);
    }
}
```

This version allows [constructor injection](https://reflectoring.io/constructor-injection) by providing a constructor that allows to pass in a `UserRepository` instance.

Spring will automatically use this constructor to instantiate a `UserRepository` object when creating the production **application context**.



*There’s a piece missing, yet, and that is how to mock away the `UserRepository` instance our class under test depends on, because we don’t want to rely on the real thing, which probably needs a connection to a database.

## Using Mockito to Mock Dependencies

Standard mocking library nowadays is Mockito. It provides at least two ways to create a mocked `UserRepository`:

#### Mocking Dependencies with Plain Mockito

1. The first way is to just use Mockito programmatically:

```java
private UserRepository userRepository = Mockito.mock(UserRepository.class);
```

Attention: **By default, it will do nothing when a method is called and return `null` if the method has a return value**.

So, we have to tell Mockito to return something when `userRepository.save()` is called. We do this with the static `when` method:

```java
when(userRepository.save(any())).thenReturn(user);
```

This will make `userRepository.save()` return the same user object that is passed into the method.



#### Mocking Dependencies with Mockito’s @Mock Annotation

2. An alternative way of creating mock objects is Mockito’s `@Mock` annotation in combination with the `MockitoExtension` for JUnit Jupiter:

```java
@ExtendWith(value = MockitoExtension.class)
public class UserServiceTest {

    @Mock
    private UserRepository userRepository;

    private UserService userService;

    @BeforeEach
    void setUp() {
        userService = new UserService(userRepository);
    }

    @Test
    void should_return_user_when_save_success() {
        ...
    }
}
```

The `@Mock` annotation specifies the fields in which Mockito should inject mock objects. 

The `@MockitoExtension` tells Mockito to evaluate those `@Mock` annotations because JUnit does not do this automatically.

**The result is the same as if calling `Mockito.mock()` manually**



## Creating Readable Assertions with AssertJ

Another library that comes automatically with the Spring Boot test support is **AssertJ**.

```java
@ExtendWith(value = MockitoExtension.class)
public class UserServiceTest {

    @Mock
    private UserRepository userRepository;

    private UserService userService;

    @BeforeEach
    void setUp() {
        userService = new UserService(userRepository);
    }

    @Test
    void should_return_user_when_save_success() {
        // given
        User user = User.builder().name("kobe").email("kobebryant@google.com").build();

        // when
        when(userRepository.save(any())).thenReturn(user);

        // then
        // Call the registerUser() method
        User actualUser = userService.registerUser(user);
        // Verify
        assertThat(actualUser).isEqualTo(user);
    }
}
```



Attention: The [Spring Boot Test Starter](https://search.maven.org/search?q=g:org.springframework.boot AND a:spring-boot-starter-test&core=gav) comes with **Mockito and AssertJ** as testing libraries.