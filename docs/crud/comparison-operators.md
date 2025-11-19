---
sidebar_position: 7
---

# Toán tử so sánh

:::info

- Trong **MongoDB** , _toán tử so sánh_ (comparison operators) là các toán tử dùng trong truy vấn để so sánh giá trị của trường (field) với một giá trị khác — tương tự như trong SQL nhưng dưới dạng cú pháp BSON.

:::

## Các toán tử so sánh trong MongoDB

| Toán tử  | Ý nghĩa                     | Ví dụ                                        |
| -------- | --------------------------- | -------------------------------------------- |
| **$eq**  | Bằng                        | `{ age: { $eq: 20 } }` – tìm age = 20        |
| **$ne**  | Không bằng                  | `{ age: { $ne: 20 } }`                       |
| **$gt**  | Lớn hơn                     | `{ age: { $gt: 20 } }`                       |
| **$gte** | Lớn hơn hoặc bằng           | `{ age: { $gte: 20 } }`                      |
| **$lt**  | Nhỏ hơn                     | `{ age: { $lt: 20 } }`                       |
| **$lte** | Nhỏ hơn hoặc bằng           | `{ age: { $lte: 20 } }`                      |
| **$in**  | Nằm trong tập giá trị       | `{ status: { $in: ["active", "pending"] } }` |
| **$nin** | Không nằm trong tập giá trị | `{ age: { $nin: [18, 21, 25] } }`            |

## Một số ví dụ

```js
// Tìm users có status = "active"
db.users.find({ status: { $eq: "active" } });

// Tương đương với
db.users.find({ status: "active" });
```

```js
// Tìm users có status khác "inactive"
db.users.find({ status: { $ne: "inactive" } });

// Tìm products có giá không bằng 100
db.products.find({ price: { $ne: 100 } });
```

```js
// Tìm products có giá > 100
db.products.find({ price: { $gt: 100 } });

// Tìm users có tuổi >= 18
db.users.find({ age: { $gte: 18 } });

// Tìm orders có tổng tiền > 1000 và < 5000
db.orders.find({
  total: {
    $gt: 1000,
    $lt: 5000,
  },
});
```

```js
// Tìm products có giá < 50
db.products.find({ price: { $lt: 50 } });

// Tìm users có tuổi <= 65
db.users.find({ age: { $lte: 65 } });

// Tìm logs cũ hơn 30 ngày
db.logs.find({
  createdAt: {
    $lt: new Date(Date.now() - 30 * 24 * 60 * 60 * 1000),
  },
});
```

```js
// Tìm users có status là "active" hoặc "pending"
db.users.find({
  status: {
    $in: ["active", "pending"],
  },
});

// Tìm products trong các category cụ thể
db.products.find({
  category: {
    $in: ["electronics", "books", "clothing"],
  },
});
```

```js
// Tìm users có status không phải "inactive" hoặc "banned"
db.users.find({
  status: {
    $nin: ["inactive", "banned"],
  },
});
```
