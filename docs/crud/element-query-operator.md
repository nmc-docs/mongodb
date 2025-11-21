---
sidebar_position: 10
---

# Element Query Operators

## 1. Giới thiệu

**Element Query Operators** là các toán tử truy vấn cho phép ta thao tác với các documents dựa trên **sự tồn tại, kiểu dữ liệu và cấu trúc của các trường (fields)** . Các toán tử này giúp xác định xem một trường có tồn tại không, có phải là kiểu dữ liệu cụ thể nào không, và có phù hợp với các điều kiện về cấu trúc không.

## 2. Các Element Query Operators

### `$exists` - Kiểm tra sự tồn tại của trường

- Toán tử `$exists` kiểm tra xem một trường có tồn tại trong document hay không.
- Cú pháp:

  ```js
  { field: { $exists: <boolean> } }
  ```

- Ví dụ:

  ```js
  // Tìm các documents có trường "email"
  db.users.find({ email: { $exists: true } });

  // Tìm các documents KHÔNG có trường "phone"
  db.users.find({ phone: { $exists: false } });

  // Tìm products có trường "discountPrice"
  db.products.find({ discountPrice: { $exists: true } });

  // Tìm users không có trường "middleName"
  db.users.find({ middleName: { $exists: false } });
  ```

- **Ví dụ thực tế:**

  ```js
  // Tìm users đã cung cấp số điện thoại
  db.users.find({
    "contact.phone": { $exists: true },
  });

  // Tìm orders chưa có ngày hoàn thành
  db.orders.find({
    completedAt: { $exists: false },
  });

  // Kết hợp với các điều kiện khác
  db.users.find({
    email: { $exists: true },
    age: { $gte: 18 },
    status: "active",
  });
  ```

### `$type` - Kiểm tra kiểu dữ liệu của trường

- Toán tử `$type` kiểm tra kiểu dữ liệu BSON của một trường.
- Cú pháp:

  ```js
  { field: { $type: <BSONType> } }
  ```

- **Các kiểu BSON phổ biến:**

  - `"double"` - Số thực
  - `"string"` - Chuỗi
  - `"object"` - Object
  - `"array"` - Mảng
  - `"binData"` - Binary data
  - `"objectId"` - ObjectId
  - `"bool"` - Boolean
  - `"date"` - Date
  - `"null"` - Null
  - `"int"` - Số nguyên 32-bit
  - `"long"` - Số nguyên 64-bit
  - `"decimal"` - Decimal128

- Ví dụ:

  ```js
  // Tìm documents có trường "age" là kiểu số
  db.users.find({ age: { $type: "number" } });

  // Tìm documents có trường "price" là kiểu số nguyên
  db.products.find({ price: { $type: ["int", "long"] } });

  // Tìm documents có trường "tags" là mảng
  db.posts.find({ tags: { $type: "array" } });

  // Tìm documents có trường "createdAt" là kiểu Date
  db.logs.find({ createdAt: { $type: "date" } });
  ```

- Ví dụ chi tiết với nhiều kiểu:

  ```js
  // Tìm users có phone là chuỗi (thay vì số)
  db.users.find({
    phone: { $type: "string" },
  });

  // Tìm products có price là số thực
  db.products.find({
    price: { $type: "double" },
  });

  // Tìm documents có trường null
  db.collection.find({
    optionalField: { $type: "null" },
  });

  // Tìm với nhiều kiểu dữ liệu
  db.collection.find({
    field: { $type: ["string", "object", "array"] },
  });
  ```

## Kết hợp các Element Operators

### Kết hợp `$exists` và `$type`

```js
// Tìm documents có trường "age" tồn tại VÀ là kiểu số
db.users.find({
  age: {
    $exists: true,
    $type: "number",
  },
});

// Tìm documents có trường "email" tồn tại nhưng KHÔNG phải kiểu string
db.users.find({
  email: {
    $exists: true,
    $type: { $ne: "string" },
  },
});
```

### Kết hợp với các toán tử khác

```js
// Tìm users có email tồn tại, là string và không rỗng
db.users.find({
  email: {
    $exists: true,
    $type: "string",
    $ne: "",
    $regex: /.+@.+\..+/, // regex kiểm tra định dạng email
  },
});

// Tìm products có price tồn tại, là number và > 0
db.products.find({
  price: {
    $exists: true,
    $type: "number",
    $gt: 0,
  },
});
```

## Ví dụ thực tế chi tiết

### Data validation và cleanup

```js
// Tìm documents có vấn đề về kiểu dữ liệu
const problematicDocuments = db.users.find({
  $or: [
    {
      // age tồn tại nhưng không phải số
      age: {
        $exists: true,
        $type: { $ne: "number" },
      },
    },
    {
      // email tồn tại nhưng không phải string
      email: {
        $exists: true,
        $type: { $ne: "string" },
      },
    },
    {
      // createdDate tồn tại nhưng không phải Date
      createdDate: {
        $exists: true,
        $type: { $ne: "date" },
      },
    },
  ],
});

// Fix dữ liệu
problematicDocuments.forEach(function (doc) {
  if (doc.age && typeof doc.age !== "number") {
    db.users.updateOne(
      { _id: doc._id },
      { $set: { age: parseInt(doc.age) || 0 } }
    );
  }
});
```

### Data quality reports

```js
// Báo cáo chất lượng dữ liệu
function generateDataQualityReport() {
  const totalUsers = db.users.countDocuments();

  const usersWithEmail = db.users.countDocuments({
    email: { $exists: true },
  });

  const usersWithValidEmail = db.users.countDocuments({
    email: {
      $exists: true,
      $type: "string",
      $regex: /.+@.+\..+/,
    },
  });

  const usersWithPhone = db.users.countDocuments({
    "contact.phone": { $exists: true },
  });

  const usersWithValidAge = db.users.countDocuments({
    age: {
      $exists: true,
      $type: "number",
      $gte: 0,
      $lte: 120,
    },
  });

  return {
    totalUsers,
    emailCoverage: `${((usersWithEmail / totalUsers) * 100).toFixed(2)}%`,
    validEmailRate: `${((usersWithValidEmail / usersWithEmail) * 100).toFixed(
      2
    )}%`,
    phoneCoverage: `${((usersWithPhone / totalUsers) * 100).toFixed(2)}%`,
    validAgeRate: `${((usersWithValidAge / totalUsers) * 100).toFixed(2)}%`,
  };
}

const report = generateDataQualityReport();
console.log("Data Quality Report:", report);
```
