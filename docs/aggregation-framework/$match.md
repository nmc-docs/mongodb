---
sidebar_position: 3
---

# $match()

## `$match()` là gì?

:::info

- `$match()` là stage quan trọng nhất, dùng để lọc dữ liệu (hoạt động giống WHERE trong SQL)

:::

- Ví dụ:

```js
// Lọc users có status active
db.users.aggregate([
  {
    $match: {
      status: "active",
      age: { $gte: 18 },
    },
  },
]);

// Tương đương SQL: SELECT * FROM users WHERE status = 'active' AND age >= 18
```

```js
// Tìm users có age > 20 và age < 40
db.users.aggregate([
  {
    $match: {
      age: {
        $gt: 20,
        $lt: 40,
      },
    },
  },
]);

// Tìm users có age > 20 và city là "Hà Nội" hoặc "Hồ Chí Minh"
db.users.aggregate([
  {
    $match: {
      age: { $gt: 20 },
      city: { $in: ["Hà Nội", "Hồ Chí Minh"] },
    },
  },
]);
```

```js
// Tìm users có (age > 20 VÀ status = "active") HOẶC (age > 25)
db.users.aggregate([
  {
    $match: {
      $or: [
        {
          $and: [{ age: { $gt: 20 } }, { status: "active" }],
        },
        {
          age: { $gt: 25 },
        },
      ],
    },
  },
]);
```

## Khi nào dùng `$expr` trong Aggregation Pipeline

:::tip

- ✅ Ta NÊN sử dụng `$expr` trong aggregation khi:
  - **So sánh giữa các trường** trong cùng document
  - **Tính toán phức tạp** trong điều kiện tìm kiếm
  - **Xử lý ngày tháng/chuỗi** trong điều kiện
  - **Validation logic** phức tạp
  - **Điều kiện không thể** thực hiện với query operators thông thường
- ❌ Tránh dùng khi `$expr`:
  - So sánh với **giá trị cố định đơn giản**
  - Điều kiện có thể thực hiện với **query operators thông thường**
  - Performance là ưu tiên hàng đầu và có cách thay thế đơn giản hơn

:::

### ✅ Ví dụ NÊN sử dụng `$expr`

```js
// ✅ DÙNG $expr: So sánh giữa hai trường trong cùng document
db.orders.aggregate([
  {
    $match: {
      $expr: {
        $gt: ["$totalAmount", "$budget"],
      },
    },
  },
]);

// Tìm users có điểm số > điểm tối thiểu yêu cầu
db.users.aggregate([
  {
    $match: {
      $expr: {
        $gte: ["$score", "$minRequiredScore"],
      },
    },
  },
]);
```

```js
// ✅ DÙNG $expr: Điều kiện với tính toán phức tạp
db.products.aggregate([
  {
    $match: {
      $expr: {
        $gt: [{ $multiply: ["$price", "$quantity"] }, 1000],
      },
    },
  },
]);

// Tìm orders có discount > 30%
db.orders.aggregate([
  {
    $match: {
      $expr: {
        $gt: [
          {
            $divide: [
              { $subtract: ["$originalPrice", "$salePrice"] },
              "$originalPrice",
            ],
          },
          0.3,
        ],
      },
    },
  },
]);
```

```js
// Tìm orders có vấn đề về tính toán giá
db.orders.aggregate([
  {
    $match: {
      $expr: {
        $or: [
          // Total không khớp với sum của items
          {
            $ne: [
              "$total",
              {
                $reduce: {
                  input: "$items",
                  initialValue: 0,
                  in: {
                    $add: [
                      "$$value",
                      { $multiply: ["$$this.price", "$$this.quantity"] },
                    ],
                  },
                },
              },
            ],
          },
          // Discount vượt quá cho phép
          {
            $gt: [
              {
                $divide: [{ $subtract: ["$subtotal", "$total"] }, "$subtotal"],
              },
              0.5, // Discount > 50%
            ],
          },
        ],
      },
    },
  },
]);
```

