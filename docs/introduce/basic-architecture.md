---
sidebar_position: 2
---

# Kiến trúc cơ bản

## Các thành phần chính: Document, Collection, Database

- MongoDB tổ chức dữ liệu theo mô hình hierarchical (phân cấp):
  - **Database**: Tương đương với database trong SQL
  - **Collection**: Tương đương với table trong SQL
  - **Document**: Tương đương với row trong SQL
  - **Field**: Tương đương với column trong SQL

```
Database
├── Collection 1
│   ├── Document 1
│   ├── Document 2
│   └── Document 3
├── Collection 2
│   ├── Document 1
│   └── Document 2
└── Collection 3
```

## Document

- Là đơn vị dữ liệu cơ bản, tương tự một record (row) trong SQL. Document là một object JSON-like, lưu trữ dưới dạng BSON (Binary JSON) để hỗ trợ binary data và hiệu suất cao. Ví dụ:

```json
{
  "_id": ObjectId("507f1f77bcf86cd799439011"),
  "name": "John Doe",
  "age": 30,
  "address": {
    "street": "123 Main St",
    "city": "Anytown"
  },
  "hobbies": ["reading", "traveling"]
}
```

- `_id`: Khóa chính tự động tạo (ObjectId).
- Có thể nested (address) hoặc arrays (hobbies).

## Collection

- Nhóm các documents tương tự, tương tự một table trong SQL. Collection không yêu cầu schema, nên các documents trong cùng collection có thể có cấu trúc khác nhau. Ví dụ: Collection "users" chứa các documents về người dùng.

## Database

- Nhóm các collections. Một MongoDB instance có thể có nhiều databases, mỗi database độc lập. Ví dụ: Database "myapp" chứa collections như "users", "products".

## BSON Format

BSON là định dạng binary của JSON, được MongoDB sử dụng để lưu trữ và truyền dữ liệu. Ưu điểm:

- **Hiệu suất** : Nhỏ gọn hơn JSON text, hỗ trợ types như Date, Binary, ObjectId.
- **Types hỗ trợ** : String, Number (int, double), Boolean, Array, Object, Null, Date, Regex, v.v.
- **So sánh với JSON** : BSON thêm metadata cho types, giúp query nhanh hơn.
