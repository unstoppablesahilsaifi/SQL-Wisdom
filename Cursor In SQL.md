Cursor ek pointer hota hai jo table ke result set ko row-by-row process karne deta hai.

Types of Cursors

Implicit Cursor

SQL automatically banata hai (ex: SELECT INTO, INSERT, UPDATE)

Explicit Cursor

Tum manually define karte ho

Mostly interviews yahi puchte hain


Cursor Life Cycle (Interview Favourite)

Ye 4 steps interviewers expect karte hain:

DECLARE – define cursor & SQL query

OPEN – execute query & lock result set

FETCH – row-by-row data nikaalna

CLOSE – cursor ko close karna

DEALLOCATE (optional) – memory free karna





Maan lo hamare paas ek table hai: Employee

EmpID	Name	Salary
1	Sahil	50000
2	Tushar	60000
3	Deepak	55000

Ab hum cursor se rows ko ek-ek karke process karenge.

📌 2. Cursor Code (WITH COMMENTS)

Is code me hum har employee ka naam print karenge — row by row.

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

📌 3. Step-by-Step Working (Row-by-Row)
🔹 Cursor OPEN hua → query result set lock hua

Table rows ready ho gaye:

(1, Sahil, 50000)

(2, Tushar, 60000)

(3, Deepak, 55000)

🔹 FETCH #1

Kaun si row mili?

EmpID	Name	Salary
1	Sahil	50000

PRINT output:

Employee Name: Sahil, Salary: 50000

🔹 FETCH #2

Row:

EmpID	Name	Salary
2	Tushar	60000

Output:

Employee Name: Tushar, Salary: 60000

🔹 FETCH #3

Row:

EmpID	Name	Salary
3	Deepak	55000

Output:

Employee Name: Deepak, Salary: 55000

🔹 FETCH #4 → No more rows

Ab @@FETCH_STATUS = -1, loop band.

Cursor close + memory free.

📌 4. FINAL OUTPUT (Exactly What You’ll See)
Employee Name: Sahil, Salary: 50000
Employee Name: Tushar, Salary: 60000
Employee Name: Deepak, Salary: 55000




== select use kyu ni kiya

1. SELECT * sirf data dikhata hai

SELECT * FROM Employee;
→ Bas table ka data de deta hai ek saath.

BUT
Cursor ka kaam data dikhana nahi,
cursor ka kaam har row ke upar custom logic apply karna hota hai.



TABLE (Before Update)
EmpID	Name	Salary
1	Sahil	50000
2	Tushar	65000
3	Deepak	40000
4	Aman	80000

Rule:

Salary > 60000 → increment 5%

Salary <= 60000 → increment 10%

SELECT se ye per-row logic possible nahi.

Cursor karega.

📌 CURSOR CODE FOR CONDITIONAL UPDATE
DECLARE @EmpID INT, @Salary INT;

DECLARE salary_cursor CURSOR FOR
SELECT EmpID, Salary FROM Employee;

OPEN salary_cursor;

FETCH NEXT FROM salary_cursor INTO @EmpID, @Salary;

WHILE @@FETCH_STATUS = 0
BEGIN
    IF @Salary > 60000
    BEGIN
        -- 5% increase
        UPDATE Employee
        SET Salary = Salary * 1.05
        WHERE EmpID = @EmpID;
    END
    ELSE
    BEGIN
        -- 10% increase
        UPDATE Employee
        SET Salary = Salary * 1.10
        WHERE EmpID = @EmpID;
    END

    FETCH NEXT FROM salary_cursor INTO @EmpID, @Salary;
END

CLOSE salary_cursor;
DEALLOCATE salary_cursor;

📌 STEP-BY-STEP WORKING
🔹 Row 1

Sahil → 50000
50000 <= 60000 → 10% increase

New salary = 50000 * 1.10 = 55000

🔹 Row 2

Tushar → 65000
65000 > 60000 → 5% increase

New salary = 65000 * 1.05 = 68250

🔹 Row 3

Deepak → 40000
40000 <= 60000 → 10% increase

New salary = 40000 * 1.10 = 44000

🔹 Row 4

Aman → 80000
80000 > 60000 → 5% increase

New salary = 80000 * 1.05 = 84000

📌 FINAL TABLE (After Cursor Processing)
EmpID	Name	Salary
1	Sahil	55000
2	Tushar	68250
3	Deepak	44000
4	Aman	84000
📌 Yaha SELECT fail kyu hota?

SELECT kabhi bhi ye nahi kar sakta:

Row-by-row check

IF/ELSE per row

Alag-alag calculation

Alag-alag update

SELECT bas data de sakta hai.
Processing nahi.

Cursor hi ye sab karta hai.


