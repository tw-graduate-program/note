# Testing MVC Web Controllers

Testing MVC Web Controllers with Spring Boot and @WebMvcTest.

First, we’re going to explore what a web controller actually does so that we can build tests that cover all of its responsibilities. Then, we’re going to find out how to cover each of those responsibilities in a test. 

```yaml
Responsibilities of a Web Controller
1. Listen to HTTP Requests
2. Deserialize Input
3. Validate Input
4. Call the Business Logic
5. Serialize the Output
6. Translate Exceptions
```



### Typical Rest Controller

```java
@RestController
@RequiredArgsConstructor
public class RegisterRestController {
    private final RegisterUseCase registerUseCase;

    @PostMapping("/users/{id}/register")
    public UserResource register(@PathVariable("id") Long id,
                                 @RequestParam("sendWelcomeMail") boolean sendWelcomeMail,
                                 @Valid @RequestBody UserResource userResource){
        User user = User.builder()
          .name(userResource.getName())
          .email(userResource.getEmail()).build();

        Long userId = registerUseCase.registerUser(user, sendWelcomeMail);

        return UserResource.builder()
                .userId(userId)
                .name(user.getName())
                .email(user.getEmail()).build();
    }
}
```



### Verifying Controller Responsibilities with @WebMvcTest
Spring Boot provides the @WebMvcTest annotation to fire up an application context that contains only the beans needed for testing a web controller:

```java
@ExtendWith(SpringExtension.class)
@WebMvcTest(controllers = RegisterRestController.class)
public class RegisterRestControllerTest {
    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private RegisterUseCase registerUseCase;

    @Test
    void should_return...when...() {
      ...
    }
}
```



1. Verifying HTTP Request Matching

Verifying that a controller listens to a certain HTTP request is pretty straightforward. We simply call the `perform()` method of `MockMvc` and provide the URL we want to test:

```java
    //1. Verifying HTTP Request Matching
    @Test
    void verify_http_request_match() throws Exception {
        mockMvc.perform(post("/users/42/register")
                        .contentType(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk());
    }
```



2. Verifying Input Deserialization

To verify that the input is successfully deserialized into Java objects, we have to provide it in the test request. Input can be either the JSON content of the request body (`@RequestBody`), a variable within the URL path (`@PathVariable`), or an HTTP request parameter (`@RequestParam`):

```java
    //2. Verifying Input Deserialization
    @Test
    void verify_input_deserialization() throws Exception {
        // Path Variable
        Long id = 77L;
        // Request Body
        UserResource userResource = new UserResource(id, "kobe", "kobebryant@google.com");

        mockMvc.perform(post("/users/{id}/register", id)
                        .contentType(MediaType.APPLICATION_JSON)
                        .param("sendWelcomeMail", "true")
                        .content(new ObjectMapper().writeValueAsString(userResource)))
                .andExpect(status().isOk());
    }
```



3. Verifying Input Validation

Bean validation is triggered automatically when we add the `@Valid` annotation to a method parameter like we did with the `userResource` parameter in our controller. 

```java
@Builder
@Data
@AllArgsConstructor
@NoArgsConstructor
public class UserResource {

    private Long userId;

    @NotNull
    private String name;

    @NotNull
    private String email;
}
```

```java
    //3. Verifying Input Validation
    void verify_input_validation() throws Exception {
        Long id = 77L;
        UserResource userResource = new UserResource(id, null, "kobebryant@google.com");

        mockMvc.perform(post("/users/{id}/register", id)
                        .contentType(MediaType.APPLICATION_JSON)
                        .param("sendWelcomeMail", "true")
                        .content(new ObjectMapper().writeValueAsString(userResource)))
                .andExpect(status().isBadRequest());
    }
```



4. Verifying Business Logic Calls

To verify this, we can ask the `RegisterUseCase` mock, which has been injected into the application context with the `@MockBean` annotation:

```java
    //4. Verifying Business Logic Calls
    @Test
    void whenValidInput_thenMapsToBusinessModel() throws Exception {
      UserResource user = new UserResource("Zaphod", "zaphod@galaxy.net");
      mockMvc.perform(...);

      ArgumentCaptor<User> userCaptor = ArgumentCaptor.forClass(User.class);
      verify(registerUseCase, times(1)).registerUser(userCaptor.capture(), eq(true));
      assertThat(userCaptor.getValue().getName()).isEqualTo("Zaphod");
      assertThat(userCaptor.getValue().getEmail()).isEqualTo("zaphod@galaxy.net");
    }

```



5. Verifying Output Serialization

After the business logic has been called, we expect the controller to map the result into a JSON string and include it in the HTTP response. In our case, we expect the HTTP response body to contain a valid `UserResource` object in JSON form:

