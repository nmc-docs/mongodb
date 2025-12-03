---
sidebar_position: 6
---

# $unwind

## Định nghĩa

:::info

- `$unwind` là một toán tử trong Aggregation Pipeline của MongoDB dùng để **phân tách (deconstruct)** một mảng trong tài liệu, tạo ra **một tài liệu mới cho mỗi phần tử** trong mảng.

:::

## Cú pháp

- Cú pháp cơ bản:

```js
{ $unwind: <field path> }
```

- Cú pháp đầy đủ:

```js
{
  $unwind: {
    path: <field path>,
    includeArrayIndex: <string>,        // Tùy chọn
    preserveNullAndEmptyArrays: <boolean> // Tùy chọn
  }
}
```

| Tham số                      | Kiểu    | Mô tả                                             | Mặc định     |
| ---------------------------- | ------- | ------------------------------------------------- | ------------ |
| `path`                       | string  | Đường dẫn đến trường mảng cần phân tách           | **Bắt buộc** |
| `includeArrayIndex`          | string  | Tên trường chứa chỉ số của phần tử trong mảng gốc | Không thêm   |
| `preserveNullAndEmptyArrays` | boolean | Giữ lại tài liệu nếu mảng null/trống/rỗng         | `false`      |

## Ví dụ

### Ví dụ cơ bản

- Giả sử ta có document sau:

```js
db.inventory.insertOne({ _id: 1, item: "ABC1", sizes: ["S", "M", "L"] });
```

- Và khi dùng `$unwind`:

```js
db.inventory.aggregate([{ $unwind: "$sizes" }]);
```

- Kết quả sẽ là:

```js
{ _id: 1, item: "ABC1", sizes: "S" }
{ _id: 1, item: "ABC1", sizes: "M" }
{ _id: 1, item: "ABC1", sizes: "L" }
```

### Ví dụ xử lý mảng null/trống với `preserveNullAndEmptyArrays`

- Giả sử ta có document:

```js
db.inventory.insertMany([
  { _id: 1, item: "ABC", price: Decimal128("80"), sizes: ["S", "M", "L"] },
  { _id: 2, item: "EFG", price: Decimal128("120"), sizes: [] },
  { _id: 3, item: "IJK", price: Decimal128("160"), sizes: "M" },
  { _id: 4, item: "LMN", price: Decimal128("10") },
  { _id: 5, item: "XYZ", price: Decimal128("5.75"), sizes: null },
]);
```

- Khi không chỉ định `preserveNullAndEmptyArrays` (tức mặc định là `false`), thì các documents có `path` là mảng rỗng hoặc có giá trị `null` sẽ bị bỏ qua:

```js
db.inventory.aggregate([{ $unwind: { path: "$sizes" } }]);
```

```js
{ _id: 1, item: "ABC", price: Decimal128("80"), sizes: "S" }
{ _id: 1, item: "ABC", price: Decimal128("80"), sizes: "M" }
{ _id: 1, item: "ABC", price: Decimal128("80"), sizes: "L" }
{ _id: 3, item: "IJK", price: Decimal128("160"), sizes: "M" }

```

- Còn khi ta chỉ định `preserveNullAndEmptyArrays` thì nó sẽ giữ lại:

```js
db.inventory.aggregate([
  { $unwind: { path: "$sizes", preserveNullAndEmptyArrays: true } },
]);
```

```js
{ _id: 1, item: "ABC", price: Decimal128("80"), sizes: "S" }
{ _id: 1, item: "ABC", price: Decimal128("80"), sizes: "M" }
{ _id: 1, item: "ABC", price: Decimal128("80"), sizes: "L" }
{ _id: 2, item: "EFG", price: Decimal128("120") }
{ _id: 3, item: "IJK", price: Decimal128("160"), sizes: "M" }
{ _id: 4, item: "LMN", price: Decimal128("10") }
{ _id: 5, item: "XYZ", price: Decimal128("5.75"), sizes: null }

```

### Ví dụ khi kết hợp với các stage khác

**Ví dụ 1**

- Vẫn là với collection inventory trên:

```js
db.inventory.aggregate([
  // First Stage
  {
    $unwind: { path: "$sizes", preserveNullAndEmptyArrays: true },
  },
  // Second Stage
  {
    $group: {
      _id: "$sizes",
      averagePrice: { $avg: "$price" },
    },
  },
  // Third Stage
  {
    $sort: { averagePrice: -1 },
  },
]);
```

- **Stage 1:**

  ```js
  { _id: 1, item: "ABC", price: Decimal128("80"), sizes: "S" }
  { _id: 1, item: "ABC", price: Decimal128("80"), sizes: "M" }
  { _id: 1, item: "ABC", price: Decimal128("80"), sizes: "L" }
  { _id: 2, item: "EFG", price: Decimal128("120") }
  { _id: 3, item: "IJK", price: Decimal128("160"), sizes: "M" }
  { _id: 4, item: "LMN", price: Decimal128("10") }
  { _id: 5, item: "XYZ", price: Decimal128("5.75"), sizes: null }
  ```

