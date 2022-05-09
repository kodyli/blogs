# [Clean Architecture][1]
----------------------

"Clean Architecture" is a software architectural pattern conined by Uncle Bob Martin in his book Clean Architecture. It is a standard, all design architectures meet the standard are clean architectures, like [hexagonal architecture][3], onion architecture, [BCE][2] and so on.

The main purpose of Clean Architecture is to allow developing a system that can adapt to changing technology. While the internet might move from desktop to web, or from web to virtual assistant, the core business remains the same.

![Clean Architecture](https://blog.cleancoder.com/uncle-bob/images/2012-08-13-the-clean-architecture/CleanArchitecture.jpg)

### 1. The Dependency Rule

The overriding rule that makes this architecture work is The Dependency Rule. This rule says that **source code dependencies** can only point **inwards**. Nothing in an inner circle can know anything at all about something in an outer circle.

### 2. The Characteristices

1. **Independent of Frameworks.** The architecture does not depend on the existence of some library of feature laden software. This allows you to use such frameworks as tools, rather than having to cram your system into their limited constraints.
2. **Testable.** The business rules can be tested without the UI, Database, Web Server, or any other external element.
3. **Independent of UI.** The UI can change easily, without changing the rest of the system. A Web UI could be replaced with a console UI, for example, without changing the business rules.
4. **Independent of Database.** You can swap out Oracle or SQL Server, for Mongo, BigTable, CouchDB, or something else. Your business rules are not bound to the database.
5. **Independent of any external agency.** In fact your business rules simply don’t know anything at all about the outside world.

<iframe width="100%" height="400" src="https://www.youtube.com/embed/o_TH-Y78tt4?t=600" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


[1]: <https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html> "The Clean Architecture"
[2]: <https://www.amazon.com/Object-Oriented-Software-Engineering-Approach/dp/0201544350> "Boundary Control Entity"
[3]: <https://www.amazon.com/Hands-Dirty-Clean-Architecture-hands/dp/1839211962> "Get Your Hands Dirty on Clean Architecture"