---
sidebar_position: 6
---

# Tìm kiếm document

:::info

- **Find document** là thao tác cơ bản và quan trọng nhất để truy vấn dữ liệu trong MongoDB. MongoDB cung cấp phương thức `find()` linh hoạt với nhiều toán tử và tùy chọn để thực hiện các truy vấn phức tạp.

:::

## Cú pháp cơ bản

### `find()` - Truy vấn documents

- Cú pháp:

```js
db.collection.find(query, projection);
```

**Tham số:**

- `query` (optional): Điều kiện lọc documents
- `projection` (optional): Xác định các trường cần trả về

## Các phương thức FIND cơ bản

### `find()` - Lấy tất cả documents

```js
// Lấy tất cả documents trong collection
db.users.find();

// Tương đương với
db.users.find({});

// Kết quả trả về cursor, dùng .pretty() để format
db.users.find().pretty();
```

### `findOne()` - Lấy một document

```js
// Lấy một document đầu tiên phù hợp
db.users.findOne()

// Lấy document theo điều kiện
db.users.findOne({ name: "Nguyễn Văn A" })

// Kết quả trả về document trực tiếp (không phải cursor)
{
    "_id": ObjectId("507f191e810c19729de860ea"),
    "name": "Nguyễn Văn A",
    "email": "nguyenvana@email.com"
}
```

### Projection - Chọn trường trả về

**Bao gồm trường**:

```js
// Chỉ trả về name và email
db.users.find(
    { status: "active" },
    { name: 1, email: 1 }
)

// Kết quả:
{
    "_id": ObjectId("..."),
    "name": "Nguyễn Văn A",
    "email": "email@example.com"
}
```

**Loại trừ trường**:

```js
// Trả về tất cả trường TRỪ password và internalData
db.users.find({ status: "active" }, { password: 0, internalData: 0 });
```

**Projection với nested fields**:

```js
// Chỉ trả về name và address.city
db.users.find(
  {},
  {
    name: 1,
    "address.city": 1,
  }
);

// Projection với mảng - chỉ lấy một số fields của phần tử mảng
db.posts.find(
  {},
  {
    title: 1,
    "comments.author": 1,
    "comments.text": 1,
  }
);
```

### Truy vấn nested fields

```js
// Tìm users có address.city = "Hà Nội"
db.users.find({
  "address.city": "Hà Nội",
});

// Tìm products có specifications.ram = "16GB"
db.products.find({
  "specifications.ram": "16GB",
});

// Tìm orders có items với price > 100
db.orders.find({
  "items.price": { $gt: 100 },
});
```