```js
// Tìm employees đủ điều kiện thăng chức
db.employees.aggregate([
  {
    $match: {
      $expr: {
        $and: [
          // Kinh nghiệm >= yêu cầu
          { $gte: ["$experienceYears", "$position.minExperience"] },
          // Điểm đánh giá >= ngưỡng
          { $gte: ["$performanceScore", "$position.minScore"] },
          // Lương hiện tại < lương tối đa của vị trí mới
          { $lt: ["$currentSalary", "$position.maxSalary"] },
        ],
      },
    },
  },
]);
```

```js
// Tìm students đạt điều kiện tốt nghiệp
db.students.aggregate([
  {
    $match: {
      $expr: {
        $and: [
          // Điểm trung bình >= 5.0
          { $gte: ["$gpa", 5.0] },
          // Số tín chỉ đạt >= yêu cầu
          { $gte: ["$completedCredits", "$program.requiredCredits"] },
          // Không môn nào dưới 3.0
          {
            $not: {
              $anyElementTrue: {
                $map: {
                  input: "$grades",
                  as: "grade",
                  in: { $lt: ["$$grade.score", 3.0] },
                },
              },
            },
          },
        ],
      },
    },
  },
]);
```

```js
// ✅ DÙNG $expr: So sánh ngày tháng với tính toán
db.users.aggregate([
  {
    $match: {
      $expr: {
        $gt: [
          "$lastLogin",
          { $subtract: [new Date(), 30 * 24 * 60 * 60 * 1000] }, // 30 ngày trước
        ],
      },
    },
  },
]);

// Tìm users có ngày sinh trong tháng hiện tại
db.users.aggregate([
  {
    $match: {
      $expr: {
        $eq: [{ $month: "$birthday" }, { $month: new Date() }],
      },
    },
  },
]);
```

```js
// ✅ DÙNG $expr: Điều kiện với xử lý chuỗi
db.users.aggregate([
  {
    $match: {
      $expr: {
        $eq: [{ $toLower: "$category" }, "premium"],
      },
    },
  },
]);

// Tìm email có domain cụ thể
db.users.aggregate([
  {
    $match: {
      $expr: {
        $eq: [
          { $arrayElemAt: [{ $split: ["$email", "@"] }, 1] },
          "company.com",
        ],
      },
    },
  },
]);
```

### ❌ Ví dụ KHÔNG CẦN sử dụng `$expr`

```js
// ❌ KHÔNG CẦN $expr: So sánh với giá trị cố định
db.users.aggregate([
  {
    $match: {
      age: { $gt: 20 }, // Đơn giản, không cần $expr
    },
  },
]);

// ❌ KHÔNG CẦN $expr: Điều kiện đơn giản
db.products.aggregate([
  {
    $match: {
      category: "electronics",
      price: { $lt: 1000 },
    },
  },
]);
```

```js
// ❌ KHÔNG CẦN $expr: Có thể dùng query operators thông thường
db.users.aggregate([
  {
    $match: {
      age: { $gte: 18, $lte: 65 },
      status: { $in: ["active", "pending"] },
    },
  },
]);
```

:::caution[Lưu ý]

- `$expr` có thể ảnh hưởng performance

```js
// ❌ CẨN THẬN: $expr phức tạp có thể chậm
db.orders.aggregate([
  {
    $match: {
      $expr: {
        $and: [
          { $gt: ["$total", 1000] },
          { $eq: [{ $year: "$createdAt" }, 2024] },
          {
            $gt: [
              {
                $divide: [{ $subtract: ["$subtotal", "$total"] }, "$subtotal"],
              },
              0.1,
            ],
          },
        ],
      },
    },
  },
]);

// ✅ TỐT HƠN: Tách thành nhiều $match đơn giản
db.orders.aggregate([
  {
    $match: {
      total: { $gt: 1000 },
      createdAt: {
        $gte: ISODate("2024-01-01"),
        $lt: ISODate("2025-01-01"),
      },
    },
  },
  {
    $addFields: {
      discountRate: {
        $divide: [{ $subtract: ["$subtotal", "$total"] }, "$subtotal"],
      },
    },
  },
  {
    $match: {
      discountRate: { $gt: 0.1 },
    },
  },
]);
```

:::
