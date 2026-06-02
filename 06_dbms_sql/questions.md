# SQL Queries — 40 Questions (basic → advanced)

⭐ = very frequently asked. These are the *write-a-query* and *what-does-this-do* questions. Assume tables: `Employees(id, name, salary, dept_id, manager_id)`, `Departments(id, name)`.

---

## A. Basics

**1. ⭐ Write a query to select all employees.**
```sql
SELECT * FROM Employees;
```
`*` means all columns. (In production, list columns explicitly — `*` is fine for quick queries.)

**2. Select only names and salaries.**
```sql
SELECT name, salary FROM Employees;
```

**3. ⭐ Select employees with salary > 50000.**
```sql
SELECT * FROM Employees WHERE salary > 50000;
```
`WHERE` filters rows by a condition.

**4. Select distinct department ids.**
```sql
SELECT DISTINCT dept_id FROM Employees;
```
`DISTINCT` removes duplicate rows from the result.

**5. ⭐ Order employees by salary descending.**
```sql
SELECT * FROM Employees ORDER BY salary DESC;
```
`ASC` (default) = ascending, `DESC` = descending.

**6. Get the top 5 highest-paid employees.**
```sql
SELECT * FROM Employees ORDER BY salary DESC LIMIT 5;
```
(`LIMIT` in MySQL/PostgreSQL; `TOP 5` in SQL Server; `FETCH FIRST 5 ROWS ONLY` in standard SQL.)

**7. Find employees whose name starts with 'A'.**
```sql
SELECT * FROM Employees WHERE name LIKE 'A%';
```
`LIKE` with `%` (any chars) and `_` (single char). `'A%'` = starts with A; `'%a%'` = contains a.

**8. ⭐ Find employees in departments 1, 2, or 3.**
```sql
SELECT * FROM Employees WHERE dept_id IN (1, 2, 3);
```
`IN` checks membership in a list.

**9. Find employees with salary between 40000 and 60000.**
```sql
SELECT * FROM Employees WHERE salary BETWEEN 40000 AND 60000;
```
`BETWEEN` is inclusive of both ends.

**10. ⭐ Find employees with no manager (NULL).**
```sql
SELECT * FROM Employees WHERE manager_id IS NULL;
```
Use `IS NULL` / `IS NOT NULL` — never `= NULL` (NULL comparisons are "unknown").

---

## B. Aggregation & Grouping

**11. ⭐ Count total employees.**
```sql
SELECT COUNT(*) FROM Employees;
```
`COUNT(*)` counts rows; `COUNT(column)` counts non-NULL values in that column.

**12. ⭐ Find the average salary.**
```sql
SELECT AVG(salary) FROM Employees;
```
Aggregate functions: COUNT, SUM, AVG, MIN, MAX.

**13. ⭐ Count employees per department.**
```sql
SELECT dept_id, COUNT(*) AS emp_count
FROM Employees
GROUP BY dept_id;
```
`GROUP BY` groups rows; aggregates apply per group.

**14. ⭐ Departments with more than 5 employees.**
```sql
SELECT dept_id, COUNT(*) AS cnt
FROM Employees
GROUP BY dept_id
HAVING COUNT(*) > 5;
```
`HAVING` filters *groups* (after GROUP BY); `WHERE` filters *rows* (before). You can't use aggregates in WHERE.

**15. ⭐ WHERE vs HAVING (the classic).**
`WHERE` filters individual rows before grouping and can't use aggregate functions. `HAVING` filters groups after `GROUP BY` and can use aggregates. Execution order: FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY.

**16. Find the max salary in each department, only for departments whose max > 70000.**
```sql
SELECT dept_id, MAX(salary) AS max_sal
FROM Employees
GROUP BY dept_id
HAVING MAX(salary) > 70000;
```

**17. Total salary paid per department, highest first.**
```sql
SELECT dept_id, SUM(salary) AS total
FROM Employees
GROUP BY dept_id
ORDER BY total DESC;
```

