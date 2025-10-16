
# 🌟 ROW_NUMBER vs RANK vs DENSE_RANK — Complete SQL Guide


---

## 🎯 Sample Table: `Employee`

| EmpID | EmpName | Department | Salary |
| ----- | ------- | ---------- | ------ |
| 101   | Aakash  | IT         | 70000  |
| 102   | Bhavesh | IT         | 90000  |
| 103   | Charan  | IT         | 90000  |
| 104   | Deepak  | HR         | 60000  |
| 105   | Esha    | HR         | 60000  |
| 106   | Farhan  | HR         | 50000  |

---

## 🧠 Requirement

Har department ke andar employees ko **salary ke descending order** me rank do.

---

### 🔹 1️⃣ ROW_NUMBER()

**Query:**

```sql
SELECT EmpName, Department, Salary,
ROW_NUMBER() OVER(PARTITION BY Department ORDER BY Salary DESC) AS RowNum
FROM Employee;
````

**Output:**

| EmpName | Department | Salary | RowNum |
| ------- | ---------- | ------ | ------ |
| Bhavesh | IT         | 90000  | 1      |
| Charan  | IT         | 90000  | 2      |
| Aakash  | IT         | 70000  | 3      |
| Deepak  | HR         | 60000  | 1      |
| Esha    | HR         | 60000  | 2      |
| Farhan  | HR         | 50000  | 3      |

🧩 **Explanation:**

* **ROW_NUMBER()** bas sequential numbering deta hai — duplicates hone par bhi number repeat nahi hota.
* Har department me numbering **1 se restart hoti** hai (`PARTITION BY Department` ke wajah se).
* Duplicate salaries hone par bhi number unique hota hai (90000 ke liye Bhavesh=1, Charan=2).

---

### 🔹 2️⃣ RANK()

**Query:**

```sql
SELECT EmpName, Department, Salary,
RANK() OVER(PARTITION BY Department ORDER BY Salary DESC) AS RankNum
FROM Employee;
```

**Output:**

| EmpName | Department | Salary | RankNum |
| ------- | ---------- | ------ | ------- |
| Bhavesh | IT         | 90000  | 1       |
| Charan  | IT         | 90000  | 1       |
| Aakash  | IT         | 70000  | 3       |
| Deepak  | HR         | 60000  | 1       |
| Esha    | HR         | 60000  | 1       |
| Farhan  | HR         | 50000  | 3       |

🧩 **Explanation:**

* **RANK()** duplicate values ko **same rank** deta hai.
* Lekin next rank **gap chhod ke** deta hai.

  * IT me 90000 ke 2 employees hain → dono rank 1
  * Next salary 70000 ke liye rank **3** (2 nahi, kyunki 2 log rank 1 pe the)

---

### 🔹 3️⃣ DENSE_RANK()

**Query:**

```sql
SELECT EmpName, Department, Salary,
DENSE_RANK() OVER(PARTITION BY Department ORDER BY Salary DESC) AS DenseRankNum
FROM Employee;
```

**Output:**

| EmpName | Department | Salary | DenseRankNum |
| ------- | ---------- | ------ | ------------ |
| Bhavesh | IT         | 90000  | 1            |
| Charan  | IT         | 90000  | 1            |
| Aakash  | IT         | 70000  | 2            |
| Deepak  | HR         | 60000  | 1            |
| Esha    | HR         | 60000  | 1            |
| Farhan  | HR         | 50000  | 2            |

🧩 **Explanation:**

* **DENSE_RANK()** bhi duplicates ko same rank deta hai.
* **Lekin gap nahi chhodta.**

  * IT me 90000 → rank 1
  * Next salary 70000 → rank **2** (not 3)

---

## 🧩 Summary Table

| Concept          | Duplicate Salary Handling | Gap in Rank? | Example (IT dept) |
| ---------------- | ------------------------- | ------------ | ----------------- |
| **ROW_NUMBER()** | Always unique number      | ❌ No         | 1, 2, 3           |
| **RANK()**       | Same rank for ties        | ✅ Yes        | 1, 1, 3           |
| **DENSE_RANK()** | Same rank for ties        | ❌ No         | 1, 1, 2           |

---

## 💡 Real-Life Analogy

Soch lo ek **race** ho rahi hai:

* **ROW_NUMBER()** → har participant ko unki position ke hisab se unique number milta hai, chahe tie ho ya nahi.
* **RANK()** → agar 2 log pehle aate hain, dono ko rank 1 milega, agla rank 3 hoga (kyunki 2 position kharch ho gayi).
* **DENSE_RANK()** → agar 2 log pehle aate hain, dono ko rank 1, next rank 2 hoga (gap nahi).


---

# 💰 Nth Highest Salary — Full Breakdown

Ab tu **"nth highest salary"** concept samjhega ekdum king level pe —
main 3 tarike se queries dikhata hoon (Row_Number, Rank, Dense_Rank),
aur fir bataata hoon **output aur difference** clearly.

---

## 🎯 Sample Table: `Employee`

| EmpID | EmpName | Department | Salary |
| ----- | ------- | ---------- | ------ |
| 101   | Aakash  | IT         | 70000  |
| 102   | Bhavesh | IT         | 90000  |
| 103   | Charan  | IT         | 90000  |
| 104   | Deepak  | HR         | 60000  |
| 105   | Esha    | HR         | 60000  |
| 106   | Farhan  | HR         | 50000  |

---

## 🧠 Goal

Find **2nd highest salary** overall (not by department).

---

### 🔹 **Query 1 – Using `ROW_NUMBER()`**

```sql
SELECT * FROM (
   SELECT EmpName, Salary,
   ROW_NUMBER() OVER (ORDER BY Salary DESC) AS RowNum
   FROM Employee
) AS t
WHERE RowNum = 2;
```

**Step-by-step Output of subquery:**

| EmpName | Salary | RowNum |
| ------- | ------ | ------ |
| Bhavesh | 90000  | 1      |
| Charan  | 90000  | 2      |
| Aakash  | 70000  | 3      |
| Deepak  | 60000  | 4      |
| Esha    | 60000  | 5      |
| Farhan  | 50000  | 6      |

**👉 Final Output (RowNum = 2):**

| EmpName | Salary |
| ------- | ------ |
| Charan  | 90000  |

✅ **Result:** 2nd row in sorted list (90000 again)
❌ **Problem:** same salary repeat hone par bhi different RowNum milta hai —
**so duplicate salary skip nahi hoti**, actual 2nd highest salary nahi mila, sirf 2nd record mila.

---

### 🔹 **Query 2 – Using `RANK()`**

```sql
SELECT * FROM (
   SELECT EmpName, Salary,
   RANK() OVER (ORDER BY Salary DESC) AS RankNum
   FROM Employee
) AS t
WHERE RankNum = 2;
```

**Step-by-step Output of subquery:**

| EmpName | Salary | RankNum |
| ------- | ------ | ------- |
| Bhavesh | 90000  | 1       |
| Charan  | 90000  | 1       |
| Aakash  | 70000  | 3       |
| Deepak  | 60000  | 4       |
| Esha    | 60000  | 4       |
| Farhan  | 50000  | 6       |

**👉 Final Output (RankNum = 2):**

| EmpName | Salary |
| ------- | ------ |
| ❌ None  |        |

❌ **Problem:** koi row ka rank 2 hi nahi hai —
kyunki 90000 ke dono ko rank 1 mila,
aur next salary 70000 ko rank 3 mila (2 skip ho gaya).

⚠️ So agar tu `RANK()` use karega to **gap ki wajah se kuch rank missing** ho sakte hain.

---

### 🔹 **Query 3 – Using `DENSE_RANK()`**

```sql
SELECT * FROM (
   SELECT EmpName, Salary,
   DENSE_RANK() OVER (ORDER BY Salary DESC) AS DenseRankNum
   FROM Employee
) AS t
WHERE DenseRankNum = 2;
```

**Step-by-step Output of subquery:**

| EmpName | Salary | DenseRankNum |
| ------- | ------ | ------------ |
| Bhavesh | 90000  | 1            |
| Charan  | 90000  | 1            |
| Aakash  | 70000  | 2            |
| Deepak  | 60000  | 3            |
| Esha    | 60000  | 3            |
| Farhan  | 50000  | 4            |

**👉 Final Output (DenseRankNum = 2):**

| EmpName | Salary |
| ------- | ------ |
| Aakash  | 70000  |

✅ **Result:** actual **2nd highest salary = 70000**
(duplicates ignore kiye gaye, gap bhi nahi chhoda)

---

## ⚖️ **Comparison Summary**

| Function         | Duplicates      | Rank Gaps         | Best For                     | Output for 2nd Highest     |
| ---------------- | --------------- | ----------------- | ---------------------------- | -------------------------- |
| **ROW_NUMBER()** | No tie-handling | No                | Just nth record              | 90000 (wrong logically)    |
| **RANK()**       | Handles ties    | Yes (skips ranks) | Ranking display              | No output (rank 2 missing) |
| **DENSE_RANK()** | Handles ties    | No                | ✅ Finding nth highest salary | 70000 (correct)            |

---

## 💡 Final Tip

> 💬 Interview line:
> “For **nth highest salary**, always prefer `DENSE_RANK()` — because it ignores duplicate salaries and doesn’t skip ranks.”
---
