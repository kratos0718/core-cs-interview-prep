# DBMS (Concepts) — 70 Questions (basic → advanced)

⭐ = very frequently asked. (SQL *query-writing* is a separate file: `06_dbms_sql/`.)

---

## A. Fundamentals

**1. ⭐ What is a DBMS?**
Database Management System — software that lets you create, store, retrieve, update, and manage data in databases, while handling concurrency, security, and integrity. Examples: MySQL, PostgreSQL, Oracle, MongoDB. It sits between the user/app and the raw data files.

**2. DBMS vs RDBMS?**
A **DBMS** stores data (possibly as files, no strict relations). An **RDBMS** (Relational DBMS) stores data in **tables** (relations) with rows and columns, enforces relationships via keys, and supports SQL + ACID. MySQL/PostgreSQL are RDBMS; MongoDB is a (non-relational) DBMS.

**3. ⭐ DBMS vs File System?**
A file system stores data in flat files with no relationships, lots of redundancy, no concurrency control, and weak security. A DBMS adds: structured relationships, reduced redundancy, concurrent multi-user access, ACID transactions, querying (SQL), backup/recovery, and access control.

**4. What is a database schema?**
The logical structure/blueprint of the database — the tables, columns, data types, relationships, and constraints. It's the *design*, defined once; the actual data is the *instance*. Schema = the skeleton, data = the flesh.

**5. What is a relation / tuple / attribute?**
In relational terms: a **relation** is a table; a **tuple** is a row (one record); an **attribute** is a column (a field). **Degree** = number of attributes; **cardinality** = number of tuples.

**6. ⭐ What is a primary key?**
A column (or set of columns) that **uniquely identifies** each row in a table. It must be unique and NOT null. Each table has at most one. Example: `student_id`. It's how you reliably reference a specific row.

**7. ⭐ What is a foreign key?**
A column in one table that refers to the primary key of another table, creating a relationship between them and enforcing **referential integrity** (you can't reference a row that doesn't exist). Example: `orders.customer_id` referencing `customers.id`.

**8. Candidate key vs Primary key vs Super key?**
A **super key** is any set of columns that uniquely identifies a row (may have extra columns). A **candidate key** is a *minimal* super key (no redundant columns). The **primary key** is the *one* candidate key you choose as the main identifier. The other candidate keys become **alternate keys**.

**9. What is a composite key?**
A primary key made of **two or more columns** together, used when no single column is unique on its own. Example: `(student_id, course_id)` uniquely identifies an enrollment.

**10. What is a unique key vs primary key?**
Both enforce uniqueness. **Primary key**: one per table, cannot be null. **Unique key**: can be several per table, and allows one null (usually). e.g. `email` can be a unique key while `id` is the primary key.

**11. What is referential integrity?**
A rule ensuring foreign-key values always match an existing primary-key value in the referenced table (or are null). It prevents "orphan" records — e.g. an order pointing to a deleted customer. Enforced via foreign-key constraints with options like ON DELETE CASCADE.

**12. What are constraints? Name them.**
Rules enforced on columns to maintain data integrity: **NOT NULL** (no empty values), **UNIQUE** (no duplicates), **PRIMARY KEY** (unique + not null), **FOREIGN KEY** (referential integrity), **CHECK** (a custom condition, e.g. `age > 0`), **DEFAULT** (a fallback value).

---

## B. Normalization (extremely high-frequency)

**13. ⭐ What is normalization?**
The process of organizing data to **reduce redundancy** and **avoid anomalies** (insert/update/delete problems) by dividing large tables into smaller related ones and defining relationships. It improves data integrity. Done in stages called normal forms.

**14. ⭐ What are insertion, update, and deletion anomalies?**
Problems in poorly designed (un-normalized) tables: **Insertion anomaly** — can't add data without unrelated data (can't add a course with no enrolled student). **Update anomaly** — must change the same fact in many rows, risking inconsistency. **Deletion anomaly** — deleting a row loses unrelated data accidentally. Normalization fixes these.

