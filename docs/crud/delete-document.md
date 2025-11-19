---
sidebar_position: 5
---

# Xóa document

:::info

- **Delete document** là thao tác quan trọng để xóa dữ liệu khỏi các collection trong MongoDB. MongoDB cung cấp các phương thức linh hoạt để xóa một hoặc nhiều documents dựa trên các điều kiện cụ thể.

:::

## Các phương thức DELETE cơ bản

### `deleteOne()` - Xóa một document

- Cú pháp:

```js
db.collection.deleteOne(filter, options);
```

- Ví dụ:

```js
// Xóa một user theo ID
db.users.deleteOne({ _id: ObjectId("507f191e810c19729de860ea") })

// Kết quả trả về:
{
    "acknowledged": true,
    "deletedCount": 1
}
```

- Ví dụ với điều kiện phức tạp hơn:

```js
// Xóa user có email cụ thể và status inactive
db.users.deleteOne({
  email: "olduser@email.com",
  status: "inactive",
});

// Xóa product hết hàng và không có đơn đặt hàng nào
db.products.deleteOne({
  stock: 0,
  onOrder: false,
});
```

### `deleteMany()` - Xóa nhiều documents

- Cú pháp:

```js
db.collection.deleteMany(filter, options);
```

- Ví dụ:

```js
// Xóa tất cả users có status inactive
db.users.deleteMany({ status: "inactive" })

// Kết quả trả về:
{
    "acknowledged": true,
    "deletedCount": 15
}
```

- Ví dụ thực tế:

```js
// Xóa tất cả logs cũ hơn 30 ngày
db.logs.deleteMany({
  createdAt: { $lt: new Date(Date.now() - 30 * 24 * 60 * 60 * 1000) },
});

// Xóa tất cả sản phẩm trong category không còn tồn tại
db.products.deleteMany({
  category: "discontinued",
});

// Xóa các temporary sessions đã hết hạn
db.sessions.deleteMany({
  expiresAt: { $lt: new Date() },
});
```

## Các tùy chọn (Options) khi delete

### Collation (So sánh chuỗi)

```js
// Xóa với collation cho so sánh chuỗi
db.users.deleteOne(
  { name: "john" },
  {
    collation: {
      locale: "en",
      strength: 2, // Case insensitive
    },
  }
);

// Ví dụ: xóa không phân biệt hoa thường
db.categories.deleteMany(
  { name: "ELECTRONICS" },
  {
    collation: {
      locale: "en",
      strength: 2,
    },
  }
);
```

## Các kịch bản DELETE phổ biến

### Xóa theo thời gian

```js
// Xóa logs cũ hơn 90 ngày
const ninetyDaysAgo = new Date();
ninetyDaysAgo.setDate(ninetyDaysAgo.getDate() - 90);

db.systemLogs.deleteMany({
  timestamp: { $lt: ninetyDaysAgo },
});

// Xóa temporary data sau 24 giờ
db.tempData.deleteMany({
  created: { $lt: new Date(Date.now() - 24 * 60 * 60 * 1000) },
});
```

### Xóa theo điều kiện phức tạp

```js
// Xóa users chưa active trong 1 năm và không có đơn hàng
db.users.deleteMany({
  $and: [
    { lastLogin: { $lt: new Date(Date.now() - 365 * 24 * 60 * 60 * 1000) } },
    { totalOrders: 0 },
    { status: "inactive" },
  ],
});

// Xóa products không bán được trong 6 tháng
db.products.deleteMany({
  lastSold: { $lt: new Date(Date.now() - 180 * 24 * 60 * 60 * 1000) },
  stock: { $gt: 0 }, // Vẫn còn hàng nhưng không bán được
});
```

### Xóa với điều kiện mảng

```js
// Xóa posts không có comments và không có likes
db.posts.deleteMany({
  $and: [
    { comments: { $size: 0 } },
    { likes: { $size: 0 } },
    { createdAt: { $lt: new Date(Date.now() - 30 * 24 * 60 * 60 * 1000) } },
  ],
});

// Xóa users không có role nào
db.users.deleteMany({
  roles: { $exists: true, $size: 0 },
});
```
