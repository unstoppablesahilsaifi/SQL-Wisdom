
# 📚 SQL Practice Guide

---

## **Sample Table – employees**

| emp_id | name    | dept_id | salary | joining_date |
| ------ | ------- | ------- | ------ | ------------ |
| 1      | Alice   | 101     | 60000  | 2020-01-15   |
| 2      | Bob     | 101     | 55000  | 2019-03-12   |
| 3      | Charlie | 102     | 40000  | 2021-07-23   |
| 4      | David   | 103     | 70000  | 2020-11-30   |
| 5      | Eve     | 102     | 45000  | 2022-05-01   |
| 6      | Frank   | 101     | 65000  | 2018-09-09   |

---

## **Your First 5 Practice Problems**

**Q1.** Select name and salary of employees who earn more than 50,000.
```sql
select name, salary 
from employee 
where salary > 50000;
````

**Q2.** Show all employees who joined after `2020-01-01`, ordered by joining\_date (newest first).

```sql
select * 
from employee 
where joining_date > '2020-01-01' 
order by joining_date desc;
```

**Q3.** Display total salary paid per department.

```sql
select dept_id, sum(salary) as total_salary 
from employee 
group by dept_id;
```

**Q4.** Display department IDs where total salary paid is more than 1,00,000.

```sql
select dept_id, sum(salary) as total 
from employee 
group by dept_id 
having sum(salary) > 100000;
```

**Q5.** Find top 3 highest paid employees.

```sql
select * 
from employee 
order by salary desc 
limit 3;
```

---

## **Practice Set 2 – SELECT / WHERE / GROUP BY / HAVING**

**Table: `sales`**

| sale\_id | product    | category    | amount | sale\_date |
| -------- | ---------- | ----------- | ------ | ---------- |
| 1        | Laptop     | Electronics | 80000  | 2024-01-15 |
| 2        | Mouse      | Electronics | 1200   | 2024-01-18 |
| 3        | Chair      | Furniture   | 3500   | 2024-02-10 |
| 4        | Desk       | Furniture   | 7000   | 2024-02-15 |
| 5        | Phone      | Electronics | 50000  | 2024-03-05 |
| 6        | Sofa       | Furniture   | 25000  | 2024-03-08 |
| 7        | Headphones | Electronics | 2500   | 2024-04-01 |

---

### **Your Questions**

1. Show product name and amount for all sales where amount is **greater than 10,000**.

```sql
select product, amount 
from sales 
where amount > 10000;
```

2. Display sales that happened in **March 2024** only.

```sql
select * 
from sales 
where sale_date between '2024-03-01' and '2024-03-31';
```

3. Find **total sales amount per category**.

```sql
select category, sum(amount) as total_sales 
from sales 
group by category;
```

4. Show categories where total sales amount is **more than 50,000**.
   *Note: In MySQL you can’t use the alias in HAVING*

```sql
select category, sum(amount) as total 
from sales 
group by category 
having sum(amount) > 50000;
```

5. Find the **top 2 highest sales** (amount wise).

```sql
select * 
from sales 
order by amount desc 
limit 2;
```

---

## **Phase 2: Joins & Relationships**

### **employees**

| emp\_id | name    | dept\_id |
| ------- | ------- | -------- |
| 1       | Alice   | 101      |
| 2       | Bob     | 101      |
| 3       | Charlie | 102      |
| 4       | David   | 103      |
| 5       | Eve     | NULL     |

### **departments**

| dept\_id | dept\_name |
| -------- | ---------- |
| 101      | HR         |
| 102      | IT         |
| 103      | Finance    |
| 104      | Marketing  |

---

## **Your Practice Problems**

* **Q1.** Show employee names with their department names (only if department exists).
* **Q2.** Show all employees and their department names, even if they don’t belong to any department.
* **Q3.** Show all departments and employees in them, even if no employee is assigned.
* **Q4.** Find employees who **do not** have any department assigned.
* **Q5.** Find departments that have **no employees**.

---

## **Correct Answers for Your Last JOIN Practice**

### Q1 – Employee names with their department names (only if department exists)

```sql
select e.name, d.dept_name
from employees as e
inner join departments as d
on e.dept_id = d.dept_id;
```

### Q2 – All employees and their department names, even if they don’t belong to any department

```sql
select e.name, d.dept_name
from employees as e
left join departments as d
on e.dept_id = d.dept_id;
```

### Q3 – All departments and employees in them, even if no employee is assigned

```sql
select d.dept_name, e.name
from departments as d
left join employees as e
on d.dept_id = e.dept_id;
```

### Q4 – Employees who do not have any department assigned

```sql
select e.name
from employees as e
left join departments as d
on e.dept_id = d.dept_id
where d.dept_name is null;
```

### Q5 – Departments that have no employees

```sql
select d.dept_name
from departments as d
left join employees as e
on d.dept_id = e.dept_id
where e.name is null;
```

---

## **Multi JOIN Example**

```sql
select e.name, d.dept_name, l.city
from employees e
inner join departments d on e.dept_id = d.dept_id
inner join locations l on d.location_id = l.location_id;
```

---

## **Phase 3: Subqueries**

**Sample Table: employees**

| emp\_id | name    | dept\_id | salary |
| ------- | ------- | -------- | ------ |
| 1       | Alice   | 10       | 90000  |
| 2       | Bob     | 20       | 60000  |
| 3       | Charlie | 10       | 75000  |
| 4       | David   | 30       | 50000  |
| 5       | Eve     | 20       | 85000  |
| 6       | Frank   | 10       | 90000  |
| 7       | Grace   | NULL     | 45000  |

**Sample Table: departments**

| dept\_id | dept\_name |
| -------- | ---------- |
| 10       | IT         |
| 20       | HR         |
| 30       | Finance    |
| 40       | Marketing  |

**Sample Table: employeeprojects**

| emp\_id | project\_id |
| ------- | ----------- |
| 1       | P1          |
| 2       | P2          |
| 3       | P1          |
| 5       | P3          |

---

### **📌 Practice Questions – Subqueries**

#### Q1. Find the employees who earn **more than the highest salary** in the IT department.

```sql
SELECT * 
FROM employees
WHERE salary > (
    SELECT MAX(salary) 
    FROM employees 
    WHERE dept_id = (
        SELECT dept_id 
        FROM departments 
        WHERE dept_name = 'IT'
    )
);

