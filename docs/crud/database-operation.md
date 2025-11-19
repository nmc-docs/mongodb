---
sidebar_position: 1
---

# Các thao tác với Database

## Tạo database

:::info

- Ta sử dụng phương thức `use DATABASE_NAME`, lệnh này sẽ:
  - Lệnh này sẽ tạo và **chuyển sang database** `DATABASE_NAME` .
  - Database **chỉ thực sự được tạo khi ta tạo collection hoặc insert dữ liệu** .

:::

## Xem tất cả các database hiện có trong hệ thống:

```bash
show dbs
```

## Xóa database

:::caution

- Để xóa database, ta kết hợp 2 lệnh:

```js
use database_name
db.dropDatabase()
```

:::
