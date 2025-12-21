---
sidebar_position: 8
---
# $facet

## Định nghĩa

:::info

- `$facet` là một stage trong **MongoDB Aggregation Pipeline** cho phép thực hiện **nhiều aggregation pipelines song song** trên cùng một tập dữ liệu đầu vào. Nó tạo ra các kết quả đa chiều trong một single aggregation operation.
- Chúng ta cần `$facet` để:
  - Thực hiện nhiều phân tích khác nhau trên cùng dữ liệu
  - Tối ưu hiệu suất: tránh phải chạy nhiều queries riêng biệt
  - Tạo các báo cáo đa chiều (multi-dimensional reports)
  - Hỗ trợ pagination phức tạp

:::

## Cú pháp

```javascript
{
  $facet: {
    <outputField1>: [ <pipeline1> ],
    <outputField2>: [ <pipeline2> ],
    ...
  }
}
```

## Ví dụ

### Dữ liệu mẫu

- Ta có collection **ecommerce_orders** như sau:

```javascript
db.ecommerce_orders.insertMany([
  {
    _id: "ORD001",
    customer_id: "CUST001",
    customer_name: "Nguyen Van A",
    customer_email: "a.nguyen@email.com",
    order_date: ISODate("2024-01-15T10:30:00Z"),
    status: "completed",
    payment_method: "credit_card",
    shipping_method: "express",
    total_amount: 2500000,
    items: [
      {
        product_id: "P001",
        name: "Laptop Dell XPS 13",
        category: "Electronics",
        subcategory: "Laptops",
        price: 2000000,
        quantity: 1,
        discount: 100000,
      },
      {
        product_id: "P002",
        name: "Wireless Mouse",
        category: "Electronics",
        subcategory: "Accessories",
        price: 250000,
        quantity: 2,
        discount: 0,
      },
    ],
    shipping_address: {
      city: "Hanoi",
      district: "Cau Giay",
      country: "Vietnam",
    },
    metadata: {
      browser: "Chrome",
      device: "desktop",
      ip_address: "192.168.1.1",
    },
  },
  {
    _id: "ORD002",
    customer_id: "CUST002",
    customer_name: "Tran Thi B",
    customer_email: "b.tran@email.com",
    order_date: ISODate("2024-01-20T14:45:00Z"),
    status: "pending",
    payment_method: "paypal",
    shipping_method: "standard",
    total_amount: 800000,
    items: [
      {
        product_id: "P003",
        name: "Keyboard Mechanical",
        category: "Electronics",
        subcategory: "Accessories",
        price: 800000,
        quantity: 1,
        discount: 0,
      },
    ],
    shipping_address: {
      city: "HCMC",
      district: "District 1",
      country: "Vietnam",
    },
    metadata: {
      browser: "Safari",
      device: "mobile",
      ip_address: "192.168.1.2",
    },
  },
  {
    _id: "ORD003",
    customer_id: "CUST001",
    customer_name: "Nguyen Van A",
    customer_email: "a.nguyen@email.com",
    order_date: ISODate("2024-02-05T09:15:00Z"),
    status: "completed",
    payment_method: "credit_card",
    shipping_method: "express",
    total_amount: 1500000,
    items: [
      {
        product_id: "P004",
        name: "Monitor 27inch 4K",
        category: "Electronics",
        subcategory: "Monitors",
        price: 700000,
        quantity: 1,
        discount: 0,
      },
      {
        product_id: "P005",
        name: "USB-C Cable",
        category: "Electronics",
        subcategory: "Cables",
        price: 800000,
        quantity: 1,
        discount: 0,
      },
    ],
    shipping_address: {
      city: "Hanoi",
      district: "Cau Giay",
      country: "Vietnam",
    },
    metadata: {
      browser: "Chrome",
      device: "desktop",
      ip_address: "192.168.1.1",
    },
  },
  {
    _id: "ORD004",
    customer_id: "CUST003",
    customer_name: "Le Van C",
    customer_email: "c.le@email.com",
    order_date: ISODate("2024-02-10T16:20:00Z"),
    status: "cancelled",
    payment_method: "bank_transfer",
    shipping_method: "standard",
    total_amount: 3000000,
    items: [
      {
        product_id: "P001",
        name: "Laptop Dell XPS 13",
        category: "Electronics",
        subcategory: "Laptops",
        price: 2000000,
        quantity: 1,
        discount: 0,
      },
      {
        product_id: "P006",
        name: "Printer Laser",
        category: "Electronics",
        subcategory: "Printers",
        price: 1000000,
        quantity: 1,
        discount: 0,
      },
    ],
    shipping_address: {
      city: "Danang",
      district: "Hai Chau",
      country: "Vietnam",
    },
    metadata: {
      browser: "Firefox",
      device: "desktop",
      ip_address: "192.168.1.3",
    },
  },
  {
    _id: "ORD005",
    customer_id: "CUST004",
    customer_name: "Pham Thi D",
    customer_email: "d.pham@email.com",
    order_date: ISODate("2024-02-15T11:30:00Z"),
    status: "shipped",
    payment_method: "credit_card",
    shipping_method: "express",
    total_amount: 4500000,
    items: [
      {
        product_id: "P007",
        name: "Gaming Laptop",
        category: "Electronics",
        subcategory: "Laptops",
        price: 4500000,
        quantity: 1,
        discount: 500000,
      },
    ],
    shipping_address: {
      city: "Hanoi",
      district: "Ba Dinh",
      country: "Vietnam",
    },
    metadata: {
      browser: "Chrome",
      device: "desktop",
      ip_address: "192.168.1.4",
    },
  },
  {
    _id: "ORD006",
    customer_id: "CUST002",
    customer_name: "Tran Thi B",
    customer_email: "b.tran@email.com",
    order_date: ISODate("2024-03-01T13:45:00Z"),
    status: "completed",
    payment_method: "paypal",
    shipping_method: "standard",
    total_amount: 1200000,
    items: [
      {
        product_id: "P002",
        name: "Wireless Mouse",
        category: "Electronics",
        subcategory: "Accessories",
        price: 250000,
        quantity: 2,
        discount: 0,
      },
      {
        product_id: "P008",
        name: "Mouse Pad",
        category: "Accessories",
        subcategory: "Desk Accessories",
        price: 700000,
        quantity: 1,
        discount: 0,
      },
    ],
    shipping_address: {
      city: "HCMC",
      district: "District 3",
      country: "Vietnam",
    },
    metadata: {
      browser: "Safari",
      device: "tablet",
      ip_address: "192.168.1.5",
    },
  },
]);
```

