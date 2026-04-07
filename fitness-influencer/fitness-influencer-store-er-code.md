```
title Fitness Influencer Online Coaching
users[icon:user, color: Red]{
  id PK
  firstname VARCHAR(45) NOT NULL
  lastname  VARCHAR(45)
  email     VARCHAR(322) UNIQUE NOT NULL
  phone_number VARCHAR(20) UNIQUE NOT NULL
  password  VARCHAR(66)
  date_of_birth  DATE
  role ENUM ("CLIENT","TRAINER")
  gender  ENUM ("MALE","FEMALE") NOT NULL
  address TEXT 
  created_at DATETIME
  updated_at DATETIME
}
trainer_profiles[icon:user-check, color: Yellow]{
  user_id PK FK
  certifications TEXT
  experience_years INT(2)
  bio TEXT
}

client_profiles[icon:user-cog, color: Green]{
  user_id PK FK
  goal VARCHAR(255)
  activity_level ENUM ("BEGINNER","INTERMEDIATE","ADVANCED")
  diet_preference TEXT
  medical_conditions TEXT
}

checkins[icon:check-circle, color: Orange]{
  id PK
  client_id FK
  trainer_id FK
  isAttended boolean DEFAULT(false)
  schedule_at DATETIME
  client_notes TEXT
  trainer_notes TEXT
  created_at DATETIME
  updated_at DATETIME
}

progress_logs[icon:progress, color: Orange]{
 id PK
 client_id FK
 checkin_id FK
 image_url TEXT
 weight DECIMAL(5,2)
 chest DECIMAL(5,2)
 waist DECIMAL(5,2)
 fat_percentage DECIMAL(10,2)
 created_at DATETIME
 updated_at DATETIME
}

plan[icon:notebook, color: Purple]{
id PK
trainer_id FK
name VARCHAR(45) NOT NULL
description TEXT 
no_of_days INT NOT NULL
price DECIMAL(10,2) 
brochure_url VARCHAR(45)
created_at DATETIME
updated_at DATETIME
}


sessions[icon:laptop, color: Blue]{
  id PK
  client_id FK
  trainer_id FK 
  type ENUM ("CONSULTATION","LIVE_SESSION")
  status ENUM ("SCHEDULED","COMPLETED","CANCELLED")
  schedule_at DATETIME
  notes TEXT
  created_at DATETIME
  updated_at DATETIME

}
subscriptions[icon:wallet]{
id PK
client_id FK NOT NULL
planid FK  NOT NULL
start_date DATE NOT NULL
end_date DATE NOT NULL
status ENUM ("ACTIVE","COMPLETED","CANCELLED") NOT NULL
created_at DATETIME
updated_at DATETIME
}

payments[icon:payment, color: Green]{
  id serial PK
  client_id INT FK
  subscription_id INT FK
  amount DECIMAL(10,2) NOT NULL
  payment_method VARCHAR(50)   // UPI, COD, etc.
  payment_status ENUM("PENDING","PAID","FAILED") NOT NULL
  transaction_ref VARCHAR(255)
  created_at DATETIME NOT NULL
  updated_at DATETIME
}

users.id - trainer_profiles.user_id
users.id - client_profiles.user_id

users.id < plan.trainer_id

users.id < subscriptions.client_id
plan.id < subscriptions.planid


users.id < sessions.client_id
users.id < sessions.trainer_id

users.id < checkins.client_id
users.id < checkins.trainer_id

users.id < progress_logs.client_id
checkins.id < progress_logs.checkinid

subscriptions.id < payments.subscriptionid
users.id < payments.clientid






```