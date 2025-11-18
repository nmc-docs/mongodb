---
sidebar_position: 2
---

# Kiểu dữ liệu

:::info

- MongoDB dùng **BSON** (Binary JSON), hỗ trợ nhiều kiểu dữ liệu không có trong JSON thông thường.

:::

## ObjectId

- Là ID mặc định cho mỗi document.
- Gồm 12 bytes, được tạo dựa trên:

  - timestamp
  - machine ID
  - process ID
  - counter

- Ví dụ:

```json
{
  "_id": ObjectId("64aeab59e5a9c431c0a7c123")
}
```

## Các kiểu dữ liệu chính trong BSON

| Kiểu dữ liệu       | Mô tả                                                                       |
| ------------------ | --------------------------------------------------------------------------- |
| String             | Chuỗi ký tự, thường dùng để lưu trữ văn bản (UTF-8 encoded).                |
| Integer (Int32)    | Số nguyên 32-bit, dùng cho các số nguyên nhỏ.                               |
| Long (Int64)       | Số nguyên 64-bit, dùng cho các số nguyên lớn.                               |
| Double             | Số thực dấu phẩy động 64-bit (floating-point).                              |
| Decimal128         | Số thập phân chính xác cao, phù hợp cho tài chính hoặc tính toán chính xác. |
| Boolean            | Giá trị đúng/sai (true/false).                                              |
| Date               | Ngày tháng, lưu trữ dưới dạng UTC datetime (không có múi giờ).              |
| Timestamp          | Dấu thời gian nội bộ của MongoDB, dùng cho replication và sharding.         |
| ObjectId           | ID duy nhất 12-byte, thường dùng làm khóa chính (\_id).                     |
| Null               | Giá trị null, đại diện cho giá trị không tồn tại.                           |
| Object             | Tài liệu nhúng (embedded document), là một đối tượng JSON-like.             |
| Array              | Mảng các giá trị, có thể chứa nhiều kiểu dữ liệu khác nhau.                 |
| Binary Data        | Dữ liệu nhị phân, dùng để lưu file hoặc dữ liệu không cấu trúc.             |
| Regular Expression | Biểu thức chính quy, dùng cho tìm kiếm chuỗi.                               |
| JavaScript         | Mã JavaScript (ít dùng, có thể deprecated).                                 |
| Min Key            | Giá trị nhỏ nhất trong so sánh BSON.                                        |
| Max Key            | Giá trị lớn nhất trong so sánh BSON.                                        |

- Ví dụ một document chứa đủ loại dữ liệu:

```json
{
  "name": "Laptop",
  "price": Decimal128("1299.99"),
  "tags": ["tech", "device"],
  "metadata": {
    "weight": 1.4,
    "color": "silver"
  },
  "createdAt": ISODate("2025-01-01T12:00:00Z")
}
```