### Ví dụ cơ bản

**Ví dụ 1: Phân tích đơn giản với nhiều metrics**

```javascript
db.ecommerce_orders.aggregate([
  {
    $facet: {
      // Pipeline 1: Thống kê tổng quan
      summary_stats: [
        {
          $group: {
            _id: null,
            total_orders: { $sum: 1 },
            total_revenue: { $sum: "$total_amount" },
            avg_order_value: { $avg: "$total_amount" },
            min_order: { $min: "$total_amount" },
            max_order: { $max: "$total_amount" }
          }
        }
      ],

      // Pipeline 2: Phân bố theo status
      status_distribution: [
        {
          $group: {
            _id: "$status",
            count: { $sum: 1 },
            total_revenue: { $sum: "$total_amount" }
          }
        },
        { $sort: { count: -1 } }
      ],

      // Pipeline 3: Phân bố theo payment method
      payment_methods: [
        {
          $group: {
            _id: "$payment_method",
            count: { $sum: 1 },
            percentage: {
              $avg: { $multiply: [100, 1] }
            }
          }
        }
      ]
    }
  }
]);

// Kết quả:
{
  summary_stats: [
    {
      _id: null,
      total_orders: 6,
      total_revenue: 13500000,
      avg_order_value: 2250000,
      min_order: 800000,
      max_order: 4500000
    }
  ],
  status_distribution: [
    { _id: "completed", count: 3, total_revenue: 5200000 },
    { _id: "cancelled", count: 1, total_revenue: 3000000 },
    { _id: "shipped", count: 1, total_revenue: 4500000 },
    { _id: "pending", count: 1, total_revenue: 800000 }
  ],
  payment_methods: [
    { _id: "credit_card", count: 3, percentage: 50 },
    { _id: "paypal", count: 2, percentage: 33.33 },
    { _id: "bank_transfer", count: 1, percentage: 16.67 }
  ]
}
```

