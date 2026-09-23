# SQL-Mastery

Grab and solve these 20 questions and boom, you're good to go for any technical interview

<details>
<summary> Create the Employees table to Continue👇</summary>
  
  ```sql
  DROP TABLE IF EXISTS `Employees`;
CREATE TABLE Employees (
    employee_id INT PRIMARY KEY,
    employee_name VARCHAR(50),
    department VARCHAR(50),
    salary INT,
    manager_id INT
);

INSERT INTO Employees (employee_id, employee_name, department, salary, manager_id) VALUES
(101, 'Aarav', 'Engineering', 95000, 105),
(102, 'Meera', 'Engineering', 72000, 105),
(103, 'Kabir', 'Sales', 65000, 106),
(104, 'Isha', 'Sales', 92000, 106),
(105, 'Rohan', 'Engineering', 90000, NULL),
(106, 'Neha', 'Sales', 88000, NULL);

  ```
</details>

- ## Q-1: Write a query to display all Engineering employees ordered by salary from highest to lowest.
```sql
  SELECT *
  FROM Employees
  WHERE department='Engineering'
  ORDER BY salary DESC;
```
- ## Q-2: Write a query to find the number of employees in each department.

```sql
-- count(*) is the aggregate function used to count the GROUP BY data
SELECT department, count(*) AS count_department 
FROM Employees
GROUP BY department;
```

- ## Q-3: Write a query to find the second-highest distinct salary.

```sql
SELECT MAX(salary) AS second_distinct
FROM Employees
WHERE salary < (SELECT MAX(salary) FROM Employees);

```

- ## Q-4: Write a query to find employees earning more than the company average salary.
```sql
SELECT employee_id, employee_name, salary AS more_than_average_salary
FROM Employees
WHERE salary > (SELECT AVG(salary) FROM Employees)
```

- ## Q-5: Write a query to find the highest salary in each department.
```sql
SELECT department, MAX(salary) AS max_department_vise_salary
FROM Employees
GROUP BY department
```

- ## Q-6: Write a query to find employees whose salary is higher than their manager salary.
```sql
SELECT e.employee_name, e.salary, s.employee_name AS manager_name, s.salary AS manager_salary
FROM Employees e JOIN Employees s
ON e.manager_id=s.employee_id
WHERE e.salary> s.salary
```

<details>
  <summary> Create the 2 tables with this queries</summary>
  
  Before starting up this Question, firstly we need to create 2 tables **Customers** & **Orders**. The query to create and insert the data in tables would be:
  ```sql
-- Remove tables if they already exist
DROP TABLE IF EXISTS Orders;
DROP TABLE IF EXISTS Customers;


-- Create Customers table
CREATE TABLE Customers (
    customer_id INT PRIMARY KEY,
    customer_name VARCHAR(100),
    city VARCHAR(100)
);


-- Create Orders table
CREATE TABLE Orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    amount DECIMAL(10, 2),
    status VARCHAR(20)
);


-- Insert Customers
INSERT INTO Customers (customer_id, customer_name, city)
VALUES
    (1, 'Ahmed', 'Islmabad'),
    (2, 'Ali', 'Multan'),
    (3, 'Salman', 'Lahore'),
    (4, 'Faizan', 'Burewala'),
    (5, 'Asad', 'Burewala');


-- Insert Orders
INSERT INTO Orders (order_id, customer_id, amount, status)
VALUES
    (501, 1, 2400, 'Delivered'),
    (502, 1, 3200, 'Delivered'),
    (503, 2, 1800, 'Delivered'),
    (504, 2, 4100, 'Pending'),
    (505, 4, 950, 'Cancelled'),
    (506, 5, 950, 'Pending');
  ```
</details>

- ## Q-7: Write a query to display each order with the customer name.
```sql
SELECT o.order_id, s.customer_name, o.amount, o.status, s.city
FROM Orders o JOIN Customers s
ON o.customer_id = s.customer_id;

```

- ## Q-8: Write a query to find customers who have never placed an order.
```sql
SELECT c.customer_id, c.customer_name, c.city
FROM Customers c LEFT JOIN Orders o
ON c.customer_id = o.customer_id
WHERE o.customer_id IS NULL;
```

