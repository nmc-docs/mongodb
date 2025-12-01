---
sidebar_position: 5
---

# $project

## `$project` là gì?

:::info

`$project` là một stage trong **MongoDB Aggregation Pipeline** dùng để:

- Chọn các trường để đưa vào hoặc loại bỏ khỏi output
- Tạo các trường mới bằng cách tính toán từ các trường hiện có
- Thay đổi cấu trúc của documents
- Chuyển đổi kiểu dữ liệu

:::

## Cú pháp cơ bản

```js
{
  $project: {
    <field1>: <expression|0|1>,
    <field2>: <expression|0|1>,
    ...
  }
}
```

## Các loại biểu thức (Expressions)

### Boolean expressions

- `$and`, `$or`, `$not`, `$nor`
- `$cmp`, `$eq`, `$gt`, `$gte`, `$lt`, `$lte`, `$ne`

### Arithmetic expressions

- `$abs`, `$add`, `$ceil`, `$divide`, `$exp`, `$floor`, `$ln`, `$log`, `$log10`
- `$mod`, `$multiply`, `$pow`, `$round`, `$sqrt`, `$subtract`, `$trunc`

### String expressions

- `$concat`, `$indexOfBytes`, `$indexOfCP`, `$ltrim`, `$rtrim`, `$trim`
- `$regexMatch`, `$regexFind`, `$regexFindAll`
- `$replaceAll`, `$replaceOne`, `$split`, `$strLenBytes`, `$strLenCP`
- `$strcasecmp`, `$substr`, `$substrBytes`, `$substrCP`, `$toLower`, `$toUpper`

### Date expressions

- `$dateFromParts`, `$dateFromString`, `$dateToParts`, `$dateToString`
- `$dayOfMonth`, `$dayOfWeek`, `$dayOfYear`, `$hour`, `$isoDayOfWeek`
- `$isoWeek`, `$isoWeekYear`, `$millisecond`, `$minute`, `$month`, `$second`
- `$week`, `$year`

### Array expressions

- `$arrayElemAt`, `$arrayToObject`, `$concatArrays`, `$filter`, `$first`
- `$in`, `$indexOfArray`, `$isArray`, `$last`, `$map`, `$objectToArray`
- `$range`, `$reduce`, `$reverseArray`, `$size`, `$slice`, `$zip`

### Conditional expressions

- `$cond`, `$ifNull`, `$switch`

## Ví dụ từ cơ bản đến nâng cao

### Dữ liệu mẫu

```js
// Collection: employees
db.employees.insertMany([
  {
    _id: 1,
    name: "Nguyen Van A",
    email: "a.nguyen@company.com",
    age: 28,
    salary: 25000000,
    department: "IT",
    skills: ["JavaScript", "Node.js", "MongoDB"],
    hire_date: ISODate("2020-03-15"),
    address: {
      street: "123 Nguyen Trai",
      district: "Thanh Xuan",
      city: "Hanoi",
    },
    projects: [
      { name: "Project Alpha", role: "Developer", hours: 120 },
      { name: "Project Beta", role: "Lead", hours: 200 },
    ],
    performance: {
      rating: 4.5,
      completed_tasks: 45,
      total_tasks: 50,
    },
  },
  {
    _id: 2,
    name: "Tran Thi B",
    email: "b.tran@company.com",
    age: 35,
    salary: 35000000,
    department: "HR",
    skills: ["Recruitment", "Training", "Communication"],
    hire_date: ISODate("2018-07-22"),
    address: {
      street: "456 Le Loi",
      district: "District 1",
      city: "HCMC",
    },
    projects: [{ name: "HR System", role: "Manager", hours: 150 }],
    performance: {
      rating: 4.2,
      completed_tasks: 38,
      total_tasks: 40,
    },
  },
  {
    _id: 3,
    name: "Le Van C",
    email: "c.le@company.com",
    age: 42,
    salary: 42000000,
    department: "Finance",
    skills: ["Accounting", "Tax", "Excel"],
    hire_date: ISODate("2015-11-10"),
    address: {
      street: "789 Tran Hung Dao",
      district: "Hai Ba Trung",
      city: "Hanoi",
    },
    projects: [
      { name: "Budget Planning", role: "Analyst", hours: 180 },
      { name: "Audit 2023", role: "Coordinator", hours: 220 },
    ],
    performance: {
      rating: 4.8,
      completed_tasks: 48,
      total_tasks: 48,
    },
  },
  {
    _id: 4,
    name: "Pham Thi D",
    email: "d.pham@company.com",
    age: 31,
    salary: 28000000,
    department: "Marketing",
    skills: ["SEO", "Social Media", "Content Writing"],
    hire_date: ISODate("2019-09-05"),
    address: {
      street: "321 Cau Giay",
      district: "Cau Giay",
      city: "Hanoi",
    },
    projects: [],
    performance: {
      rating: 3.9,
      completed_tasks: 35,
      total_tasks: 42,
    },
  },
]);
```