**Ví dụ 2: Phân tích theo thời gian**

```javascript
db.ecommerce_orders.aggregate([
  {
    $facet: {
      // Daily statistics
      daily_stats: [
        {
          $group: {
            _id: {
              year: { $year: "$order_date" },
              month: { $month: "$order_date" },
              day: { $dayOfMonth: "$order_date" },
            },
            orders_count: { $sum: 1 },
            daily_revenue: { $sum: "$total_amount" },
            avg_order_value: { $avg: "$total_amount" },
          },
        },
        { $sort: { "_id.year": 1, "_id.month": 1, "_id.day": 1 } },
      ],

      // Monthly statistics
      monthly_stats: [
        {
          $group: {
            _id: {
              year: { $year: "$order_date" },
              month: { $month: "$order_date" },
            },
            orders_count: { $sum: 1 },
            monthly_revenue: { $sum: "$total_amount" },
            avg_daily_orders: {
              $avg: { $dayOfMonth: "$order_date" },
            },
          },
        },
        { $sort: { "_id.year": 1, "_id.month": 1 } },
      ],

      // Hourly distribution
      hourly_distribution: [
        {
          $group: {
            _id: { $hour: "$order_date" },
            orders_count: { $sum: 1 },
            percentage: {
              $avg: { $multiply: [100, 1] },
            },
          },
        },
        { $sort: { _id: 1 } },
      ],
    },
  },
]);
```

### Ví dụ trung cấp

**Ví dụ 3: Phân tích khách hàng và sản phẩm**

```javascript
db.ecommerce_orders.aggregate([
  { $unwind: "$items" }, // Unwind trước $facet để xử lý items
  {
    $facet: {
      // Customer Analysis
      customer_analysis: [
        {
          $group: {
            _id: "$customer_id",
            customer_name: { $first: "$customer_name" },
            total_orders: { $sum: 1 },
            total_spent: { $sum: "$items.price" },
            avg_order_value: {
              $avg: { $multiply: ["$items.price", "$items.quantity"] },
            },
            favorite_category: {
              $addToSet: "$items.category",
            },
          },
        },
        { $sort: { total_spent: -1 } },
        { $limit: 10 },
      ],

      // Product Analysis
      product_analysis: [
        {
          $group: {
            _id: "$items.product_id",
            product_name: { $first: "$items.name" },
            category: { $first: "$items.category" },
            subcategory: { $first: "$items.subcategory" },
            total_sold: { $sum: "$items.quantity" },
            total_revenue: {
              $sum: { $multiply: ["$items.price", "$items.quantity"] },
            },
            avg_price: { $avg: "$items.price" },
            unique_customers: { $addToSet: "$customer_id" },
          },
        },
        {
          $addFields: {
            customer_count: { $size: "$unique_customers" },
          },
        },
        { $sort: { total_revenue: -1 } },
      ],

      // Category Analysis
      category_analysis: [
        {
          $group: {
            _id: "$items.category",
            subcategories: { $addToSet: "$items.subcategory" },
            products_count: { $addToSet: "$items.product_id" },
            total_items_sold: { $sum: "$items.quantity" },
            category_revenue: {
              $sum: { $multiply: ["$items.price", "$items.quantity"] },
            },
            avg_item_price: { $avg: "$items.price" },
          },
        },
        {
          $addFields: {
            unique_products: { $size: "$products_count" },
            unique_subcategories: { $size: "$subcategories" },
          },
        },
        { $sort: { category_revenue: -1 } },
      ],
    },
  },
]);
```

**Ví dụ 4: Phân tích địa lý và thiết bị**

