## 📘 **Introduction**

```
Cursor ek pointer hota hai jo table ke result set ko row-by-row process karne deta hai.

Types of Cursors

Implicit Cursor:
SQL automatically banata hai (ex: SELECT INTO, INSERT, UPDATE)

Explicit Cursor:

Tum manually define karte ho

```

---

## 🌀 **Cursor Life Cycle (Interview Favourite)**

```
Ye 4 steps interviewers expect karte hain:

DECLARE – define cursor & SQL query
OPEN – execute query & lock result set
FETCH – row-by-row data nikaalna
CLOSE – cursor ko close karna
DEALLOCATE (optional) – memory free karna
```

---

## 🧱 **Sample Table**

| EmpID | Name   | Salary |
| ----- | ------ | ------ |
| 1     | Sahil  | 50000  |
| 2     | Tushar | 60000  |
| 3     | Deepak | 55000  |

```
Ab hum cursor se rows ko ek-ek karke process karenge.
```

---

## 🧩 **Cursor Code (WITH COMMENTS)**

```sql
-- Step 1: Variables banaye to store fetched row
DECLARE @EmpID INT, @Name VARCHAR(50), @Salary INT;

-- Step 2: Cursor DECLARE
DECLARE emp_cursor CURSOR FOR
SELECT EmpID, Name, Salary FROM Employee;

-- Step 3: Cursor OPEN
OPEN emp_cursor;

-- Step 4: Pehli row FETCH
FETCH NEXT FROM emp_cursor INTO @EmpID, @Name, @Salary;

-- Step 5: Jab tak rows mil rahi hain (@@FETCH_STATUS = 0)
WHILE @@FETCH_STATUS = 0
BEGIN
    PRINT 'Employee Name: ' + @Name 
          + ', Salary: ' + CAST(@Salary AS VARCHAR);

    -- NEXT row fetch
    FETCH NEXT FROM emp_cursor INTO @EmpID, @Name, @Salary;
END

-- Step 6: Cursor CLOSE
CLOSE emp_cursor;

-- Step 7: Memory free
DEALLOCATE emp_cursor;
```

---

## 🪜 **Step-by-Step Working**

```
🔹 Cursor OPEN hua → query result set lock hua

(1, Sahil, 50000)
(2, Tushar, 60000)
(3, Deepak, 55000)

🔹 FETCH #1 → Sahil
🔹 FETCH #2 → Tushar
🔹 FETCH #3 → Deepak
🔹 FETCH #4 → No more rows
```

---

## 🟢 **Final Output**

```
Employee Name: Sahil, Salary: 50000
Employee Name: Tushar, Salary: 60000
Employee Name: Deepak, Salary: 55000
```

---

## ❓ **SELECT use kyu nahi kiya?**

```
1. SELECT * sirf data dikhata hai
BUT cursor per-row custom logic apply karta hai
```

### 📄 **Table (Before Update)**

| EmpID | Name   | Salary |
| ----- | ------ | ------ |
| 1     | Sahil  | 50000  |
| 2     | Tushar | 65000  |
| 3     | Deepak | 40000  |
| 4     | Aman   | 80000  |

```
Rule:
Salary > 60000 → increment 5%
Salary <= 60000 → increment 10%
```

---

## 🛠 **Cursor Code for Conditional Update**

```sql
DECLARE @EmpID INT, @Salary INT;

DECLARE salary_cursor CURSOR FOR
SELECT EmpID, Salary FROM Employee;

OPEN salary_cursor;

FETCH NEXT FROM salary_cursor INTO @EmpID, @Salary;

WHILE @@FETCH_STATUS = 0
BEGIN
    IF @Salary > 60000
    BEGIN
        UPDATE Employee
        SET Salary = Salary * 1.05
        WHERE EmpID = @EmpID;
    END
    ELSE
    BEGIN
        UPDATE Employee
        SET Salary = Salary * 1.10
        WHERE EmpID = @EmpID;
    END

    FETCH NEXT FROM salary_cursor INTO @EmpID, @Salary;
END

CLOSE salary_cursor;
DEALLOCATE salary_cursor;
```

---

## 🔍 **Step-by-Step Working (Update)**

```
🔹 Row 1 → Sahil → 10% → 55000
🔹 Row 2 → Tushar → 5% → 68250
🔹 Row 3 → Deepak → 10% → 44000
🔹 Row 4 → Aman → 5% → 84000
```

---

## 📊 **Final Table (After Cursor Processing)**

| EmpID | Name   | Salary |
| ----- | ------ | ------ |
| 1     | Sahil  | 55000  |
| 2     | Tushar | 68250  |
| 3     | Deepak | 44000  |
| 4     | Aman   | 84000  |

---

## ❌ **Yaha SELECT fail kyu hota?**

```
SELECT kabhi bhi ye nahi kar sakta:
Row-by-row check
IF/ELSE per row
Alag-alag calculation
Alag-alag update
Cursor hi ye sab karta hai.
```

