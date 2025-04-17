---
title: M2 Exercise
description: This docs is the possible answers (in SQL) for M2 database exercise
pubDate: 30 Mar 2025
category: database
---

## SQL

```sql
 -- Find name and department of students
SELECT
  S.Name,
  D.DepartmentName
FROM
  Student S
  JOIN Department D ON S.DepartmentID = D.DepartmentID;

-- Find student names and show them from A to Z
SELECT
  S.Name
FROM
  Student S
ORDER BY
  S.Name ASC;

-- Find number of students in each department
SELECT
  D.DepartmentName,
  COUNT(S.StudentID) AS NumStudents
FROM
  Student S
  JOIN Department D ON S.DepartmentID = D.DepartmentID
GROUP BY
  D.DepartmentName;

-- Find Students who ever receoved an 'F'.
SELECT DISTINCT
  S.Name
FROM
  Student S
  JOIN Enrollment E ON E.StudentID = S.StudentID
WHERE
  E.Grade = 'F';

-- Find Students who never have an 'F'.
SELECT
  S.Name
FROM
  Student S
WHERE
  S.StudentID NOT IN(
    SELECT
      E.StudentID
    FROM
      Enrollment E
    WHERE
      E.Grade = 'F'
  );

-- Find Students with the most number of 'A' grades.course
SELECT
  StudentID
FROM
  Enrollment
WHERE
  Grade = 'A'
GROUP BY
  StudentID
HAVING
  COUNT(*) >= ALL (
    SELECT
      COUNT(*)
    FROM
      Enrollment
    WHERE
      Grade = 'A'
    GROUP BY
      StudentID
  );

-- Find courses that are taken by all students
SELECT
  C.CourseID,
  C.CourseName
FROM
  Course C
WHERE
  NOT EXISTS (
    SELECT
      *
    FROM
      Student S
    WHERE
      NOT EXISTS (
        SELECT
          *
        FROM
          Enrollment E
        WHERE
          E.StudentID = S.StudentID
          AND E.CourseID = C.CourseID
      )
  );

-- Find courses that no one is taking
SELECT
  CourseID,
  CourseName
FROM
  Course
WHERE
  CourseID NOT IN(
    SELECT DISTINCT
      CourseID
    FROM
      Enrollment
  );

-- Find course that are not taken by any student in a specific department Eg. Computer Science
SELECT
  C.CourseID,
  C.CourseName
FROM
  Course C
WHERE
  C.CourseID NOT IN(
    SELECT
      E.CourseID
    FROM
      Enrollment E
      JOIN Student S ON S.StudentID = E.StudentID
      JOIN Department D ON D.DepartmentID = S.DepartmentID
    WHERE
      D.DepartmentName = 'Computer Science'
  );

-- Find the oldest student
SELECT
  Name,
  Age
FROM
  Student
WHERE
  Age = (
    SELECT
      Max(AGE)
    FROM
      Student
  );

-- Find the average age of students
SELECT
  AVG(Age) AS AvgAge
FROM
  Student;

-- Find students who are younger then the average age
SELECT
  Name,
  Age
FROM
  Student
WHERE
  Age < (
    SELECT
      AVG(Age)
    FROM
      Student
  );

-- Find the average of students in each department
SELECT
  D.DepartmentName,
  AVG(S.Age) AS AvgAge
FROM
  Student S
  JOIN Department D ON D.DepartmentID = S.DepartmentID
GROUP BY
  D.DepartmentName;

-- Find the oldest student in each department
SELECT
  S.Name,
  D.DepartmentName,
  S.Age
FROM
  Student S
  JOIN Department D ON D.DepartmentID = S.DepartmentID
WHERE
  S.Age = (
    SELECT
      MAX(S2.Age)
    FROM
      Student S2
    WHERE
      S2.DepartmentID = S.DepartmentID
  );
```

## Normalization

### Unnormalized Table

Product_Inventory(
	ProductID,
	ProductName,
	ProductCategory,
	Warehouses **Multivalued**,
	Quantities **Multivalued**
)

### 1NF : Split any multivalued into records

- Change from `Warehouses` to `WarehouseName`
- Change from `Quantities` to `QuantityInStock`
### 2NF :  Eliminates partial dependency

Product(ProductID, ProductName, ProductCategory)
Inventory(ProductID, WarehouseName, QuantityInStock)

### 3NF : All attributes are only dependent on the primary key

- เห็นป่ะว่า WarehouseName มัน depend on product ซึ่งมันควรแยกไป ถ้ามันมีหลาย warehouse มันจะนรกทันที

Warehouse(WarehouseName, WarehouseLocation)


### Final Tables

Product(ProductID, ProductName, ProductCategory)
Inventory(ProductID, WarehouseName, QuantityInStock)
Warehouse(WarehouseName, WarehouseLocation)