```javascript
db.ecommerce_orders.aggregate([
  {
    $facet: {
      // Geographic Analysis
      geographic_analysis: [
        {
          $group: {
            _id: {
              city: "$shipping_address.city",
              district: "$shipping_address.district",
            },
            orders_count: { $sum: 1 },
            total_revenue: { $sum: "$total_amount" },
            avg_order_value: { $avg: "$total_amount" },
            customers: { $addToSet: "$customer_id" },
          },
        },
        {
          $group: {
            _id: "$_id.city",
            districts: {
              $push: {
                district: "$_id.district",
                orders_count: "$orders_count",
                revenue: "$total_revenue",
              },
            },
            city_orders: { $sum: "$orders_count" },
            city_revenue: { $sum: "$total_revenue" },
            unique_customers: { $sum: { $size: "$customers" } },
          },
        },
        { $sort: { city_revenue: -1 } },
      ],

      // Device & Browser Analysis
      technology_analysis: [
        {
          $group: {
            _id: {
              browser: "$metadata.browser",
              device: "$metadata.device",
            },
            sessions: { $sum: 1 },
            total_revenue: { $sum: "$total_amount" },
            conversion_rate: {
              $avg: {
                $cond: [{ $in: ["$status", ["completed", "shipped"]] }, 1, 0],
              },
            },
          },
        },
        {
          $group: {
            _id: "$_id.browser",
            devices: {
              $push: {
                device: "$_id.device",
                sessions: "$sessions",
                revenue: "$total_revenue",
              },
            },
            browser_sessions: { $sum: "$sessions" },
            browser_revenue: { $sum: "$total_revenue" },
            avg_conversion_rate: { $avg: "$conversion_rate" },
          },
        },
        { $sort: { browser_revenue: -1 } },
      ],

      // Time-based Device Usage
      time_device_analysis: [
        {
          $group: {
            _id: {
              hour: { $hour: "$order_date" },
              device: "$metadata.device",
            },
            orders_count: { $sum: 1 },
            peak_hour: { $first: { $hour: "$order_date" } },
          },
        },
        {
          $group: {
            _id: "$_id.device",
            hourly_distribution: {
              $push: {
                hour: "$_id.hour",
                orders: "$orders_count",
              },
            },
            total_orders: { $sum: "$orders_count" },
            peak_hour: {
              $max: {
                hour: "$_id.hour",
                orders: "$orders_count",
              },
            },
          },
        },
      ],
    },
  },
]);
```

### Ví dụ nâng cao

**Ví dụ 5: E-commerce Dashboard Analytics**