### Ví dụ cơ bản

**Ví dụ 1: Chọn trường đơn giản**

```js
// Chỉ lấy name và department
db.employees.aggregate([
  {
    $project: {
      name: 1,
      department: 1,
      _id: 0, // Loại bỏ _id
    },
  },
]);

// Kết quả:
[
  { name: "Nguyen Van A", department: "IT" },
  { name: "Tran Thi B", department: "HR" },
  { name: "Le Van C", department: "Finance" },
  { name: "Pham Thi D", department: "Marketing" },
];
```

**Ví dụ 2: Loại bỏ trường**

```js
// Loại bỏ address và performance
db.employees.aggregate([
  {
    $project: {
      address: 0,
      performance: 0,
    },
  },
]);
```

**Ví dụ 3: Đổi tên trường**

```js
// Đổi tên trường và định dạng lại
db.employees.aggregate([
  {
    $project: {
      full_name: "$name",
      email_address: "$email",
      years_of_experience: {
        $subtract: [{ $year: new Date() }, { $year: "$hire_date" }],
      },
      department: 1,
      _id: 0,
    },
  },
]);
```

### Ví dụ trung cấp

**Ví dụ 4: Tính toán với arithmetic expressions**

```js
// Tính lương theo tháng và năm
db.employees.aggregate([
  {
    $project: {
      name: 1,
      monthly_salary: "$salary",
      annual_salary: { $multiply: ["$salary", 12] },
      salary_after_tax: {
        $multiply: [
          "$salary",
          {
            $cond: {
              if: { $gte: ["$salary", 30000000] },
              then: 0.9, // 10% tax
              else: 0.95, // 5% tax
            },
          },
        ],
      },
      _id: 0,
    },
  },
]);
```

**Ví dụ 5: Xử lý string**

```js
// Xử lý và format tên
db.employees.aggregate([
  {
    $project: {
      original_name: "$name",
      first_name: {
        $arrayElemAt: [{ $split: ["$name", " "] }, -1],
      },
      last_name: {
        $arrayElemAt: [{ $split: ["$name", " "] }, 0],
      },
      email_local: {
        $arrayElemAt: [{ $split: ["$email", "@"] }, 0],
      },
      email_domain: {
        $arrayElemAt: [{ $split: ["$email", "@"] }, 1],
      },
      name_uppercase: { $toUpper: "$name" },
      name_length: { $strLenCP: "$name" },
      _id: 0,
    },
  },
]);
```

**Ví dụ 6: Xử lý date**

```js
// Phân tích ngày tháng
db.employees.aggregate([
  {
    $project: {
      name: 1,
      hire_date: 1,
      hire_year: { $year: "$hire_date" },
      hire_month: { $month: "$hire_date" },
      hire_day: { $dayOfMonth: "$hire_date" },
      hire_quarter: {
        $ceil: { $divide: [{ $month: "$hire_date" }, 3] },
      },
      formatted_date: {
        $dateToString: {
          format: "%d/%m/%Y",
          date: "$hire_date",
        },
      },
      years_of_service: {
        $divide: [
          { $subtract: [new Date(), "$hire_date"] },
          1000 * 60 * 60 * 24 * 365, // milliseconds to years
        ],
      },
      _id: 0,
    },
  },
]);
```

**Xử lý missing fields**

