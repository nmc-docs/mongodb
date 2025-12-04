---
sidebar_position: 7
---
# $lookup

# `$lookup` là gì?

:::info

- `$lookup` là một stage trong **MongoDB Aggregation Pipeline** thực hiện **left outer join** giữa hai collections, cho phép kết hợp documents từ collection được join vào documents của collection chính.
- Có 4 loại lookup:
  - **Equality Match** : Join đơn giản dựa trên giá trị bằng nhau
  - **Uncorrelated Subquery** : Join với subquery không liên quan
  - **Correlated Subquery** : Join với subquery có liên quan
  - **Multiple Joins** : Join nhiều collections

:::

## Cú pháp

### Cú pháp cơ bản (Equality Match)

```js
{
  $lookup: {
    from: "<collection-to-join>",
    localField: "<field-from-input-documents>",
    foreignField: "<field-from-from-collection>",
    as: "<output-array-field>"
  }
}
```

### Cú pháp nâng cao (Subquery)

```js
{
  $lookup: {
    from: "<collection-to-join>",
    let: { <variables> },
    pipeline: [ <pipeline-to-execute> ],
    as: "<output-array-field>"
  }
}
```

## Dữ liệu mẫu

### Collection: customers

```js
db.customers.insertMany([
  {
    _id: "C001",
    name: "Nguyen Van A",
    email: "a.nguyen@email.com",
    phone: "0912345678",
    address: {
      city: "Hanoi",
      district: "Cau Giay"
    },
    membership_level: "Gold",
    join_date: ISODate("2020-01-15"),
    status: "active"
  },
  {
    _id: "C002",
    name: "Tran Thi B",
    email: "b.tran@email.com",
    phone: "0923456789",
    address: {
      city: "HCMC",
      district: "District 1"
    },
    membership_level: "Silver",
    join_date: ISODate("2021-03-20"),
    status: "active"
  },
  {
    _id: "C003",
    name: "Le Van C",
    email: "c.le@email.com",
    phone: "0934567890",
    address: {
      city: "Hanoi",
      district: "Hai Ba Trung"
    },
    membership_level: "Platinum",
    join_date: ISODate("2019-11-10"),
    status: "inactive"
  },
  {
    _id: "C004",
    name: "Pham Thi D",
    email: "d.pham@email.com",
    phone: "0945678901",
    address: {
      city: "Danang",
      district: "Hai Chau"
    },
    membership_level: "Silver",
    join_date: ISODate("2022-05-15"),
    status: "active"
  }
]);
```

### Collection: orders

```js
db.orders.insertMany([
  {
    _id: "O001",
    customer_id: "C001",
    order_date: ISODate("2024-01-15"),
    total_amount: 2500000,
    status: "completed",
    items: [
      { product_id: "P001", name: "Laptop", quantity: 1, price: 2000000 },
      { product_id: "P002", name: "Mouse", quantity: 2, price: 250000 }
    ],
    shipping_address: {
      city: "Hanoi",
      district: "Cau Giay"
    }
  },
  {
    _id: "O002",
    customer_id: "C001",
    order_date: ISODate("2024-02-20"),
    total_amount: 1500000,
    status: "shipped",
    items: [
      { product_id: "P003", name: "Keyboard", quantity: 1, price: 800000 },
      { product_id: "P004", name: "Monitor", quantity: 1, price: 700000 }
    ],
    shipping_address: {
      city: "Hanoi",
      district: "Cau Giay"
    }
  },
  {
    _id: "O003",
    customer_id: "C002",
    order_date: ISODate("2024-01-25"),
    total_amount: 500000,
    status: "completed",
    items: [
      { product_id: "P005", name: "Headphones", quantity: 1, price: 500000 }
    ],
    shipping_address: {
      city: "HCMC",
      district: "District 1"
    }
  },
  {
    _id: "O004",
    customer_id: "C003",
    order_date: ISODate("2023-12-10"),
    total_amount: 3000000,
    status: "cancelled",
    items: [
      { product_id: "P001", name: "Laptop", quantity: 1, price: 2000000 },
      { product_id: "P006", name: "Printer", quantity: 1, price: 1000000 }
    ],
    shipping_address: {
      city: "Hanoi",
      district: "Hai Ba Trung"
    }
  },
  {
    _id: "O005",
    customer_id: "C004",
    order_date: ISODate("2024-03-05"),
    total_amount: 800000,
    status: "pending",
    items: [
      { product_id: "P007", name: "Tablet", quantity: 1, price: 800000 }
    ],
    shipping_address: {
      city: "Danang",
      district: "Hai Chau"
    }
  }
]);
```