```javascript
db.ecommerce_orders.aggregate([
  {
    $facet: {
      // 1. OVERVIEW METRICS
      overview: [
        {
          $group: {
            _id: null,
            // Basic Metrics
            total_orders: { $sum: 1 },
            total_revenue: { $sum: "$total_amount" },
            avg_order_value: { $avg: "$total_amount" },

            // Customer Metrics
            unique_customers: { $addToSet: "$customer_id" },
            repeat_customers: {
              $sum: {
                $cond: [
                  { $gt: [{ $size: { $setUnion: ["$customer_id", []] } }, 1] },
                  1,
                  0,
                ],
              },
            },

            // Time Metrics
            first_order_date: { $min: "$order_date" },
            last_order_date: { $max: "$order_date" },
          },
        },
        {
          $addFields: {
            customer_count: { $size: "$unique_customers" },
            repeat_customer_rate: {
              $multiply: [
                { $divide: ["$repeat_customers", "$customer_count"] },
                100,
              ],
            },
            days_active: {
              $divide: [
                { $subtract: ["$last_order_date", "$first_order_date"] },
                1000 * 60 * 60 * 24,
              ],
            },
            avg_daily_orders: {
              $divide: ["$total_orders", "$days_active"],
            },
            avg_daily_revenue: {
              $divide: ["$total_revenue", "$days_active"],
            },
          },
        },
      ],

      // 2. SALES PERFORMANCE
      sales_performance: [
        {
          $group: {
            _id: {
              year: { $year: "$order_date" },
              month: { $month: "$order_date" },
              week: { $week: "$order_date" },
            },
            weekly_revenue: { $sum: "$total_amount" },
            weekly_orders: { $sum: 1 },
            daily_avg: { $avg: "$total_amount" },
          },
        },
        {
          $group: {
            _id: {
              year: "$_id.year",
              month: "$_id.month",
            },
            weeks: {
              $push: {
                week: "$_id.week",
                revenue: "$weekly_revenue",
                orders: "$weekly_orders",
                daily_avg: "$daily_avg",
              },
            },
            monthly_revenue: { $sum: "$weekly_revenue" },
            monthly_orders: { $sum: "$weekly_orders" },
            growth_rate: {
              $avg: {
                $multiply: [
                  {
                    $divide: ["$weekly_revenue", { $avg: "$weekly_revenue" }],
                  },
                  100,
                ],
              },
            },
          },
        },
        { $sort: { "_id.year": -1, "_id.month": -1 } },
      ],

      // 3. CUSTOMER SEGMENTATION
      customer_segments: [
        {
          $group: {
            _id: "$customer_id",
            customer_name: { $first: "$customer_name" },
            total_orders: { $sum: 1 },
            total_spent: { $sum: "$total_amount" },
            first_purchase: { $min: "$order_date" },
            last_purchase: { $max: "$order_date" },
            avg_order_value: { $avg: "$total_amount" },
            cities: { $addToSet: "$shipping_address.city" },
          },
        },
        {
          $addFields: {
            customer_lifetime: {
              $divide: [
                { $subtract: ["$last_purchase", "$first_purchase"] },
                1000 * 60 * 60 * 24,
              ],
            },
            purchase_frequency: {
              $divide: ["$total_orders", "$customer_lifetime"],
            },
            segment: {
              $switch: {
                branches: [
                  {
                    case: {
                      $and: [
                        { $gte: ["$total_spent", 5000000] },
                        { $gte: ["$total_orders", 3] },
                      ],
                    },
                    then: "VIP",
                  },
                  {
                    case: {
                      $and: [
                        { $gte: ["$total_spent", 2000000] },
                        { $gte: ["$total_orders", 2] },
                      ],
                    },
                    then: "Loyal",
                  },
                  {
                    case: { $gte: ["$total_orders", 2] },
                    then: "Repeat",
                  },
                ],
                default: "New",
              },
            },
          },
        },
        {
          $group: {
            _id: "$segment",
            customers: {
              $push: {
                customer_id: "$_id",
                name: "$customer_name",
                total_spent: "$total_spent",
                orders: "$total_orders",
              },
            },
            segment_size: { $sum: 1 },
            segment_revenue: { $sum: "$total_spent" },
            avg_customer_value: { $avg: "$total_spent" },
            avg_orders_per_customer: { $avg: "$total_orders" },
          },
        },
        { $sort: { segment_revenue: -1 } },
      ],

      // 4. PRODUCT ANALYSIS
      product_analytics: [
        { $unwind: "$items" },
        {
          $group: {
            _id: "$items.product_id",
            product_name: { $first: "$items.name" },
            category: { $first: "$items.category" },
            subcategory: { $first: "$items.subcategory" },

            // Sales Metrics
            total_quantity: { $sum: "$items.quantity" },
            total_revenue: {
              $sum: { $multiply: ["$items.price", "$items.quantity"] },
            },
            avg_price: { $avg: "$items.price" },

            // Customer Metrics
            unique_customers: { $addToSet: "$customer_id" },
            repeat_purchases: {
              $sum: {
                $cond: [{ $gt: ["$items.quantity", 1] }, 1, 0],
              },
            },

            // Discount Analysis
            total_discount: { $sum: "$items.discount" },
            discount_orders: {
              $sum: {
                $cond: [{ $gt: ["$items.discount", 0] }, 1, 0],
              },
            },
          },
        },
        {
          $addFields: {
            // Product Performance Metrics
            sell_through_rate: {
              $multiply: [
                {
                  $divide: [
                    "$total_quantity",
                    { $add: ["$total_quantity", 100] },
                  ],
                },
                100,
              ],
            },
            customer_reach: { $size: "$unique_customers" },
            avg_quantity_per_order: {
              $divide: ["$total_quantity", "$discount_orders"],
            },
            discount_rate: {
              $multiply: [
                {
                  $divide: [
                    "$total_discount",
                    { $add: ["$total_revenue", "$total_discount"] },
                  ],
                },
                100,
              ],
            },
            profitability_score: {
              $divide: ["$total_revenue", "$total_quantity"],
            },
          },
        },
        {
          $group: {
            _id: "$category",
            products: {
              $push: {
                product_id: "$_id",
                name: "$product_name",
                revenue: "$total_revenue",
                quantity: "$total_quantity",
                avg_price: "$avg_price",
                profitability: "$profitability_score",
              },
            },
            category_revenue: { $sum: "$total_revenue" },
            category_quantity: { $sum: "$total_quantity" },
            top_product: {
              $max: {
                revenue: "$total_revenue",
                product_id: "$_id",
                name: "$product_name",
              },
            },
          },
        },
        { $sort: { category_revenue: -1 } },
      ],

      // 5. FRAUD & RISK ANALYSIS
      risk_analysis: [
        {
          $group: {
            _id: {
              customer_id: "$customer_id",
              ip_address: "$metadata.ip_address",
            },
            orders_count: { $sum: 1 },
            total_amount: { $sum: "$total_amount" },
            devices_used: { $addToSet: "$metadata.device" },
            browsers_used: { $addToSet: "$metadata.browser" },
            cities: { $addToSet: "$shipping_address.city" },
            failed_orders: {
              $sum: {
                $cond: [{ $eq: ["$status", "cancelled"] }, 1, 0],
              },
            },
          },
        },
        {
          $match: {
            $or: [
              { orders_count: { $gt: 3 } },
              { total_amount: { $gt: 10000000 } },
              { $expr: { $gt: [{ $size: "$cities" }, 2] } },
            ],
          },
        },
        {
          $addFields: {
            risk_score: {
              $add: [
                { $multiply: ["$orders_count", 10] },
                { $multiply: [{ $divide: ["$total_amount", 1000000] }, 5] },
                { $multiply: [{ $size: "$cities" }, 20] },
                { $multiply: ["$failed_orders", 30] },
              ],
            },
            risk_level: {
              $switch: {
                branches: [
                  { case: { $gte: ["$risk_score", 100] }, then: "HIGH" },
                  { case: { $gte: ["$risk_score", 50] }, then: "MEDIUM" },
                ],
                default: "LOW",
              },
            },
          },
        },
        { $sort: { risk_score: -1 } },
        { $limit: 10 },
      ],

      // 6. OPERATIONAL METRICS
      operational_metrics: [
        {
          $group: {
            _id: {
              shipping_method: "$shipping_method",
              payment_method: "$payment_method",
            },
            orders_count: { $sum: 1 },
            total_revenue: { $sum: "$total_amount" },
            avg_processing_time: {
              $avg: {
                $cond: [
                  { $eq: ["$status", "completed"] },
                  { $hour: "$order_date" },
                  null,
                ],
              },
            },
            success_rate: {
              $avg: {
                $cond: [{ $in: ["$status", ["completed", "shipped"]] }, 1, 0],
              },
            },
          },
        },
        {
          $group: {
            _id: "$_id.shipping_method",
            payment_methods: {
              $push: {
                method: "$_id.payment_method",
                orders: "$orders_count",
                revenue: "$total_revenue",
                success_rate: "$success_rate",
              },
            },
            total_orders: { $sum: "$orders_count" },
            method_revenue: { $sum: "$total_revenue" },
            overall_success_rate: { $avg: "$success_rate" },
            preferred_payment: {
              $max: {
                orders: "$orders_count",
                method: "$_id.payment_method",
              },
            },
          },
        },
        { $sort: { method_revenue: -1 } },
      ],
    },
  },
  {
    $project: {
      // Combine all facets into a structured dashboard
      dashboard: {
        timestamp: new Date(),
        data_range: {
          start_date: { $arrayElemAt: ["$overview.first_order_date", 0] },
          end_date: { $arrayElemAt: ["$overview.last_order_date", 0] },
        },

        // Key Metrics
        key_metrics: {
          total_orders: { $arrayElemAt: ["$overview.total_orders", 0] },
          total_revenue: { $arrayElemAt: ["$overview.total_revenue", 0] },
          avg_order_value: { $arrayElemAt: ["$overview.avg_order_value", 0] },
          unique_customers: { $arrayElemAt: ["$overview.customer_count", 0] },
          repeat_customer_rate: {
            $arrayElemAt: ["$overview.repeat_customer_rate", 0],
          },
          avg_daily_orders: {
            $arrayElemAt: ["$overview.avg_daily_orders", 0],
          },
          avg_daily_revenue: {
            $arrayElemAt: ["$overview.avg_daily_revenue", 0],
          },
        },

        // Detailed Analyses
        sales_trends: "$sales_performance",
        customer_segments: "$customer_segments",
        product_analytics: "$product_analytics",
        risk_analysis: "$risk_analysis",
        operational_metrics: "$operational_metrics",

        // Summary Stats
        summary: {
          total_categories: { $size: "$product_analytics" },
          total_products: {
            $sum: {
              $map: {
                input: "$product_analytics",
                as: "category",
                in: { $size: "$$category.products" },
              },
            },
          },
          high_risk_customers: { $size: "$risk_analysis" },
          active_shipping_methods: { $size: "$operational_metrics" },
        },
      },
    },
  },
]);
```