```js
// Sử dụng $ifNull cho fields có thể thiếu
db.employees.aggregate([
  {
    $project: {
      name: 1,
      projects: { $ifNull: ["$projects", []] },
      skills: { $ifNull: ["$skills", ["Not specified"]] },
      performance_rating: {
        $ifNull: ["$performance.rating", 3.0],
      },
      _id: 0,
    },
  },
]);
```

**Kết hợp với các stages khác**

```js
// Kết hợp $project với $group
db.employees.aggregate([
  {
    $project: {
      department: 1,
      salary_band: {
        $switch: {
          branches: [
            { case: { $lt: ["$salary", 20000000] }, then: "Low" },
            { case: { $lt: ["$salary", 40000000] }, then: "Medium" },
          ],
          default: "High",
        },
      },
    },
  },
  {
    $group: {
      _id: {
        department: "$department",
        salary_band: "$salary_band",
      },
      count: { $sum: 1 },
    },
  },
]);
```

### Ví dụ nâng cao

**Ví dụ 7: Xử lý array phức tạp**

```js
// Phân tích skills và projects
db.employees.aggregate([
  {
    $project: {
      name: 1,
      department: 1,
      // Xử lý skills array
      skill_count: { $size: "$skills" },
      first_skill: { $arrayElemAt: ["$skills", 0] },
      last_skill: { $arrayElemAt: ["$skills", -1] },
      skills_string: { $concat: ["$skills"] },
      has_js: {
        $in: ["JavaScript", "$skills"],
      },

      // Xử lý projects array
      project_count: { $size: "$projects" },
      total_project_hours: {
        $sum: "$projects.hours",
      },
      project_roles: {
        $map: {
          input: "$projects",
          as: "project",
          in: "$$project.role",
        },
      },
      senior_projects: {
        $filter: {
          input: "$projects",
          as: "project",
          cond: { $gte: ["$$project.hours", 150] },
        },
      },

      // Tạo mảng mới từ tính toán
      skill_levels: {
        $map: {
          input: "$skills",
          as: "skill",
          in: {
            skill: "$$skill",
            level: {
              $switch: {
                branches: [
                  { case: { $eq: ["$$skill", "JavaScript"] }, then: "Expert" },
                  { case: { $eq: ["$$skill", "Node.js"] }, then: "Advanced" },
                  { case: { $eq: ["$$skill", "MongoDB"] }, then: "Advanced" },
                ],
                default: "Intermediate",
              },
            },
          },
        },
      },
      _id: 0,
    },
  },
]);
```

**Ví dụ 8: Conditional expressions**

```js
// Phân loại nhân viên dựa trên nhiều tiêu chí
db.employees.aggregate([
  {
    $project: {
      name: 1,
      age: 1,
      salary: 1,
      department: 1,

      // Phân loại tuổi
      age_group: {
        $switch: {
          branches: [
            { case: { $lt: ["$age", 30] }, then: "Junior" },
            { case: { $lt: ["$age", 40] }, then: "Mid-level" },
            { case: { $lt: ["$age", 50] }, then: "Senior" },
          ],
          default: "Veteran",
        },
      },

      // Phân loại lương
      salary_grade: {
        $cond: {
          if: { $gte: ["$salary", 40000000] },
          then: "A",
          else: {
            $cond: {
              if: { $gte: ["$salary", 30000000] },
              then: "B",
              else: "C",
            },
          },
        },
      },

      // Tính hiệu suất
      performance_rate: {
        $divide: ["$performance.completed_tasks", "$performance.total_tasks"],
      },

      performance_status: {
        $cond: {
          if: { $gte: ["$performance.rating", 4.5] },
          then: "Excellent",
          else: {
            $cond: {
              if: { $gte: ["$performance.rating", 4.0] },
              then: "Good",
              else: "Needs Improvement",
            },
          },
        },
      },

      // Bonus calculation
      eligible_for_bonus: {
        $and: [
          { $gte: ["$performance.rating", 4.0] },
          { $gt: [{ $size: "$projects" }, 0] },
        ],
      },

      bonus_amount: {
        $cond: {
          if: { $gte: ["$performance.rating", 4.5] },
          then: { $multiply: ["$salary", 0.2] },
          else: {
            $cond: {
              if: { $gte: ["$performance.rating", 4.0] },
              then: { $multiply: ["$salary", 0.1] },
              else: 0,
            },
          },
        },
      },

      _id: 0,
    },
  },
]);
```

