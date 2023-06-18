# SQL Case Study 2: Human Resources

View original link [here](https://d-i-motion.com/lessons/kedeishas-banking-services/) by Kedeisha Bryan.

![image](https://github.com/veekool/Human_Resources-SQL/assets/114795923/10121a84-b37b-4e24-808b-c55c76151be6)

<details><summary>Creating databases and Tables:</summary>

```sql
-- Create 'departments' table
CREATE TABLE departments (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50),
    manager_id INT
);

-- Create 'employees' table
CREATE TABLE employees (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50),
    hire_date DATE,
    job_title VARCHAR(50),
    department_id INT REFERENCES departments(id)
);

-- Create 'projects' table
CREATE TABLE projects (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50),
    start_date DATE,
    end_date DATE,
    department_id INT REFERENCES departments(id)
);

-- Insert data into 'departments'
INSERT INTO departments (name, manager_id)
VALUES ('HR', 1), ('IT', 2), ('Sales', 3);

-- Insert data into 'employees'
INSERT INTO employees (name, hire_date, job_title, department_id)
VALUES ('John Doe', '2018-06-20', 'HR Manager', 1),
       ('Jane Smith', '2019-07-15', 'IT Manager', 2),
       ('Alice Johnson', '2020-01-10', 'Sales Manager', 3),
       ('Bob Miller', '2021-04-30', 'HR Associate', 1),
       ('Charlie Brown', '2022-10-01', 'IT Associate', 2),
       ('Dave Davis', '2023-03-15', 'Sales Associate', 3);

-- Insert data into 'projects'
INSERT INTO projects (name, start_date, end_date, department_id)
VALUES ('HR Project 1', '2023-01-01', '2023-06-30', 1),
       ('IT Project 1', '2023-02-01', '2023-07-31', 2),
       ('Sales Project 1', '2023-03-01', '2023-08-31', 3);
       
       UPDATE departments
SET manager_id = (SELECT id FROM employees WHERE name = 'John Doe')
WHERE name = 'HR';

UPDATE departments
SET manager_id = (SELECT id FROM employees WHERE name = 'Jane Smith')
WHERE name = 'IT';

UPDATE departments
SET manager_id = (SELECT id FROM employees WHERE name = 'Alice Johnson')
WHERE name = 'Sales';

```
</details>

SQL interactive playground >> [View on DB Fiddle](https://www.db-fiddle.com/f/xckGL9ZW73A6FWhsmPogm7/6)

---

### Question 1: Find the longest ongoing project for each department.
<details><summary>Solution:</summary>
  
  ````sql
  SELECT d.name AS department_name, p.name AS project_name, p.start_date, p.end_date, end_date - start_date AS duration
  FROM projects p
  INNER JOIN departments d ON p.department_id = d.id
  ORDER BY duration DESC
  ````

</details>

<details><summary>Output:</summary>

  ![image](https://github.com/veekool/Human_Resources-SQL/assets/114795923/cc120f61-231b-4829-ba98-253cbf6ef0a7)

</details>

### Question 2: Find all employees who are not managers.

<details><summary>Solution:</summary>
  
  ````sql
  SELECT * 
  FROM employees
  WHERE job_title NOT LIKE '%Manager%';
  ````

</details>

<details><summary>Output:</summary>

  ![image](https://github.com/veekool/Human_Resources-SQL/assets/114795923/559d099b-d4ca-4625-8e56-66670f6477a7)

</details>

### Question 3: Find all employees who have been hired after the start of a project in their department.

<details><summary>Solution:</summary>
  
  ````sql
  SELECT e.name AS employee_name, e.hire_date, p.start_date as project_start_date, d.name as department_name 
  FROM employees e
  INNER JOIN projects p ON e.department_id = p.department_id
  INNER JOIN departments d ON p.department_id = d.id
  WHERE e.hire_date > p.start_date
  ````

</details>

<details><summary>Output:</summary>

  ![image](https://github.com/veekool/Human_Resources-SQL/assets/114795923/678e1399-c553-4bb6-bd1e-a827a326006f)

</details>

### Question 4: Rank employees within each department based on their hire date (earliest hire gets the highest rank).

<details><summary>Solution:</summary>
  
  ````sql
  SELECT e.name, hire_date, d.name AS department_name, DENSE_RANK() OVER (PARTITION BY(department_id) ORDER BY hire_date ASC) AS rank_
  FROM employees e
  INNER JOIN departments d ON e.department_id = d.id
  ````

</details>

<details><summary>Output:</summary>

  ![image](https://github.com/veekool/Human_Resources-SQL/assets/114795923/18359816-bd58-40f1-8771-6cb88fa48f53)

</details>

### Question 5: Find the duration between the hire date of each employee and the hire date of the next employee hired in the same department.

<details><summary>Solution:</summary>
  
  ````sql
  SELECT e.name, hire_date, d.name AS department_name,
  	LEAD(e.hire_date) OVER (PARTITION BY e.department_id ORDER BY e.hire_date) - e.hire_date AS duration_days
  FROM employees e
  JOIN departments d ON e.department_id = d.id
  ORDER BY e.department_id, e.hire_date
  ````

</details>

<details><summary>Output:</summary>

  ![image](https://github.com/veekool/Human_Resources-SQL/assets/114795923/3c3e9e7c-7ee9-4fb2-9b69-93615579750b)

</details>






