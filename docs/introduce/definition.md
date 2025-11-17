---
sidebar_position: 1
slug: /
---
# MongoDB là gì?

## Định nghĩa và lịch sử

- MongoDB là một hệ quản trị cơ sở dữ liệu (Database Management System - DBMS) mã nguồn mở, thuộc loại NoSQL (Not Only SQL), tập trung vào việc lưu trữ dữ liệu dưới dạng tài liệu (documents) linh hoạt. Không giống như các cơ sở dữ liệu quan hệ truyền thống (như MySQL hoặc PostgreSQL), MongoDB sử dụng mô hình dữ liệu dựa trên JSON-like, gọi là BSON (Binary JSON), cho phép lưu trữ dữ liệu không cấu trúc hoặc bán cấu trúc mà không cần schema cố định.
- MongoDB được phát triển bởi công ty 10gen (nay là MongoDB Inc.) vào năm 2007, và phiên bản đầu tiên được ra mắt vào năm 2009. Ban đầu, nó được thiết kế để giải quyết vấn đề scalability trong các ứng dụng web lớn, nơi dữ liệu thay đổi nhanh chóng và cần mở rộng ngang (horizontal scaling). Đến năm 2025, MongoDB đã phát triển lên phiên bản 7.x (với phiên bản mới nhất là 7.0 vào năm 2023, và các cập nhật liên tục), hỗ trợ nhiều tính năng nâng cao như time-series data, vector search, và tích hợp AI.
- Tên "MongoDB" xuất phát từ "humongous" (khổng lồ), nhấn mạnh khả năng xử lý lượng dữ liệu lớn.

### Đặc điểm chính của MongoDB:

- **Linh hoạt**: Schema không cố định, cho phép thay đổi cấu trúc dữ liệu dễ dàng
- **Hiệu suất cao**: Tối ưu hóa cho việc đọc và ghi dữ liệu nhanh chóng
- **Khả năng mở rộng**: Hỗ trợ horizontal scaling thông qua sharding
- **Truy vấn phong phú**: Hỗ trợ các truy vấn phức tạp, indexing, và aggregation

### Lợi ích của MongoDB

MongoDB nổi bật với các ưu điểm sau:

- **Linh hoạt (Flexible Schema)**: Không yêu cầu schema cố định, cho phép thêm trường dữ liệu mới mà không cần thay đổi cấu trúc toàn bộ. Điều này lý tưởng cho các ứng dụng phát triển nhanh, nơi yêu cầu dữ liệu thay đổi thường xuyên.
- **Scalable (Khả năng mở rộng)**: Hỗ trợ sharding (phân mảnh dữ liệu) và replica sets (bản sao) để phân phối tải trên nhiều server. MongoDB có thể xử lý hàng triệu query/giây và petabytes dữ liệu.
- **Hiệu suất cao**: Với indexing mạnh mẽ và aggregation framework, MongoDB nhanh chóng trong việc đọc/ghi dữ liệu lớn. Nó hỗ trợ in-memory storage cho các trường hợp cần tốc độ cao.
- **NoSQL vs SQL**:

  - **NoSQL (MongoDB)**: Dữ liệu dạng document, không cần join phức tạp, dễ scale horizontally, phù hợp với big data và real-time apps.
  - **SQL (RDBMS)**: Dữ liệu dạng bảng với schema cố định, hỗ trợ ACID transactions mạnh mẽ, phù hợp với dữ liệu có quan hệ phức tạp như tài chính.

  MongoDB kết hợp lợi ích của cả hai thế giới: Từ phiên bản 4.0, nó hỗ trợ multi-document transactions với tính chất ACID.
- **Mã nguồn mở và Community**: Phiên bản Community Edition miễn phí, với cộng đồng lớn và tài liệu phong phú. MongoDB Atlas (dịch vụ cloud) cung cấp hosting managed, tích hợp với AWS, Azure, Google Cloud.

### Các Use Case Phổ Biến

MongoDB được sử dụng rộng rãi trong nhiều lĩnh vực nhờ tính linh hoạt:

- **Ứng dụng Web và Mobile**: Lưu trữ dữ liệu người dùng, sessions, hoặc nội dung động (e.g., Netflix sử dụng MongoDB cho recommendation system).
- **IoT (Internet of Things)**: Xử lý dữ liệu thời gian thực từ sensor, với lượng dữ liệu lớn và schema thay đổi (e.g., Bosch sử dụng cho dữ liệu thiết bị).
- **Big Data và Analytics**: Kết hợp với Hadoop hoặc Spark để phân tích dữ liệu lớn (e.g., eBay cho search và product catalog).
- **Content Management Systems (CMS)**: Lưu trữ bài viết, media với cấu trúc linh hoạt (e.g., Forbes sử dụng cho website).
- **Real-time Applications**: Như chat apps (e.g., WhatsApp-like) hoặc gaming, nhờ change streams cho cập nhật thời gian thực.
- **E-commerce**: Quản lý sản phẩm, orders với embedding documents để tránh join chậm.

## NoSQL là gì?

NoSQL (Not Only SQL) là một thuật ngữ chỉ các hệ quản trị cơ sở dữ liệu không sử dụng mô hình quan hệ truyền thống. NoSQL được thiết kế để xử lý:

- **Dữ liệu lớn (Big Data)**: Khối lượng dữ liệu khổng lồ
- **Tốc độ cao**: Xử lý hàng triệu giao dịch mỗi giây
- **Đa dạng**: Các loại dữ liệu khác nhau (văn bản, hình ảnh, video, etc.)

