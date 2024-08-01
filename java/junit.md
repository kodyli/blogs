### [JUnit 5](https://nipafx.dev/junit-5-architecture-jupiter/#junit-5)

The JUnit 5 architecture promotes a better separation of concerns and provides clear APIs for testers (Jupiter) and tools (Platform).


![JUnit 5](https://nipafx.dev/static/b862798a258987401978b6731416a360/f11bc/junit-5-architecture-diagram.webp)

Architecture:

Separating Concerns:

1. an API to write tests against
2. a mechanism to discover and run tests


JUnit 5's architecture is the result of that distinction. These are some of its artifacts:

    junit-jupiter-api (1):

The API against which developers write tests. Contains all the annotations, assertions, etc. that we saw when we discussed JUnit 5's basics.

    junit-jupiter-engine (2a):

An implementation of the junit-platform-engine API that runs JUnit 5 tests.

    junit-vintage-engine (2a):

An implementation of the junit-platform-engine API that runs tests written with JUnit 3 or 4. Here, the JUnit 4 artifact junit-4.12 acts as the API the developer implements her tests against (1) but also contains the main functionality of how to run the tests. The engine could be seen as an adapter of JUnit 3/4 for version 5.

    junit-platform-engine (2c):

The API all test engines have to implement, so they are accessible in a uniform way. Engines might run typical JUnit tests but alternatively implementations could run tests written with TestNG, Spock, Cucumber, etc.

    junit-platform-launcher (2b):

Uses the ServiceLoader to discover test engine implementations and to orchestrate their execution. It provides an API to IDEs and build tools so they can interact with test execution, e.g. by launching individual tests and showing their results.