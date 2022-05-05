# [Clean Architecture][1]
----------------------

![Clean Architecture](https://blog.cleancoder.com/uncle-bob/images/2012-08-13-the-clean-architecture/CleanArchitecture.jpg)

## The Dependency Rule

The overriding rule that makes this architecture work is The Dependency Rule. This rule says that **source code dependencies** can only point **inwards**. Nothing in an inner circle can know anything at all about something in an outer circle.

### Clean Architecture can produce Systems that are:

1. **Independent of Frameworks.** The architecture does not depend on the existence of some library of feature laden software. This allows you to use such frameworks as tools, rather than having to cram your system into their limited constraints.
2. **Testable.** The business rules can be tested without the UI, Database, Web Server, or any other external element.
3. **Independent of UI.** The UI can change easily, without changing the rest of the system. A Web UI could be replaced with a console UI, for example, without changing the business rules.
4. **Independent of Database.** You can swap out Oracle or SQL Server, for Mongo, BigTable, CouchDB, or something else. Your business rules are not bound to the database.
5. **Independent of any external agency.** In fact your business rules simply don’t know anything at all about the outside world.

<iframe width="100%" height="400" src="https://www.youtube.com/embed/o_TH-Y78tt4?t=600" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


[1]: <https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html> "The Clean Architecture"