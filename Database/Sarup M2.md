---
title: Sarup M2
description: This docs is the summary of M2 database lecture
pubDate: 30 Mar 2025
---

> This Sarup doesn't make you a master of SQL, it just makes you not KYS in the exam room.
## Database Normalization

- **First Normal Form (1NF)** – Removes multi-valued dependencies and that each row has a unique identifier (primary key).
- **Second Normal Form (2NF)** – Builds on 1NF by ensuring that all non-key attributes are fully dependent on the primary key (eliminates partial dependency).
- **Third Normal Form (3NF)** – Ensures that all attributes are only dependent on the primary key and not on other non-key attributes (removes transitive dependencies).
- **Boyce-Codd Normal Form (BCNF)** – A stricter version of 3NF where every determinant is a candidate key.
### BCNF Example in SQL

```sql
-- Imagine we have many-to-many relationship between Professor and Course

CREATE TABLE Professor (
    professor_id INT PRIMARY KEY,
    professor_name VARCHAR(100)
);

CREATE TABLE Course (
    course_id INT PRIMARY KEY,
    course_name VARCHAR(100)
);

CREATE TABLE CourseSchedule (
    professor_id INT,
    course_id INT,
    classroom VARCHAR(10),
    PRIMARY KEY (professor_id, course_id)
);

-- ✅ (professor_id, course_id) are candidate key
-- ❌ but you can see that classroom should be unique but can't be candidate key because we want classroom to determines the professor, not course.
```

```sql
-- So, we use Boyce-Codd Normal Form (BCNF) to normalize CourseSchedule and we'll get.

CREATE TABLE CourseSchedule (
    professor_id INT,
    course_id INT,
    PRIMARY KEY (professor_id, course_id)
);

CREATE TABLE ClassroomAssignment (
    classroom VARCHAR(10) PRIMARY KEY,
    professor_id INT
);

```
## SQL Statements 
### DDL - Data Definition Language

```sql 
-- DDL : CREATE, DROP, ALTER, (TRUNCATE, COMMENT, RENAME)
CREATE TABLE Students (
    id INT NOT NULL PRIMARY KEY AUTO_INCREMENT,
    firstname VARCHAR(255) NOT NULL,
    middlename VARCHAR(255),
    lastname VARCHAR(255) NOT NULL,
    current_year INT NOT NULL,
    department_id INT NOT NULL,

    FOREIGN KEY (department_id) REFERENCES Departments(id)
);

DROP TABLE Students;

ALTER TABLE Students ADD COLUMN email VARCHAR(255) UNIQUE;
```
### DML - Data Manipulation Language

```sql 
-- DML : INSERT, UPDATE, DELETE, LOCK, CALL, EXPLAIN PLAN
INSERT INTO Departments(department_code, department_name) VALUE ('CPE', 'Computer Engineering');

UPDATE Students SET firstname = 'Phubordin', lastname = 'Poolnai', middlename = '', current_year = 2 WHERE id = 1;

DELETE FROM Students WHERE id = 1;
```
### DQL - Data Query Language

```sql 
-- DQL : SELECT, DISTINCT, WHERE, ORDER BY, Aggregate functions, JOIN, Wildcards
SELECT * FROM Students;
SELECT DISTINCT Students; -- Distinct will only return unique records

-- ORDER BY
SELECT * FROM Students ORDER BY createdAt DESC;

-- Aggregate Functions : MIN() MAX() COUNT() SUM() AVG()
SELECT AVG(Grades.grade) AS gpa FROM Grades WHERE Grades.studentId = 1 AND Grades.courseId = 1;

-- JOIN : Left, Right, Inner, Full, Self
SELECT * FROM Students JOIN Departments ON Students.departmentId = Departments.id

-- Wildcards : LIKE
SELECT * FROM Students WHERE firstname LIKE '%phubordin%';

-- Unspecfied Where
SELECT * FROM Students WHERE departments, course; -- All combinations of Department and Course

-- AS SETS
(QUERY 1)
<set operations> -- UNION, EXCEPT, INTERSECT
(QUERY 2)

-- Example using set operations with Query
(SELECT * FROM Students)
UNION
(SELECT * FROM Participants);

-- Arithmetic Operations
-- Basically just add operations in selected columns
SELECT 1.1*grade AS increased_grade FROM Grades WHERE id = 1;

-- Sub Queries
INSERT INTO Students(gpax) SELECT AVG(Grades.grade) FROM Grades WHERE Grades.studentId = 1;

SELECT S.firstname, S.lastname FROM Students AS S WHERE S.Foo IN (SELECT * FROM Bar AS B WHERE B.slave = S.id)

-- IN : Check if result is in that table (usually use with subquery)
SELECT * FROM Students WHERE Student.country IN (SELECT country_name AS country FROM Countries WHERE country_continent = "Asia");

-- EXISTS : Check if result is exist in that table (usually use with subquery)
SELECT SupplierName
FROM Suppliers
WHERE EXISTS (SELECT ProductName FROM Products WHERE Products.SupplierID = Suppliers.supplierID AND Price < 20);

-- GROUP BY
SELECT * FROM Students LEFT JOIN Departments ON Students.departmentId = Departments.id GROUP BY Departments.name

-- HAVING
-- we use having when we want condition for aggregation (usually when we want condition for GROUP BY)
SELECT department_id, AVG(current_year) AS avg_year
FROM Students
GROUP BY department_id
HAVING AVG(current_year) > 2;

-- CASE
SELECT department_id, 
       AVG(current_year) AS avg_year,
       CASE 
           WHEN AVG(current_year) >= 4 THEN 'Senior'
           WHEN AVG(current_year) = 3 THEN 'Junior'
           WHEN AVG(current_year) = 2 THEN 'Sophomore'
           ELSE 'Freshman'
       END AS year_category
FROM Students
GROUP BY department_id
HAVING AVG(current_year) > 1; -- Only show departments where avg year is greater than 1
```
### Assertion

