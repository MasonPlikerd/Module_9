SQL Challenge: Employee Database Analysis at Pewlett Hackard
Project Overview
This project involved designing a SQL database for the fictional company Pewlett Hackard to analyze employee data from the 1980s and 1990s. As a new data engineer, the main tasks included data modeling, data engineering, and data analysis. Using six CSV files representing different aspects of employee information, we created a database schema, loaded the data, and ran queries to answer key questions.

Repository Structure
EmployeeSQL/ - Main project directory
data/ - Contains the six CSV files used to populate the database.
schema.sql - SQL script to create the database schema.
queries.sql - SQL script with queries for data analysis.
ERD.png - Entity Relationship Diagram (ERD) illustrating table relationships.
Project Steps
1. Data Modeling
We started by inspecting the six CSV files:

departments.csv - Contains department information.
employees.csv - Contains employee details.
titles.csv - Lists job titles.
salaries.csv - Contains employee salary data.
dept_emp.csv - Links employees to departments.
dept_manager.csv - Lists department managers.
Using this information, we created an Entity Relationship Diagram (ERD) to visualize the relationships between tables and determine the primary and foreign keys necessary for the database structure.

2. Data Engineering
Schema Creation: Based on the ERD, we created tables with appropriate primary keys, foreign keys, and constraints.
Table Relationships: The schema connects tables via primary and foreign keys, such as linking employees to departments through dept_emp and dept_manager tables.
Data Types and Constraints: We specified data types, NOT NULL constraints, and composite keys where needed to ensure data integrity.
The database schema includes the following tables:

departments - Stores department details with dept_no as the primary key.
employees - Stores employee data with emp_no as the primary key and links to titles through emp_title_id.
titles - Stores job titles.
salaries - Stores each employee’s salary, linked by emp_no.
dept_emp - A linking table associating employees with departments, using a composite primary key (emp_no, dept_no).
dept_manager - A linking table for department managers, also with a composite primary key (dept_no, emp_no).
3. Data Analysis
We created SQL queries to answer specific questions about the employee data:

Employee Details: Retrieved employee numbers, names, sex, and salaries.
Hire Date Filtering: Listed employees hired in 1986.
Department Managers: Displayed department managers with their department details.
Employee Departments: Linked each employee to their department.
Pattern Matching: Listed employees named Hercules with a last name beginning with “B.”
Department-Specific Queries: Retrieved employees in the Sales and Development departments.
Frequency Analysis: Displayed the frequency of each last name in descending order.
How to Run the Project
Database Setup:

Load the provided schema from schema.sql into your SQL environment.
Use SQL commands (COPY or LOAD DATA) to import data from the CSV files.
Run Queries:

Execute queries from queries.sql to perform the data analysis tasks outlined.
ERD

This ERD illustrates the relationships and constraints in the database, such as the one-to-many and many-to-many relationships between tables.

Conclusion
This challenge reinforced key skills in data modeling, data engineering, and SQL-based data analysis. The database we created provides a robust structure for analyzing employee data from past decades at Pewlett Hackard.


-- Table: departments
CREATE TABLE departments (
    dept_no VARCHAR(4) PRIMARY KEY,
    dept_name VARCHAR(40) NOT NULL
);

-- Table: employees
CREATE TABLE employees (
    emp_no INT PRIMARY KEY,
    emp_title_id VARCHAR(5),
    birth_date DATE NOT NULL,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    sex CHAR(1) NOT NULL,
    hire_date DATE NOT NULL,
    FOREIGN KEY (emp_title_id) REFERENCES titles(title_id)
);

-- Table: titles
CREATE TABLE titles (
    title_id VARCHAR(5) PRIMARY KEY,
    title VARCHAR(50) NOT NULL
);

-- Table: dept_emp
CREATE TABLE dept_emp (
    emp_no INT,
    dept_no VARCHAR(4),
    FOREIGN KEY (emp_no) REFERENCES employees(emp_no),
    FOREIGN KEY (dept_no) REFERENCES departments(dept_no),
    PRIMARY KEY (emp_no, dept_no)  -- Composite Key
);

-- Table: dept_manager
CREATE TABLE dept_manager (
    dept_no VARCHAR(4),
    emp_no INT,
    FOREIGN KEY (dept_no) REFERENCES departments(dept_no),
    FOREIGN KEY (emp_no) REFERENCES employees(emp_no),
    PRIMARY KEY (dept_no, emp_no)  -- Composite Key
);

-- Table: salaries
CREATE TABLE salaries (
    emp_no INT PRIMARY KEY,
    salary INT NOT NULL,
    FOREIGN KEY (emp_no) REFERENCES employees(emp_no)
);

SELECT e.emp_no, e.last_name, e.first_name, e.sex, s.salary
FROM employees e
JOIN salaries s ON e.emp_no = s.emp_no;

SELECT first_name, last_name, hire_date
FROM employees
WHERE hire_date BETWEEN '1986-01-01' AND '1986-12-31';

SELECT d.dept_no, d.dept_name, dm.emp_no, e.last_name, e.first_name
FROM dept_manager dm
JOIN employees e ON dm.emp_no = e.emp_no
JOIN departments d ON dm.dept_no = d.dept_no;

SELECT d.dept_no, e.emp_no, e.last_name, e.first_name, d.dept_name
FROM dept_emp de
JOIN employees e ON de.emp_no = e.emp_no
JOIN departments d ON de.dept_no = d.dept_no;

SELECT first_name, last_name, sex
FROM employees
WHERE first_name = 'Hercules' AND last_name LIKE 'B%';

SELECT e.emp_no, e.last_name, e.first_name
FROM dept_emp de
JOIN employees e ON de.emp_no = e.emp_no
JOIN departments d ON de.dept_no = d.dept_no
WHERE d.dept_name = 'Sales';

SELECT e.emp_no, e.last_name, e.first_name, d.dept_name
FROM dept_emp de
JOIN employees e ON de.emp_no = e.emp_no
JOIN departments d ON de.dept_no = d.dept_no
WHERE d.dept_name IN ('Sales', 'Development');

SELECT last_name, COUNT(last_name) AS frequency
FROM employees
GROUP BY last_name
ORDER BY frequency DESC;