**15. ⭐ Explain 1NF.**
First Normal Form: each column holds **atomic (indivisible) values** — no repeating groups, no arrays/lists in a cell. Each row is unique. Example fix: replace a "phone_numbers" cell containing "123, 456" with separate rows/columns.

**16. ⭐ Explain 2NF.**
Second Normal Form: must be in 1NF **and** have no **partial dependency** — every non-key attribute depends on the *whole* primary key, not just part of it. Relevant only when the PK is composite. Fix: move partially-dependent columns to their own table.

**17. ⭐ Explain 3NF.**
Third Normal Form: must be in 2NF **and** have no **transitive dependency** — non-key attributes must depend *only* on the primary key, not on another non-key attribute. Example: if `zip → city`, and `city` is a non-key, move it out. (Rule of thumb: "every non-key attribute depends on the key, the whole key, and nothing but the key.")

**18. What is BCNF?**
Boyce-Codd Normal Form: a stricter 3NF. For every functional dependency X→Y, X must be a **super key**. It handles edge cases 3NF misses (when there are multiple overlapping candidate keys). Most well-designed schemas aim for 3NF/BCNF.

**19. What is a functional dependency?**
A relationship where one attribute (or set) uniquely determines another: X → Y means "if you know X, you know Y." Example: `student_id → student_name`. Functional dependencies are the basis for defining normal forms.

**20. What is a transitive dependency?**
When a non-key attribute depends on another non-key attribute rather than directly on the key: A → B and B → C implies A → C transitively. 3NF removes these. Example: `student_id → dept_id → dept_name`.

**21. What is a partial dependency?**
When a non-key attribute depends on only *part* of a composite primary key, not the whole. 2NF removes these.

**22. ⭐ Why (and when) do we denormalize?**
**Denormalization** deliberately adds redundancy (merging tables, duplicating columns) to **improve read performance** by avoiding expensive joins — at the cost of more storage and harder updates. Used in read-heavy systems / data warehouses / reporting where speed beats strict integrity.

**23. Normalization vs Denormalization trade-off?**
Normalization → less redundancy, better integrity, more (slower) joins, optimized for writes. Denormalization → faster reads, fewer joins, more redundancy, harder/inconsistent writes. Choose based on read-vs-write balance.

**24. What is a higher normal form (4NF/5NF) — briefly?**
**4NF** removes multi-valued dependencies (independent multi-valued facts in one table). **5NF** deals with join dependencies. Rarely needed in practice for typical apps; 3NF/BCNF usually suffices. Knowing they exist is enough for most interviews.

---

## C. Transactions, ACID, Concurrency (high-frequency)

**25. ⭐ What is a transaction?**
A single logical unit of work — a sequence of operations that must execute **completely or not at all**. Classic example: a bank transfer (debit A, credit B) must both happen or neither. Bounded by BEGIN ... COMMIT (or ROLLBACK).

**26. ⭐ What are the ACID properties?**
**Atomicity**: all-or-nothing — a transaction fully completes or fully rolls back. **Consistency**: a transaction moves the DB from one valid state to another (constraints hold). **Isolation**: concurrent transactions don't interfere; each behaves as if alone. **Durability**: once committed, changes survive crashes (persisted). ACID guarantees reliable transactions.

**27. Explain Atomicity with an example.**
A money transfer: subtract ₹100 from A, add ₹100 to B. If the system crashes after the subtraction but before the addition, atomicity ensures the whole transaction rolls back — A's money is restored. No partial transfer.

**28. Explain Isolation and why it's hard.**
Isolation ensures concurrent transactions don't see each other's incomplete work. It's hard because allowing full concurrency without coordination causes anomalies (dirty reads, etc.), but enforcing strict isolation reduces performance. Hence **isolation levels** trade off correctness vs speed.

**29. ⭐ What are the concurrency problems (read phenomena)?**
**Dirty read**: reading uncommitted data from another transaction (that might roll back). **Non-repeatable read**: reading the same row twice gives different values (another transaction updated it in between). **Phantom read**: re-running a query returns different *rows* (another transaction inserted/deleted matching rows).

