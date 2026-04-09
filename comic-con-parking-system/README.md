# Comic-Con Parking System – ER Design (MVP)

## Overview

This project defines the database design for a multi-zone parking system used during a large-scale event like Comic-Con India.

The system is designed to handle:

- Vehicle entry and exit
- Parking spot allocation
- Multi-zone and multi-level parking
- Reserved parking categories (VIP, staff, exhibitors, EV)
- Parking sessions and tickets
- Payment tracking

The goal is to model a **real-world event parking system** that supports high traffic, multiple vehicle types, and efficient space utilization.

---

## Core Concepts

The system revolves around a parking lifecycle:

Vehicle → Entry → Parking Session → Spot Allocation → Exit → Payment

Key principles:
- One vehicle can enter multiple times (multiple sessions)
- Parking spots are reusable across sessions
- Zones and levels structure the parking facility
- Tickets and sessions are separated for clarity
- Payments are tied to parking sessions

---

## Entities

### VEHICLE
Represents a physical vehicle entering the parking facility.

**Key Points:**
- A vehicle can have multiple parking sessions
- Linked to a vehicle category

---

### VEHICLE_CATEGORY
Defines types of vehicles.

Examples:
- Bike
- Car
- SUV
- EV

**Key Points:**
- Used to determine compatible parking spots

---

### PARKING_ZONE
Represents a zone or level in the parking facility.

**Key Points:**
- One zone contains multiple parking spots

---

### PARKING_SPOT
Represents an individual parking space.

**Key Points:**
- Belongs to a zone
- Can be reused across multiple sessions
- Can have reservation categories

---

### SPOT_CATEGORY
Defines reservation or usage type of a parking spot.

Examples:
- VIP
- Staff
- Exhibitor
- EV Charging

---

### PARKING_SESSION
Represents a vehicle’s stay in the parking facility.

**Key Points:**
- Stores entry and exit timestamps
- Links vehicle to a parking spot
- Core entity for tracking activity

---

### PARKING_TICKET
Represents the ticket issued when a vehicle enters.

**Key Points:**
- Linked to a parking session
- Used for identification and tracking

---

### PAYMENT
Represents payment for a parking session.

**Key Points:**
- Linked to a session
- Stores amount and status

---

## Relationships and Cardinality

### Vehicle Relationships
- One vehicle can have many parking sessions
- Each session belongs to one vehicle

---

### Vehicle Category
- One category can have many vehicles
- Each vehicle belongs to one category

---

### Zone and Spot
- One zone contains many parking spots
- Each spot belongs to one zone

---

### Spot and Category
- One category can apply to many spots
- Each spot belongs to one category

---

### Session and Spot
- One spot can be used in many sessions over time
- Each session uses one spot

---

### Session and Ticket
- One session has exactly one ticket
- One ticket belongs to one session

---

### Session and Payment
- One session can have one or more payments
- Each payment belongs to one session

---

## Design Decisions

### 1. Session as Core Entity
Parking session tracks entry and exit timestamps and connects vehicle, spot, and payment.

---

### 2. Spot Reusability
Parking spots are reused across sessions instead of being tied permanently to a vehicle.

---

### 3. Separation of Ticket and Session
Tickets are identifiers, while sessions hold lifecycle data.

---

### 4. Reservation Modeling
Spot categories allow modeling reserved spaces (VIP, EV, etc.).

---

### 5. Payment Flexibility
Payments are linked to sessions, allowing retries and partial payments.

---

## Limitations (MVP Scope)

- No dynamic pricing model
- No real-time availability caching
- No enforcement of vehicle-spot compatibility rules
- No audit logs

---

## Future Improvements

- Dynamic pricing based on time or demand
- Real-time availability tracking system
- Reservation booking system
- Multiple spot categories per spot
- Integration with entry/exit hardware

---

## Summary

This design models a scalable and realistic event parking system by:

- Separating sessions, tickets, and payments
- Supporting multiple visits per vehicle
- Allowing spot reuse across time
- Structuring parking using zones and categories

The system is ready for implementation and can evolve with additional features.