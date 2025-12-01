---
sidebar_position: 4
---
# $group

## `$group()` là gì?

:::info

- `$group` là một stage trong **MongoDB Aggregation Pipeline** dùng để nhóm các documents lại với nhau dựa trên một hoặc nhiều trường cụ thể, sau đó thực hiện các phép tính tổng hợp (aggregation) trên các nhóm đó.

:::

## Cú pháp cơ bản

```js
{
  $group: {
    _id: <expression>,  // Trường hoặc biểu thức để nhóm
    <field1>: { <accumulator1>: <expression1> },
    <field2>: { <accumulator2>: <expression2> },
    ...
  }
}
```

## Các toán tử tích lũy (accumulator operators)

### Toán tử cơ bản

| Toán tử  | Mô tả                    |
| ---------- | -------------------------- |
| `$sum`   | Tính tổng                |
| `$avg`   | Tính trung bình          |
| `$min`   | Tìm giá trị nhỏ nhất  |
| `$max`   | Tìm giá trị lớn nhất  |
| `$first` | Lấy giá trị đầu tiên |
| `$last`  | Lấy giá trị cuối cùng |

### Toán tử nâng cao

| Toán tử         | Mô tả                                                   |
| ----------------- | --------------------------------------------------------- |
| `$push`         | Tạo mảng chứa các giá trị                           |
| `$addToSet`     | Tạo mảng chứa các giá trị duy nhất (không trùng) |
| `$stdDevPop`    | Độ lệch chuẩn của tổng thể                         |
| `$stdDevSamp`   | Độ lệch chuẩn của mẫu                               |
| `$mergeObjects` | Hợp nhất các documents                                 |

## Ví dụ

- Giả sử ta có dữ liệu:

```js
// Collection: orders
db.orders.insertMany([
  {
    _id: 1,
    customer_id: "C001",
    product: "Laptop",
    category: "Electronics",
    amount: 1200,
    quantity: 1,
    date: ISODate("2024-01-15"),
    location: { city: "Hanoi", country: "Vietnam" },
  },
  {
    _id: 2,
    customer_id: "C002",
    product: "Mouse",
    category: "Electronics",
    amount: 50,
    quantity: 2,
    date: ISODate("2024-01-16"),
    location: { city: "HCMC", country: "Vietnam" },
  },
  {
    _id: 3,
    customer_id: "C001",
    product: "Keyboard",
    category: "Electronics",
    amount: 80,
    quantity: 1,
    date: ISODate("2024-01-17"),
    location: { city: "Hanoi", country: "Vietnam" },
  },
  {
    _id: 4,
    customer_id: "C003",
    product: "Shirt",
    category: "Fashion",
    amount: 30,
    quantity: 3,
    date: ISODate("2024-01-15"),
    location: { city: "Hanoi", country: "Vietnam" },
  },
  {
    _id: 5,
    customer_id: "C002",
    product: "Monitor",
    category: "Electronics",
    amount: 300,
    quantity: 1,
    date: ISODate("2024-01-18"),
    location: { city: "HCMC", country: "Vietnam" },
  },
]);
```

### Ví dụ cơ bản

**Ví dụ 1: Nhóm theo một trường đơn**

```js
// Nhóm theo category và tính tổng amount
db.orders.aggregate([
  {
    $group: {
      _id: "$category",
      totalAmount: { $sum: "$amount" },
      totalOrders: { $sum: 1 },
    },
  },
]);

// Kết quả:
[
  { _id: "Electronics", totalAmount: 1630, totalOrders: 4 },
  { _id: "Fashion", totalAmount: 30, totalOrders: 1 },
];
```

**Ví dụ 2: Tính giá trị trung bình**

```js
// Tính giá trung bình của từng category
db.orders.aggregate([
  {
    $group: {
      _id: "$category",
      averageAmount: { $avg: "$amount" },
      minAmount: { $min: "$amount" },
      maxAmount: { $max: "$amount" },
    },
  },
]);
```

**Ví dụ 3: Nhóm theo nhiều trường**

```js
// Nhóm theo category và location.city
db.orders.aggregate([
  {
    $group: {
      _id: {
        category: "$category",
        city: "$location.city",
      },
      totalAmount: { $sum: "$amount" },
      count: { $sum: 1 },
    },
  },
]);

// Kết quả:
[
  {
    _id: { category: "Electronics", city: "Hanoi" },
    totalAmount: 1280,
    count: 2,
  },
  {
    _id: { category: "Electronics", city: "HCMC" },
    totalAmount: 350,
    count: 2,
  },
  { _id: { category: "Fashion", city: "Hanoi" }, totalAmount: 30, count: 1 },
];
```

### Ví dụ trung cấp

**Sử dụng `$push` để thu thập dữ liệu**

