```erd
title Store

product_master[icon:store, color: Yellow]{
  id serial PK
  name VARCHAR(300) NOT NULL
  description TEXT
  image_url VARCHAR(255)
  original_price DECIMAL(10,2) NOT NULL
  discounted_price DECIMAL(10,2) NOT NULL
  category VARCHAR(100)
  color VARCHAR(25)
  type ENUM("THRIFTED","HANDMADE") NOT NULL
  status ENUM("ACTIVE","INACTIVE") NOT NULL
  created_at DATETIME NOT NULL
  updated_at DATETIME
}

thrift_products[icon:box, color: Blue]{
  product_id PK FK
  condition VARCHAR(20)
}

handmade_products[icon:boxes, color: Blue]{
  product_id PK FK
  stock_quantity INT NOT NULL CHECK(stock_quantity >= 0)
  batch_size INT
}

customer_details[icon:user, color: Red]{
  id serial PK
  fullname VARCHAR(255) NOT NULL
  whatsapp_phone_number VARCHAR(20) UNIQUE
  instagram_username VARCHAR(255) UNIQUE
  address TEXT
  created_at DATETIME NOT NULL
  updated_at DATETIME
}

orders[icon:gcp-billing, color: Purple]{
  id serial PK
  customer_id INT FK
  order_status ENUM(
    "PLACED",
    "SHIPPED",
    "DELIVERED",
    "CANCELLED"
  ) NOT NULL
  order_placed_date DATETIME NOT NULL
  order_delivery_estimated_date DATETIME
  order_delivered_date DATETIME
  created_at DATETIME NOT NULL
  updated_at DATETIME
}

order_items[icon:receipt, color: Orange]{
  id serial PK
  order_id INT FK
  product_id INT FK
  quantity INT NOT NULL CHECK(quantity > 0)
  price_at_time DECIMAL(10,2) NOT NULL
  created_at DATETIME NOT NULL
  updated_at DATETIME
}

payments[icon:payment, color: Black]{
  id serial PK
  order_id INT FK
  amount DECIMAL(10,2) NOT NULL
  payment_method VARCHAR(50)   // UPI, COD, etc.
  payment_status ENUM("PENDING","PAID","FAILED") NOT NULL
  transaction_ref VARCHAR(255)
  created_at DATETIME NOT NULL
  updated_at DATETIME
}

shipping[icon:ship, color: Green]{
  id serial PK
  order_id INT FK
  address TEXT NOT NULL
  courier_name VARCHAR(100)
  tracking_id VARCHAR(100)
  shipped_at DATETIME
  delivered_at DATETIME
  created_at DATETIME NOT NULL
  updated_at DATETIME
}


orders.id  < order_items.order_id
order_items.product_id > product_master.id
customer_details.id <  orders.customer_id
product_master.id - thrift_products.product_id
product_master.id - handmade_products.product_id
orders.id - payments.order_id
orders.id - shipping.order_id
```