- ## Q-9: Write a query to find customers who placed more than one order.
```sql
SELECT c.customer_name , COUNT(*) AS count_customers_with_more_than_one_order
FROM Customers c JOIN Orders o
ON c.customer_id = o.customer_id
GROUP BY c.customer_name
HAVING COUNT(*) > 1;	
```

- ## Q-10: Write a query to find the highest-spending customer based only on delivered orders.
```sql
SELECT SUM(o.amount) AS highest_spending
FROM Customers c JOIN Orders o
ON c.customer_id = o.customer_id
WHERE o.status='Delivered'
GROUP BY c.customer_name
ORDER BY highest_spending DESC
LIMIT 1
```

- ## Q-11: A table has 100 million records. You want to remove all rows as quickly as possible while keeping the table. Which SQL command will you use?

```sql
-- TRUNCATE vs DELETE — Remove all rows quickly while keeping the table.

GRANT SELECT ON Employees TO intern_user;

-- TRUNCATE is used to delete or remove the data quickly and permanently and the characteristics of the table will still remain the same
-- like number of rows, number of attributes and the primary ket's etc
-- But we have not any back up or any type of recovery after deletion

-- DELETE is used to delete the data one by one row or column, the data could be recovered after accidentally deletion
-- It's s a bit slow but safe than TRUNCATE

-- DROP is used to delete the entire table and its structure
```

- ## Q-12: A new intern should only be able to view the Employees table but should not modify it. Which SQL command will you use?
```sql

-- GRANT have the Syntax: "GRANT (SELECT, INSERT, UPDATE or according to the usage) ON (Table name) TO (name of user)
-- REVOKE works same and the Syntax would be: "REVOKE ON (Table Name) FROM (name of user)

-- GRANT SELECT — Give an intern read-only access to the Employees table.
GRANT SELECT ON Employees TO intern_user;
```

- ## Q-13: SQL Logical Execution Order — Why can't SELECT aliases be used in WHERE?

  Pattern of Queries Execution:

  <mark>FROM -> WHERE -> GROUP BY -> HAVING -> SELECT -> ORDER BY -> LIMIT</mark>




- ## Q-14: ROW_NUMBER() vs RANK() vs DENSE_RANK()

```sql
SELECT employee, salary,
    ROW_NUMBER() OVER (ORDER BY salary DESC) AS row_no,
    RANK() OVER (ORDER BY salary DESC) AS rank,
    DENSE_RANK() (ORDER BY salary DESC) AS dense_rank
FROM Employees
```

**Output:**
| Employee | Salary | ROW_NUMBER | RANK | DENSE_RANK |
|----------|--------|------------|------|------------|
| Salman   | 50000  | 1          | 1    | 1          |
| Ahmed    | 45000  | 2          | 2    | 2          |
| Hamza    | 45000  | 3          | 2    | 2          |
| Ali      | 40000  | 4          | 4    | 3          |

1. **ROW_NUMBER():** gives us the number of row
2. **RANK():** didn't catch the duplicate rank, it can skip rank
3. **DENSE_RANK():** Unlike RANK() it's didn't skip the rank on duplication and can be used based on use cases


- ## Q-15: COUNT(*) vs COUNT(email) vs COUNT(DISTINCT email)
  
| COUNT(*) | COUNT(email) | COUNT(DISTINCT email) |
|----------|--------------|-----------------------|
|  **COUNT** is the only function that can count even  the **NULL** Values in the table | **COUNT (email)** will only count the email fields on the table and skill the **NULL** Values | **COUNT(DISTINCT email)** will only count the unique emails and neglect the **duplicate emails** and the **NULL** or Empty fiels |


- ## Q-16: COALESCE() — Return the first available non-NULL value.

- ## Q-17: LAG() — Find each user's previous login date.

- ## Q-18: CASE — Classify employees into Low, Medium, and High salary bands.

- ## Q-19: NULLIF() — Prevent divide-by-zero errors.

- ## Q-20: Delete duplicate records while keeping the newest record for each email.
