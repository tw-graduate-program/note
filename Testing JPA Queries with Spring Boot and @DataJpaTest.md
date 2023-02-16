# Testing JPA Queries with Spring Boot and @DataJpaTest

Aside from unit tests, integration tests play a vital role in producing quality software. A special kind of integration test deals with **the integration between our code and the database.**



#### @DataJpaTest

With the `@DataJpaTest` annotation, Spring Boot provides a convenient way to set up an environment with an embedded database to test our database queries against.



## Dependencies

Aside from the usual Spring Boot dependencies, we’re using **JUnit Jupiter** as our testing framework and **H2** as an in-memory database.

```xml
dependencies {
  compile('org.springframework.boot:spring-boot-starter-data-jpa')
  compile('org.springframework.boot:spring-boot-starter-web')
  runtime('com.h2database:h2')
  testCompile('org.springframework.boot:spring-boot-starter-test')
  testCompile('org.junit.jupiter:junit-jupiter-engine:5.2.0')
}
```



## Responsibilities

**What to Test?**

What we need to test? Let’s consider a Spring Data repository responsible for `UserEntity` objects:

```java
interface UserRepository extends CrudRepository<UserEntity, Long> {
  // query methods
}
```



## Inferred Queries

```java
UserEntity findByName(String name);
```

Don’t need to tell Spring Data what to do, since it automatically infers the SQL query from the name of the method name.



## Custom JPQL Queries with @Query

If queries become more complex, it makes sense to provide a custom JPQL query:

```java
@Query("select u from UserEntity u where u.name = :name")
UserEntity findByNameCustomQuery(@Param("name") String name);
```



## Native Queries with @Query

Another way is to use a *native query*: (原生查询)

```java
@Query(
  value = "select * from user as u where u.name = :name",
  nativeQuery = true)
UserEntity findByNameNativeQuery(@Param("name") String name);
```

It’s important to note that **neither Hibernate nor Spring Data validate native queries at startup**. Since the query may contain database-specific SQL, there’s no way Spring Data or Hibernate can know what to check for.



## Steps

To write test code for the `UserRepository` interface with the `findByNameNativeQuery` method, you can follow these steps:

1. Create a test class for the `UserRepository`. The test class should have a `@RunWith` annotation that specifies the JUnit runner, and a `@DataJpaTest` annotation to configure the test with an in-memory database.

```java
@RunWith(SpringRunner.class)
@DataJpaTest
public class UserRepositoryTest {
    // ...
}
```

2. In the test class, use the `@Autowired` annotation to inject an instance of the `UserRepository` that you want to test.

```java
@Autowired
private UserRepository userRepository;
```

3. Write test methods to test the `findByNameNativeQuery` method. Each test method should use the `userRepository` to call the method and verify the results using assertions. You should test for both the positive case (when a user is found) and the negative case (when no user is found).

```java
@Test
public void testFindByNameNativeQuery() {
    // Arrange
    String name = "John";

    // Act
    User user = userRepository.findByNameNativeQuery(name);

    // Assert
    assertNotNull(user);
    assertEquals(name, user.getName());
}

@Test
public void testFindByNameNativeQueryNotFound() {
    // Arrange
    String name = "Non-existing user";

    // Act
    User user = userRepository.findByNameNativeQuery(name);

    // Assert
    assertNull(user);
}
```

In the first test method, we are searching for a user with the name "John", and we expect to find one. In the second test method, we are searching for a user that doesn't exist, and we expect to get a null value.

4. Use the `@Before` annotation to set up any necessary data in the in-memory database before running each test method. For example, you can use the `userRepository` to save a user with a specific name.

```java
@Before
public void setUp() {
    User user = new User();
    user.setName("John");
    userRepository.save(user);
}
```

This will create a new user with the name "John" in the in-memory database before each test method is run.

With these steps, you can write test code for the `UserRepository` interface with the `findByNameNativeQuery` method.



```java
@ExtendWith(SpringExtension.class)
@DataJpaTest
@TestExecutionListeners({
        DependencyInjectionTestExecutionListener.class,
        TransactionDbUnitTestExecutionListener.class
})
class SpringDbUnitTest {

  @Autowired
  private UserRepository userRepository;

  @Test
  @DatabaseSetup("createUser.xml")
  void whenInitializedByDbUnit_thenFindsByName() {
    UserEntity user = userRepository.findByName("Zaphod Beeblebrox");
    assertThat(user).isNotNull();
  }
}
```

