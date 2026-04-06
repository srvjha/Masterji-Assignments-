# Instagram Thrift Creator Store Database Design

## Context

This project models a small Instagram-based store where products are sold via **DMs and WhatsApp**. Initially, operations are manual, but as the business grows, the owner needs a structured system to:

* Manage products (thrifted + handmade)
* Track inventory
* Handle customer orders
* Manage payments and deliveries

The system must reflect real-world differences:

* **Thrift items** → unique, single piece
* **Handmade items** → multiple units, inventory-based

---

## Design Approach

The goal was to design a **clean, normalized, and scalable MVP schema** that:

* Avoids redundancy
* Reflects real business behavior
* Supports growth without overengineering

---

## Core Entities

### 1. Product Modeling

We use a **base + subtype pattern**:

* `product_master` → common product data
* `thrift_products` → thrift-specific details
* `handmade_products` → inventory-specific details

### Why?

Because:

* Thrift items are **unique (1 unit only)**
* Handmade items have **stock and quantity**

This avoids:

* unnecessary NULL fields
* mixing unrelated attributes

---

### 2. Orders System

Orders are split into:

* `orders` → represents the transaction
* `order_items` → represents products inside the order

### Why?

Because:

* One order can contain **multiple products**
* This models real-world purchasing behavior

---

### 3. Customer Design

Customers are identified by:

* phone number (WhatsApp)
* Instagram username (optional)

### Why?

Because:

* business operates on Instagram + WhatsApp
* customers may not always share both

---

### 4. Payment & Shipping Separation

* `payments` → handles transaction status
* `shipping` → handles delivery tracking

### Why?

Because:

* payment lifecycle ≠ shipping lifecycle
* keeps system modular and clean

---

## ⚙️ Key Design Decisions

### Product Type Handling

Used:

```
type = THRIFTED | HANDMADE
```

* subtype tables

Instead of:

* single table with many NULLs

---

### Thrift vs Handmade Logic

| Feature        | Thrift     | Handmade       |
| -------------- | ---------- | -------------- |
| Quantity       | Always 1   | Multiple units |
| Condition      | Required   | Not needed     |
| Stock Tracking | Not needed | Required       |

---

### Price Snapshot

Stored:

```
price_at_time
```

### Why?

Product prices can change, but orders must preserve **historical price**.

---

### Status Design

* Product → `ACTIVE / INACTIVE`
* Order → `PLACED / SHIPPED / DELIVERED / CANCELLED`

### Why?

Separates:

* product lifecycle
* order lifecycle

---

### Minimal but Extendable

This is an **MVP-first design**, meaning:

* simple enough to implement quickly
* structured enough to scale later

---

## Relationships

* Customer → Orders → **one-to-many**
* Orders → Order Items → **one-to-many**
* Order Items → Product → **many-to-one**
* Product → Thrift/Handmade → **one-to-one**
* Orders → Payments → **one-to-one (MVP)**
* Orders → Shipping → **one-to-one (MVP)**

---

## What This Design Supports

* Multiple items per order
* Unique thrift item handling
* Inventory tracking for handmade products
* Payment tracking (UPI, COD, etc.)
* Delivery tracking with courier details
* Customer order history

---

## Known Tradeoffs (MVP Scope)

This design intentionally simplifies:

* Payments → assumes one per order (no retries/refunds yet)
* Shipping → assumes one shipment per order
* Category → stored as text (can be normalized later)
* No strict DB constraint between product type & subtype (handled in app)

---

## Future Improvements

* Support multiple payments (retry/refund)
* Inventory history tracking
* Product variants (size/color combinations)
* Category normalization
* Concurrency handling (prevent double-selling thrift items)

---

##  Summary

This design focuses on:

* Correct business modeling
* Clean separation of concerns
* Avoiding premature complexity

I can say 😅 **production-ready for MVP** and can evolve into a full-scale system without major redesign.

---