### Collection: products

```js
db.products.insertMany([
  {
    _id: "P001",
    name: "Laptop",
    category: "Electronics",
    price: 2000000,
    stock_quantity: 50,
    supplier_id: "S001",
    specifications: {
      brand: "Dell",
      model: "XPS 13",
      ram: "16GB",
      storage: "512GB SSD"
    },
    tags: ["electronics", "computers", "laptops"]
  },
  {
    _id: "P002",
    name: "Mouse",
    category: "Electronics",
    price: 250000,
    stock_quantity: 200,
    supplier_id: "S002",
    specifications: {
      brand: "Logitech",
      model: "MX Master 3",
      wireless: true,
      dpi: "4000"
    },
    tags: ["electronics", "peripherals", "wireless"]
  },
  {
    _id: "P003",
    name: "Keyboard",
    category: "Electronics",
    price: 800000,
    stock_quantity: 100,
    supplier_id: "S001",
    specifications: {
      brand: "Keychron",
      model: "K8",
      mechanical: true,
      layout: "TKL"
    },
    tags: ["electronics", "keyboards", "mechanical"]
  },
  {
    _id: "P004",
    name: "Monitor",
    category: "Electronics",
    price: 700000,
    stock_quantity: 75,
    supplier_id: "S003",
    specifications: {
      brand: "LG",
      model: "27UL850",
      resolution: "4K",
      size: "27 inch"
    },
    tags: ["electronics", "monitors", "4k"]
  },
  {
    _id: "P005",
    name: "Headphones",
    category: "Audio",
    price: 500000,
    stock_quantity: 150,
    supplier_id: "S002",
    specifications: {
      brand: "Sony",
      model: "WH-1000XM4",
      wireless: true,
      noise_cancelling: true
    },
    tags: ["audio", "headphones", "wireless"]
  }
]);
```

### Collection: suppliers

```js
db.suppliers.insertMany([
  {
    _id: "S001",
    name: "TechCorp Vietnam",
    contact_person: "Mr. Nguyen",
    email: "contact@techcorp.vn",
    phone: "028 1234 5678",
    address: {
      city: "HCMC",
      district: "District 7"
    },
    products_supplied: ["P001", "P003"],
    rating: 4.5
  },
  {
    _id: "S002",
    name: "AudioTech Ltd.",
    contact_person: "Ms. Tran",
    email: "sales@audiotech.com",
    phone: "024 8765 4321",
    address: {
      city: "Hanoi",
      district: "Hoan Kiem"
    },
    products_supplied: ["P002", "P005"],
    rating: 4.2
  },
  {
    _id: "S003",
    name: "Display Solutions",
    contact_person: "Mr. Le",
    email: "info@displaysolutions.com",
    phone: "0236 5555 6666",
    address: {
      city: "Danang",
      district: "Thanh Khe"
    },
    products_supplied: ["P004"],
    rating: 4.0
  }
]);
```

## Ví dụ từ cơ bản đến nâng cao

### Ví dụ cơ bản

**Ví dụ 1: Simple Equality Join**

```js
// Join orders với customers (1-N relationship)
db.orders.aggregate([
  {
    $lookup: {
      from: "customers",           // Collection cần join
      localField: "customer_id",   // Field từ orders
      foreignField: "_id",         // Field từ customers
      as: "customer_info"          // Output array field
    }
  },
  {
    $project: {
      _id: 1,
      order_date: 1,
      total_amount: 1,
      status: 1,
      customer_name: { $arrayElemAt: ["$customer_info.name", 0] },
      customer_email: { $arrayElemAt: ["$customer_info.email", 0] }
    }
  }
]);

// Kết quả:
[
  {
    _id: "O001",
    order_date: ISODate("2024-01-15T00:00:00Z"),
    total_amount: 2500000,
    status: "completed",
    customer_name: "Nguyen Van A",
    customer_email: "a.nguyen@email.com"
  },
  // ... các orders khác
]
```

