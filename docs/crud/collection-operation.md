---
sidebar_position: 2
---

# Các thao tác với collection

## Tạo collection

:::info

- Để tạo 1 collection, ta dùng:
  ```js
  db.createCollection(COLLECTION_NAME);
  ```

:::

### Schema validation

- **Schema Validation** (Xác thực Schema) là một tính năng của MongoDB cho phép ta **áp dụng các quy tắc cụ thể lên cấu trúc của các document trong một collection**. Nói cách khác, nó cho phép ta kiểm soát:

  - **Trường (Field) nào là bắt buộc** phải có.
  - **Kiểu dữ liệu (Data Type)** của mỗi trường (string, number, array, date...).
  - **Điều kiện (Rules)** cho giá trị của trường (ví dụ: tuổi phải lớn hơn 0, email phải đúng định dạng, giá trị phải nằm trong một danh sách cho trước).

- Mục đích chính là **đảm bảo tính toàn vẹn và nhất quán của dữ liệu** ngay tại cơ sở dữ liệu, ngăn chặn việc chèn các document "bẩn" hoặc không đúng chuẩn vào collection.
- Mặc dù MongoDB là một cơ sở dữ liệu NoSQL linh hoạt (schemaless) - nghĩa là các document trong cùng một collection không cần phải có cùng cấu trúc - nhưng trong thực tế, hầu hết các ứng dụng đều hoạt động tốt hơn khi dữ liệu tuân theo một cấu trúc nhất định.
- Schema Validation hoạt động dựa trên **JSON Schema** - một tiêu chuẩn mở để mô tả cấu trúc của dữ liệu JSON.
- Khi ta thiết lập các quy tắc validation cho một collection, MongoDB sẽ kiểm tra mọi thao tác **insert** (chèn mới) và **update** (cập nhật). Nếu một thao tác nào đó vi phạm các quy tắc, nó sẽ bị từ chối và trả về lỗi.
- Ta có thể cấu hình 2 chế độ cho validation:

  - **`validationLevel`:** Xác định xem validation được áp dụng cho document nào.
    - `strict` (Mặc định): Áp dụng quy tắc cho MỌI thao tác insert và update.
    - `moderate`: Chỉ áp dụng cho các document đã hợp lệ sẵn và các document mới được insert/update. Bỏ qua các document đã tồn tại mà không hợp lệ từ trước.
  - **`validationAction`:** Xác định MongoDB phản ứng thế nào khi có lỗi validation.
    - `error` (Mặc định): Từ chối thao tác và trả về lỗi.
    - `warn`: Ghi lại lỗi vào log, nhưng VẪN CHO PHÉP thao tác đó thực hiện. (Hữu ích cho giai đoạn chuyển đổi).

- Giả sử chúng ta có một collection `users` và muốn các document phải tuân theo các quy tắc sau:

  - `name`: Bắt buộc, kiểu string.
  - `email`: Bắt buộc, kiểu string và phải khớp với định dạng email.
  - `age`: Không bắt buộc, nhưng nếu có thì phải là số nguyên lớn hơn hoặc bằng 0.
  - `status`: Bắt buộc, giá trị phải nằm trong mảng `["active", "inactive"]`.

**1. Tạo Validation Schema khi tạo Collection mới**

```js
db.createCollection("users", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["name", "email", "status"], // Các trường bắt buộc
      properties: {
        // Định nghĩa các thuộc tính
        name: {
          bsonType: "string",
          description: "Tên phải là một chuỗi và là bắt buộc",
        },
        email: {
          bsonType: "string",
          pattern: "^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}$", // Regex cho email
          description: "Email phải là một chuỗi và theo định dạng email hợp lệ",
        },
        age: {
          bsonType: "int",
          minimum: 0,
          description: "Tuổi phải là số nguyên không âm",
        },
        status: {
          enum: ["active", "inactive"], // Giá trị phải nằm trong danh sách
          description: "Trạng thái phải là 'active' hoặc 'inactive'",
        },
      },
    },
  },
});
```

**2. Thêm Validation Schema vào một Collection đã tồn tại**

- Sử dụng lệnh `collMod` (collection modification):

```js
db.runCommand({
  collMod: "users",
  validator: {
    $jsonSchema: {
      // ... (Schema giống như ở trên)
    },
  },
});
```

## Xem tất cả các collections hiện có trong database

```bash
use database_name
show collections
```

## Đổi tên collection

```js
db.old_name.renameCollection("New name");
```

## Xóa collection

```js
db.collection_name.drop();
```