- **Stage 2:**

  ```js
  { _id: "S", averagePrice: Decimal128("80") }
  { _id: "L", averagePrice: Decimal128("80") }
  { _id: "M", averagePrice: Decimal128("120") }
  { _id: null, averagePrice: Decimal128("45.25") }
  ```

- **Stage 3:**

  ```js
  { _id : "M", averagePrice: Decimal128("120") }
  { _id : "L", averagePrice: Decimal128("80") }
  { _id : "S", averagePrice: Decimal128("80") }
  { _id : null, averagePrice: Decimal128("45.25") }
  ```

**Ví dụ 2**

- Ta có document sau:

```js
db.sales.insertMany([
  {
    _id: "1",
    items: [
      {
        name: "pens",
        tags: ["writing", "office", "school", "stationary"],
        price: Decimal128("12.00"),
        quantity: Int32("5"),
      },
      {
        name: "envelopes",
        tags: ["stationary", "office"],
        price: Decimal128("19.95"),
        quantity: Int32("8"),
      },
    ],
  },
  {
    _id: "2",
    items: [
      {
        name: "laptop",
        tags: ["office", "electronics"],
        price: Decimal128("800.00"),
        quantity: Int32("1"),
      },
      {
        name: "notepad",
        tags: ["stationary", "school"],
        price: Decimal128("14.95"),
        quantity: Int32("3"),
      },
    ],
  },
]);
```

- Với aggregation pipeline sau:

```js
db.sales.aggregate([
  // First Stage
  { $unwind: "$items" },

  // Second Stage
  { $unwind: "$items.tags" },

  // Third Stage
  {
    $group: {
      _id: "$items.tags",
      totalSalesAmount: {
        $sum: { $multiply: ["$items.price", "$items.quantity"] },
      },
    },
  },
]);
```

- **Stage 1:**

  ```js
  { _id: "1", items: { name: "pens", tags: [ "writing", "office", "school", "stationary" ], price: Decimal128("12.00"), quantity: 5 } }
  { _id: "1", items: { name: "envelopes", tags: [ "stationary", "office" ], price: Decimal128("19.95"), quantity: 8 } }
  { _id: "2", items: { name: "laptop", tags: [ "office", "electronics" ], price: Decimal128("800.00"), quantity": 1 } }
  { _id: "2", items: { name: "notepad", tags: [ "stationary", "school" ], price: Decimal128("14.95"), quantity: 3 } }
  ```

- **Stage 2:**

  ```js
  { _id: "1", items: { name: "pens", tags: "writing", price: Decimal128("12.00"), quantity: 5 } }
  { _id: "1", items: { name: "pens", tags: "office", price: Decimal128("12.00"), quantity: 5 } }
  { _id: "1", items: { name: "pens", tags: "school", price: Decimal128("12.00"), quantity: 5 } }
  { _id: "1", items: { name: "pens", tags: "stationary", price: Decimal128("12.00"), quantity: 5 } }
  { _id: "1", items: { name: "envelopes", tags: "stationary", price: Decimal128("19.95"), quantity: 8 } }
  { _id: "1", items: { name: "envelopes", tags: "office", "price" : Decimal128("19.95"), quantity: 8 } }
  { _id: "2", items: { name: "laptop", tags: "office", price: Decimal128("800.00"), quantity: 1 } }
  { _id: "2", items: { name: "laptop", tags: "electronics", price: Decimal128("800.00"), quantity: 1 } }
  { _id: "2", items: { name: "notepad", tags: "stationary", price: Decimal128("14.95"), quantity: 3 } }
  { _id: "2", items: { name: "notepad", "ags: "school", price: Decimal128("14.95"), quantity: 3 } }
  ```

- **Stage 3:**

  ```js
  { _id: "writing", totalSalesAmount: Decimal128("60.00") }
  { _id: "stationary", totalSalesAmount: Decimal128("264.45") }
  { _id: "electronics", totalSalesAmount: Decimal128("800.00") }
  { _id: "school", totalSalesAmount: Decimal128("104.85") }
  { _id: "office", totalSalesAmount: Decimal128("1019.60") }
  ```

## Các lưu ý quan trọng

- Một document có mảng **n phần tử** sẽ tạo ra **n documents** sau `$unwind`
- Có thể dẫn đến **số lượng documents tăng đột biến**

:::tip

1. **Luôn đặt `$match` trước `$unwind`** khi có thể
2. **Sử dụng `$limit`** để kiểm soát số lượng documents
3. **Xem xét `preserveNullAndEmptyArrays`** nếu cần giữ lại tất cả documents
4. **Kiểm tra kiểu dữ liệu** trước khi unwind
5. **Theo dõi memory usage** khi xử lý mảng lớn

:::