**Ví dụ 2: Join với Array Fields**

```js
// Join orders với products qua items array
db.orders.aggregate([
  {
    $unwind: "$items"  // Tách mảng items thành các documents riêng
  },
  {
    $lookup: {
      from: "products",
      localField: "items.product_id",
      foreignField: "_id",
      as: "product_details"
    }
  },
  {
    $project: {
      order_id: "$_id",
      customer_id: 1,
      product_name: "$items.name",
      quantity: "$items.quantity",
      price: "$items.price",
      category: { $arrayElemAt: ["$product_details.category", 0] },
      brand: { $arrayElemAt: ["$product_details.specifications.brand", 0] }
    }
  }
]);
```

### Ví dụ trung cấp

**Ví dụ 3: Nested Lookup (Multiple Joins)**

```js
// Join orders -> products -> suppliers (3 levels)
db.orders.aggregate([
  {
    $lookup: {
      from: "customers",
      localField: "customer_id",
      foreignField: "_id",
      as: "customer"
    }
  },
  {
    $unwind: "$items"
  },
  {
    $lookup: {
      from: "products",
      localField: "items.product_id",
      foreignField: "_id",
      as: "product"
    }
  },
  {
    $lookup: {
      from: "suppliers",
      localField: "product.supplier_id",
      foreignField: "_id",
      as: "supplier"
    }
  },
  {
    $project: {
      order_id: "$_id",
      order_date: 1,
      customer_name: { $arrayElemAt: ["$customer.name", 0] },
      product_name: "$items.name",
      quantity: "$items.quantity",
      product_category: { $arrayElemAt: ["$product.category", 0] },
      supplier_name: { $arrayElemAt: ["$supplier.name", 0] },
      supplier_rating: { $arrayElemAt: ["$supplier.rating", 0] }
    }
  }
]);
```

**Ví dụ 4: Lookup với điều kiện phức tạp**

```js
// Join với điều kiện trên nhiều fields
db.customers.aggregate([
  {
    $lookup: {
      from: "orders",
      let: { customerId: "$_id", customerCity: "$address.city" },
      pipeline: [
        {
          $match: {
            $expr: {
              $and: [
                { $eq: ["$customer_id", "$$customerId"] },
                { $eq: ["$shipping_address.city", "$$customerCity"] },
                { $eq: ["$status", "completed"] }
              ]
            }
          }
        },
        {
          $project: {
            _id: 1,
            order_date: 1,
            total_amount: 1,
            item_count: { $size: "$items" }
          }
        }
      ],
      as: "completed_orders"
    }
  },
  {
    $project: {
      name: 1,
      email: 1,
      city: "$address.city",
      order_count: { $size: "$completed_orders" },
      total_spent: {
        $sum: "$completed_orders.total_amount"
      },
      orders: "$completed_orders"
    }
  }
]);
```

### Ví dụ nâng cao

**Ví dụ 5: Uncorrelated Subquery Lookup**

```js
// Join không phụ thuộc (uncorrelated) - lấy top 3 products bán chạy
db.customers.aggregate([
  {
    $lookup: {
      from: "orders",
      pipeline: [
        { $unwind: "$items" },
        {
          $group: {
            _id: "$items.product_id",
            total_quantity: { $sum: "$items.quantity" },
            total_revenue: {
              $sum: { $multiply: ["$items.quantity", "$items.price"] }
            }
          }
        },
        { $sort: { total_quantity: -1 } },
        { $limit: 3 },
        {
          $lookup: {
            from: "products",
            localField: "_id",
            foreignField: "_id",
            as: "product_info"
          }
        },
        {
          $project: {
            product_name: { $arrayElemAt: ["$product_info.name", 0] },
            category: { $arrayElemAt: ["$product_info.category", 0] },
            total_quantity: 1,
            total_revenue: 1
          }
        }
      ],
      as: "top_selling_products"
    }
  },
  {
    $project: {
      name: 1,
      membership_level: 1,
      top_selling_products: 1
    }
  }
]);
```

**Ví dụ 6: Correlated Subquery với Aggregation trong Pipeline**

