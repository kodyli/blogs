# Galaxy
------------------------------

### The problems that Galaxy project run into
Galaxy is an ERP system including accounts payable system, payroll system, asset management system, and so on.

#### 1. Can we reuse the business code?

The first version of Galaxy is a desktop application and is developed with Silverstream; the second version of Galaxy is a web application and developed with Grails. When we develop an application, usually it includes capturing user's request, validating the data again the business rules, and saving the data to the database. When we switch Silverstream to Grails, the ways to capture user's request, and to save the data to the database will be absolutely different, becaue they are two different framworks. However the way how to validate the data is same, because we still run the same business. When we design the application, can we use [Bridge Design Pattern][1] to devide the code to framework code and business code?

![Bridge Design Pattern](https://refactoring.guru/images/patterns/diagrams/bridge/structure-en-2x.png?id=77e749744fb5375839b1cf1aa1061648)
![Framework code & Business code](../img/cleanArchitecture/framework_business.png)

The business code could become use cases and entities in Clean Architecture; the Framework code will handle the persistence and web controllers. Instead of call the use case implementation directly, we use [DI][2] to inject the use case implementation to framework code? If so, our application will be Independent of Frameworks.

![Clean Architecture](../img/cleanArchitecture/usecase.png)

[SOA][3] also do the same thing. it tries to remove tasks to develope an existing functionalitites, and makes software components reusable between applications. Instead of exposing the use case using standard network protocols, we can use packages to reuse the use case code.
~~~java
//src folder
package com.kody.payroll
package  com.kody.timesheet
~~~


And then we can follow [Use Case Driven Development](./cleanArchitecture/useCaseDriven) to develop our application.

Long term developers will work on the use cases; sort term developers who do not understand the business rules will work on the adapters.

But the data mapping between boundaries will be a lot work.[4]

#### 2. How do we test business rules?

As an ERP system, it has hundreds of use cases, and need to fully test each use case. For right now, when we test a use case, we need to set up data through UI and save it to DB, then test the use case, this is more like a system test not a unit test. And later on, if when need to test it again, the data maybe changed by someone else or the data alread passes the payroll due date. So can we use unit test to test those use case and ignore the UI and the database? Clean arichitecture can help use to achieve this goal, at least in Uncle Bob Martin's conference video.

#### 3. how to decouple Frontend code?

The frontend code is composite of AngularJS code and libraries code, like JqGrid, JQuery Widgets, they all mixed togerther to make the UI work. And now we have a hard time if we want to update AngularJS to Angular, because of Angular does not have scope any more. Can we devide the code to framework code, libraries, and company code, this could be easy for us to update framework, and libraries?

The company code includes all the features the componey required, the features can be implemented by the compay itself, or reuse the third party libraries by using adapter design pattern. After finishing the feature, it can be easily integrated in framework.

![Bridge & Adapter](../img/cleanArchitecture/bridge_adapter_decorater.png)

#### 4. how to handle two field have the same name when handle click on an error message?

[1]: <https://refactoring.guru/design-patterns/bridge> "Bridge Design Pattern"
[2]: <https://en.wikipedia.org/wiki/Dependency_injection> "Dependency Injection"
[3]: <https://www.ibm.com/cloud/learn/soa> "Service-Oriented Architecture"
[4]: <https://www.amazon.com/Hands-Dirty-Clean-Architecture-hands/dp/1839211962> "Get Your Hands Dirty on Clean Architecture"
