# my-sql
vegetables market
create database vegan;
use vegan;

CREATE TABLE vegetable (
    id INT AUTO_INCREMENT PRIMARY KEY,
    product_name VARCHAR(50) UNIQUE,
    price DECIMAL(10,2),
    profit DECIMAL(10,2),
    stock INT DEFAULT 20
);

-- Example stock setup
INSERT INTO vegetable (product_name, price, profit, stock) VALUES
('Tomato', 20.00, 2.00, 20),
('Carrot', 20.00, 2.00, 20),
('Beans', 20.00, 2.00, 20),
('Onion', 20.00, 2.00, 20),
('Cabbage', 20.00, 2.00, 20),
('Cauliflower', 20.00, 2.00, 20),
('Capsicum', 20.00, 2.00, 20),
('Okra', 20.00, 2.00, 20),
('Bitter Gourd', 20.00, 2.00, 20),
('Brinjal', 20.00, 2.00, 20);
drop table vegetable;
select * from vegetable;

CREATE TABLE customer_orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    customer_name VARCHAR(50),
    product_name VARCHAR(50),
    quantity_kg INT DEFAULT NULL,
    price DECIMAL(10,2),
    profit DECIMAL(10,2),
    total_amount DECIMAL(10,2)
);
drop table customer_orders;
DELIMITER $$

CREATE TRIGGER before_customer_order
BEFORE INSERT ON customer_orders
FOR EACH ROW
BEGIN
    DECLARE available_stock INT;
    DECLARE unit_price DECIMAL(10,2);
    DECLARE unit_profit DECIMAL(10,2);

    -- Get current stock, price, profit
    SELECT stock, price, profit
    INTO available_stock, unit_price, unit_profit
    FROM vegetable
    WHERE product_name = NEW.product_name;

    -- If enough stock, reduce and calculate totals
    IF available_stock >= NEW.quantity_kg THEN
        UPDATE vegetable
        SET stock = stock - NEW.quantity_kg
        WHERE product_name = NEW.product_name;

        SET NEW.price = unit_price;
        SET NEW.profit = unit_profit;
        SET NEW.total_amount = NEW.quantity_kg * unit_price;
    ELSE
    SET NEW.quantity_kg = NULL;
    SET NEW.total_amount = NULL;
    SET NEW.price = 0.00;
    SET NEW.profit = 0.00;
END IF;
END$$

DELIMITER ;
DROP TRIGGER IF EXISTS before_customer_order;
INSERT INTO customer_orders (customer_name, product_name, quantity_kg)
VALUES
('Vijay', 'Tomato', 10),
('Vijay', 'Carrot', 10),
('Vijay', 'Beans', 10),
('Ashok', 'Carrot', 10),
('Vinoth', 'Carrot', 2),
('Ravi', 'Onion', 5),
('Kumar', 'Cabbage', 3),
('Suresh', 'Cauliflower', 4),
('Anita', 'Beans', 10),
('Priya', 'Peas', 6),
('Rahul', 'Radish', 8),
('Meena', 'Beetroot', 7),
('Arun', 'Capsicum', 5),
('Deepa', 'Brinjal', 9),
('Karthik', 'Okra', 4),
('Lakshmi', 'Brinjal', 11),
('Manoj', 'Pumpkin', 6),
('Geetha', 'Beans', 10),
('Naveen', 'Bitter Gourd', 2),
('Shalini', 'Brinjal', 10);
select * from customer_orders;

select sum(profit) as total_profit
from customer_orders;

select sum(total_amount) as total_amount1
from customer_orders
where customer_name="vijay";

select  customer_name , count(customer_name) as no_of_product ,sum(total_amount)from customer_orders
group by customer_name;


CREATE TABLE employee (
    emp_id INT AUTO_INCREMENT PRIMARY KEY,
    emp_name VARCHAR(50) NOT NULL,
    salary DECIMAL(10,2) NOT NULL,
    manager_id INT,
    CONSTRAINT fk_manager FOREIGN KEY (manager_id) REFERENCES employee(emp_id)
);

INSERT INTO employee (emp_name, salary, manager_id) VALUES
('Ravi', 50000, NULL),   -- Ravi is a manager (no manager above him)
('Vijay', 30000, 1),     -- Vijay reports to Ravi
('Anita', 35000, 1),     -- Anita reports to Ravi
('Kumar', 25000, 2),     -- Kumar reports to Vijay
('Suresh', 28000, 2);    -- Suresh reports to Vijay

SELECT e.emp_id, e.emp_name AS Employee, e.salary,e.manager_id, m.emp_name AS Manager
FROM employee e
LEFT JOIN employee m ON e.manager_id = m.emp_id;

select sum(salary) as overall_salary
from employee;
