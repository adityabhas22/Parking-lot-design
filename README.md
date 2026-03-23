# Parking Lot Design (LLD)

A multilevel parking lot system built to practice low-level design and SOLID principles.

## What it does

- Multi-level parking lot with configurable slots and gates
- Assigns the **nearest available slot** to whichever gate a vehicle enters from
- Generates tickets on entry, calculates fare on exit
- Slot picking and fare calculation are **swappable strategies** (injected at runtime)

## Class Diagram

```mermaid
classDiagram
    class ParkingLot {
        -Map~Integer, List~Slot~~ levelSlotMap
        -List~Gate~ gates
        -Map~String, Integer~ gateSlotDist
        -SlotPicker slotPicker
        -FareStrategy fareStrategy
        -Map~String, Ticket~ activeTickets
        +park(Vehicle, Gate) Ticket
        +exit(Ticket) double
        +status() Map
    }

    class SlotPicker {
        <<interface>>
        +findSlot(Vehicle, Gate, List~Slot~, Map) Slot
    }

    class NearestSlotPicker {
        +findSlot(Vehicle, Gate, List~Slot~, Map) Slot
    }

    class FareStrategy {
        <<interface>>
        +calculate(Ticket) double
    }

    class HourlyFareStrategy {
        -Map~SlotType, Double~ ratePerHour
        +calculate(Ticket) double
    }

    class Ticket {
        -String id
        -LocalDateTime entryTime
        -LocalDateTime exitTime
        -Vehicle vehicle
        -Gate entryGate
        -Slot slot
        +issueTicket(Vehicle, Gate, Slot)$ Ticket
        +markExit()
    }

    class Vehicle {
        -String number
        -VehicleType type
    }

    class Slot {
        -int id
        -int level
        -SlotType slotType
        -boolean taken
        +occupy()
        +free()
    }

    class Gate {
        -int id
        -int level
        -int position
    }

    class SlotType {
        <<enum>>
        SMALL
        MEDIUM
        LARGE
    }

    class VehicleType {
        <<enum>>
        TWO_WHEELER
        FOUR_WHEELER
        BUS
    }

    class VehicleSlotMapper {
        +mapToSlotType(VehicleType)$ SlotType
    }

    ParkingLot --> SlotPicker : uses
    ParkingLot --> FareStrategy : uses
    ParkingLot --> Ticket : manages
    ParkingLot --> Gate : has
    ParkingLot --> Slot : has
    SlotPicker <|.. NearestSlotPicker : implements
    FareStrategy <|.. HourlyFareStrategy : implements
    NearestSlotPicker --> VehicleSlotMapper : uses
    Ticket --> Vehicle
    Ticket --> Gate
    Ticket --> Slot
    Slot --> SlotType
    Vehicle --> VehicleType
```

## How to run

```bash
cd src
javac com/example/parking/*.java
java com.example.parking.App
```
