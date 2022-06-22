# [New Design](http://www.plantuml.com/plantuml/svg/lLTBR-Cs43utlsAG7ce3MQEFea7TRRjR9m62Fh3OR0u5Sg75iSqDHMh9aRtwzoKfYf8jArQDeZuaaZZVDu_vXgBUM6A6obnUN2prPxXSI86ABk7Bkvkxcnz_k60PQGFnhuNBTwmoMihB2rGS72B2xWpBSQVDiqE-lLvStrvVWFjbMf4pR_vBh4bk5Q69J6_vnz9IZehi8bbTxg4jcgt8y2NeGssoZpDOQ2j8c-gXm_27PlmsBwIk4D_vTQ7LAQOYshqVpPXuKg52dNv7er3wyjujlbxyTX3kfn8j1SexeLuAydrjMWUvrANYp5HGMbmmYsC87clJcW0L2Qg02qj6P7K0D_5cpwAewnuCulNUEaQ7mQ3nVX1asvGntMWBVa2JEdAQhakIkX65_UgNhZRdiEgMhQUooPDweW45-e8lT9upvo2h_p-fgNSZjeomuoOaeZGX6B7UKBW5YDWvS4OCbevDg7LS-x3n-CWjcRMGimg4sg0H1BJ1f9sm6b6aWMsru3LZpeh2CS8IQOUeGB8AZGLFwGmvRb7g8YATgHGPyyM3JwN8dcLr5IoTDoQjTgupMNAqmVVZlRjVQPC7m2mAWeSKkmAN6EeTf_bahMF2qq2HoXZHuM6_Zl7tqBWWmvHbMKZ4aR4rUkvesOqo_YAx3pDMT9vA-pCetC6umNH2oNIfgFfhQQxIOFcR9-NojXcJVoDpIt6A5fN4TFEXAj3PfMboiBe8Wpcdtutlz9zrfw-WjDuis_W6M3AbL_pi0EAidvrnNqGWyhmajlI1QPLXIu8SR9bbQEsgb4uPXumxPyiKBD6P6NJChDQe9Tz0OM7BfE1UJzvEQVAcsOQfDGOj7l9PdIDjV9121MZCOQY9KIifdwvryynrEBLbdgvYZvkzRtAyXrCOOaoQiKY9f45EXr2nxVFc8aOHHs8oHYxJCH4rX14tX4cwcwUh656Z-qbNcMCyzu0jYkD0kte3sNkRppkFOeJZtksJiUCqaW-4Cy7mP3H3x_tsqoZekIuxZeQ7fr_quOFFl5qQquINIYznrQ_y3HQeEAgiWdlr4JDFycyYJuUX8C0Keq0xQLLpTII9qTfu46ejaRJghJnQ10OhbeMYa8RCe1Su2pjovgFIIvzJ3xcid4cp7Tl60N2qOgsWO4HerEpCEk1dp4h2IPopQN_3ZBPTgeJlKjKxE6kvHXVjNXKbHRYlUtsiMFgaqThEmgm7NJ05bIx18eAWNzDzQVVlkVQt9slKJInciVSYM7UsyEb9OBjrhzD6EmBMsM3NxJOv3EpBTGvICvx6BEsm8-ViNe4s74qwB3OdGJJrAzcBiU7yoa9X_19LlbCbzbxztymWPyyOvDjaStYSECHyEwjRg0ZDYjMVL0KpB4VoSLL53_7T37P6UBqgGK1eoG9fxpaTEH2OMPT-VmHXVVhD0QEdg-zZo37l036bHDjMmeBTw59oMBckwjs8-KiJrM_OqTQ-VdMqjGldhzxwNZSTRZLWHtJuh2FXGgij6h9WK8PPP39yGYoPnN3X5Z9jE92E-vWTKHAEuulI3ig1phlV1ysiNYLzfYFHz7WppCacc87Y7twTk2uSFleFs_75_zcCWQB4DuWhIghFsINf-ejilGz3voUwFCzDrqKaYf_uZhdZvmnsZiS-uj-WucKk_m40)
------------------------------------------
![New Design](../img/cleanArchitecture/design.svg)
### Framework needs to do:
1. Mapping user input to Command object, and construct the Use Case Request.
2. Run the use case.
3. Implements persistence layer and manages the database transactions.
4. return data to client.