**Ví dụ 6: Real-time Analytics với Multiple Dimensions**

```javascript
db.ecommerce_orders.aggregate([
  {
    $match: {
      order_date: {
        $gte: ISODate("2024-01-01"),
        $lte: ISODate("2024-03-31"),
      },
    },
  },
  {
    $facet: {
      // REAL-TIME METRICS
      real_time_metrics: [
        {
          $group: {
            _id: {
              $dateToString: {
                format: "%Y-%m-%d %H:00:00",
                date: "$order_date",
              },
            },
            hourly_orders: { $sum: 1 },
            hourly_revenue: { $sum: "$total_amount" },
            current_hour: { $first: { $hour: "$order_date" } },
          },
        },
        {
          $match: {
            _id: {
              $gte:
                new Date(new Date().setHours(new Date().getHours() - 24))
                  .toISOString()
                  .slice(0, 13) + ":00:00",
            },
          },
        },
        { $sort: { _id: -1 } },
        { $limit: 24 },
      ],

      // GEO-SPATIAL HEATMAP
      geo_heatmap: [
        {
          $group: {
            _id: {
              city: "$shipping_address.city",
              district: "$shipping_address.district",
            },
            order_count: { $sum: 1 },
            revenue: { $sum: "$total_amount" },
            coordinates: {
              $first: {
                // Giả sử có coordinates data
                lat: { $literal: 21.0285 },
                lng: { $literal: 105.8542 },
              },
            },
          },
        },
        {
          $bucket: {
            groupBy: "$order_count",
            boundaries: [0, 5, 10, 20, 50, 100],
            default: "100+",
            output: {
              locations: {
                $push: {
                  city: "$_id.city",
                  district: "$_id.district",
                  orders: "$order_count",
                  revenue: "$revenue",
                  coordinates: "$coordinates",
                },
              },
              total_orders: { $sum: "$order_count" },
            },
          },
        },
      ],

      // CUSTOMER JOURNEY ANALYSIS
      customer_journey: [
        {
          $group: {
            _id: "$customer_id",
            sessions: {
              $push: {
                order_id: "$_id",
                timestamp: "$order_date",
                status: "$status",
                amount: "$total_amount",
                device: "$metadata.device",
                browser: "$metadata.browser",
              },
            },
            first_session: { $min: "$order_date" },
            last_session: { $max: "$order_date" },
            total_sessions: { $sum: 1 },
          },
        },
        {
          $match: {
            total_sessions: { $gt: 1 },
          },
        },
        {
          $addFields: {
            sessions: {
              $sortArray: {
                input: "$sessions",
                sortBy: { timestamp: 1 },
              },
            },
            session_gaps: {
              $map: {
                input: { $range: [1, { $size: "$sessions" }] },
                as: "idx",
                in: {
                  $divide: [
                    {
                      $subtract: [
                        { $arrayElemAt: ["$sessions.timestamp", "$$idx"] },
                        {
                          $arrayElemAt: [
                            "$sessions.timestamp",
                            { $subtract: ["$$idx", 1] },
                          ],
                        },
                      ],
                    },
                    1000 * 60 * 60 * 24, // Convert to days
                  ],
                },
              },
            },
          },
        },
        {
          $group: {
            _id: null,
            avg_session_gap: { $avg: { $avg: "$session_gaps" } },
            common_paths: {
              $push: {
                customer_id: "$_id",
                path: "$sessions.status",
                devices: "$sessions.device",
                total_spent: { $sum: "$sessions.amount" },
              },
            },
            conversion_funnel: {
              $push: {
                from_status: {
                  $arrayElemAt: ["$sessions.status", 0],
                },
                to_status: {
                  $arrayElemAt: ["$sessions.status", -1],
                },
                success: {
                  $cond: [
                    {
                      $eq: [
                        { $arrayElemAt: ["$sessions.status", -1] },
                        "completed",
                      ],
                    },
                    true,
                    false,
                  ],
                },
              },
            },
          },
        },
      ],

      // PREDICTIVE ANALYTICS
      predictive_metrics: [
        {
          $group: {
            _id: {
              $dateToString: {
                format: "%Y-%m-%d",
                date: "$order_date",
              },
            },
            daily_orders: { $sum: 1 },
            daily_revenue: { $sum: "$total_amount" },
            weekday: { $first: { $dayOfWeek: "$order_date" } },
          },
        },
        { $sort: { _id: 1 } },
        {
          $group: {
            _id: null,
            time_series: {
              $push: {
                date: "$_id",
                orders: "$daily_orders",
                revenue: "$daily_revenue",
                weekday: "$weekday",
              },
            },
            avg_daily_orders: { $avg: "$daily_orders" },
            avg_daily_revenue: { $avg: "$daily_revenue" },
            std_dev_orders: { $stdDevPop: "$daily_orders" },
            std_dev_revenue: { $stdDevPop: "$daily_revenue" },
          },
        },
        {
          $addFields: {
            forecast: {
              next_day_orders: {
                $add: [
                  "$avg_daily_orders",
                  { $multiply: ["$std_dev_orders", 0.5] },
                ],
              },
              next_day_revenue: {
                $add: [
                  "$avg_daily_revenue",
                  { $multiply: ["$std_dev_revenue", 0.5] },
                ],
              },
              confidence_interval: {
                lower: {
                  orders: {
                    $subtract: [
                      "$avg_daily_orders",
                      { $multiply: ["$std_dev_orders", 1.96] },
                    ],
                  },
                  revenue: {
                    $subtract: [
                      "$avg_daily_revenue",
                      { $multiply: ["$std_dev_revenue", 1.96] },
                    ],
                  },
                },
                upper: {
                  orders: {
                    $add: [
                      "$avg_daily_orders",
                      { $multiply: ["$std_dev_orders", 1.96] },
                    ],
                  },
                  revenue: {
                    $add: [
                      "$avg_daily_revenue",
                      { $multiply: ["$std_dev_revenue", 1.96] },
                    ],
                  },
                },
              },
            },
          },
        },
      ],
    },
  },
]);
```