**30. ⭐ What are the isolation levels?**
From weakest to strongest: **Read Uncommitted** (allows dirty reads), **Read Committed** (no dirty reads), **Repeatable Read** (no dirty or non-repeatable reads), **Serializable** (no dirty, non-repeatable, or phantom reads — fully isolated, slowest). Higher levels = more correctness, less concurrency.

**31. Which isolation level prevents which problem? (mapping)**
| Level | Dirty | Non-repeatable | Phantom |
|-------|-------|----------------|---------|
| Read Uncommitted | ✗ allowed | ✗ | ✗ |
| Read Committed | ✓ prevented | ✗ | ✗ |
| Repeatable Read | ✓ | ✓ | ✗ |
| Serializable | ✓ | ✓ | ✓ |

**32. What is a schedule? Serial vs concurrent?**
A **schedule** is the order in which operations of concurrent transactions execute. A **serial schedule** runs transactions one fully after another (always correct but slow). A **concurrent schedule** interleaves them (fast but must be controlled to stay correct).

**33. What is serializability?**
A concurrent schedule is **serializable** if its outcome is equivalent to *some* serial schedule — meaning it's correct despite interleaving. It's the gold standard of correctness for concurrent transactions. Types: conflict-serializable, view-serializable.

**34. ⭐ What is a deadlock in DBMS? How is it handled?**
Two transactions each hold a lock the other needs, waiting forever. Handled by **detection** (build a wait-for graph, find a cycle, abort one transaction — the "victim"), **prevention** (timestamp ordering like wait-die/wound-wait), or **timeouts**. (Same concept as OS deadlock.)

**35. What is locking? Shared vs Exclusive lock?**
Locks control concurrent access. A **shared (read) lock**: multiple transactions can read simultaneously. An **exclusive (write) lock**: only one transaction can hold it, for writing — blocks all others. A row being written can't be read/written by others.

**36. What is Two-Phase Locking (2PL)?**
A protocol guaranteeing serializability: each transaction has a **growing phase** (acquire locks, release none) and a **shrinking phase** (release locks, acquire none). Once it releases any lock, it can't acquire more. **Strict 2PL** holds all locks until commit (prevents cascading rollbacks).

**37. Optimistic vs Pessimistic concurrency control?**
**Pessimistic**: assume conflicts are likely — lock data before use (locking). **Optimistic**: assume conflicts are rare — let transactions run, then validate at commit time and roll back if a conflict occurred. Optimistic suits low-contention workloads.

**38. What is a dirty read again, concretely?**
T1 updates a row but hasn't committed. T2 reads that updated value. Then T1 rolls back. T2 now holds data that never officially existed — a "dirty" value. Prevented at Read Committed and above.

---

## D. Indexing & Storage (high-frequency)

**39. ⭐ What is an index?**
A data structure (usually a B+ tree) that speeds up data retrieval on certain columns, like a book's index lets you find a topic without scanning every page. It trades extra storage and slower writes (the index must be updated) for much faster reads/searches.

**40. ⭐ Why not index every column?**
Each index consumes storage and **slows down INSERT/UPDATE/DELETE** (every change must update the indexes). Indexes help reads but hurt writes, so you index only columns frequently used in WHERE, JOIN, or ORDER BY.

**41. Clustered vs Non-clustered index?**
A **clustered index** determines the *physical order* of rows on disk — so there's only one per table (often the primary key); range queries are fast. A **non-clustered index** is a separate structure with pointers to the rows — you can have many; it adds a lookup step. (Clustered = the actual book pages in order; non-clustered = a separate index card pointing to pages.)

**42. ⭐ Why do databases use B+ trees for indexes?**
B+ trees are balanced, keep data sorted, support fast lookups, insertions, deletions in O(log n), and — crucially — store all values in leaf nodes linked together, making **range queries** efficient. They're disk-friendly (high fan-out = shallow tree = few disk reads). 