```js
// Phân tích customers với các chỉ số chi tiết
db.customers.aggregate([
  {
    $lookup: {
      from: "orders",
      let: { customerId: "$_id" },
      pipeline: [
        {
          $match: {
            $expr: { $eq: ["$customer_id", "$$customerId"] }
          }
        },
        {
          $facet: {
            // Tổng quan orders
            summary: [
              {
                $group: {
                  _id: null,
                  total_orders: { $sum: 1 },
                  total_spent: { $sum: "$total_amount" },
                  avg_order_value: { $avg: "$total_amount" }
                }
              }
            ],
            // Phân tích theo status
            by_status: [
              {
                $group: {
                  _id: "$status",
                  count: { $sum: 1 },
                  total_amount: { $sum: "$total_amount" }
                }
              }
            ],
            // Phân tích theo tháng
            monthly_trend: [
              {
                $group: {
                  _id: {
                    year: { $year: "$order_date" },
                    month: { $month: "$order_date" }
                  },
                  order_count: { $sum: 1 },
                  monthly_spent: { $sum: "$total_amount" }
                }
              },
              { $sort: { "_id.year": 1, "_id.month": 1 } }
            ],
            // Sản phẩm mua nhiều nhất
            top_products: [
              { $unwind: "$items" },
              {
                $group: {
                  _id: "$items.product_id",
                  total_quantity: { $sum: "$items.quantity" },
                  total_spent: { 
                    $sum: { $multiply: ["$items.quantity", "$items.price"] }
                  }
                }
              },
              { $sort: { total_quantity: -1 } },
              { $limit: 5 }
            ]
          }
        }
      ],
      as: "order_analysis"
    }
  },
  {
    $project: {
      customer_id: "$_id",
      name: 1,
      email: 1,
      membership_level: 1,
      join_date: 1,
      status: 1,
  
      // Lấy summary
      order_summary: { $arrayElemAt: ["$order_analysis.summary", 0] },
  
      // Chuyển đổi by_status thành object
      order_status: {
        $arrayToObject: {
          $map: {
            input: { $arrayElemAt: ["$order_analysis.by_status", 0] },
            as: "status",
            in: {
              k: "$$status._id",
              v: {
                count: "$$status.count",
                total_amount: "$$status.total_amount"
              }
            }
          }
        }
      },
  
      // Lấy monthly trend
      monthly_trend: { $arrayElemAt: ["$order_analysis.monthly_trend", 0] },
  
      // Top products với product names
      top_products: { $arrayElemAt: ["$order_analysis.top_products", 0] },
  
      // Tính customer lifetime value
      clv: {
        $ifNull: [
          { $arrayElemAt: ["$order_analysis.summary.total_spent", 0] },
          0
        ]
      },
  
      // Phân loại customer
      customer_segment: {
        $switch: {
          branches: [
            {
              case: {
                $gt: [
                  { $ifNull: [
                    { $arrayElemAt: ["$order_analysis.summary.total_spent", 0] },
                    0
                  ]},
                  5000000
                ]
              },
              then: "VIP"
            },
            {
              case: {
                $gt: [
                  { $ifNull: [
                    { $arrayElemAt: ["$order_analysis.summary.total_spent", 0] },
                    0
                  ]},
                  2000000
                ]
              },
              then: "Regular"
            }
          ],
          default: "New"
        }
      }
    }
  }
]);
```

**Ví dụ 7: Real-time Inventory + Sales Analysis**

