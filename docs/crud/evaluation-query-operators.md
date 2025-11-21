---
sidebar_position: 11
---

# Evaluation Query Operators

## 1. Giới thiệu

**Evaluation Query Operators** là các toán tử truy vấn cho phép ta thực hiện các **phép đánh giá nâng cao** trên dữ liệu, bao gồm: biểu thức chính quy, biểu thức JavaScript, tìm kiếm văn bản, và các phép so sánh phức tạp khác. Các toán tử này cung cấp khả năng linh hoạt cao cho các truy vấn phức tạp.

## 2. Các Evaluation Query Operators

### `$regex` - Biểu thức chính quy (Regular Expression)

- Toán tử `$regex` cung cấp khả năng so khớp mẫu (pattern matching) sử dụng biểu thức chính quy.
- Cú pháp:
  ```js
  { field: { $regex: /pattern/, $options: '<options>' } }
  // hoặc
  { field: { $regex: 'pattern', $options: '<options>' } }
  ```

**Các options phổ biến:**

- `i` - Không phân biệt hoa thường
- `m` - Multi-line matching
- `x` - Bỏ qua whitespace
- `s` - Cho phép dot (.) khớp với tất cả ký tự

* Ví dụ:

```js
// Tìm users có email kết thúc bằng @gmail.com
db.users.find({
  email: {
    $regex: /@gmail\.com$/,
  },
});

// Tìm products có name chứa "laptop" (không phân biệt hoa thường)
db.products.find({
  name: {
    $regex: /laptop/i,
  },
});

// Tìm documents có description bắt đầu bằng "Premium"
db.products.find({
  description: {
    $regex: /^Premium/,
  },
});

// Sử dụng string pattern thay vì regex literal
db.users.find({
  name: {
    $regex: "^Nguyễn",
    $options: "i",
  },
});
```

- Ví dụ nâng cao:

```js
// Tìm phone numbers theo định dạng Việt Nam
db.users.find({
  phone: {
    $regex: /^(\\+84|0)[1-9][0-9]{8}$/,
  },
});

// Tìm emails hợp lệ
db.users.find({
  email: {
    $regex: /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/,
  },
});

// Tìm usernames chỉ chứa chữ cái và số
db.users.find({
  username: {
    $regex: /^[a-zA-Z0-9]+$/,
  },
});
```

### `$text` - Tìm kiếm văn bản (Text Search)

- Toán tử `$text` thực hiện tìm kiếm văn bản trên các trường được index dạng text.
- Cú pháp:

```js
{
  $text: {
    $search: <string>,
    $language: <string>,
    $caseSensitive: <boolean>,
    $diacriticSensitive: <boolean>
  }
}
```

- Ví dụ:

```js
// Tạo text index trước
db.products.createIndex({
  name: "text",
  description: "text",
});

// Tìm kiếm products chứa từ "laptop" hoặc "computer"
db.products.find({
  $text: {
    $search: "laptop computer",
  },
});

// Tìm kiếm cụm từ "gaming laptop"
db.products.find({
  $text: {
    $search: '"gaming laptop"',
  },
});

// Tìm kiếm với loại trừ từ
db.products.find({
  $text: {
    $search: "laptop -used", // Tìm laptop nhưng loại trừ used
  },
});

// Tìm kiếm với ngôn ngữ cụ thể
db.articles.find({
  $text: {
    $search: "database",
    $language: "en",
  },
});
```

- **Text search với scoring:**

```js
// Tìm kiếm và sắp xếp theo relevance score
db.products
  .find(
    {
      $text: {
        $search: "wireless mouse",
      },
    },
    {
      score: {
        $meta: "textScore",
      },
    }
  )
  .sort({
    score: {
      $meta: "textScore",
    },
  });
```

### `$where` - Biểu thức JavaScript

- Toán tử `$where` cho phép sử dụng biểu thức JavaScript để truy vấn documents.
- Cú pháp:

```js
{ $where: <javascript expression> }
```

- Ví dụ:

```js
// Tìm users có name và username giống nhau
db.users.find({
  $where: "this.name === this.username",
});

// Tìm products có discount > 50%
db.products.find({
  $where:
    "this.originalPrice > 0 && (this.originalPrice - this.price) / this.originalPrice > 0.5",
});

// Tìm orders có tổng items khớp với total
db.orders.find({
  $where: function () {
    let calculatedTotal = 0;
    this.items.forEach((item) => {
      calculatedTotal += item.price * item.quantity;
    });
    return Math.abs(calculatedTotal - this.total) < 0.01;
  },
});
```

:::caution[Lưu ý quan trọng]

- `$where` chậm hơn các toán tử khác vì phải execute JavaScript
- Không sử dụng `$where` nếu có thể dùng các toán tử khác
- Luôn kết hợp với các điều kiện khác để giảm số lượng documents cần xử lý

:::

### `$expr` - Biểu thức Aggregation

- Toán tử `$expr` cho phép sử dụng các biểu thức aggregation trong truy vấn.
- Cú pháp:

```js
{ $expr: { <expression> } }
```

- Ví dụ:

```js
// Tìm orders có total > subtotal + tax
db.orders.find({
  $expr: {
    $gt: ["$total", { $add: ["$subtotal", "$tax"] }],
  },
});

// Tìm users có năm sinh = 1990
db.users.find({
  $expr: {
    $eq: [{ $year: "$birthDate" }, 1990],
  },
});

// Tìm products có giá khuyến mãi < 50% giá gốc
db.products.find({
  $expr: {
    $lt: ["$salePrice", { $multiply: ["$originalPrice", 0.5] }],
  },
});

// So sánh giữa hai trường trong cùng document
db.employees.find({
  $expr: {
    $gt: ["$salary", "$budget"],
  },
});
```

### `$mod` - Phép chia lấy dư (Modulo)

- Toán tử `$mod` thực hiện phép chia lấy dư trên các trường số.
- Cú pháp:

```js
{
  field: {
    $mod: [divisor, remainder];
  }
}
```

- Ví dụ:

```js
// Tìm products có price chia hết cho 10
db.products.find({
  price: {
    $mod: [10, 0],
  },
});

// Tìm users có age lẻ
db.users.find({
  age: {
    $mod: [2, 1],
  },
});

// Tìm orders có orderId chia 5 dư 2
db.orders.find({
  orderId: {
    $mod: [5, 2],
  },
});
```