### Các loại cơ sở dữ liệu NoSQL:

1. **Document Database** (MongoDB, CouchDB)
2. **Key-Value Store** (Redis, Amazon DynamoDB)
3. **Column-Family** (Cassandra, HBase)
4. **Graph Database** (Neo4j, Amazon Neptune)

## Sự khác biệt giữa SQL & NoSQL

| Đặc điểm                  | MongoDB (NoSQL)                              | RDBMS (SQL)                                  |
| ----------------------------- | -------------------------------------------- | -------------------------------------------- |
| **Mô hình dữ liệu** | Documents (JSON-like, schema-free)           | Tables với rows/columns (schema-fixed)      |
| **Quan hệ dữ liệu**  | Embedding (nested docs) hoặc Referencing    | Joins qua foreign keys                       |
| **Scaling**             | Horizontal (sharding dễ dàng)              | Vertical (thêm hardware) hoặc replication  |
| **Transactions**        | Hỗ trợ multi-doc ACID từ v4.0             | ACID mạnh mẽ từ lâu                      |
| **Query Language**      | MongoDB Query Language (MQL) với operators  | SQL chuẩn                                   |
| **Use Case**            | Dữ liệu linh hoạt, big data, real-time    | Dữ liệu có cấu trúc chặt chẽ, reports |
| **Performance**         | Cao cho read/write lớn, indexing linh hoạt | Cao cho complex joins, nhưng scale khó     |

### Ví dụ so sánh:

**SQL (MySQL):**

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100),
    age INT
);
```

**NoSQL (MongoDB):**

```javascript
{
  "_id": ObjectId("..."),
  "name": "Nguyễn Văn A",
  "email": "nguyenvana@email.com",
  "age": 25,
  "hobbies": ["đọc sách", "thể thao"],
  "address": {
    "city": "Hà Nội",
    "district": "Cầu Giấy"
  }
}
```

## Khi nào nên dùng MongoDB?

### ✅ Nên sử dụng MongoDB khi:

- **Dữ liệu không có cấu trúc cố định**: Schema thay đổi thường xuyên
- **Phát triển nhanh**: Cần tốc độ phát triển ứng dụng cao
- **Dữ liệu phức tạp**: Cần lưu trữ nested objects, arrays
- **Khả năng mở rộng**: Cần horizontal scaling
- **Big Data**: Xử lý khối lượng dữ liệu lớn
- **Real-time**: Ứng dụng thời gian thực

### ❌ Không nên sử dụng MongoDB khi:

- **Giao dịch phức tạp**: Cần ACID transactions nghiêm ngặt
- **Báo cáo phức tạp**: Cần JOIN nhiều bảng thường xuyên
- **Dữ liệu có quan hệ chặt chẽ**: Mô hình quan hệ rõ ràng
- **Ngân sách hạn chế**: Cần tối ưu chi phí lưu trữ

## Cài đặt & môi trường

### Cài MongoDB trên Windows

1. **Tải MongoDB Community Server**:

   ```bash
   # Truy cập: https://www.mongodb.com/try/download/community
   # Chọn phiên bản Windows và tải về
   ```
2. **Cài đặt**:

   ```bash
   # Chạy file .msi và làm theo hướng dẫn
   # Chọn "Complete" installation
   # Cài đặt MongoDB Compass (GUI tool)
   ```
3. **Khởi động MongoDB**:

   ```bash
   # Tạo thư mục data
   mkdir C:\data\db

   # Khởi động MongoDB
   "C:\Program Files\MongoDB\Server\7.0\bin\mongod.exe"
   ```

### Cài MongoDB trên macOS

```bash
# Sử dụng Homebrew
brew tap mongodb/brew
brew install mongodb-community@7.0

# Khởi động
brew services start mongodb/brew/mongodb-community@7.0

# Hoặc chạy trực tiếp
mongod --config /usr/local/etc/mongod.conf
```

### Cài MongoDB trên Linux (Ubuntu)

```bash
# Import public key
wget -qO - https://www.mongodb.org/static/pgp/server-7.0.asc | sudo apt-key add -

# Thêm repository
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list

# Cài đặt
sudo apt-get update
sudo apt-get install -y mongodb-org

# Khởi động
sudo systemctl start mongod
sudo systemctl enable mongod
```

### Chạy MongoDB bằng Docker

```bash
# Pull MongoDB image
docker pull mongo:7.0

# Chạy MongoDB container
docker run -d \
  --name mongodb \
  -p 27017:27017 \
  -e MONGO_INITDB_ROOT_USERNAME=admin \
  -e MONGO_INITDB_ROOT_PASSWORD=password \
  -v mongodb_data:/data/db \
  mongo:7.0

# Kết nối tới MongoDB
docker exec -it mongodb mongosh
```

**Docker Compose:**

```yaml
version: "3.8"
services:
  mongodb:
    image: mongo:7.0
    container_name: mongodb
    restart: always
    ports:
      - "27017:27017"
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: password
    volumes:
      - mongodb_data:/data/db

volumes:
  mongodb_data:
```

---

## Tổng kết

MongoDB là một lựa chọn tuyệt vời cho các ứng dụng hiện đại cần:

- **Linh hoạt** trong thiết kế schema
- **Hiệu suất cao** cho big data
- **Khả năng mở rộng** horizontal
- **Phát triển nhanh** với JSON-like documents

Trong các phần tiếp theo, chúng ta sẽ tìm hiểu chi tiết về các thao tác CRUD, query, indexing, và các tính năng nâng cao của MongoDB.
