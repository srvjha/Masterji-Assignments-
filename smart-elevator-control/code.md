```
BUILDING [color: Red] {
  id SERIAL (PK)
  name VARCHAR(255) NOT NULL
  address TEXT
  created_at TIMESTAMPTZ NOT NULL DEFAULT now()
  updated_at TIMESTAMPTZ
}

FLOOR [color: Purple] {
  id SERIAL (PK)
  building_id BIGINT NOT NULL (FK)
  floor_number INT NOT NULL
}

ELEVATOR [color: Yellow] {
  id SERIAL (PK)
  building_id BIGINT NOT NULL (FK)
  shaft_number INT NOT NULL
  min_floor INT NOT NULL
  max_floor INT NOT NULL
  status TEXT NOT NULL CHECK (
    status IN ('ACTIVE', 'MAINTENANCE', 'OUT_OF_SERVICE')
  )
  current_floor INT NOT NULL
  direction TEXT NOT NULL CHECK (
    direction IN ('UP', 'DOWN', 'IDLE')
  )
  created_at TIMESTAMPTZ NOT NULL DEFAULT now()
  updated_at TIMESTAMPTZ
}

REQUEST [color: Orange] {
  id SERIAL (PK)
  building_id INT NOT NULL (FK)
  floor_id INT NOT NULL (FK)
  direction TEXT NOT NULL CHECK (
    direction IN ('UP', 'DOWN')
  )
  status TEXT NOT NULL CHECK (
    status IN ('PENDING', 'ASSIGNED', 'IN_PROGRESS', 'COMPLETED')
  )
  assigned_elevator_id INT (FK)
  created_at TIMESTAMPTZ NOT NULL DEFAULT now()
  updated_at TIMESTAMPTZ
}

MAINTENANCE {
  id SERIAL (PK)
  elevator_id INT NOT NULL (FK)
  issue TEXT NOT NULL
  status TEXT NOT NULL CHECK (
    status IN ('OPEN', 'IN_PROGRESS', 'RESOLVED')
  )
  started_at TIMESTAMPTZ NOT NULL DEFAULT now()
  resolved_at TIMESTAMPTZ
}

RIDE_LOG [color: Blue] {
  id SERIAL (PK)
  request_id INT NOT NULL (FK)
  elevator_id INT NOT NULL (FK)
  start_time TIMESTAMPTZ NOT NULL
  end_time TIMESTAMPTZ
  from_floor INT NOT NULL
  to_floor INT NOT NULL
}

EVENT_LOG [color: Green] {
  id SERIAL (PK)
  entity_type TEXT NOT NULL
  entity_id INT NOT NULL
  event_type TEXT NOT NULL
  metadata JSON
  created_at TIMESTAMPTZ NOT NULL DEFAULT now()
}

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