**43. What is a hash index?**
An index using a hash table — O(1) for *exact-match* lookups (`WHERE x = 5`) but useless for ranges (`WHERE x > 5`) or sorting. B+ trees are more versatile; hash indexes shine for equality-only lookups.

**44. What is a covering index?**
An index that contains *all* the columns a query needs, so the query can be answered from the index alone without touching the table ("index-only scan"). Faster, since it avoids extra row lookups.

**45. What is database denormalization's effect on indexing?**
Denormalized tables have fewer joins but wider rows; indexing strategy shifts toward the columns used in filtered reads. (Links to Q22.)

---

## E. Joins, Views, and more

**46. ⭐ What is a JOIN? Name the types.**
Combines rows from two or more tables based on a related column. **INNER JOIN** (only matching rows in both), **LEFT (OUTER) JOIN** (all from left + matches from right, nulls otherwise), **RIGHT JOIN** (all from right + matches), **FULL OUTER JOIN** (all rows from both, nulls where no match), **CROSS JOIN** (Cartesian product — every combination), **SELF JOIN** (a table joined to itself).

**47. INNER vs LEFT JOIN — when to use?**
**INNER**: when you only want rows that have a match in both tables (e.g. orders that have a customer). **LEFT**: when you want *all* rows from the left table even if there's no match (e.g. all customers, including those with zero orders → their order columns are null).

**48. What is a view?**
A **virtual table** defined by a stored query — it doesn't store data itself but presents data from underlying tables. Uses: simplify complex queries, restrict access to certain columns (security), and provide a stable interface. A **materialized view** *does* store the result (faster reads, must be refreshed).

**49. View vs Materialized view?**
A **view** is computed on every access (always fresh, no storage). A **materialized view** stores the precomputed result (fast reads, uses storage, can be stale until refreshed). Use materialized views for expensive queries on slowly-changing data.

**50. What is a stored procedure?**
A precompiled set of SQL statements stored in the database, callable by name, optionally with parameters. Benefits: reusability, reduced network traffic, better performance (precompiled), and centralized logic. e.g. a `transfer_money(a, b, amount)` procedure.

**51. Stored procedure vs Function?**
A **function** must return a value and is usually used within queries (e.g. in SELECT); typically no side effects. A **stored procedure** may return zero or many results, can perform actions (insert/update), and is called explicitly. Procedures = actions; functions = computations.

**52. What is a trigger?**
A procedure that automatically executes in response to a database event (INSERT/UPDATE/DELETE) on a table. Uses: enforce complex rules, auditing/logging, maintaining derived data. e.g. "after inserting an order, decrement inventory."

