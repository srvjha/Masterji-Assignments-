```
VEHICLE[icon:car, color: Red]{
  id (PK)
  name VARCHAR(50)
  vehicle_number VARCHAR(20)
  vehicle_category_id (FK)
  created_at TIMESTAMP
}

PARKING_ZONE[icon:map, color: Yellow]{
  id (PK)
  name VARCHAR(50)
  level INT
}

PARKING_SPOT[icon:square, color: Green]{
  id (PK)
  zone_id (FK)
  spot_category_id (FK)
  spot_number VARCHAR(20)
  is_active BOOLEAN
}

SPOT_CATEGORY[icon:bookmark, color: Purple]{
  id (PK)
  name VARCHAR(50)
}

PARKING_SESSION[icon:clock, color: Orange]{
  id (PK)
  vehicle_id (FK)
  parking_spot_id (FK)
  entry_time TIMESTAMP
  exit_time TIMESTAMP
  status ENUM("ACTIVE", "COMPLETED")
}

PARKING_TICKET[icon:ticket, color: Pink]{
  id (PK)
  session_id (FK)
  ticket_number VARCHAR(50)
  issued_at TIMESTAMP
}

PAYMENT[icon:credit-card, color: Purple]{
  id (PK)
  session_id (FK)
  amount DECIMAL
  status ENUM("PENDING", "PAID", "FAILED")
  paid_at TIMESTAMP
  created_at TIMESTAMP
}



VEHICLE < PARKING_SESSION

PARKING_ZONE < PARKING_SPOT

SPOT_CATEGORY < PARKING_SPOT

PARKING_SPOT < PARKING_SESSION

PARKING_SESSION - PARKING_TICKET

PARKING_SESSION < PAYMENT

```