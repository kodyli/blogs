# Mockito Cheat Sheet
Mockito is a popular Java mocking framework that provides methods for creating mock objects and behavior verification in tests. Here's a list of some commonly used methods in Mockito:

## Methods:

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

## Annotations:

1. **`@Mock`**
   - **Purpose:** Indicate a field to be mocked.

2. **`@Spy`**
   - **Purpose:** Indicate a field to be spied (partial mock).

3. **`@Captor`**
   - **Purpose:** Indicate a field to capture method arguments.

4. **`@InjectMocks`**
   - **Purpose:** Automatically inject mock or spy objects into the target object being tested.

## Additional:

8. **`ArgumentCaptor.forClass(Class<T> argumentClass)`**
   - **Purpose:** Capture arguments passed to a method.

9. **`InOrder inOrder(firstMock, secondMock)`**
   - **Purpose:** Verify that interactions with mocks occurred in a specific order.

10. **Timeouts:**
    - **`verify(mock, timeout(milliseconds))`**: Verify that a method was called within a specified time.

11. **Answering:**
    - **`Answer<T>`**: Allow custom behavior when a mocked method is called.