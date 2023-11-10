# Unit Test
- [1. JUnit](#section-1)
- [2. Mockito](#section-2)

Unit testing is a fundamental practice in software development that ensures individual components of a codebase function as expected. JUnit, a widely adopted **testing framework** for Java, provides a robust and standardized approach to writing and executing unit tests. When combined with Mockito, a powerful **mocking framework**, developers can create isolated test environments, simulate dependencies, and verify interactions between components.

<a name="section-1"></a>
## JUnit
JUnit is a widely used **testing framework** for Java that plays a crucial role in ensuring the reliability and correctness of Java applications. 

### Basics:
1. **Annotations:**
   - `@Test`: Denotes a test method.
   - `@Before`: Executed before each test method.
   - `@After`: Executed after each test method.
   - `@BeforeClass`: Executed once before any test methods in the class.
   - `@AfterClass`: Executed once after all test methods in the class.
   
### Assertions:
2. **Assertions:**
   - `assertTrue(condition)`: Checks if the condition is true.
   - `assertFalse(condition)`: Checks if the condition is false.
   - `assertEquals(expected, actual)`: Checks if the values are equal.
   - `assertNotEquals(unexpected, actual)`: Checks if the values are not equal.
   - `assertNull(object)`: Checks if the object is null.
   - `assertNotNull(object)`: Checks if the object is not null.
   - `assertSame(expected, actual)`: Checks if the objects refer to the same instance.
   - `assertNotSame(unexpected, actual)`: Checks if the objects do not refer to the same instance.
   
### Exception Handling:
3. **Exception Handling:**
   - `@Test(expected = Exception.class)`: Expects a specific exception in a test.
   - `assertThrows(Exception.class, () -> methodCall)`: Asserts that a specific exception is thrown.
   - `assertDoesNotThrow(() -> methodCall)`: Asserts that no exception is thrown.
   
### Parameterized Tests:
4. **Parameterized Tests:**
   - `@RunWith(Parameterized.class)`: Enables parameterized tests.
   - `@Parameters`: Provides data for parameterized tests.
   - `@Parameter(index)` and `@Parameterized.Parameters(name)`: Define parameters and their names.
   - `ParameterizedTest`: Annotation for parameterized tests.
   
### Test Suites:
5. **Test Suites:**
   - `@RunWith(Suite.class)`: Runs multiple test classes.
   - `@Suite.SuiteClasses({TestClass1.class, TestClass2.class})`: Lists the test classes in the suite.
   
### Assume:
6. **Assume:**
   - `assumeTrue(condition)`: Skips a test if the condition is false.
   - `assumeFalse(condition)`: Skips a test if the condition is true.

### Timeout and Performance Testing:
7. **Timeout and Performance Testing:**
   - `@Test(timeout = milliseconds)`: Sets a timeout for a test.
   - `@RepeatedTest(n)`: Repeats a test a specified number of times.
   - `@Disabled`: Disables a test.

### Rules:
8. **Rules:**
   - `@Rule`: Annotation for test rules.
   - `TestRule` interface and built-in rules like `TemporaryFolder` and `ExpectedException`.

### Mocking:
9. **Mocking:**
   - Integration with mocking frameworks like **Mockito** for more advanced mocking capabilities.

#### Miscellaneous:
10. **Miscellaneous:**
    - `@DisplayName`: Provides a custom name for a test method.
    - `@Tag`: Adds tags to categorize tests.
    - `@Disabled`: Disables a test.

<a name="section-2"></a>
## Mockito
Mockito is a popular Java **mocking framework** that provides methods for creating mock objects and behavior verification in tests. Here's a list of some commonly used methods in Mockito:

### Methods:
1. **Mock Creation:**
   - `mock(Class<T> classToMock)`: Create a mock object for the specified class.
   - `mock(Class<T> classToMock, String name)`: Create a named mock object.
2. **Stubbing:**
   - `when(methodCall)`: Stub method calls on mocks.
   - `thenReturn(value)`: Specify the return value.
   - `thenThrow(exception)`: Specify an exception to be thrown.
   - `doReturn(value).when(mock).method()`: Alternative for specifying return values.
   - `doThrow(exception).when(mock).method()`: Alternative for specifying thrown exceptions.
   - `thenCallRealMethod()`: Execute the real method on a spy.
3. **Stubbing `void` methods:**
   - `doNothing().when(mock).voidMethod()`: Stub a void method with no side effects.
   - `doThrow(exception).when(mock).voidMethod()`: Stub a void method to throw an exception.
   - `doAnswer(answer).when(mock).voidMethod()`: Stub a void method with custom behavior.
   - `doCallRealMethod().when(mock).voidMethod()`: Stub a void method to call the real method.
4. **Verification:**
   - `verify(mock)`: Verify method calls on mocks.
   - `verify(mock, times(n))`: Verify the number of method calls.
   - `verify(mock, atLeastOnce())`: Verify that a method was called at least once.
   - `verify(mock, never())`: Verify that a method was never called.
   - `verifyNoMoreInteractions(mock)`: Verify that no other methods were called.
5. **Argument Matchers:**
   - `any()`: Match any argument of the specified type.
   - `eq(value)`: Match the specified value.
   - `anyString()`, `anyInt()`, `anyObject()`: Match any string, integer, or object.
   - `isNull()`, `isNotNull()`: Match null or non-null values.
   - `argThat(Matcher<T> matcher)`: Match an argument based on a custom matcher.
6. **Spying:**
   - `spy(object)`: Create a spy (partial mock) of a real object.
7. **Resetting Mocks:**
   - `reset(mock)`: Reset the mock, clearing all stubbing and recorded behavior.
   
### Additional:
8. **`ArgumentCaptor.forClass(Class<T> argumentClass)`**
   - **Purpose:** Capture arguments passed to a method.
9. **`InOrder inOrder(firstMock, secondMock)`**
   - **Purpose:** Verify that interactions with mocks occurred in a specific order.
10. **Timeouts:**
    - **`verify(mock, timeout(milliseconds))`**: Verify that a method was called within a specified time.
11. **Answering:**
    - **`Answer<T>`**: Allow custom behavior when a mocked method is called.

### Annotations:
12. **`@Mock`**
   - **Purpose:** Indicate a field to be mocked.
13. **`@Spy`**
   - **Purpose:** Indicate a field to be spied (partial mock).
14. **`@Captor`**
   - **Purpose:** Indicate a field to capture method arguments.
15. **`@InjectMocks`**
   - **Purpose:** Automatically inject mock or spy objects into the target object being tested.
