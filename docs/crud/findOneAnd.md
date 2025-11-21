---
sidebar_position: 12
---

# findOneAnd...

## Nhóm _findOneAnd..._ là gì?

- Các hàm:
  - **findOneAndUpdate()**
  - **findOneAndReplace()**
  - **findOneAndDelete()**

👉 Đều có cơ chế **"find + modify + return document"** .

- Nói cách khác:

  - Chúng **tìm một document**,
  - **thực hiện hành động**,
  - và **trả về document trước hoặc sau khi sửa** (tùy option).

- Đây là **atomic operation** (theo cách của MongoDB — dùng `findAndModify` phía server).
- Nó luôn **chỉ tác động đúng 1 document** , document đầu tiên phù hợp.
- Ví dụ `findOneAndUpdate`

```js
const doc = await db
  .collection("users")
  .findOneAndUpdate(
    { email: "test@gmail.com" },
    { $set: { age: 22 } },
    { returnDocument: "after" }
  );
```

➡️ Trả về document sau khi update.

## So sánh với `updateOne`, `replaceOne`, `deleteOne`

### `findOneAndUpdate` vs `updateOne`

| Tiêu chí               | findOneAndUpdate                       | updateOne                         |
| ---------------------- | -------------------------------------- | --------------------------------- |
| **Tác động**           | 1 document                             | 1 document                        |
| **Trả về document?**   | ✔️ Có (before/after)                   | ❌ Không                          |
| **Mục đích chính**     | Lấy bản ghi vừa update                 | Chỉ update, không cần dữ liệu mới |
| **Underlying command** | `findAndModify`                        | `update`                          |
| **Tốc độ**             | Chậm hơn chút (vì cần return document) | Nhanh hơn                         |

### `findOneAndReplace` vs `replaceOne`

| Tiêu chí                      | findOneAndReplace          | replaceOne       |
| ----------------------------- | -------------------------- | ---------------- |
| **Trả về document?**          | ✔️ Có (before/after)       | ❌ Không         |
| **Replace toàn bộ document?** | ✔️                         | ✔️               |
| **Khi dùng**                  | Cần trả về document cũ/mới | Chỉ muốn replace |

### `findOneAndDelete` vs `deleteOne`

| Tiêu chí                    | findOneAndDelete                         | deleteOne   |
| --------------------------- | ---------------------------------------- | ----------- |
| **Trả về document bị xóa?** | ✔️ Có                                    | ❌ Không    |
| **Tác động**                | 1 document                               | 1 document  |
| **Khi dùng**                | Cần biết nội dung document trước khi xóa | Chỉ cần xóa |

## Khi nào dùng cái nào?

**Dùng `findOneAndUpdate` , `findOneAndDelete` , `findOneAndReplace` khi**:

- Cần lấy **document trước** khi thay đổi → logging, backup
- Cần lấy **document sau** khi update → UI trả về data mới
- Bạn muốn thực hiện **atomic "find + change + return"** trong 1 round-trip

**Dùng `updateOne` , `deleteOne` , `replaceOne` khi**:

- Không cần nội dung document
- Chỉ cần biết thành công hay thất bại
- Muốn hiệu năng tốt hơn
- Chạy tác vụ nền, cronjob, migration…
