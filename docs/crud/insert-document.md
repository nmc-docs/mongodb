---
sidebar_position: 3
---

# Chèn document

## `insertOne()` - Chèn một document

- Cú pháp:

```js
db.collection.insertOne(document, options);
```

- Ví dụ:

```js
// Chèn một document đơn giản
db.users.insertOne({
    name: "Nguyễn Văn A",
    email: "nguyenvana@email.com",
    age: 25,
    status: "active"
})

// Kết quả trả về:
{
    "acknowledged": true,
    "insertedId": ObjectId("507f191e810c19729de860ea")
}
```

- Ví dụ với các kiểu dữ liệu khác nhau:

```js
db.products.insertOne({
  name: "Laptop Dell XPS",
  price: 1599.99,
  inStock: true,
  tags: ["laptop", "dell", "premium"],
  specifications: {
    cpu: "Intel i7",
    ram: "16GB",
    storage: "512GB SSD",
  },
  createdAt: new Date(),
  features: null,
});
```

## `insertMany()` - Chèn nhiều documents

- Cú pháp:

```js
db.collection.insertMany([document1, document2, ...], options)
```

- Ví dụ:

```js
// Chèn nhiều documents cùng lúc
db.users.insertMany([
    {
        name: "Trần Thị B",
        email: "tranthib@email.com",
        age: 30,
        status: "active"
    },
    {
        name: "Lê Văn C",
        email: "levanc@email.com",
        age: 28,
        status: "inactive"
    },
    {
        name: "Phạm Thị D",
        email: "phamthid@email.com",
        age: 35,
        status: "active"
    }
])

// Kết quả trả về:
{
    "acknowledged": true,
    "insertedIds": [
        ObjectId("507f191e810c19729de860eb"),
        ObjectId("507f191e810c19729de860ec"),
        ObjectId("507f191e810c19729de860ed")
    ]
}
```