```js
// Thu thập tất cả products trong mỗi category
db.orders.aggregate([
  {
    $group: {
      _id: "$category",
      products: { $push: "$product" },
      customers: { $push: "$customer_id" },
      allDetails: { $push: { product: "$product", amount: "$amount" } },
    },
  },
]);

// Kết quả:
[
  {
    _id: "Electronics",
    products: ["Laptop", "Mouse", "Keyboard", "Monitor"],
    customers: ["C001", "C002", "C001", "C002"],
    allDetails: [
      { product: "Laptop", amount: 1200 },
      { product: "Mouse", amount: 50 },
      { product: "Keyboard", amount: 80 },
      { product: "Monitor", amount: 300 },
    ],
  },
];
```

**Sử dụng `$addToSet` để loại bỏ trùng lặp**

```js
// Lấy danh sách khách hàng duy nhất theo city
db.orders.aggregate([
  {
    $group: {
      _id: "$location.city",
      uniqueCustomers: { $addToSet: "$customer_id" },
      customerCount: { $sum: 1 },
    },
  },
]);

// Kết quả:
[
  { _id: "Hanoi", uniqueCustomers: ["C001", "C003"], customerCount: 3 },
  { _id: "HCMC", uniqueCustomers: ["C002"], customerCount: 2 },
];
```

**Tính toán phức tạp**

```js
// Tính tổng doanh thu và số lượng trung bình
db.orders.aggregate([
  {
    $group: {
      _id: "$customer_id",
      totalSpent: { $sum: { $multiply: ["$amount", "$quantity"] } },
      avgOrderValue: { $avg: "$amount" },
      orderCount: { $sum: 1 },
      firstOrderDate: { $min: "$date" },
      lastOrderDate: { $max: "$date" },
    },
  },
]);
```

### Ví dụ nâng cao

**Nhóm với biểu thức phức tạp**

```js
// Nhóm theo năm-tháng và tính toán
db.orders.aggregate([
  {
    $group: {
      _id: {
        year: { $year: "$date" },
        month: { $month: "$date" },
      },
      monthlyRevenue: { $sum: "$amount" },
      dailyStats: {
        $push: {
          day: { $dayOfMonth: "$date" },
          amount: "$amount",
          product: "$product",
        },
      },
      // Tính độ lệch chuẩn
      amountStdDev: { $stdDevPop: "$amount" },
    },
  },
  {
    $sort: { "_id.year": 1, "_id.month": 1 },
  },
]);
```

**Sử dụng `$mergeObjects` để hợp nhất**

- Giả sử collection có 5 documents:
  ```json
  [
    {
      "_id": 1,
      "customer_id": "C01",
      "location": { "city": "Hanoi", "country": "Vietnam" }
    },
    {
      "_id": 2,
      "customer_id": "C01",
      "location": { "city": "Saigon", "country": "Vietnam" }
    },
    {
      "_id": 3,
      "customer_id": "C01",
      "location": { "city": "Hanoi", "country": "Vietnam" }
    },
    {
      "_id": 4,
      "customer_id": "C02",
      "location": { "city": "Bangkok", "country": "Thailand" }
    },
    {
      "_id": 5,
      "customer_id": "C02",
      "location": { "city": "Chiang Mai", "country": "Thailand" }
    }
  ]
  ```

```js
// Hợp nhất thông tin location
db.orders.aggregate([
  {
    $group: {
      _id: "$customer_id",
      customerInfo: {
        $mergeObjects: {
          city: "$location.city",
          country: "$location.country",
        },
      },
      allLocations: { $addToSet: "$location.city" },
    },
  },
  {
    $project: {
      customerId: "$_id",
      city: "$customerInfo.city",
      country: "$customerInfo.country",
      visitedCities: "$allLocations",
      _id: 0,
    },
  },
]);
```

- Kết quả đầu ra:

  ```json
  [
    {
      "customerId": "C01",
      "city": "Hanoi",
      "country": "Vietnam",
      "visitedCities": ["Hanoi", "Saigon"]
    },
    {
      "customerId": "C02",
      "city": "Chiang Mai",
      "country": "Thailand",
      "visitedCities": ["Bangkok", "Chiang Mai"]
    }
  ]
  ```
- Ví dụ tiếp theo:

  ```js
  db.sales.insertMany([
    {
      _id: 1,
      year: 2017,
      item: "A",
      quantity: { "2017Q1": 500, "2017Q2": 500 },
    },
    {
      _id: 2,
      year: 2016,
      item: "A",
      quantity: { "2016Q1": 400, "2016Q2": 300, "2016Q3": 0, "2016Q4": 0 },
    },
    { _id: 3, year: 2017, item: "B", quantity: { "2017Q1": 300 } },
    {
      _id: 4,
      year: 2016,
      item: "B",
      quantity: { "2016Q3": 100, "2016Q4": 250 },
    },
  ]);
  ```

```js
db.sales.aggregate([
  { $group: { _id: "$item", mergedSales: { $mergeObjects: "$quantity" } } },
]);
```

```json
{
  _id: 'A',
  mergedSales: { '2017Q1': 500, '2017Q2': 500, '2016Q1': 400, '2016Q2': 300, '2016Q3': 0, '2016Q4': 0 }
},
{
  _id: 'B',
  mergedSales: { '2017Q1': 300, '2016Q3': 100, '2016Q4': 250 }
}
```