---

## C. Joins (very high-frequency)

**18. ⭐ List each employee with their department name.**
```sql
SELECT e.name, d.name AS dept_name
FROM Employees e
INNER JOIN Departments d ON e.dept_id = d.id;
```
`INNER JOIN` returns only rows with a match in both tables. Aliases (`e`, `d`) keep it readable.

**19. ⭐ List ALL departments, even those with no employees.**
```sql
SELECT d.name, COUNT(e.id) AS emp_count
FROM Departments d
LEFT JOIN Employees e ON d.id = e.dept_id
GROUP BY d.name;
```
`LEFT JOIN` keeps all rows from the left table (Departments), with NULLs where no employee matches. `COUNT(e.id)` counts 0 for empty departments (COUNT ignores NULLs).

**20. ⭐ INNER vs LEFT JOIN — difference?**
**INNER JOIN**: only rows with a match in *both* tables. **LEFT JOIN**: all rows from the left table + matched rows from the right (NULLs where no match). Use LEFT when you want to keep unmatched left rows (e.g. departments with zero employees).

**21. Find employees who have NO department (orphans).**
```sql
SELECT e.name
FROM Employees e
LEFT JOIN Departments d ON e.dept_id = d.id
WHERE d.id IS NULL;
```
The "LEFT JOIN + WHERE right IS NULL" pattern finds rows with no match — a very common trick.

**22. ⭐ Self join: list employees with their manager's name.**
```sql
SELECT e.name AS employee, m.name AS manager
FROM Employees e
LEFT JOIN Employees m ON e.manager_id = m.id;
```
A **self join** joins a table to itself (here, employees to their managers, both in the same table). LEFT JOIN so top-level employees (no manager) still appear.

**23. What does a CROSS JOIN do?**
Returns the **Cartesian product** — every row of table A paired with every row of table B (A rows × B rows). Rarely what you want; usually a mistake (forgot the join condition). Useful for generating combinations.