```

OR

```

SELECT e.name
FROM employee e
JOIN department d ON e.dept_id = d.dept_id
WHERE e.salary > (
    SELECT MAX(e2.salary)
    FROM employee e2
    JOIN department d2 ON e2.dept_id = d2.dept_id
    WHERE d2.dept_name = 'IT'
);
```

#### Q2. Find employees whose salary is **equal to the minimum salary** in their department.

```sql
SELECT * 
FROM employees e
WHERE salary = (
    SELECT MIN(salary) 
    FROM employees 
    WHERE dept_id = e.dept_id
);
```

#### Q3. List departments where the **average salary is more than 55,000**.

```sql
SELECT d.dept_name
FROM departments d
JOIN employees e ON d.dept_id = e.dept_id
GROUP BY d.dept_name
HAVING AVG(salary) > 55000;
```

#### Q4. Find employees who work in a department where **at least one employee earns above 70,000**.

```sql
SELECT name, dept_id, salary
FROM employee e1
WHERE dept_id IN (
    SELECT dept_id
    FROM employee
    WHERE salary > 70000
);
```

#### Q5. List employees whose department has **more than 2 employees**.

```sql
SELECT name, dept_id
FROM employee
WHERE dept_id IN (
    SELECT dept_id
    FROM employee
    GROUP BY dept_id
    HAVING COUNT(*) > 2
);
```

#### Q6. Find the **second highest salary** in the company using a subquery.

```sql
select max(salary) 
from employee 
where salary < (select max(salary) from employee);
```

#### Q7. Find all employees who earn **less than the average salary of the Finance department**.

```sql
SELECT *
FROM employee
WHERE salary < (
    SELECT AVG(salary)
    FROM employee
    WHERE dept_id = (
        SELECT dept_id
        FROM department
        WHERE dept_name = 'Finance'
    )
);
```


```