```java
    //5. Verifying Output Serialization
    void verify_output_serialization() throws Exception {
        // given
        Long id = 42L;
        UserResource userResource = new UserResource(id, "kobe", "kobebryant@google.com");

        // when
        MvcResult mvcResult = mockMvc.perform(post("/users/{id}/register", id)
                        .contentType(MediaType.APPLICATION_JSON)
                        .param("sendWelcomeMail", "true")
                        .content(new ObjectMapper().writeValueAsString(userResource)))
                .andExpect(status().isOk())
                .andReturn();

        String actualUserResource = mvcResult.getResponse().getContentAsString();

        // then
        assertThat(actualUserResource).isEqualToIgnoringWhitespace(new ObjectMapper().writeValueAsString(userResource));
    }
```



6. Verifying Exception Handling

If bean validation fails, Spring throws an `MethodArgumentNotValidException`. We handle this exception by mapping Spring’s `FieldError` objects into our own `ErrorResult` data structure. The exception handler causes all controllers to return HTTP status 400 in this case and puts the `ErrorResult` object into the response body as a JSON string.

```java
    //6. Verifying Exception Handling
    void verify_exception_handling() throws Exception {
        // given
        Long id = 42L;
        UserResource userResource = new UserResource(id, null, "kobebryant@google.com");

        // when
        MvcResult mvcResult = mockMvc.perform(post("/users/{id}/register", id)
                        .contentType(MediaType.APPLICATION_JSON)
                        .param("sendWelcomeMail", "true")
                        .content(new ObjectMapper().writeValueAsString(userResource)))
                .andExpect(status().isBadRequest())
                .andReturn();

        String actualResponse = mvcResult.getResponse().getContentAsString();
        ControllerExceptionHandler.ErrorResult errorResult = new ControllerExceptionHandler.ErrorResult("name", "must not be null");

        // then
        assertThat(actualResponse).isEqualToIgnoringWhitespace(new ObjectMapper().writeValueAsString(errorResult));
    }
```



```java
@ExtendWith(value = SpringExtension.class)
@WebMvcTest(value = RegisterRestController.class)
public class RegisterControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private RegisterUseCase registerUseCase;

    @Autowired
    private ObjectMapper objectMapper;

    /**
     * 1. verify HTTP Request Matching
     * 2. verify Input Deserialization
     * 3. verify Input Validation
     * 4. verify Output Serialization
     */
    @Test
    void should_return_200_when_input_valid() throws Exception {
        // given
        Long id = 42L;
        UserResource user = new UserResource(id, "kobe", "kobebryant@google.com");

        // when
        MvcResult mvcResult = mockMvc.perform(post("/users/{id}/register", id)
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(user))
                .param("sendWelcomeMail", "true"))
                .andExpect(status().isOk())
                .andReturn();

        String actualResponse = mvcResult.getResponse().getContentAsString();

        // then
        assertThat(actualResponse)
          .isEqualToIgnoringWhitespace(objectMapper.writeValueAsString(user));
    }

    /**
     * 1. verify HTTP Request Matching
     * 2. verify Input Deserialization
     * 3. verify Input Validation
     * 4. verify Exception Handling
     */
    @Test
    void should_return_400_when_input_invalid() throws Exception {
        Long id = 42L;
        UserResource user = new UserResource(id, null, "kobebryant@google.com");

        MvcResult mvcResult = mockMvc.perform(post("/users/{id}/register")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(user))
                .param("sendWelcomeMail", "true"))
                .andExpect(status().isBadRequest())
                .andReturn();

        String actualResponse = mvcResult.getResponse().getContentAsString();
        ControllerExceptionHandler.ErrorResult errorResult = 
          new ControllerExceptionHandler.ErrorResult("name", "must not be null");

        assertThat(actualResponse).isEqualToIgnoringWhitespace(objectMapper.writeValueAsString(errorResult));
    }

    @Test
    void verify_business_logic_be_called() throws Exception {
        // given
        Long id = 42L;
        boolean sendWelcomeMail = true;
        User user = User.builder().name("kobe").email("kobebryant@google.com").build();
        UserResource userResource = new UserResource(id, "kobe", "kobebryant@google.com");

        // when
        when(registerUseCase.registerUser(user, sendWelcomeMail)).thenReturn(id);
        MvcResult mvcResult = mockMvc.perform(post("/users/{id}/register", id)
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(objectMapper.writeValueAsString(userResource))
                        .param("sendWelcomeMail", "true"))
                .andExpect(status().isOk())
                .andReturn();

        String actualResponse = mvcResult.getResponse().getContentAsString();

        // then
        assertThat(actualResponse).isEqualToIgnoringWhitespace(objectMapper.writeValueAsString(userResource));
    }
}

```