**53. What is a cursor?**
A pointer that lets you iterate over query result rows one at a time, for row-by-row processing (when set-based SQL isn't enough). Generally avoided when a set-based query works, because cursors are slower.

---

## F. SQL language structure & types

**54. ⭐ DDL vs DML vs DCL vs TCL?**
**DDL** (Data Definition): defines schema — CREATE, ALTER, DROP, TRUNCATE. **DML** (Data Manipulation): works with data — SELECT, INSERT, UPDATE, DELETE. **DCL** (Data Control): permissions — GRANT, REVOKE. **TCL** (Transaction Control): COMMIT, ROLLBACK, SAVEPOINT.

**55. ⭐ DELETE vs TRUNCATE vs DROP?**
**DELETE** (DML): removes rows (optionally with WHERE), can be rolled back, fires triggers, slower. **TRUNCATE** (DDL): removes *all* rows fast, can't filter, usually can't roll back, resets identity, no triggers. **DROP** (DDL): removes the *entire table* (structure + data).

**56. WHERE vs HAVING?**
**WHERE** filters rows *before* grouping (can't use aggregate functions). **HAVING** filters *after* grouping (used with GROUP BY, can use aggregates like `COUNT(*) > 5`). Order: WHERE → GROUP BY → HAVING.

**57. What is GROUP BY?**
Groups rows sharing a value in specified columns so you can apply aggregate functions (COUNT, SUM, AVG, MIN, MAX) per group. e.g. total sales *per* region.

**58. What are aggregate functions?**
Functions that compute a single value over a set of rows: COUNT (number of rows), SUM, AVG, MIN, MAX. Usually paired with GROUP BY.

**59. What is a subquery? Correlated subquery?**
A **subquery** is a query nested inside another. A **correlated subquery** references columns from the outer query and runs once per outer row (slower) — e.g. "employees earning more than their department's average." Non-correlated subqueries run once independently.

**60. UNION vs UNION ALL?**
Both combine results of two queries (same columns). **UNION** removes duplicates (slower, does a sort/dedupe). **UNION ALL** keeps all rows including duplicates (faster). Use UNION ALL unless you specifically need dedup.

**61. What is the difference between `NULL` and 0 / empty string?**
**NULL** means "unknown/missing/no value" — it is not 0, not empty string, not false. Comparisons with NULL yield "unknown" (e.g. `NULL = NULL` is not true — use `IS NULL`). A frequent source of bugs.

---

## G. Advanced / theory

**62. ⭐ SQL vs NoSQL?**
**SQL (relational)**: structured tables, fixed schema, ACID, great for complex queries and relationships, scales *vertically* (MySQL/PostgreSQL). **NoSQL**: flexible/schemaless (documents, key-value, graph, column), scales *horizontally*, great for huge unstructured data and high throughput, often sacrifices strict consistency (MongoDB, Cassandra, Redis). Choose based on data structure, scale, and consistency needs.

**63. What is the CAP theorem?**
For a distributed data store, you can guarantee at most **two** of: **Consistency** (every read sees the latest write), **Availability** (every request gets a response), **Partition tolerance** (works despite network splits). Since partitions are unavoidable in distributed systems, you effectively trade Consistency vs Availability (CP vs AP systems).

**64. What is the difference between consistency in ACID vs CAP?**
**ACID consistency**: the database stays valid w.r.t. constraints after a transaction. **CAP consistency**: all nodes see the same data at the same time (a distributed-systems property). They're different uses of the word — a common trap.

**65. What is sharding?**
Horizontally partitioning data across multiple database servers (each holds a *subset* of rows), so no single server holds everything — enabling scale beyond one machine. e.g. users A–M on shard 1, N–Z on shard 2. Adds complexity (cross-shard queries).

**66. What is replication?**
Keeping copies of the database on multiple servers for **availability** and **read scaling**. **Master-slave**: writes go to master, reads can hit replicas. Trade-off: replication lag can cause replicas to be slightly stale (eventual consistency).

**67. Vertical vs Horizontal scaling?**
**Vertical (scale up)**: add more power (CPU/RAM) to one server — simple but limited and a single point of failure. **Horizontal (scale out)**: add more servers (sharding/replication) — virtually unlimited but more complex. NoSQL is built for horizontal; SQL traditionally favors vertical.

**68. What is a data warehouse vs database (OLTP vs OLAP)?**
**OLTP** (Online Transaction Processing): everyday operational database — many small, fast read/write transactions, normalized (e.g. an e-commerce DB). **OLAP** (Online Analytical Processing): a data warehouse for analytics/reporting — large complex read queries over historical data, often denormalized (star schema).

**69. What is ETL?**
Extract, Transform, Load — the process of pulling data from sources, cleaning/reshaping it, and loading it into a data warehouse for analysis. The backbone of analytics pipelines.

**70. What is a transaction log / WAL?**
A **Write-Ahead Log** records changes *before* they're applied to the database. It ensures **durability** and enables crash **recovery** (replay committed transactions, undo incomplete ones) — the mechanism behind ACID's Atomicity and Durability.

---

## ⭐ The 12 DBMS must-knows:
Primary vs foreign key (6,7) · Normalization + 1NF/2NF/3NF (13,15-17) · Anomalies (14) · Denormalization (22) · Transaction (25) · ACID (26) · Isolation levels + read phenomena (29,30) · Index + why B+ tree (39,42) · Clustered vs non-clustered (41) · JOIN types (46) · DELETE vs TRUNCATE vs DROP (55) · SQL vs NoSQL + CAP (62,63).