```sql
-- ASSERTION : MySQL don't support this!
-- So We'll use something like this
CREATE TABLE Students (
    id SERIAL PRIMARY KEY,
    firstname VARCHAR(255) NOT NULL,
    current_year INT NOT NULL,
    CHECK (current_year BETWEEN 1 AND 4) -- Ensures current_year is between 1 and 4
);

-- Teacher's way
CREATE ASSERTION SALARY_CONSTRAINT CHECK (NOT EXISTS (SELECT * FROM EMPLOYEE E, EMPLOYEE M, DEPARTMENT D WHERE E.Dno = D.Dnumber AND D.Mgr_ssn = M.Ssn AND E.Salary > M.Salary));
```
### Trigger

```sql
-- TRIGGER : Run SQL statement after one triggered
CREATE TABLE StudentLogs (
    log_id INT PRIMARY KEY AUTO_INCREMENT,
    student_id INT,
    action VARCHAR(50),
    log_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TRIGGER after_student_insert
AFTER INSERT ON Students
FOR EACH ROW
BEGIN
    INSERT INTO StudentLogs(student_id, action)
    VALUES (NEW.id, 'INSERTED');
END;
```
### View

```sql
CREATE VIEW StudentDepartment AS
SELECT S.id, S.firstname, S.lastname, D.department_name
FROM Students S
JOIN Departments D ON S.department_id = D.id;
```

### Indexing
#### Create Index

```sql
-- We can't see the index directly since it's like magic that makes SQL run faster.
-- Index works like "cache," which means it sucks at updating data but is good at retrieving data.
CREATE INDEX student_index
ON Students (id, firstname, lastname);
```
#### Drop Index

```sql
-- Other DBMS can DROP INDEX directly, but in MySQL we have to do this.
ALTER TABLE Students
DROP INDEX student_index;
```

## Relational Algebra

- **Selection(σ)** : allows you to filter rows from a given table **(IT IS WHERE IN SQL)**

```
σ(c>3)(R) ; replace R by table name

meaning : SELECT * FROM R WHERE c > 3;
```

- **Projection(π)** : allows you to pick specific columns **(IT IS SELECT IN SQL)**

```
π(B,C)(R) ; replace R by table name

meaning : SELECT B, C FROM R;
```

- **Union(U)** : combine the results of two queries into a single result **(IT IS UNION IN SQL)**

```
π(A)(C) U π(B)(D)

meaning: (SELECT A FROM C) UNION (SELECT B FROM D);
```

- **Set Difference(-)** : Same as set difference operation in math **(IT IS EXCEPT IN SQL)**

```
π(A)(C) - π(B)(D)

meaning: (SELECT A FROM C) EXCEPT (SELECT B FROM D);
```

- **Rename(ρ)** : give a temporary name to a specific relational table or to its columns **(IT IS ALTER TABLE WITH RENAME IN SQL)**

```
ρ(D/B)(R)

meaning: ALTER TABLE R RENAME COLUMN B D;
```

- **Cross Product(X)** : Same as cross product in math **(IT IS JOIN IN SQL)**

```
A x B

meaning: SELECT * FROM A JOIN B ON A.bid = B.id;
```

- **Set Intersection(∩)** : Same as intersection in math **(IT IS INTERSECT IN SQL)**

```
π(A)(C) ∩ π(B)(D)

meaning: (SELECT A FROM C) INTERSECT (SELECT B FROM D);
```

- **Division (÷)** : find tuples in one relation that are related to all tuples in another relation

Student_Course Table

| Student_ID | Course_ID |
| ---------- | --------- |
| 101        | C1        |
| 101        | C2        |
| 102        | C1        |
| 103        | C1        |
| 103        | C2        |

Course Table

| Course_ID |
| --------- |
| C1        |
| C2        |

```
Student_Course(Student_ID, Course_ID) ÷ Course(Course_ID)
```

Expected

|Student_ID|
|---|
|101|
|103|
