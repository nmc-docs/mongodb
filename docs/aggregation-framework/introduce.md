---
sidebar_position: 1
---

# Giới thiệu

## Aggregation Framework là gì?

**Aggregation Framework** là một framework mạnh mẽ trong MongoDB cho phép bạn xử lý và biến đổi dữ liệu thông qua một chuỗi các bước (pipeline). Nó tương tự như "SQL GROUP BY" nhưng linh hoạt và mạnh mẽ hơn rất nhiều.

## Tại sao cần Aggregation Framework?

- **Phân tích dữ liệu** : Thống kê, báo cáo, phân tích
- **Biến đổi dữ liệu** : Thay đổi cấu trúc documents
- **Tính toán phức tạp** : Các phép toán không thể thực hiện với query thông thường
- **Xử lý dữ liệu thời gian thực** : Analytics, dashboard

## Cấu trúc cơ bản

```js
db.collection.aggregate([
    { $stage1: { ... } },
    { $stage2: { ... } },
    { $stage3: { ... } },
    // ... more stages
])
```
