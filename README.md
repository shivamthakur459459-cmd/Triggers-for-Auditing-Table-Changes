# Triggers-for-Auditing-Table-Changes
# 📘 Project: Triggers for Auditing Table Changes (MySQL)

## 🎯 Objective  
Track all **INSERT, UPDATE, and DELETE** operations on a table using **triggers** and save the change history into an **audit log table**.  

---

## 🛠 Tools Used  
- **MySQL** (MySQL Workbench / phpMyAdmin / CLI)  

---

## 📂 Database Script (All-in-One)

```sql
-- Step 1: Create Main Table
CREATE TABLE employees (
    emp_id INT AUTO_INCREMENT PRIMARY KEY,
    emp_name VARCHAR(100),
    emp_salary DECIMAL(10,2),
    department VARCHAR(50)
);

-- Step 2: Create Audit Table
CREATE TABLE employees_audit (
    audit_id INT AUTO_INCREMENT PRIMARY KEY,
    emp_id INT,
    operation_type VARCHAR(10),
    old_name VARCHAR(100),
    new_name VARCHAR(100),
    old_salary DECIMAL(10,2),
    new_salary DECIMAL(10,2),
    old_department VARCHAR(50),
    new_department VARCHAR(50),
    changed_on TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Step 3: Trigger for INSERT
DELIMITER $$
CREATE TRIGGER trg_employees_insert
AFTER INSERT ON employees
FOR EACH ROW
BEGIN
    INSERT INTO employees_audit(emp_id, operation_type, new_name, new_salary, new_department)
    VALUES (NEW.emp_id, 'INSERT', NEW.emp_name, NEW.emp_salary, NEW.department);
END$$
DELIMITER ;

-- Step 4: Trigger for UPDATE
DELIMITER $$
CREATE TRIGGER trg_employees_update
AFTER UPDATE ON employees
FOR EACH ROW
BEGIN
    INSERT INTO employees_audit(emp_id, operation_type, old_name, new_name,
                                old_salary, new_salary, old_department, new_department)
    VALUES (OLD.emp_id, 'UPDATE', OLD.emp_name, NEW.emp_name,
            OLD.emp_salary, NEW.emp_salary, OLD.department, NEW.department);
END$$
DELIMITER ;

-- Step 5: Trigger for DELETE
DELIMITER $$
CREATE TRIGGER trg_employees_delete
AFTER DELETE ON employees
FOR EACH ROW
BEGIN
    INSERT INTO employees_audit(emp_id, operation_type, old_name, old_salary, old_department)
    VALUES (OLD.emp_id, 'DELETE', OLD.emp_name, OLD.emp_salary, OLD.department);
END$$
DELIMITER ;

-- Step 6: Sample Run
INSERT INTO employees(emp_name, emp_salary, department)
VALUES ('Alice', 50000, 'HR');

UPDATE employees SET emp_salary = 55000 WHERE emp_name = 'Alice';

DELETE FROM employees WHERE emp_name = 'Alice';

-- Step 7: View Audit Log
SELECT * FROM employees_audit;
