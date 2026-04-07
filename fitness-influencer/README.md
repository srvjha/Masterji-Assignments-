# Fitness Influencer Coaching Platform — Database Design (ER Model)

## Context

This system is designed for an **online fitness coaching business**, where trainers (influencers/coaches) manage multiple clients digitally.

The platform supports:

* Client onboarding
* Selling coaching plans/programs
* Scheduling consultations and live sessions
* Subscription management
* Progress tracking (weight, measurements, etc.)
* Weekly check-ins and trainer feedback
* Payment handling

This is **not a gym management system**, but a **marketplace-like coaching ecosystem**.

---

## Core Design Philosophy

The schema is built on these principles:

* **Separation of concerns** (identity vs behavior vs history)
* **Avoiding data duplication**
* **Modeling relationships explicitly**
* **Handling time-series data correctly**
* **Designing for future scalability without overengineering**

---

## Entity Overview

### 1. `users`

Represents all system users (both trainers and clients).

#### Why it exists:

* Central identity layer
* Avoids duplication of authentication logic
* Supports role-based behavior

#### Key Design Choice:

* Single table with `role` enum (`CLIENT`, `TRAINER`)
* Avoids splitting into separate `trainer` and `client` tables

#### Cardinality:

* One user → one optional trainer profile
* One user → one optional client profile

---

### 2. `trainer_profiles`

Stores trainer-specific metadata.

#### Why separate table:

* Not all users are trainers
* Keeps `users` table clean and normalized

#### Fields include:

* experience
* certifications
* bio

#### Cardinality:

* **1 : 1 (optional)** with `users`

---

### 3. `client_profiles`

Stores client-specific information.

#### Why minimal:

* Most client data is dynamic (progress, plans, sessions)
* Only stable attributes are stored

#### Fields include:

* goal
* activity level
* diet preference
* medical conditions

#### Cardinality:

* **1 : 1 (optional)** with `users`

---

### 4. `plans`

Represents coaching programs created by trainers.

#### Why it exists:

* Trainers can sell structured offerings
* Same plan can be reused across multiple clients

#### Key Design Choice:

* `duration_days` instead of fixed validity date

#### Cardinality:

* One trainer → many plans (**1 : N**)

---

### 5. `subscriptions`

Tracks which client purchased which plan.

#### Why it exists:

* Resolves many-to-many between clients and plans
* Tracks lifecycle (start/end/status)

#### Key Design Insight:

> A client can purchase multiple plans over time
> A plan can be purchased by multiple clients

#### Cardinality:

* Client → subscriptions (**1 : N**)
* Plan → subscriptions (**1 : N**)
* Client ↔ Plan → **M : N via subscriptions**

---

### 6. `sessions`

Represents scheduled interactions:

* consultations
* live sessions

#### Why it exists:

* Models trainer-client interaction explicitly
* Tracks scheduling and completion

#### Cardinality:

* One trainer → many sessions
* One client → many sessions
* Trainer ↔ Client → **M : N via sessions**

---

### 7. `checkins`

Represents structured weekly updates submitted by clients.

#### Why separate from progress:

* Check-ins are **events/interactions**
* Not just raw data

#### Fields include:

* client notes
* trainer feedback
* scheduling and submission tracking

#### Cardinality:

* Client → checkins (**1 : N**)
* Trainer → checkins (**1 : N**)

---

### 8. `progress_logs`

Stores measurable fitness data over time.

#### Why separate from checkins:

* Represents **time-series data**
* Not all progress updates come from check-ins

#### Fields include:

* weight
* body measurements
* fat percentage
* optional image uploads

#### Design Choice:

* `checkin_id` is optional → allows flexible logging

#### Cardinality:

* Client → progress logs (**1 : N**)
* Checkin → progress logs (**1 : N, optional**)

---

### 9. `payments`

Tracks financial transactions.

#### Why it exists:

* Links payments to subscriptions
* Supports retries, failures, and multiple payments

#### Cardinality:

* Subscription → payments (**1 : N**)
* Client → payments (**1 : N**)

---

## Relationship Summary

| Relationship            | Cardinality               |
| ----------------------- | ------------------------- |
| User → Trainer Profile  | 1 : 1 (optional)          |
| User → Client Profile   | 1 : 1 (optional)          |
| Trainer → Plans         | 1 : N                     |
| Client → Subscriptions  | 1 : N                     |
| Plan → Subscriptions    | 1 : N                     |
| Client ↔ Plan           | M : N (via subscriptions) |
| Trainer ↔ Client        | M : N (via sessions)      |
| Client → Checkins       | 1 : N                     |
| Client → Progress Logs  | 1 : N                     |
| Checkin → Progress Logs | 1 : N (optional)          |
| Subscription → Payments | 1 : N                     |

---

## Key Design Decisions

### 1. No `trainer_id` in client table

* Avoids rigid 1:1 mapping
* Supports multiple trainers over time

### 2. No `plan_id` in client table

* Handled via subscriptions (M:N relationship)

### 3. No progress fields in client

* Progress is time-based → stored in `progress_logs`

### 4. No arrays (like allocated clients)

* All relationships are normalized

### 5. Profiles are optional extensions

* Identity always comes from `users`

---

## Mental Model

* **users** → identity
* **profiles** → additional attributes
* **plans** → offerings
* **subscriptions** → purchases
* **sessions** → interactions
* **checkins** → structured updates
* **progress_logs** → historical data
* **payments** → financial layer

---

## Summary

This design achieves:

* Clean separation of concerns
* Proper handling of many-to-many relationships
* Scalable progress tracking
* Flexibility for future features (diet plans, analytics, etc.)
* Avoidance of common relational modeling mistakes

The schema is **MVP-friendly**, yet **production-scalable**, making it suitable for real-world coaching platforms.

---

## 📈 Future Enhancements

* Workout plan and diet modeling
* Trainer earnings & commission tracking
* Notifications/reminders system
* Advanced analytics on progress trends
* Role-based access control (RBAC)

---