**Pipeline phức tạp với nhiều giai đoạn**

```js
// Phân tích doanh thu theo category và location
db.orders.aggregate([
  // Giai đoạn 1: Lọc dữ liệu
  {
    $match: {
      amount: { $gt: 0 },
    },
  },
  // Giai đoạn 2: Thêm trường mới
  {
    $addFields: {
      revenue: { $multiply: ["$amount", "$quantity"] },
    },
  },
  // Giai đoạn 3: Nhóm theo category và city
  {
    $group: {
      _id: {
        category: "$category",
        city: "$location.city",
      },
      totalRevenue: { $sum: "$revenue" },
      avgTransaction: { $avg: "$revenue" },
      transactionCount: { $sum: 1 },
      products: { $addToSet: "$product" },
      // Tính phần trăm đóng góp
      revenueBreakdown: {
        $push: {
          product: "$product",
          amount: "$revenue",
          percentage: {
            $multiply: [{ $divide: ["$revenue", { $sum: "$revenue" }] }, 100],
          },
        },
      },
    },
  },
  // Giai đoạn 4: Sắp xếp
  {
    $sort: { totalRevenue: -1 },
  },
  // Giai đoạn 5: Chỉ lấy kết quả quan trọng
  {
    $project: {
      category: "$_id.category",
      city: "$_id.city",
      totalRevenue: 1,
      avgTransaction: { $round: ["$avgTransaction", 2] },
      transactionCount: 1,
      productCount: { $size: "$products" },
      _id: 0,
    },
  },
]);
```

## Performance Optimization

```js
// Sử dụng index để tối ưu hiệu suất
db.orders.createIndex({ category: 1, date: -1 });

// Nhóm với $match trước để giảm số lượng documents
db.orders.aggregate([
  {
    $match: {
      date: { $gte: ISODate("2024-01-01"), $lte: ISODate("2024-01-31") }
    }
  },
  {
    $group: { ... }
  }
]);
```

## Ví dụ thực tế

### Phân tích khách hàng

```js
// Tìm top 3 khách hàng có tổng chi tiêu cao nhất
db.orders.aggregate([
  {
    $group: {
      _id: "$customer_id",
      totalSpent: { $sum: "$amount" },
      orderCount: { $sum: 1 },
      firstPurchase: { $min: "$date" },
      lastPurchase: { $max: "$date" },
    },
  },
  { $sort: { totalSpent: -1 } },
  { $limit: 3 },
  {
    $project: {
      customerId: "$_id",
      totalSpent: 1,
      orderCount: 1,
      customerSince: "$firstPurchase",
      daysActive: {
        $divide: [
          { $subtract: ["$lastPurchase", "$firstPurchase"] },
          1000 * 60 * 60 * 24, // Chuyển từ milliseconds sang days
        ],
      },
      _id: 0,
    },
  },
]);
```

### Phân tích sản phẩm

```js
// Phân tích hiệu suất sản phẩm
db.orders.aggregate([
  {
    $group: {
      _id: "$product",
      category: { $first: "$category" },
      totalRevenue: { $sum: { $multiply: ["$amount", "$quantity"] } },
      totalUnits: { $sum: "$quantity" },
      avgPrice: { $avg: "$amount" },
      customers: { $addToSet: "$customer_id" },
      locations: { $addToSet: "$location.city" },
    },
  },
  {
    $addFields: {
      avgRevenuePerCustomer: {
        $divide: ["$totalRevenue", { $size: "$customers" }],
      },
      geographicCoverage: { $size: "$locations" },
    },
  },
  {
    $project: {
      product: "$_id",
      category: 1,
      totalRevenue: 1,
      totalUnits: 1,
      avgPrice: { $round: ["$avgPrice", 2] },
      customerCount: { $size: "$customers" },
      avgRevenuePerCustomer: { $round: ["$avgRevenuePerCustomer", 2] },
      geographicCoverage: 1,
      _id: 0,
    },
  },
  { $sort: { totalRevenue: -1 } },
]);
```

## Kết luận

`$group` là một trong những stage mạnh mẽ nhất trong MongoDB Aggregation Pipeline. Khi sử dụng hiệu quả, nó cho phép:

1. **Phân tích dữ liệu phức tạp** với nhiều mức độ tổng hợp
2. **Xử lý lượng dữ liệu lớn** một cách hiệu quả
3. **Tạo báo cáo động** với các chỉ số kinh doanh
4. **Phân tích hành vi** người dùng và xu hướng
5. **Tối ưu hiệu suất** với proper indexing và pipeline design

Luôn nhớ:

* Sử dụng `$match` sớm để giảm số lượng documents
* Tạo index cho các trường được dùng trong `$group`
* Kiểm tra kiểu dữ liệu trước khi thực hiện các phép tính
* Sử dụng `$project` để giảm kích thước dữ liệu giữa các stage

Với sự linh hoạt và mạnh mẽ, `$group` là công cụ không thể thiếu cho bất kỳ ứng dụng phân tích dữ liệu nào sử dụng MongoDB.
