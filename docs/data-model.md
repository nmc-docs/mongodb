---
sidebar_position: 3
---

# Mô hình dữ liệu

## Thiết kế schema

MongoDB là cơ sở dữ liệu NoSQL document-oriented, nghĩa là dữ liệu được lưu trữ dưới dạng documents (tài liệu) mà không yêu cầu schema cố định như trong RDBMS. Schema ở đây là "schema-on-read" hoặc "dynamic schema", cho phép:

- **Linh hoạt**: Mỗi document trong cùng một collection có thể có cấu trúc khác nhau. Ta có thể thêm trường mới mà không cần thay đổi toàn bộ database.
- **Ưu điểm**: Phù hợp cho ứng dụng phát triển nhanh (agile development), nơi yêu cầu dữ liệu thay đổi thường xuyên, như startup hoặc apps với user-generated content.
- **Nhược điểm**: Có thể dẫn đến dữ liệu không nhất quán nếu không quản lý tốt, nên cần best practices để tránh.

Ví dụ: Trong collection "users", một document có thể có trường "email", document khác có thêm "phone" mà không báo lỗi.

Để thiết kế schema:

- Xác định entities chính (e.g., users, products).
- Quyết định embedding hay referencing dựa trên quan hệ dữ liệu.
- Sử dụng validation rules (từ MongoDB 3.6) để enforce schema partial, ví dụ: Yêu cầu trường "name" phải là string.

Ví dụ tạo schema validation:

```js
db.createCollection("users", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["name", "email"],
      properties: {
        name: { bsonType: "string" },
        email: { bsonType: "string" },
      },
    },
  },
});
```

## Embedding Documents vs Referencing (DBRefs)

MongoDB hỗ trợ hai cách xử lý quan hệ dữ liệu:

### Embedding

- **Embedding (Nhúng)** : Lưu dữ liệu liên quan trực tiếp vào document cha. Lý tưởng cho quan hệ one-to-one hoặc one-to-few, nơi dữ liệu con không phát triển độc lập.
- Ví dụ:

  ```json
  {
    "_id": 1,
    "title": "Post A",
    "comments": [
      { "user": "alice", "text": "Nice post!" },
      { "user": "bob", "text": "Thanks for sharing!" }
    ]
  }
  ```

* Ưu điểm:

  - 1 query → lấy đủ dữ liệu.
  - Không cần JOIN.
  - Rất nhanh cho use case đọc nhiều.

* Nhược điểm:

  - Document quá lớn → vượt 16MB limit.
  - Update một phần trong array có thể nặng.
  - Không phù hợp dữ liệu có tốc độ tăng mạnh (unbounded arrays).

### Referencing (Tham chiếu)

- **Referencing**: Lưu ID của document khác, tương tự foreign key trong SQL. Sử dụng cho one-to-many hoặc many-to-many, nơi dữ liệu con lớn hoặc cần truy cập độc lập.
- Ví dụ:

`posts`

```json
{
  "_id": 1,
  "title": "Post A",
  "commentIds": [100, 101]
}
```

`comments`

```json
{
  "_id": 100,
  "postId": 1,
  "text": "Nice post!"
}
```

- Ưu điểm:

  - Document nhỏ, dễ quản lý.
  - Dễ scale.
  - Phù hợp dữ liệu lớn hoặc tăng theo thời gian.

- Nhược điểm:
  - Muốn lấy đủ thông tin → phải gọi nhiều query.
  - Không nhanh bằng embedding.
