
# 💼  Nth Highest Salary (Department-wise)

---

## 📌 Problem Statement

**“Company ke employees me se kisi department ka Nth highest salary kaise find karoge?”**

For example:
- 🥈 2nd highest salary
- 🥉 3rd highest salary
- 🎯 5th highest salary

---

## 1️⃣ Simple Approach (Subquery)

### 🔹 Example: 2nd Highest Salary in department 10

```sql
SELECT MAX(salary) 
FROM employee 
WHERE salary < (
    SELECT MAX(salary) 
    FROM employee 
    WHERE dept_id = 10
)
AND dept_id = 10;
````

**📝 Explanation:**

* Pehle subquery highest salary nikalti hai department 10 ki.
* Fir outer query us se chhoti salary me sabse badi (MAX) nikalti hai.
* Wo **2nd highest salary** hoti hai.

**⚠️ Limitation:**

* Agar 3rd ya 4th highest salary chahiye toh query bahut complex ho jati hai.
* Duplicate salaries ka issue nahi solve karta.

---

## 2️⃣ Advanced Approach (Window Functions)

SQL me **window functions** `ROW_NUMBER()`, `RANK()`, aur `DENSE_RANK()` bahut powerful hote hain nth highest salary jaise questions ke liye.

### 🔹 Example: 2nd Highest Salary Using `ROW_NUMBER()`

```sql
SELECT salary 
FROM (
    SELECT salary,
           ROW_NUMBER() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rn
    FROM employee
    WHERE dept_id = 10
) t
WHERE rn = 2;
```

**📝 Explanation:**

* `ROW_NUMBER()` salary ko descending order me number assign karta hai (1, 2, 3, …).
* `PARTITION BY dept_id` karta hai taaki har department alag consider ho.
* Fir hum outer query me `rn = 2` filter karte hain → **2nd highest salary**.

---

## 📌 Query jo tumne di hai:

```sql
SELECT salary 
FROM (
    SELECT salary,
           ROW_NUMBER() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rn
    FROM employee
    WHERE dept_id = 10
) t
WHERE rn = 2;
```

---

### 🛠 Step 1: Understand the inner query

```sql
SELECT salary,
       ROW_NUMBER() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rn
FROM employee
WHERE dept_id = 10
```

* `FROM employee WHERE dept_id = 10` → sirf department 10 ke employees consider ho rahe hain.
* `ROW_NUMBER() OVER (PARTITION BY dept_id ORDER BY salary DESC)` ka matlab:

  * **Partition by dept\_id** → department-wise alag groups banao (yahaan ek hi group hai).
  * **Order by salary DESC** → salary highest to lowest arrange karo.
  * **ROW\_NUMBER()** → har row ko sequential number do (1 se shuru).

💡 **Note:** `t` mtlb subquery ka alias name hai.
`OVER` ka matlab: Window function ka calculation kis **window/frame** ke andar hoga.

---

### 🛠 Step 2: Example Result

Suppose `employee` table me dept 10 ke salaries:

| salary |
| ------ |
| 90000  |
| 85000  |
| 85000  |
| 70000  |

Inner query ka output:

| salary | rn |
| ------ | -- |
| 90000  | 1  |
| 85000  | 2  |
| 85000  | 3  |
| 70000  | 4  |

⚠️ **Note:** `ROW_NUMBER()` har row ko unique number deta hai, even if salaries same ho.

---

### 🛠 Step 3: Outer query filters `rn = 2`

```sql
SELECT salary
FROM (...)
WHERE rn = 2;
```

* Matlab bas wo row chahiye jiska rank 2 hai.
* Is example me **85000** return hoga (pehla 2nd rank salary).

---

## 📌 Important Note:

* `ROW_NUMBER()` ties ke case me unique sequence deta hai (duplicate salaries ke liye alag rank ban jata hai).
* Ties handle karne ke liye:

  * **`RANK()`** → same salary = same rank, lekin ranks skip ho sakte hain.
  * **`DENSE_RANK()`** → same salary = same rank, aur ranks skip nahi hote.

Example with `RANK()`:

| salary | rank |
| ------ | ---- |
| 90000  | 1    |
| 85000  | 2    |
| 85000  | 2    |
| 70000  | 4    |

---

## 📌 Summary

* Inner query → rank assign karti hai salary ko (`rn`) descending order me.
* Outer query → bas wo salary select karti hai jiska rank `2` ho → **Second Highest Salary**.

  ---


---

## 👇 Table: `Employee`

| emp_id | emp_name | salary |
| ------ | -------- | ------ |
| 1      | Amit     | 90000  |
| 2      | Sahil    | 70000  |
| 3      | Ravi     | 70000  |
| 4      | Mohit    | 50000  |
| 5      | Ankit    | 40000  |

---

## 🔹 Query with `ROW_NUMBER()`

```sql
SELECT salary 
FROM (
   SELECT salary,
          ROW_NUMBER() OVER (ORDER BY salary DESC) AS rn
   FROM Employee
) t
WHERE rn = 2;
```

### 🧾 Step-by-step internal ranking:

| salary | rn |
| ------ | -- |
| 90000  | 1  |
| 70000  | 2  |
| 70000  | 3  |
| 50000  | 4  |
| 40000  | 5  |

### ✅ Final Output:

```
salary
-------
70000
```

👉 Sirf ek record (first 70000) aaya kyunki `ROW_NUMBER()` **unique rank assign karta hai**,
even if values same ho.
Dusre 70000 wale ko rn = 3 mil gaya tha.

---

## 🔹 Query with `DENSE_RANK()`

```sql
SELECT salary 
FROM (
   SELECT salary,
          DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
   FROM Employee
) t
WHERE rnk = 2;
```

### 🧾 Step-by-step internal ranking:

| salary | rnk |
| ------ | --- |
| 90000  | 1   |
| 70000  | 2   |
| 70000  | 2   |
| 50000  | 3   |
| 40000  | 4   |

### ✅ Final Output:

```
salary
-------
70000
70000
```

👉 Dono records aaye kyunki `DENSE_RANK()` **same salary ko same rank** deta hai.
Yani ye *distinct salary-based* ranking karta hai.

---

## ⚖️ Final Comparison Summary

| Function         | Internal Rank | Final Output for rank=2 | Description               |
| ---------------- | ------------- | ----------------------- | ------------------------- |
| **ROW_NUMBER()** | 1, 2, 3, 4, 5 | One row (first 70000)   | Ranks every row uniquely  |
| **DENSE_RANK()** | 1, 2, 2, 3, 4 | Two rows (both 70000)   | Groups duplicate salaries |

---

💬 **Interview-ready line:**

> “If interviewer wants *second record*, I’ll use ROW_NUMBER().
> But if he wants *second highest distinct salary*, I’ll use DENSE_RANK().”




