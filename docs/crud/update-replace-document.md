---
sidebar_position: 4
---

# Update và Replace document

## Giới thiệu

:::info

- **Update và Replace** là các thao tác quan trọng để sửa đổi dữ liệu hiện có trong MongoDB. Có hai cách tiếp cận chính:
  - **Replace** : Thay thế toàn bộ document
  - **Update** : Sửa đổi một phần document sử dụng các toán tử update

:::

## Replace document

### `replaceOne()` - Thay thế một document

- Cú pháp:

```js
db.collection.replaceOne(filter, replacement, options);
```

- Ví dụ cơ bản:

```js
// Document gốc
db.users.insertOne({
    _id: 1,
    name: "Nguyễn Văn A",
    email: "old@email.com",
    age: 25,
    status: "active"
})

// Thay thế toàn bộ document
db.users.replaceOne(
    { _id: 1 },
    {
        name: "Nguyễn Văn A Updated",
        email: "new@email.com",
        status: "inactive",
        updatedAt: new Date()
    }
)

// Kết quả:
{
    "acknowledged": true,
    "matchedCount": 1,
    "modifiedCount": 1
}
```

:::caution[Lưu ý quan trọng]

- Toàn bộ document được thay thế, chỉ giữ lại `_id`
- Các trường không có trong replacement document sẽ bị mất

:::

- Ví dụ:

  ```js
  // Document gốc trong products collection
  {
      _id: "P001",
      name: "Laptop Old Model",
      price: 1000,
      category: "electronics",
      inStock: true,
      createdAt: ISODate("2023-01-01")
  }

  // Thay thế với document mới
  db.products.replaceOne(
      { _id: "P001" },
      {
          name: "Laptop New Model 2024",
          price: 1200,
          specifications: {
              cpu: "Intel i7",
              ram: "16GB",
              storage: "1TB SSD"
          },
          updatedAt: new Date()
      }
  )

  // Kết quả: mất các trường category, inStock, createdAt
  ```

## Update document

### `updateOne()` - Cập nhật một document

- Cú pháp:

```js
db.collection.updateOne(filter, update, options);
```

### `updateMany()` - Cập nhật nhiều documents

- Cú pháp:

```js
db.collection.updateMany(filter, update, options);
```

## Các toán tử update quan trọng

### `$set` - Thiết lập giá trị trường

```js
// Cập nhật một trường
db.users.updateOne({ _id: 1 }, { $set: { status: "inactive" } });

// Cập nhật nhiều trường
db.users.updateOne(
  { _id: 1 },
  {
    $set: {
      status: "active",
      lastLogin: new Date(),
      loginCount: 0,
    },
  }
);

// Cập nhật nested fields
db.products.updateOne(
  { _id: "P001" },
  {
    $set: {
      "specifications.ram": "32GB",
      "specifications.display": "15.6 inch",
    },
  }
);
```

### `$unset` - Xóa trường

```js
// Xóa một trường
db.users.updateOne({ _id: 1 }, { $unset: { temporaryField: "" } });

// Xóa nhiều trường
db.users.updateOne(
  { _id: 1 },
  {
    $unset: {
      oldField1: "",
      oldField2: "",
      "nested.oldField": "",
    },
  }
);
```

### `$inc` - Tăng/giảm giá trị số

```js
// Tăng giá trị
db.products.updateOne(
  { _id: "P001" },
  { $inc: { stock: 10 } } // Tăng stock lên 10
);

// Giảm giá trị
db.products.updateOne(
  { _id: "P001" },
  { $inc: { stock: -5 } } // Giảm stock 5
);

// Cập nhật nhiều trường
db.users.updateOne(
  { _id: 1 },
  {
    $inc: {
      loginCount: 1,
      points: 50,
    },
  }
);
```

### `$mul` - Nhân giá trị

```js
// Nhân giá trị với hệ số
db.products.updateOne(
  { _id: "P001" },
  { $mul: { price: 1.1 } } // Tăng giá 10%
);

// Giảm giá 20%
db.products.updateOne({ _id: "P001" }, { $mul: { price: 0.8 } });
```

### `$rename` - Đổi tên trường

```js
// Đổi tên một trường
db.users.updateOne({ _id: 1 }, { $rename: { oldFieldName: "newFieldName" } });

// Đổi tên nested field
db.users.updateOne(
  { _id: 1 },
  { $rename: { "contact.phone": "contact.mobile" } }
);
```

### `$min` và `$max`

```js
// $min - Chỉ cập nhật nếu giá trị mới NHỎ HƠN
db.scores.updateOne(
  { _id: 1 },
  { $min: { highScore: 1500 } } // Chỉ cập nhật nếu 1500 < highScore hiện tại
);

// $max - Chỉ cập nhật nếu giá trị mới LỚN HƠN
db.scores.updateOne(
  { _id: 1 },
  { $max: { lowScore: 500 } } // Chỉ cập nhật nếu 500 > lowScore hiện tại
);
```

## Toán tử update với mảng

### `$push` - Thêm phần tử vào mảng

```js
// Thêm một phần tử
db.posts.updateOne({ _id: 1 }, { $push: { tags: "mongodb" } });

// Thêm nhiều phần tử với $each
db.posts.updateOne(
  { _id: 1 },
  {
    $push: {
      tags: {
        $each: ["database", "nosql", "tutorial"],
      },
    },
  }
);

// Kết hợp với $sort và $slice
db.posts.updateOne(
  { _id: 1 },
  {
    $push: {
      comments: {
        $each: [{ user: "newUser", text: "Great post!" }],
        $sort: { createdAt: -1 },
        $slice: 10, // Giữ chỉ 10 comments mới nhất
      },
    },
  }
);
```

### `$addToSet` - Thêm phần tử nếu chưa tồn tại

```js
// Thêm nếu chưa tồn tại
db.users.updateOne({ _id: 1 }, { $addToSet: { roles: "admin" } });

// Thêm nhiều phần tử
db.users.updateOne(
  { _id: 1 },
  {
    $addToSet: {
      roles: {
        $each: ["editor", "viewer", "admin"], // "admin" sẽ không được thêm lại
      },
    },
  }
);
```

### `$pull` - Xóa phần tử khỏi mảng

```js
// Xóa phần tử theo giá trị
db.users.updateOne({ _id: 1 }, { $pull: { roles: "viewer" } });

// Xóa với điều kiện phức tạp
db.posts.updateOne(
  { _id: 1 },
  {
    $pull: {
      comments: {
        user: "spamUser",
        likes: { $lt: 0 },
      },
    },
  }
);
```

### `$pop` - Xóa phần tử đầu/cuối mảng

```js
// Xóa phần tử cuối cùng
db.users.updateOne({ _id: 1 }, { $pop: { recentSearches: 1 } });

// Xóa phần tử đầu tiên
db.users.updateOne({ _id: 1 }, { $pop: { recentSearches: -1 } });
```

### `arrayFilters` - Lọc phần tử trong mảng

```js
// Document gốc
{
    _id: 1,
    items: [
        { id: 1, name: "item1", status: "active" },
        { id: 2, name: "item2", status: "inactive" },
        { id: 3, name: "item3", status: "active" }
    ]
}

// Cập nhật các phần tử cụ thể trong mảng
db.collection.updateOne(
    { _id: 1 },
    { $set: { "items.$[elem].status": "archived" } },
    {
        arrayFilters: [
            { "elem.status": "inactive" }
        ]
    }
)
// Chỉ cập nhật item có status = "inactive"
```
