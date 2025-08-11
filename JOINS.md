
# 📝 SQL JOIN Types Explained with Examples

---

## 📊 Sample Tables

### Table A (Employees)

| emp_id | name    | dept_id |
| ------ | ------- | ------- |
| 1      | Alice   | 10      |
| 2      | Bob     | 20      |
| 3      | Charlie | 10      |
| 4      | David   | NULL    |

### Table B (Departments)

| dept_id | dept_name |
| ------- | --------- |
| 10      | HR        |
| 20      | Finance   |
| 30      | Marketing |

---

## 1️⃣ INNER JOIN

🔹 Returns only the rows where there is a **match in both tables**.  
🔹 If no matching `dept_id`, row is excluded.

**Query:**

```sql
SELECT A.emp_id, A.name, B.dept_name
FROM A
INNER JOIN B ON A.dept_id = B.dept_id;
````

**Result:**

| emp\_id | name    | dept\_name |
| ------- | ------- | ---------- |
| 1       | Alice   | HR         |
| 2       | Bob     | Finance    |
| 3       | Charlie | HR         |

\*️⃣ *(David is excluded because `dept_id` is NULL, no match)*

---

## 2️⃣ LEFT JOIN (or LEFT OUTER JOIN)

🔹 Returns **all rows from left table (A)**, and matched rows from right table (B).
🔹 If no match in B, columns from B show `NULL`.

**Query:**

```sql
SELECT A.emp_id, A.name, B.dept_name
FROM A
LEFT JOIN B ON A.dept_id = B.dept_id;
```

**Result:**

| emp\_id | name    | dept\_name |
| ------- | ------- | ---------- |
| 1       | Alice   | HR         |
| 2       | Bob     | Finance    |
| 3       | Charlie | HR         |
| 4       | David   | NULL       |

\*️⃣ *(David included, but no matching dept, so `dept_name` is NULL)*

---

## 3️⃣ RIGHT JOIN (or RIGHT OUTER JOIN)

🔹 Returns **all rows from right table (B)**, and matched rows from left table (A).
🔹 If no match in A, columns from A show `NULL`.

**Query:**

```sql
SELECT A.emp_id, A.name, B.dept_name
FROM A
RIGHT JOIN B ON A.dept_id = B.dept_id;
```

**Result:**

| emp\_id | name    | dept\_name |
| ------- | ------- | ---------- |
| 1       | Alice   | HR         |
| 3       | Charlie | HR         |
| 2       | Bob     | Finance    |
| NULL    | NULL    | Marketing  |

\*️⃣ *(Marketing has no employee, so emp\_id and name are NULL)*

---

## 4️⃣ FULL JOIN (or FULL OUTER JOIN)

🔹 Returns **all rows from both tables**.
🔹 Matched rows combined, unmatched rows have NULLs on the other side.

**Query:**

```sql
SELECT A.emp_id, A.name, B.dept_name
FROM A
FULL JOIN B ON A.dept_id = B.dept_id;
```

**Result:**

| emp\_id | name    | dept\_name |
| ------- | ------- | ---------- |
| 1       | Alice   | HR         |
| 3       | Charlie | HR         |
| 2       | Bob     | Finance    |
| 4       | David   | NULL       |
| NULL    | NULL    | Marketing  |

\*️⃣ *(David from A with no matching dept, Marketing from B with no employee)*

---

## 5️⃣ CROSS JOIN

🔹 Returns **Cartesian product** — every row of A combined with every row of B.
🔹 Use carefully — result size = rows in A × rows in B.

**Query:**

```sql
SELECT A.emp_id, A.name, B.dept_name
FROM A
CROSS JOIN B;
```

**Result:** (First few rows shown)

| emp\_id | name  | dept\_name |
| ------- | ----- | ---------- |
| 1       | Alice | HR         |
| 1       | Alice | Finance    |
| 1       | Alice | Marketing  |
| 2       | Bob   | HR         |
| 2       | Bob   | Finance    |
| 2       | Bob   | Marketing  |
| ...     | ...   | ...        |

---

## 📋 Summary Table

| Join Type  | Rows Returned                                | Nulls for Unmatched Side? |
| ---------- | -------------------------------------------- | ------------------------- |
| INNER JOIN | Only matching rows in both tables            | No                        |
| LEFT JOIN  | All rows from left + matched rows from right | Yes, right side NULLs     |
| RIGHT JOIN | All rows from right + matched rows from left | Yes, left side NULLs      |
| FULL JOIN  | All rows from both tables                    | Yes, both sides NULLs     |
| CROSS JOIN | Cartesian product (all combinations)         | No                        |

---