**Ví dụ 9: Reshape document structure**

```js
// Tái cấu trúc hoàn toàn document
db.employees.aggregate([
  {
    $project: {
      // Thông tin cơ bản
      employee_info: {
        id: "$_id",
        full_name: "$name",
        contact: {
          email: "$email",
          location: {
            city: "$address.city",
            district: "$address.district",
          },
        },
      },

      // Thông tin nghề nghiệp
      career_info: {
        department: "$department",
        years_of_service: {
          $floor: {
            $divide: [
              { $subtract: [new Date(), "$hire_date"] },
              1000 * 60 * 60 * 24 * 365,
            ],
          },
        },
        salary_info: {
          monthly: "$salary",
          annual: { $multiply: ["$salary", 12] },
          grade: {
            $cond: {
              if: { $gte: ["$salary", 35000000] },
              then: "Senior",
              else: "Junior",
            },
          },
        },
      },

      // Kỹ năng và dự án
      competencies: {
        technical_skills: {
          $filter: {
            input: "$skills",
            as: "skill",
            cond: {
              $in: ["$$skill", ["JavaScript", "Node.js", "MongoDB", "Excel"]],
            },
          },
        },
        soft_skills: {
          $filter: {
            input: "$skills",
            as: "skill",
            cond: {
              $in: ["$$skill", ["Communication", "Training", "Recruitment"]],
            },
          },
        },
        current_projects: {
          $map: {
            input: "$projects",
            as: "proj",
            in: {
              project_name: "$$proj.name",
              role: "$$proj.role",
              estimated_days: {
                $divide: ["$$proj.hours", 8],
              },
            },
          },
        },
      },

      // Đánh giá
      evaluation: {
        performance_score: "$performance.rating",
        task_completion_rate: {
          $multiply: [
            {
              $divide: [
                "$performance.completed_tasks",
                "$performance.total_tasks",
              ],
            },
            100,
          ],
        },
        overall_rating: {
          $avg: [
            "$performance.rating",
            {
              $divide: [
                "$performance.completed_tasks",
                "$performance.total_tasks",
              ],
            },
          ],
        },
      },

      // Metadata
      metadata: {
        last_updated: new Date(),
        data_source: "HR Database",
        version: "1.0",
      },

      _id: 0,
    },
  },
]);
```

**Ví dụ 10: Pipeline phức tạp kết hợp nhiều stages**

