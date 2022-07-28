~~~md
@startuml

class AdjustmentCode {}
class WorkersCompensationAdjustmentCode {}
class WorkersCompensationOffsetAdjustmentCode {}
AdjustmentCode <|-- WorkersCompensationAdjustmentCode
AdjustmentCode <|-- WorkersCompensationOffsetAdjustmentCode

class Position {}
class NonretirementPosition {}
class RetirmentPosition {}
class PersPosition {}
class StrsPosition {}
Position <|-- NonretirementPosition
Position <|-- RetirmentPosition
RetirmentPosition <|-- PersPosition
RetirmentPosition <|-- StrsPosition

class FundingLine {}

class PayrollAdjustment {
  + Position position
  + FundingLine fundingLine
  + AdjustmentCode adjustmentCode
  + double amount
  + double unit
}

PayrollAdjustment o-- Position
PayrollAdjustment o-- FundingLine
PayrollAdjustment o-- AdjustmentCode

@enduml

@startuml
class ModifyPayrollAdjustmentsUseCase {
  # void validate(PayrollAdjustment adjustment, Errors errors)
  - void validateUnit(Errors errors, AdjustmentCode adjCode, Position position, double unit)
  - void validateUnit(Errors errors, AdjustmentCode adjCode, NonretirementPosition position, double unit)
  - void validateUnit(Errors errors, AdjustmentCode adjCode, RetirmentPosition position, double unit)
  - void validateUnit(Errors errors, AdjustmentCode adjCode, PersPosition position, double unit)
  - void validateUnit(Errors errors, AdjustmentCode adjCode, StrsPosition position, double unit)

  - void validateUnit(Errors errors, WorkersCompensationAdjustmentCode adjCode, Position position, double unit)
  - void validateUnit(Errors errors, WorkersCompensationAdjustmentCode adjCode, NonretirementPosition position, double unit)
  - void validateUnit(Errors errors, WorkersCompensationAdjustmentCode adjCode, RetirmentPosition position, double unit)
  - void validateUnit(Errors errors, WorkersCompensationAdjustmentCode adjCode, PersPosition position, double unit)
  - void validateUnit(Errors errors, WorkersCompensationAdjustmentCode adjCode, StrsPosition position, double unit)

  - void validateUnit(Errors errors, WorkersCompensationOffsetAdjustmentCode adjCode, Position position, double unit)
  - void validateUnit(Errors errors, WorkersCompensationOffsetAdjustmentCode adjCode, NonretirementPosition position, double unit)
  - void validateUnit(Errors errors, WorkersCompensationOffsetAdjustmentCode adjCode, RetirmentPosition position, double unit)
  - void validateUnit(Errors errors, WorkersCompensationOffsetAdjustmentCode adjCode, PersPosition position, double unit)
  - void validateUnit(Errors errors, WorkersCompensationOffsetAdjustmentCode adjCode, StrsPosition position, double unit)

}
@enduml
~~~
~~~md
@startuml

class AdjustmentCode {
  + boolean isWorkersCompensation()
  + boolean isWorksersCompensationOffset()
}

class Position {
  + boolean isRetirment()
  + boolean isPERS()
  + boolean isSTRS()
}

class FundingLine {}

class PayrollAdjustment {
  + Position position
  + FundingLine fundingLine
  + AdjustmentCode adjustmentCode
  + double amount
  + double unit
}

PayrollAdjustment o-- Position
PayrollAdjustment o-- FundingLine
PayrollAdjustment o-- AdjustmentCode

class Employee {
  + boolean isActive()
  + boolean isNonActive()
  + boolean isTerminated()
}
@enduml

~~~