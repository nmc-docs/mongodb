---
sidebar_position: 9
---

# Toán tử với mảng

## `$all` - Tất cả phần tử trong mảng

```js
// Tìm posts có tags chứa CẢ "mongodb" VÀ "tutorial"
db.posts.find({
  tags: {
    $all: ["mongodb", "tutorial"],
  },
});
```

## `$size` - Kích thước mảng

```js
// Tìm users có exactly 3 roles
db.users.find({
  roles: {
    $size: 3,
  },
});

// Tìm posts có ít nhất 5 comments
db.posts
  .find({
    comments: {
      $exists: true,
      $ne: [],
    },
  })
  .where("this.comments.length >= 5");
```

## `$elemMatch` - Phần tử phù hợp trong mảng

```js
// Tìm users có ít nhất một role với level > 5 và active = true
db.users.find({
  roles: {
    $elemMatch: {
      level: { $gt: 5 },
      active: true,
    },
  },
});

// Tìm posts có comments với likes > 10 và của author cụ thể
db.posts.find({
  comments: {
    $elemMatch: {
      likes: { $gt: 10 },
      author: "expertUser",
    },
  },
});
```
