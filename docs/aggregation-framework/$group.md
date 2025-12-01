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

| Toán tử  | Mô tả                 |
| -------- | --------------------- |
| `$sum`   | Tính tổng             |
| `$avg`   | Tính trung bình       |
| `$min`   | Tìm giá trị nhỏ nhất  |
| `$max`   | Tìm giá trị lớn nhất  |
| `$first` | Lấy giá trị đầu tiên  |
| `$last`  | Lấy giá trị cuối cùng |

### Toán tử nâng cao

| Toán tử         | Mô tả                                            |
| --------------- | ------------------------------------------------ |
| `$push`         | Tạo mảng chứa các giá trị                        |
| `$addToSet`     | Tạo mảng chứa các giá trị duy nhất (không trùng) |
| `$stdDevPop`    | Độ lệch chuẩn của tổng thể                       |
| `$stdDevSamp`   | Độ lệch chuẩn của mẫu                            |
| `$mergeObjects` | Hợp nhất các documents                           |

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