## Limitations và Workarounds

### Memory Limitations

```javascript
// Problem: Facet có thể vượt quá 100MB memory limit
// Solution: Sử dụng allowDiskUse và optimize pipeline

db.ecommerce_orders.aggregate(
  [
    {
      $match: {
        /* filter early */
      },
    },
    {
      $project: {
        /* select only needed fields */
      },
    },
    {
      $facet: {
        // Keep each sub-pipeline small
      },
    },
  ],
  { allowDiskUse: true, maxTimeMS: 60000 }
);
```

### Không chia sẻ kết quả giữa các sub-pipelines

```javascript
// Problem: Mỗi sub-pipeline tính toán độc lập
// Solution: Tính toán chung trước facet

db.ecommerce_orders.aggregate([
  {
    $addFields: {
      computed_field: { $multiply: ["$total_amount", 0.9] }
    }
  },
  {
    $facet: {
      pipeline1: [
        { $match: { computed_field: { $gt: 1000000 } } }
      ],
      pipeline2: [
        { $group: { _id: null, avg: { $avg: "$computed_field" } } }
      ]
    }
  }
]);
```

## Best Practices

:::tip

1. **Filter Early** : Dùng `$match` ngay đầu pipeline
2. **Project Early** : Chỉ select fields cần thiết
3. **Use Indexes** : Đảm bảo các fields filtered/sorted có index
4. **Avoid Duplicate Work** : Tính toán chung trước facet
5. **Monitor Memory** : Sử dụng `allowDiskUse` cho large datasets
6. **Limit Sub-pipelines** : Giữ mỗi sub-pipeline đơn giản
7. **Test Performance** : Profile aggregation với `explain()`

:::