```js
// Báo cáo nhân sự chi tiết
db.employees.aggregate([
  // Stage 1: Lọc dữ liệu
  {
    $match: {
      department: { $in: ["IT", "Finance"] },
    },
  },

  // Stage 2: Tính toán các trường mới
  {
    $addFields: {
      tenure_years: {
        $floor: {
          $divide: [
            { $subtract: [new Date(), "$hire_date"] },
            1000 * 60 * 60 * 24 * 365,
          ],
        },
      },
      performance_score: {
        $multiply: [
          {
            $divide: [
              "$performance.completed_tasks",
              "$performance.total_tasks",
            ],
          },
          "$performance.rating",
        ],
      },
    },
  },

  // Stage 3: Project với logic phức tạp
  {
    $project: {
      // Basic Info
      employee_id: "$_id",
      name: {
        $concat: [
          { $arrayElemAt: [{ $split: ["$name", " "] }, -1] }, // Last name
          " ",
          {
            $substrCP: [
              { $arrayElemAt: [{ $split: ["$name", " "] }, 0] }, // First name
              0,
              1,
            ],
          },
          ".",
        ],
      },

      // Department & Position
      department: 1,
      position_level: {
        $switch: {
          branches: [
            {
              case: {
                $and: [
                  { $gte: ["$tenure_years", 5] },
                  { $gte: ["$salary", 40000000] },
                ],
              },
              then: "Director",
            },
            {
              case: {
                $and: [
                  { $gte: ["$tenure_years", 3] },
                  { $gte: ["$salary", 30000000] },
                ],
              },
              then: "Manager",
            },
            {
              case: { $gte: ["$tenure_years", 1] },
              then: "Senior",
            },
          ],
          default: "Junior",
        },
      },

      // Compensation
      compensation: {
        monthly_salary: "$salary",
        annual_salary: { $multiply: ["$salary", 12] },
        salary_band: {
          $concat: [
            "VND ",
            {
              $toString: {
                $multiply: [{ $floor: { $divide: ["$salary", 5000000] } }, 5],
              },
            },
            "M - VND ",
            {
              $toString: {
                $add: [
                  {
                    $multiply: [
                      { $floor: { $divide: ["$salary", 5000000] } },
                      5,
                    ],
                  },
                  5,
                ],
              },
            },
            "M",
          ],
        },
      },

      // Skills Analysis
      core_competencies: {
        $reduce: {
          input: "$skills",
          initialValue: "",
          in: {
            $concat: [
              "$$value",
              { $cond: [{ $eq: ["$$value", ""] }, "", ", "] },
              "$$this",
            ],
          },
        },
      },
      primary_skill: { $arrayElemAt: ["$skills", 0] },

      // Performance Metrics
      metrics: {
        rating: { $round: ["$performance.rating", 1] },
        completion_rate: {
          $round: [
            {
              $multiply: [
                {
                  $divide: [
                    "$performance.completed_tasks",
                    "$performance.total_tasks",
                  ],
                },
                100,
              ],
            },
            1,
          ],
        },
        overall_score: { $round: ["$performance_score", 2] },
        grade: {
          $switch: {
            branches: [
              { case: { $gte: ["$performance_score", 4.5] }, then: "A+" },
              { case: { $gte: ["$performance_score", 4.0] }, then: "A" },
              { case: { $gte: ["$performance_score", 3.5] }, then: "B+" },
              { case: { $gte: ["$performance_score", 3.0] }, then: "B" },
            ],
            default: "C",
          },
        },
      },

      // Project Involvement
      active_projects: { $size: "$projects" },
      total_project_hours: { $sum: "$projects.hours" },
      avg_hours_per_project: {
        $cond: {
          if: { $gt: [{ $size: "$projects" }, 0] },
          then: {
            $divide: [{ $sum: "$projects.hours" }, { $size: "$projects" }],
          },
          else: 0,
        },
      },

      // Tenure Information
      tenure: {
        years: "$tenure_years",
        hire_date: {
          $dateToString: {
            format: "%Y-%m-%d",
            date: "$hire_date",
          },
        },
        career_stage: {
          $cond: {
            if: { $gte: ["$tenure_years", 5] },
            then: "Established",
            else: {
              $cond: {
                if: { $gte: ["$tenure_years", 2] },
                then: "Developing",
                else: "Newcomer",
              },
            },
          },
        },
      },

      // Calculated Fields
      salary_to_performance_ratio: {
        $round: [{ $divide: ["$salary", "$performance_score"] }, 2],
      },

      _id: 0,
    },
  },

  // Stage 4: Sắp xếp
  {
    $sort: { "metrics.overall_score": -1 },
  },

  // Stage 5: Giới hạn kết quả
  {
    $limit: 10,
  },
]);
```

## Một số ví dụ thực tế

### Tạo báo cáo lương