```js
// Phân tích tồn kho và doanh số theo thời gian thực
db.products.aggregate([
  {
    $lookup: {
      from: "orders",
      let: { productId: "$_id" },
      pipeline: [
        { $unwind: "$items" },
        {
          $match: {
            $expr: {
              $and: [
                { $eq: ["$items.product_id", "$$productId"] },
                { $gte: ["$order_date", ISODate("2024-01-01")] },
                { $lte: ["$order_date", ISODate("2024-03-31")] }
              ]
            }
          }
        },
        {
          $group: {
            _id: {
              product_id: "$items.product_id",
              year_month: {
                $dateToString: {
                  format: "%Y-%m",
                  date: "$order_date"
                }
              }
            },
            total_sold: { $sum: "$items.quantity" },
            total_revenue: {
              $sum: { $multiply: ["$items.quantity", "$items.price"] }
            },
            order_count: { $sum: 1 }
          }
        },
        {
          $group: {
            _id: "$_id.product_id",
            monthly_sales: {
              $push: {
                month: "$_id.year_month",
                sold: "$total_sold",
                revenue: "$total_revenue",
                orders: "$order_count"
              }
            },
            total_sold: { $sum: "$total_sold" },
            total_revenue: { $sum: "$total_revenue" }
          }
        }
      ],
      as: "sales_data"
    }
  },
  {
    $lookup: {
      from: "suppliers",
      localField: "supplier_id",
      foreignField: "_id",
      as: "supplier_info"
    }
  },
  {
    $project: {
      product_id: "$_id",
      name: 1,
      category: 1,
      price: 1,
      current_stock: "$stock_quantity",
  
      // Sales Analysis
      sales_summary: {
        $cond: {
          if: { $gt: [{ $size: "$sales_data" }, 0] },
          then: { $arrayElemAt: ["$sales_data", 0] },
          else: {
            total_sold: 0,
            total_revenue: 0,
            monthly_sales: []
          }
        }
      },
  
      // Inventory Metrics
      inventory_metrics: {
        months_of_supply: {
          $cond: {
            if: {
              $gt: [
                { $arrayElemAt: ["$sales_data.total_sold", 0] },
                0
              ]
            },
            then: {
              $divide: [
                "$stock_quantity",
                {
                  $divide: [
                    { $arrayElemAt: ["$sales_data.total_sold", 0] },
                    3  // 3 months data
                  ]
                }
              ]
            },
            else: 999  // Infinite supply if no sales
          }
        },
        stock_out_risk: {
          $cond: {
            if: { $lt: ["$stock_quantity", 10] },
            then: "HIGH",
            else: {
              $cond: {
                if: { $lt: ["$stock_quantity", 25] },
                then: "MEDIUM",
                else: "LOW"
              }
            }
          }
        },
        reorder_point: {
          $multiply: [
            {
              $divide: [
                { $arrayElemAt: ["$sales_data.total_sold", 0] },
                90  // daily average over 90 days
              ]
            },
            14  // 14 days lead time
          ]
        }
      },
  
      // Supplier Info
      supplier: {
        $cond: {
          if: { $gt: [{ $size: "$supplier_info" }, 0] },
          then: {
            name: { $arrayElemAt: ["$supplier_info.name", 0] },
            rating: { $arrayElemAt: ["$supplier_info.rating", 0] },
            contact: { $arrayElemAt: ["$supplier_info.email", 0] }
          },
          else: null
        }
      },
  
      // Product Performance
      performance: {
        sell_through_rate: {
          $multiply: [
            {
              $divide: [
                { $arrayElemAt: ["$sales_data.total_sold", 0] },
                { $add: [
                  "$stock_quantity",
                  { $arrayElemAt: ["$sales_data.total_sold", 0] }
                ]}
              ]
            },
            100
          ]
        },
        revenue_per_unit: {
          $cond: {
            if: {
              $gt: [
                { $arrayElemAt: ["$sales_data.total_sold", 0] },
                0
              ]
            },
            then: {
              $divide: [
                { $arrayElemAt: ["$sales_data.total_revenue", 0] },
                { $arrayElemAt: ["$sales_data.total_sold", 0] }
              ]
            },
            else: 0
          }
        }
      }
    }
  },
  {
    $sort: { "sales_summary.total_revenue": -1 }
  }
]);
```

### Ví dụ Many-to-Many Relationship

```js
// Giả sử có collection orders_products (junction table)
db.orders_products.insertMany([
  { order_id: "O001", product_id: "P001", quantity: 1 },
  { order_id: "O001", product_id: "P002", quantity: 2 },
  { order_id: "O002", product_id: "P003", quantity: 1 }
]);

// Many-to-Many join
db.orders.aggregate([
  {
    $lookup: {
      from: "orders_products",
      localField: "_id",
      foreignField: "order_id",
      as: "order_products"
    }
  },
  { $unwind: "$order_products" },
  {
    $lookup: {
      from: "products",
      localField: "order_products.product_id",
      foreignField: "_id",
      as: "product"
    }
  },
  {
    $group: {
      _id: "$_id",
      order_date: { $first: "$order_date" },
      products: {
        $push: {
          product: { $arrayElemAt: ["$product", 0] },
          quantity: "$order_products.quantity"
        }
      }
    }
  }
]);
```