**24. FULL OUTER JOIN — what is it?**
Returns all rows from *both* tables, matching where possible, NULLs where not. (MySQL doesn't support it directly — emulate with LEFT JOIN UNION RIGHT JOIN.)

---

## D. Subqueries & advanced

**25. ⭐ Find employees earning more than the average salary.**
```sql
SELECT name, salary
FROM Employees
WHERE salary > (SELECT AVG(salary) FROM Employees);
```
A **subquery** — the inner query computes the average, the outer compares to it.

**26. ⭐ Find the second-highest salary (classic interview Q).**
```sql
-- Method 1: subquery
SELECT MAX(salary) FROM Employees
WHERE salary < (SELECT MAX(salary) FROM Employees);

-- Method 2: with LIMIT/OFFSET
SELECT DISTINCT salary FROM Employees
ORDER BY salary DESC LIMIT 1 OFFSET 1;
```
Both find the 2nd highest. Method 2 generalizes to Nth highest via `OFFSET N-1`.

**27. Find the Nth highest salary using a window function.**
```sql
SELECT salary FROM (
  SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
  FROM Employees
) t WHERE rnk = 2;   -- N = 2
```

**28. ⭐ What is a correlated subquery?**
A subquery that references the outer query and runs once per outer row. Example — employees earning more than their *department's* average:
```sql
SELECT e.name FROM Employees e
WHERE e.salary > (
  SELECT AVG(salary) FROM Employees e2
  WHERE e2.dept_id = e.dept_id   -- references outer e
);
```
Slower than a regular subquery (re-runs per row) but powerful.

**29. ⭐ Find duplicate names.**
```sql
SELECT name, COUNT(*)
FROM Employees
GROUP BY name
HAVING COUNT(*) > 1;
```
Group by the column, keep groups appearing more than once.

**30. ⭐ Delete duplicate rows, keeping one.**
```sql
DELETE e1 FROM Employees e1
JOIN Employees e2
  ON e1.name = e2.name AND e1.id > e2.id;
```
Keeps the row with the smallest id per duplicate name. (Approaches vary by DB.)

---

## E. Window functions, CTEs, and modern SQL

**31. ⭐ What is a window function?**
Performs a calculation across a set of rows *related to the current row*, without collapsing them into groups (unlike GROUP BY). Syntax: `func() OVER (PARTITION BY ... ORDER BY ...)`. e.g. running totals, rankings, row numbers — while still showing every row.

**32. ROW_NUMBER vs RANK vs DENSE_RANK?**
All assign a number ordered within partitions. **ROW_NUMBER**: unique sequential (1,2,3,4 — no ties handling). **RANK**: ties get the same rank, then *skips* (1,2,2,4). **DENSE_RANK**: ties get the same rank, *no skip* (1,2,2,3). Use DENSE_RANK for "Nth highest distinct value."

**33. ⭐ Rank employees by salary within each department.**
```sql
SELECT name, dept_id, salary,
       RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS dept_rank
FROM Employees;
```
`PARTITION BY` restarts the ranking per department.

**34. What is a CTE (Common Table Expression)?**
A named temporary result set defined with `WITH`, usable within the main query — improves readability and enables recursion. 
```sql
WITH high_earners AS (
  SELECT * FROM Employees WHERE salary > 80000
)
SELECT dept_id, COUNT(*) FROM high_earners GROUP BY dept_id;
```
Cleaner than nested subqueries.

**35. What is a recursive CTE used for?**
Querying hierarchical data (org charts, tree/graph structures) — e.g. "all employees under a manager, at any depth." The CTE references itself, building the hierarchy level by level.

---

## F. Concepts that come up in query rounds

**36. ⭐ What is an index and how does it speed up a query?**
An index (B+ tree) lets the DB find rows matching a `WHERE`/`JOIN` condition without scanning the whole table — turning a full O(n) scan into an O(log n) lookup. Index columns used in WHERE/JOIN/ORDER BY. Trade-off: slower writes, more storage. (See DBMS file Q39.)

**37. Why is `SELECT *` discouraged in production?**
It fetches all columns (more data over the network, more memory), prevents covering-index optimizations, and breaks if column order/count changes. List only the columns you need.

**38. ⭐ DELETE vs TRUNCATE vs DROP (query context)?**
`DELETE FROM t WHERE ...` removes specific rows, logged, rollback-able. `TRUNCATE TABLE t` removes all rows fast, usually no rollback, resets auto-increment. `DROP TABLE t` removes the table entirely (structure + data).

**39. What does `GROUP BY` with `COUNT(*)` vs `COUNT(column)` differ?**
`COUNT(*)` counts all rows in the group. `COUNT(column)` counts rows where that column is NOT NULL. So in a LEFT JOIN, `COUNT(e.id)` correctly returns 0 for unmatched groups, while `COUNT(*)` would return 1.

**40. ⭐ Explain the logical order of execution of a SELECT query.**
SQL is written `SELECT ... FROM ... WHERE ... GROUP BY ... HAVING ... ORDER BY` but *executes* roughly: **FROM** (and JOINs) → **WHERE** (filter rows) → **GROUP BY** (group) → **HAVING** (filter groups) → **SELECT** (pick columns, compute) → **DISTINCT** → **ORDER BY** → **LIMIT**. This is why you can't use a SELECT alias in WHERE (WHERE runs before SELECT) but can in ORDER BY.

---

## ⭐ The 10 SQL must-knows:
WHERE vs HAVING (15) · GROUP BY + aggregates (13) · INNER vs LEFT JOIN (20) · self join (22) · find no-match rows (21) · second/Nth highest salary (26,27) · correlated subquery (28) · find/delete duplicates (29,30) · window functions + RANK (31,32) · logical execution order (40).