```js
// Báo cáo lương chi tiết với phân tích
db.employees.aggregate([
  {
    $project: {
      employee_id: "$_id",
      full_name: "$name",
      department: "$department",

      // Salary Information
      base_salary: "$salary",
      monthly_tax: {
        $multiply: [
          "$salary",
          {
            $cond: {
              if: { $gte: ["$salary", 11000000] },
              then: 0.1,
              else: 0.05,
            },
          },
        ],
      },
      net_salary: {
        $subtract: [
          "$salary",
          {
            $multiply: [
              "$salary",
              {
                $cond: {
                  if: { $gte: ["$salary", 11000000] },
                  then: 0.1,
                  else: 0.05,
                },
              },
            ],
          },
        ],
      },

      // Benefits Calculation
      insurance: { $multiply: ["$salary", 0.08] },
      annual_bonus: {
        $multiply: [
          "$salary",
          {
            $switch: {
              branches: [
                { case: { $gte: ["$performance.rating", 4.5] }, then: 2 },
                { case: { $gte: ["$performance.rating", 4.0] }, then: 1.5 },
                { case: { $gte: ["$performance.rating", 3.5] }, then: 1 },
              ],
              default: 0.5,
            },
          },
        ],
      },

      // Salary Comparison
      department_avg_comparison: {
        $concat: [
          {
            $toString: {
              $round: [
                {
                  $multiply: [
                    {
                      $divide: [
                        "$salary",
                        { $avg: "$salary" }, // Lưu ý: cần $group trước
                      ],
                    },
                    100,
                  ],
                },
                1,
              ],
            },
          },
          "% of department average",
        ],
      },

      // Formatted Output
      formatted_salary: {
        $concat: [
          "VND ",
          {
            $toString: {
              $divide: ["$salary", 1000000],
            },
          },
          " million",
        ],
      },

      _id: 0,
    },
  },
]);
```

### Phân tích kỹ năng

```js
// Phân tích chi tiết kỹ năng nhân viên
db.employees.aggregate([
  {
    $project: {
      name: 1,
      department: 1,

      // Skills Analysis
      total_skills: { $size: { $ifNull: ["$skills", []] } },
      skill_categories: {
        $map: {
          input: "$skills",
          as: "skill",
          in: {
            $switch: {
              branches: [
                {
                  case: {
                    $in: [
                      "$$skill",
                      ["JavaScript", "Node.js", "MongoDB", "Python"],
                    ],
                  },
                  then: "Technical",
                },
                {
                  case: {
                    $in: [
                      "$$skill",
                      ["Communication", "Leadership", "Training"],
                    ],
                  },
                  then: "Soft Skills",
                },
                {
                  case: {
                    $in: ["$$skill", ["Accounting", "Tax", "Finance"]],
                  },
                  then: "Financial",
                },
              ],
              default: "Other",
            },
          },
        },
      },

      // Skill Diversity Score
      unique_skill_categories: { $size: { $setUnion: ["$skill_categories"] } },
      skill_diversity_score: {
        $divide: ["$unique_skill_categories", "$total_skills"],
      },

      // In-demand Skills Check
      has_in_demand_skills: {
        $gt: [
          {
            $size: {
              $setIntersection: [
                "$skills",
                ["JavaScript", "MongoDB", "Python", "Cloud"],
              ],
            },
          },
          0,
        ],
      },

      in_demand_skills: {
        $setIntersection: [
          "$skills",
          ["JavaScript", "MongoDB", "Python", "Cloud"],
        ],
      },

      // Skill Growth Potential
      recommended_skills: {
        $setDifference: [
          ["JavaScript", "MongoDB", "Python", "Cloud", "Docker"],
          "$skills",
        ],
      },

      _id: 0,
    },
  },
]);
```

## Kết luận

`$project` là một trong những stages quan trọng nhất trong MongoDB Aggregation Pipeline với khả năng:

1. **Linh hoạt trong việc định hình dữ liệu** : Chọn, loại bỏ, đổi tên và tạo trường mới
2. **Mạnh mẽ trong tính toán** : Hỗ trợ hàng chục toán tử từ số học, chuỗi, mảng đến điều kiện
3. **Tối ưu hiệu suất** : Giảm kích thước document sớm trong pipeline
4. **Chuyển đổi dữ liệu** : Chuyển đổi kiểu dữ liệu và cấu trúc

**Best practices cần nhớ** :

- Sử dụng `$project` sớm để giảm lượng dữ liệu
- Sử dụng `$ifNull` để xử lý fields missing
- Kết hợp với `$addFields` khi cần thêm fields mà vẫn giữ tất cả fields cũ
- Sử dụng indexing hiệu quả với các fields được dùng trong `$project`
- Tránh tính toán không cần thiết bằng `$cond`

Với sự linh hoạt và mạnh mẽ, `$project` là công cụ không thể thiếu cho việc xử lý và chuyển đổi dữ liệu trong MongoDB.