## Khi nào nên dùng $lookup với let và pipeline?

### Khi cần truyền nhiều biến từ collection chính sang sub-pipeline

- Dạng đơn giản chỉ cho phép:
  ```js
  localField: "_id",
  foreignField: "user_id"
  ```

→ Tức là chỉ join theo 1 trường.

Nhưng nếu ta cần truyền **nhiều giá trị** vào pipeline để filter (ví dụ: userId, status, roles,…), thì phải dùng:

```js
let: { userId: "$_id", status: "$status" }
```

- Và trong pipeline:
  ```js
  $match: {
     $expr: {
        $and: [
           { $eq: ["$user_id", "$$userId"] },
           { $eq: ["$status", "$$status"] }
        ]
     }
  }
  ```

### Khi cần join có điều kiện phức tạp

Nếu điều kiện join không đơn thuần là `localField = foreignField` mà có logic:

* So sánh nhiều field
* So sánh field với giá trị tính toán (use expression)
* Kết hợp `$and`, `$or`, `$gte`, `$lte`,…

Ví dụ join order theo khoảng thời gian của user:

```js
$lookup: {
  from: "orders",
  let: { uId: "$_id", start: "$startDate", end: "$endDate" },
  pipeline: [
     {
       $match: {
         $expr: {
           $and: [
             { $eq: ["$user_id", "$$uId"] },
             { $gte: ["$createdAt", "$$start"] },
             { $lte: ["$createdAt", "$$end"] }
           ]
         }
       }
     }
  ],
  as: "orders"
}
```

### Khi cần sort/limit trong lookup

- Dạng đơn giản **không hỗ trợ** `$sort`, `$limit`, `$project`, `$group`…, nhưng pipeline thì hỗ trợ đầy đủ.
- Ví dụ lấy **3 comments mới nhất** cho mỗi bài post:

  ```js
  $lookup: {
    from: "comments",
    let: { postId: "$_id" },
    pipeline: [
      { $match: { $expr: { $eq: ["$post_id", "$$postId"] } } },
      { $sort: { createdAt: -1 } },
      { $limit: 3 }
    ],
    as: "recentComments"
  }
  ```

### Khi cần project hoặc transform dữ liệu trước khi gán vào `as`

- Dạng đơn giản không project được.
- Nhưng pipeline có thể:
  ```js
  $lookup: {
    from: "orders",
    let: { uId: "$_id" },
    pipeline: [
      { 
        $match: { 
          $expr: { $eq: ["$user_id", "$$uId"] } 
        } 
      },
      { 
        $project: { 
          total: 1, 
          createdAt: 1, 
          _id: 0 
        } 
      }
    ],
    as: "orders"
  }
  ```

| Trường hợp                         | Dùng dạng đơn giản | Dùng let + pipeline |
| ------------------------------------- | ----------------------- | -------------------- |
| Join 1 field = 1 field                | ✔                      | ❌                   |
| Join theo nhiều điều kiện         | ❌                      | ✔                   |
| So sánh phức tạp, dùng expression | ❌                      | ✔                   |
| Sort/limit/project/group trong join   | ❌                      | ✔                   |
| Chỉ cần join cơ bản               | ✔                      | ❌                   |

## Giới hạn và lưu ý

:::caution[Lưu ý]

1. **Memory Limit** : Mỗi stage không được vượt quá 100MB RAM (có thể dùng `allowDiskUse`)
2. **No Right Join** : MongoDB chỉ hỗ trợ left outer join
3. **Performance** : Lookup nhiều collections có thể chậm, cần tối ưu
4. **Denormalization** : Đôi khi denormalize data tốt hơn là join nhiều

:::

:::tip[Best practices]

1. **Luôn dùng Index** : Đảm bảo các fields dùng trong lookup có index
2. **Filter sớm** : Dùng `$match` trước `$lookup` khi có thể
3. **Project sớm** : Giảm kích thước document trước khi lookup
4. **Tránh Unwind không cần thiết** : Chỉ dùng `$unwind` khi thực sự cần
5. **Sử dụng Pipeline trong Lookup** : Cho các join phức tạp
6. **Giới hạn kết quả** : Dùng `$limit` sau lookup để tránh memory issues
7. **Monitor Performance** : Theo dõi execution time và memory usage

:::
