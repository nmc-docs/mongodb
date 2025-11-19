---
sidebar_position: 8
---

# Toán tử logic

:::info

- Trong **MongoDB** , _toán tử logic_ (logical operators) dùng để kết hợp nhiều điều kiện trong truy vấn — tương tự như `AND`, `OR`, `NOT` trong SQL nhưng dùng cú pháp BSON.

:::

## Các toán tử logic trong MongoDB

| Toán tử   | Ý nghĩa                                                   | Ví dụ                                                      |
| --------- | --------------------------------------------------------- | ---------------------------------------------------------- |
| **$and**  | Tất cả điều kiện đều đúng                                 | `{ $and: [ { age: { $gt: 18 } }, { status: "active" } ] }` |
| **$or**   | Một trong các điều kiện đúng                              | `{ $or: [ { age: { $lt: 18 } }, { age: { $gt: 60 } } ] }`  |
| **$not**  | Phủ định một điều kiện                                    | `{ age: { $not: { $gt: 18 } } }`                           |
| **$nor**  | Không điều kiện nào đúng                                  | `{ $nor: [ { status: "active" }, { age: { $lt: 18 } } ] }` |
| **$expr** | Sử dụng biểu thức trong truy vấn (so sánh giữa các field) | `{ $expr: { $gt: ["$price", "$discountPrice"] } }`         |

## Một số ví dụ

```js
// Tìm users có status "active" VÀ age >= 18
db.users.find({
  $and: [{ status: "active" }, { age: { $gte: 18 } }],
});

// Tương đương với
db.users.find({
  status: "active",
  age: { $gte: 18 },
});
```

```js
// Tìm users có status "active" HOẶC "pending"
db.users.find({
  $or: [{ status: "active" }, { status: "pending" }],
});

// Tìm products có giá < 50 HOẶC category = "sale"
db.products.find({
  $or: [{ price: { $lt: 50 } }, { category: "sale" }],
});
```

```js
// Tìm users có age KHÔNG >= 18
db.users.find({
  age: { $not: { $gte: 18 } },
});

// Tìm products có giá không phải số chẵn
db.products.find({
  price: { $not: { $mod: [2, 0] } },
});
```

```js
// Tìm users KHÔNG có status "active" VÀ KHÔNG có age < 18
db.users.find({
  $nor: [{ status: "active" }, { age: { $lt: 18 } }],
});
```
