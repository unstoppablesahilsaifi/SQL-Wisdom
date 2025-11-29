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

---

# 🟦 **EXAMPLE 1: Cursor se Negative Balance Accounts ko Suspend karna**

## 📌 **Table: Account**

| AccID | Name   | Balance |
| ----- | ------ | ------- |
| 101   | Sahil  | 2000    |
| 102   | Tushar | -500    |
| 103   | Deepak | 8000    |
| 104   | Aman   | -1200   |

### 🎯 **Requirement**

```
Balance < 0 ho → status = 'SUSPENDED'
Balance >= 0 ho → status = 'ACTIVE'

SELECT ye row-by-row IF/ELSE nahi kar sakta → cursor kar sakta hai.
```

---

## 📌 **Cursor Code**

```sql
DECLARE @AccID INT, @Balance INT;

DECLARE acc_cursor CURSOR FOR
SELECT AccID, Balance FROM Account;

OPEN acc_cursor;

FETCH NEXT FROM acc_cursor INTO @AccID, @Balance;

WHILE @@FETCH_STATUS = 0
BEGIN
    IF @Balance < 0
        UPDATE Account SET Status = 'SUSPENDED' WHERE AccID = @AccID;
    ELSE
        UPDATE Account SET Status = 'ACTIVE' WHERE AccID = @AccID;

    FETCH NEXT FROM acc_cursor INTO @AccID, @Balance;
END

CLOSE acc_cursor;
DEALLOCATE acc_cursor;
```

---

## 🔍 **Step-by-Step Processing**

```
Row 1 → (2000)
Balance >= 0 → ACTIVE

Row 2 → (-500)
Negative → SUSPENDED

Row 3 → (8000)
Positive → ACTIVE

Row 4 → (-1200)
Negative → SUSPENDED
```

---

## 📊 **Final Output Table**

| AccID | Name   | Balance | Status    |
| ----- | ------ | ------- | --------- |
| 101   | Sahil  | 2000    | ACTIVE    |
| 102   | Tushar | -500    | SUSPENDED |
| 103   | Deepak | 8000    | ACTIVE    |
| 104   | Aman   | -1200   | SUSPENDED |

---

# 🟩 **EXAMPLE 2: Cursor se Product Price Auto-Update**

## 📌 **Table: Product**

| ProductID | ProductName | Price |
| --------- | ----------- | ----- |
| 1         | Laptop      | 60000 |
| 2         | Mouse       | 500   |
| 3         | Keyboard    | 1500  |
| 4         | Chair       | 7000  |

### 🎯 **Requirement**

```
Price > 5000 → Increase by 8%
Price <= 5000 → Increase by 3%
```

---

## 📌 **Cursor Code**

```sql
DECLARE @PID INT, @Price INT;

DECLARE price_cursor CURSOR FOR
SELECT ProductID, Price FROM Product;

OPEN price_cursor;

FETCH NEXT FROM price_cursor INTO @PID, @Price;

WHILE @@FETCH_STATUS = 0
BEGIN
    IF @Price > 5000
        UPDATE Product SET Price = Price * 1.08 WHERE ProductID = @PID;
    ELSE
        UPDATE Product SET Price = Price * 1.03 WHERE ProductID = @PID;

    FETCH NEXT FROM price_cursor INTO @PID, @Price;
END

CLOSE price_cursor;
DEALLOCATE price_cursor;
```

---

## 🔍 **Step-by-Step Processing**

```
Row 1 → Laptop (60000)
60000 > 5000 → 8% increase
New = 60000 × 1.08 = 64800

Row 2 → Mouse (500)
<= 5000 → 3% increase
New = 500 × 1.03 = 515

Row 3 → Keyboard (1500)
<= 5000 → 3% increase
New = 1500 × 1.03 = 1545

Row 4 → Chair (7000)
5000 → 8% increase
New = 7000 × 1.08 = 7560
```

---

## 📊 **Final Output Table**

| ProductID | ProductName | Price (Updated) |
| --------- | ----------- | --------------- |
| 1         | Laptop      | 64800           |
| 2         | Mouse       | 515             |
| 3         | Keyboard    | 1545            |
| 4         | Chair       | 7560            |

---



