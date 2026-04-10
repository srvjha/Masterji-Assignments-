# Elevator Control System – Database Design

## Overview

This system models a **smart elevator control platform** used in large buildings (malls, airports, offices) where multiple elevators operate simultaneously.

The goal is to:

* efficiently handle ride requests
* assign elevators in real time
* track system state
* maintain historical logs for analysis and debugging

---

## Core Design Philosophy

The design is built on a key principle:

> Separate **real-time state** from **historical events**

### Why this matters

* Real-time decisions (like assigning an elevator) must be **fast**
* Historical data (logs, analytics) can be **large and slower**

Mixing both leads to:

* heavy joins
* slow performance
* poor scalability

---

## System Breakdown

### 1. Structural Entities (Stable)

These represent physical or static components:

* **BUILDING** → top-level container
* **FLOOR** → floors within a building
* **ELEVATOR** → individual elevators (one per shaft)

```text
BUILDING < FLOOR
BUILDING < ELEVATOR
```

---

### 2. Operational Entities (Real-Time State)

These drive system behavior:

* **REQUEST**

  * Created when a user presses a button
  * Represents a *live* pending or active request

* **ELEVATOR (state fields)**

  * current_floor
  * direction
  * status

```text
BUILDING < REQUEST
FLOOR < REQUEST
ELEVATOR < REQUEST
```

### Key Idea

> These tables store **current truth**, not history

---

### 3. Lifecycle Modeling

A request flows through states:

```text
PENDING → ASSIGNED → IN_PROGRESS → COMPLETED
```

We explicitly store `status` instead of deriving it from logs.

### Why?

* avoids recomputation
* enables fast filtering (`WHERE status = 'PENDING'`)
* simplifies assignment logic

---

### 4. Maintenance Tracking

* **MAINTENANCE** tracks elevator issues and service lifecycle

```text
ELEVATOR < MAINTENANCE
```

### Design choice

We do NOT derive elevator availability from maintenance records.

Instead:

* elevator has a `status` field (`ACTIVE`, `MAINTENANCE`, etc.)
* maintenance table is for tracking history and workflow

---

### 5. Ride Execution (Structured Logs)

* **RIDE_LOG** represents completed rides

```text
REQUEST - RIDE_LOG
ELEVATOR < RIDE_LOG
```

### Why separate table?

* clean representation of completed rides
* avoids polluting request table
* optimized for analytics

### Note

Currently modeled as **one-to-one** with request.
Can be extended to one-to-many if retries/reassignments are introduced.

---

### 6. Event Logging (Flexible History Layer)

* **EVENT_LOG** captures all system events

```text
REQUEST < EVENT_LOG
ELEVATOR < EVENT_LOG
```

### Examples of events:

* REQUEST_CREATED
* ELEVATOR_ASSIGNED
* ELEVATOR_MOVED
* RIDE_COMPLETED

### Design choice

Uses:

* `entity_type`
* `entity_id`
* `metadata (JSON)`

### Why?

* flexible schema
* easy to extend without migrations
* supports debugging and replay

---

## Important Design Decisions

### 1. State is stored, not derived

We store:

* `current_floor`
* `direction`
* `request.status`

Instead of computing from logs.

**Reason:**
Real-time systems require low-latency reads.

---

### 2. Controlled denormalization

Example:

* `RIDE_LOG.from_floor`, `to_floor`

Even though this exists in other tables.

**Reason:**

* avoids joins in analytics
* improves read performance

---

### 3. Avoid over-normalization

We intentionally avoid:

* deriving everything through relationships
* excessive joins

**Tradeoff:**

* slight duplication
* much better performance

---

### 4. Event-driven thinking

The system captures **what happened** separately from **what is true now**.

| Layer        | Purpose              |
| ------------ | -------------------- |
| State Tables | Current system state |
| Event Log    | Historical timeline  |

---

### 5. Cardinality-driven modeling

Relationships are expressed clearly:

```text
BUILDING < FLOOR
BUILDING < ELEVATOR
BUILDING < REQUEST

FLOOR < REQUEST

ELEVATOR < REQUEST
ELEVATOR < MAINTENANCE
ELEVATOR < RIDE_LOG

REQUEST - RIDE_LOG

REQUEST < EVENT_LOG
ELEVATOR < EVENT_LOG
```

---

## Tradeoffs & Future Improvements

### 1. FLOOR Table

**Current:** separate table
**Alternative:** store `floor_number` directly

* Pros: simpler, faster
* Cons: less normalized

---

### 2. EVENT_LOG Polymorphism

Current design:

* flexible
* no strict foreign keys

Future option:

* separate event tables per entity (more strict)

---

### 3. Request Lifecycle Storage

Currently:

* stored in REQUEST + EVENT_LOG

Alternative:

* full event sourcing (rebuild state from events)

---

### 4. Scaling Considerations

At high scale:

* partition EVENT_LOG by time
* cache elevator state (Redis)
* use message queue (Kafka) for async processing

---

## Summary

This design focuses on:

* **fast real-time decision making**
* **clear separation of state and history**
* **flexibility for future evolution**
* **practical tradeoffs over theoretical purity**

> The system is optimized for how it will be used, not just how it looks.

---
