
sudo mysql -u root -p
CREATE DATABASE dbms_lab;
USE dbms_lab;

Schema:
employee(emp_id : integer, emp_name: string)
department(dept_id: integer, dept_name:string, dept_location:string)
paydetails(emp_id : integer, dept_id: integer, basic: integer, deductions: integer, additions: integer, DOJ: date)
payroll(emp_id : integer, pay_date: date)
a) Create tables:
CREATE TABLE employee (
  emp_id INTEGER PRIMARY KEY,
  emp_name VARCHAR(50) NOT NULL
);

CREATE TABLE department (
  dept_id INTEGER PRIMARY KEY,
  dept_name VARCHAR(50) NOT NULL,
  dept_location VARCHAR(50)
);

CREATE TABLE paydetails (
  emp_id INTEGER,
  dept_id INTEGER,
  basic INTEGER,
  deductions INTEGER,
  additions INTEGER,
  DOJ DATE,
  FOREIGN KEY (emp_id) REFERENCES employee(emp_id),
  FOREIGN KEY (dept_id) REFERENCES department(dept_id)
);

CREATE TABLE payroll (
  emp_id INTEGER,
  pay_date DATE,
  FOREIGN KEY (emp_id) REFERENCES employee(emp_id)
);
b) Insert records:
INSERT INTO employee VALUES (1, 'John'), (2, 'Alice'), ..., (10, 'Steve');
c) Employee details department-wise:
SELECT e.emp_id, e.emp_name, d.dept_name 
FROM employee e 
JOIN paydetails p ON e.emp_id = p.emp_id
JOIN department d ON p.dept_id = d.dept_id
ORDER BY d.dept_name;
d) Employees who joined after a particular date:
SELECT emp_name FROM paydetails 
WHERE DOJ > '2023-01-01';
e) Employees with basic salary between 10,000 and 20,000:
SELECT e.emp_name, p.basic 
FROM employee e 
JOIN paydetails p ON e.emp_id = p.emp_id
WHERE p.basic BETWEEN 10000 AND 20000;
f) Count of employees in each department:
SELECT d.dept_name, COUNT(p.emp_id) AS employee_count 
FROM paydetails p
JOIN department d ON p.dept_id = d.dept_id 
GROUP BY d.dept_name;
g) Employees with net salary > 10,000:
SELECT e.emp_name 
FROM employee e 
JOIN paydetails p ON e.emp_id = p.emp_id
WHERE (p.basic + p.additions - p.deductions) > 10000;
h) Details for employee with emp_id = 5:
SELECT * FROM employee e 
JOIN paydetails p ON e.emp_id = p.emp_id 
WHERE e.emp_id = 5;
i) Create view with employee name and net salary:
CREATE VIEW emp_netsalary AS
SELECT e.emp_name, (p.basic + p.additions - p.deductions) AS net_salary
FROM employee e 
JOIN paydetails p ON e.emp_id = p.emp_id;

** cursor
A cursor can iterate through employees to calculate and display their net salary.
DELIMITER //
CREATE PROCEDURE CalculateNetSalaryCursor()
BEGIN
  DECLARE done BOOLEAN DEFAULT FALSE;
  DECLARE empID INT;
  DECLARE empName VARCHAR(50);
  DECLARE basic, additions, deductions, netSalary INT;

  DECLARE cur_salary CURSOR FOR
  SELECT e.emp_id, e.emp_name, p.basic, p.additions, p.deductions
  FROM employee e
  JOIN paydetails p ON e.emp_id = p.emp_id;
  DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = TRUE;
  OPEN cur_salary;
  read_loop: LOOP
    FETCH cur_salary INTO empID, empName, basic, additions, deductions;
    IF done THEN
      LEAVE read_loop;
    END IF;
    SET netSalary = basic + additions - deductions;
    SELECT CONCAT('Employee: ', empName, ', Net Salary: ', netSalary) AS salary_info;
  END LOOP;
  CLOSE cur_salary;
END;
// DELIMITER ;

**Trigger 
A trigger that logs when a new employee is added to the paydetails table.
DELIMITER //
CREATE TRIGGER before_paydetails_insert
BEFORE INSERT ON paydetails
FOR EACH ROW
BEGIN
  INSERT INTO payroll (emp_id, pay_date)
  VALUES (NEW.emp_id, NOW());
END;
// DELIMITER ;

**function
A function to calculate the net salary of a specific employee.
DELIMITER //
CREATE FUNCTION GetNetSalary(empID INT) RETURNS INT
BEGIN
  DECLARE basic, additions, deductions, netSalary INT;
    SELECT p.basic, p.additions, p.deductions INTO basic, additions, deductions
  FROM paydetails p
  WHERE p.emp_id = empID;
  
  SET netSalary = basic + additions - deductions;
  
  RETURN netSalary;
END;
// DELIMITER ;
Call function 
SELECT GetNetSalary(5) AS net_salary_for_emp5; 

**pl /sql
DECLARE
  CURSOR cur_students IS SELECT * FROM Student;
  student_rec Student%ROWTYPE;
BEGIN
  OPEN cur_students;
  LOOP
    FETCH cur_students INTO student_rec;
    EXIT WHEN cur_students%NOTFOUND;
    DBMS_OUTPUT.PUT_LINE(student_rec.name);
  END LOOP;
  CLOSE cur_students;
END;
**view

CREATE VIEW StudentCourses AS
SELECT s.name, c.course_name, e.grade
FROM Student s
JOIN Enrollment e ON s.student_id = e.student_id
JOIN Course c ON e.course_id = c.course_id;



Creation
CREATE TABLE employee (
);
insert
INSERT INTO employee VALUES 
(1, 'Ram', 'Manager', 'Chennai'),

Add a column 
ALTER TABLE employee ADD COLUMN Salary INT;

Modify
ALTER TABLE employee MODIFY COLUMN Name VARCHAR(100);

Copy table 
CREATE TABLE emp AS SELECT * FROM employee;
TRUNCATE TABLE employee;

DELETE FROM employee WHERE SNo = 2;
Delete Second Row:
DELETE FROM employee WHERE SNo = 2;
Drop Table:
DROP TABLE employee;
Select 

SELECT * FROM bank;
SELECT * FROM bank WHERE Balance > 150000;
SELECT * FROM bank WHERE Balance BETWEEN 100000 AND 200000;
Update 
UPDATE bank SET Cus_Branch = 'Poonamallee' WHERE SNo = 2;
