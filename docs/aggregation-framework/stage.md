---
sidebar_position: 2
---

# Danh sách các Stage trong Aggregation

Dưới đây là toàn bộ các stage thường được dùng trong MongoDB:

| Stage                      | Chức năng                        |
| -------------------------- | -------------------------------- |
| `$match`                   | Lọc dữ liệu (giống WHERE)        |
| `$project`                 | Chọn và transform field          |
| `$group`                   | Nhóm dữ liệu (giống GROUP BY)    |
| `$sort`                    | Sắp xếp                          |
| `$limit`                   | Giới hạn số document             |
| `$skip`                    | Bỏ qua số document               |
| `$lookup`                  | JOIN 2 collection                |
| `$unwind`                  | Tách array thành nhiều document  |
| `$addFields`/`$set`        | Thêm hoặc ghi đè field           |
| `$unset`                   | Xóa field                        |
| `$count`                   | Đếm số document                  |
| `$facet`                   | Chạy nhiều pipeline song song    |
| `$bucket`                  | Phân nhóm theo giá trị           |
| `$bucketAuto`              | Tự chia bucket                   |
| `$replaceRoot`             | Gán document mới cho root        |
| `$sample`                  | Random documents                 |
| `$sortByCount`             | Nhóm theo giá trị và đếm         |
| `$merge`                   | Ghi kết quả vào collection khác  |
| `$out`                     | Xuất dữ liệu sang collection mới |
| `$map`,`$filter`,`$reduce` | Transform array                  |
| `$expr`                    | Dùng biểu thức trong match       |
| `$function`                | Chạy function JavaScript         |