### Kernel needs to do:
1. Receive requests from framework.
2. Do input validation and business validation.
3. Execute the requst.
4. return data to framework.

When we need a new framework, we could only implements the same codes in the new framework, and reuse the kernel. Because the framework codes do not include business rules and the most works are transfering data, run use case code from the kernel, so new junior team memenbers can still easily implement the code.

~~~md
@startuml

box "Client" #FDFD96
actor actor as "Payroll"
end box

box "Framework" #A7C7E7
    control controller as "EnterPayrollAdjustmentsController"
    control service as "EnterPayrollAdjustmentsService"
    control repositoryService as "LoadEmployeePositionAdjustmentsService"
    control positionRepositoryService as "LoadEmployeePositionsService"
end box
box "Kernel" #FFC0CB
    control request as "FindEmployeePositionAdjustmentsRequest"
    boundary useCase as "FindEmployeePositionAdjustmentsUseCase"
    entity district as "District"
    entity payroll as "payroll"
    entity employee as "Employee"
    boundary repository as "LoadEmployeePositionAdjustmentsRepository"
    boundary positionRepository as "LoadEmployeePositionsRepository"
end box

actor -> controller : findEmployeePayrollAdjustments(\n\tEnterPayrollAdjustmentsFindCommand command\n)
activate controller
    note right
      Command: data binding
      The command class may inherit the request class
      to avoid data mapping between layers and to develop
      application quickly, and it also includes the UI information.
    end note
    controller -> service: findEmployeePayrollAdjustments(command)
    activate service
        note right
          Service: transaction management
        end note
        service -> request: request = new FindEmployeePositionAdjustmentsRequest(\ncountyNbr, districtNbr,\n fiscalYear, payrollCyle, payrollType,\n employeeNbr)
            note right
               Data Mapping, use rich constructor.
               Making the request class immutable, once constructed successfully,
               we can be sure that the state is valid and cannot be changed to 
               something invalid.
            end note
        activate request
            request -> district: district = new District(countyNbr, districtNbr) 
            request -> request: setDistrict(district)
            request -> payroll: payroll = new Payroll(fiscalYear, payrollCyle, payrollType)
            request -> request: setPayroll(payroll)
            request -> employee: employee = new Employee(employeeNbr)
            request -> request: setEmployee(employee)
        deactivate request
            service -> repositoryService: repository = new LoadEmployeePositionAdjustmentsService()
            note right
                LoadEmployeePositionAdjustmentsService implements
                LoadEmployeePositionAdjustmentsRepository;
                LoadEmployeePositionsService implements
                LoadEmployeePositionsRepository
            end note
            service -> positionRepositoryService: positionRepository = new LoadEmployeePositionsService()
            note left
                Dependency Injection
            end note
            service -> useCase : setRepositories(repository, positionRepository)
            note right
                Singleton
                Use a factory to create a use case object
                by using the singleton design pattern.
            end note
            service -> useCase : execute(request)
        activate useCase
            useCase -> useCase : validate(request)
            note right
                Input validation, and business validation.
                Can you see it? Can you use it? Can you do it?
            end note
            useCase -> request: district = getDistrict()
            useCase -> request: payroll = getPayroll()
            useCase -> request: employee = getEmployee()
            useCase -> positionRepository: loadPositions(employee)
            note left
               In order to avoid developer changing employee's property value,
               1. make the Employee class immutable
               2. use interface as parameter type
               3. write unit tests to make sure the argument is not changed.
               4. make development rules developers should follow.
            end note
            activate positionRepository
            useCase <- positionRepository: Collection<Position> positions
            note left
               Data Mapping, use rich constructor.
              Converts relational database data record to objects.
            end note
            deactivate positionRepository
            useCase -> repository: loadAdjustments(district, employee, payroll)
            activate repository
            useCase <- repository: Collection<PayrollAdjustment> payrollAdjustments
            note left
               Data Mapping, use rich constructor.
	           Converts relational database data record to objects.
            end note
            deactivate repository
            service <- useCase : Collection<PayrollAdjustment> payrollAdjustments
        deactivate useCase
    controller <- service: Collection<PayrollAdjustment> payrollAdjustments
    deactivate service
actor <- controller : Collection<PayrollAdjustment> payrollAdjustments
deactivate controller
@enduml
~~~