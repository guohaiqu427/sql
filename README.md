# DATABASE 
- DBMS : database management system 
    - Relational | RDBMS  : store in table and link to each other   
        - MySQL | SQL Server | Oracle ...
    - No Relational : 
        - MongoDB

- Install MySQL: 
    mysql 
    mysql workbench 

# Retrive Data from a single table

1. select statement 
    - the order of the statement matters
    - line has to be eneded with ;
    - keywords: 
                USE
                SELECT 
                FROM
                WHERE
                ORDER BY 
    ```sql
    use sql_store;

    select *
    from customers
    -- where customer_id = 1
    order by first_name
    ```

2. SELECT clause
    1. apply changes to table
    2. select distinct vlaue from a column 
    3. create a new column based on another column
    4. SQL keywords are CAP

    ```sql
    SELECT  last_name,
            first_name, 
            points, 
            (poinits +10) * 100 AS "discount factor"
    FROM 
            customers

    SELECT DISTINCT state 
    FROM customers
    ```

3. WHERE clause 
    1. it is used to compare data
    comparison operators:  >= <= = ( != <>)
    ```sql
    SELECT *
    FROM orders
    WHERE points > 3000
    -- WHERE points > 3000
    -- WHERE state = "VA"
    -- WHERE state = "va"
    -- WHERE order_date >= "2019-01-01"
    ```

4. OR, AND, NOT operator 
    AND has high precedence than OR
    ```sql
    SELECT *
    FROM orders
    WHERE points > 3000 AND birth_date >= "2019-01-01"
    -- WHERE points > 3000 OR birth_date >= "2019-01-01"
    
    -- WHERE points > 3000 OR birth_date >= "2019-01-01" AND state = "VA"
    -- WHERE points > 3000 OR (birth_date >= "2019-01-01" AND state = "VA")
    
    -- WHERE NOT (points > 3000 OR birth_date > "2019-01-01")
    -- WHERE points <= 3000 OR birth_date <= "2019-01-01"
    ```

5. IN operator / NOT IN  operator 
     ```sql
    SELECT *
    FROM orders
    -- WHERE prov = "VA" OR prov = "GA" OR prov = "FL"
    WHERE prov IN ("VA", "GA", "FL")
    WHERE prov NOT IN ("VA", "GA", "FL")
    ```

6. BETWEEN operator
    ```sql
    SELECT *
    FROM customers
    -- WHERE points >= 1000 AND points <= 3000
    WHERE points BETWEEN 1000 AND 3000
    WHERE birth_date BETWEEN "1990-01-01" AND "2000-01-01"
    ```

7. LIKE operator 
    match string pattern 
    %   : multi characters 
    _   : single character 

    ```sql
    SELECT *
    FROM customers
    WHERE last_name LIKE "b%"           -- starts with b or B
    WHERE last_name LIKE "brush%"       -- starts with brush
    WHERE last_name LIKE "%b%"          -- contains b 
    WHERE last_name LIKE "%y"           -- ends with y 
    WHERE last_name LIKE "_y"           -- 2 letters, ends with y
    WHERE last_name LIKE "__y"          -- 3 letters, ends with y
    WHERE last_name LIKE "b__y"         -- 4 letters, starts with b ends with y
    ```
 

8. REGEXP operator 
    ```sql
        SELECT *
        FROM customers
        WHERE first_name REGEXP "elka| ambur"   -- contians elka or ambur
        WHERE last_name REGEXP "ey$|on$"        -- ends with ey or on
        WHERE last_name REGEXP "^my|se"         -- starts with my or contains se
        WHERE last_name REGEXP "b[ru]"          -- container b that follows by r or u
        WHERE last_name REGEXP "br|bu"          -- container b that follows by r or u
    ```

9. IS NULL operator 
    ```sql
        SELECT *
        FROM customers
        WHERE phone IS NULL
        WHERE phone IS NOT NULL
    ```


10. ORDER BY clause 
   default: sorted by primary key | yellow key
    ```sql
    SELECT * quantity * unit_price AS total_price
    FROM customers
    ORDER BY first_name
    ORDER BY first_name DESC
    ORDER BY lastname, first_name
    ORDER BY lastname DESC, first_name DESC
    ORDER BY quantity * unit_price DESC
    ORDER BY total_price DESC
    ```

11. LIMIT clause
    LIMIT comes as last 
    ```sql
    SELECT 
    FROM customers
    LIMIT 3
    LIMIT 6, 3
    ```

# Retrive Data from a multiple table
1. inner join
    - keywords: inner | alias | ON | prefix with table name 
    - prefix with table name, if both table has same column
    - INNER keyword is optional 
    - o & c are alias 
    - if table alias are set, then you can only use alias 

    ```sql
    SELECT order_id, o.customer_id, firstname, lastname
    FROM orders o
    INNER JOIN customers c ON o.customer_id = c.customer_id
    ```

2. join across db 
    - prefix database name that are not the current database
    ```sql
    SELECT *
    FROM orders_items oi
    JOIN sql_inventory.products p -- the other db's table
        ON oi.product_id = p.product_id
    
    ```


3. self joins 
    ```sql
    USE sql_hr;
    SELECT e.employee_id, e.firstName, m.firstName AS manager

    FROM employees e 
    JOIN employees m
        ON e.reports_to =m.employee_id
    ```

4. join multiple tables 
    ```sql
    use sql_store;              -- database
    SELECT 
        o.order_id,
        o.order_date,
        c.last_name,
        os.name AS order_status
    FROM orders o               -- table base 
    JOIN  customers c           -- table a
        ON o.customer_id = c.customer_id
    JOIN  order_status os       -- table b 
        ON o.status = os.order_status
    ```

5. compound join conditions
    use two column value to identify a row.  | when there are multiple primary keys
    ```sql
    SELECT *
    FROM order_items oi               
    JOIN  oreder_item_notes oin          
        ON oi.order_id = oin.order_id
        AND oi.product_id = oin.product_id
    ```

6. implicit join syntax
    ```sql
    SELECT order_id, o.customer_id, firstname, lastname
    FROM orders o
    INNER JOIN customers c 
        ON o.customer_id = c.customer_id
    ```
    <!-- implicit join syntax : not recommended  -->
    ```sql
    SELECT *
    FROM orders o, customers c 
    WHERE o.customer_id = c.customer_id -- IF WHERE IS MISSING : 10*10
    ```

7. outer joins 
    - INNER JOIN : only rows that match ON are listed
    - OUTER JOIN 
        - LEFT  OUTER JOIN : FROM SHWN ALL
        - RIGHT OUTER JOIN : JOIN SHOWN ALL  **AVOID RIGHT JOIN**
    - INNER AND OUTER KEYWORDS CAN BE IGNORED

    ```sql
    SELECT order_id, o.customer_id, firstname, lastname
    FROM orders o
    LEFT OUTER JOIN customers c 
        ON o.customer_id = c.customer_id
    ```
    
8. outer join between multiple tables 
    ```sql
    SELECT order_id, o.customer_id, firstname, lastname
    FROM orders o
    LEFT OUTER JOIN customers c 
        ON o.customer_id = c.customer_id
    LEFT OUTER JOIN shipper sh
        ON c.shipper_id = sh.shipper_id
    ```
    
9. slef outer joins 
     ```sql
    USE sql_hr;
    SELECT e.employee_id, e.firstName, m.firstName AS manager

    FROM employees e 
    LEFT JOIN employees m
        ON e.reports_to =m.employee_id
    ```

10. USING clasue 
    - when the two tables's column name are the same 

    ```sql
    SELECT o.order_id, c.first_name, sh.name AS shipper
    FROM orders o 
    JOIN customer c
        USING (customer_id)
    LEFT JOIN shippers sh
        USING (shipper_id)
    ```

11. natural joins 
    <!--NOT RECOMENDED-->
    <!--LET DB ENGINE GUESS HOW TO JOIN-->
    ```sql
    SELECT o.order_id, c.first_name
    FROM orders o
    NATURAL JOIN customers  c 
    ```
    
12. cross joins 
    ```sql
    SELECT c.first_name AS customer,
            p.name AS product 
    FROM customers c 
    CROSS JOIN products p 
    ```
    ```sql
    SELECT c.first_name AS customer,
           p.name AS product 
    FROM customers c, products p 
    ```

13. unions 
    - combine data from multiple queries 
    ```sql
    SELECT order_id, order_date, "Active" AS status
    FROM orders
    WHERE order_date >="2010-01-01"

    UNION 

    SELECT order_id, order_date, "Archived" AS status
    FROM orders
    WHERE order_date < "2010-01-01"
    ```
    
# Inserting, Updating Deleting

1. column attributes 
    - Column Name 
    - Datatype 
    - PK : primary key 
    - NN: not null
    - AI : auto increment
    - Default 

2. insert a row 
    - match all colummns 
    ```sql 
    INSERT INTO customers 
    VALUE (DEFAULT, 'john', 'smith', NULL)
    ```
    - match provided columns
    ```sql 
    INSERT INTO customers (first_name, last_name, city)
    VALUE ( 'john', 'smith', 'antwerp')
    ```

3. insert multiple rows
    ```sql 
    INSERT INTO customers (name)
    VALUES ('john'),
           ('mark'),
           ('maxi')
    ```

4. insert data into multiple table 
    ```sql 
    INSERT INTO orders (customer_id, order_date)        -- PARENT TABLE
    VALUES (1, '2023-04-03'); 

    INSERT INTO order_items                             -- CHILD TABLE
    VALUES 
        (LAST_INSERT_ID(), 1, 1, 2,95)
        (LAST_INSERT_ID(), 1, 1, 2,95)
    ```

5. create a copy of a Table 
    ```sql 
    CREATE TABLE invoice_archived AS
    SELECT * FROM invoice           -- Truncate table if need it empty 

    INSERT INTO invoice_archived    -- Query 
    SELECT *                        -- SUB Query
    FROM invoices 
    WHERE order_date < '2023-04-03'

    -- OR: 

    CREATE TABLE invoice_archived AS
    SELECT *                       
    FROM invoices 
    WHERE order_date < '2023-04-03'
    ```

6. update a single row / multiple rows
    ```sql 
    UPDATE invoices 
    SET payment_total =  200 
        payment_date = due_date
    WHERE invoice_id = 3        --single row
    WHERE client_id = 3         -- multiple row 
    WHERE client_id IN (3,4)    -- multiple row
    ```

7. use sub query in update
    ```sql 
    UPDATE orders 
    SET comments = 'gold customer'

    WHERE  customer_id IN 
                        (SELECT customer_id     --sub query 
                        FROM customers
                        WHERE points > 3000)
    
    WHERE  customer_id IN 
                        (SELECT customer_id     --sub query 
                        FROM customers
                        WHERE prov IN ('CA', 'NY'))

    WHERE payment_date is NULL                 -- is NULL is sub query 
    ```

8. delete 
    ```sql 
    DELETE FROM invoices 
    WHERE client_id = (
        SELECT *
        FROM clients 
        WHERE client_name = 'Myworks'
    )
    ```


# Summarizing Data
1. Aggregate Functions 
    ```sql 
    SELECT 
        MAX(invoice_total) AS highest, 
        MIN(invoice_total) AS lowest, 
        AVG(invoice_total) AS average, 
        SUM(invoice_total * 1.1 ) AS total, 
        COUNT(invoice_total) AS number_of_invoices -- only non-null rows
        COUNT(*) AS total_records -- all rows
        COUNT(DISTINCT client_id) AS total_records_user
    FROM invoices 
    WHERE invoice_data > '2019-01-01'
    ```

2. GROUP BY Clause
    - the order of the clause matters 
    - group by  can not use alias name
    ```sql 
    SELECT 
        client_id,
        SUM(invoice_total) AS  total_sales 
    FROM invoices 
    WHERE invoice_date >= '2019-07-01'
    GROUP BY client_id  --grouop total sales by client_id
    ORDER BY total_sales DESC   
    ```

3. HAVINIG Clause
    - use WHERE before group by, use HAVING  after GROUP BY
    ```sql 
    SELECT 
        client_id,
        SUM(invoice_total) AS  total_sales 
    FROM invoices 
    WHERE invoice_date >= '2019-07-01'
    GROUP BY client_id 
    HAVING invoice_total > 500  -- single 
    HAVING invoice_total > 500 AND invoice_date > '2020-07-01' -- compound 
    -- HAVING Clause can only use VARIABLE indicated in SELECT Clause
    ```

4. ROLLUP Operator 
    - only available in MySQL
    - get sum from each group 
    ```sql 
    SELECT 
        prov,
        city, 
        SUM(invoice_total) AS total_sales 
    FROM invoices i
    JOIN client c USING (client_id)
    GROUP BY prov, city WITH ROLLUP     
    ```

# Complex Query
1.  Subqueries
    - evaluate sub query and pass it to parent 
    - sub query can be written in SELECT , FROM, WHERE
    ```sql 
    SELECT * 
    FROM products 
    WHERE unit_price > (
        SELECT unit_price
        FROM products
        WHERE prodcut_id = 3
    )
    ```

2.  The IN Operator
    ```sql 
    SELECT * 
    FROM products
    WHERE product_id NOT IN (
        SELECT DISTINCT product_id 
        FROM order_items
    )
    ```

3.  Subqueries vs Joins 
    - Subqueries & Joins can achieve the same thing
    - question: Find customers that have ordered lettuce (id=3), select customer_id,  first_name, last_name
    ```sql 
    -- sub query
    SELECT * 
    FROM customers 
    WHERE customer_id IN (
        SELECT o.customer_id
        FROM order_items oi 
        JOIN orders o USING (order_id)
        WHERE product_id = 3
    )

    ```
    ```sql 
    -- join
    SELECT DISTINCT customer_id, first_name, last_name
    FROM customer c
    JOIN orders o using (customer_id)
    JOIN order_items oi USING (order_id)
    WHERE oi.prodcut_id =3
    ```

4.  The ALL Keyword 
    ```sql 
    SELECT * 
    FROM invoices 
    WHERE invoice_total > ALL(  -- ALL compares all items of returned list ALL(100, 200, 3)
        SELECT invoice_total    -- sub query returns a list of item
        FROM invoices 
        WHERE client_id =3 
    )
    ```

5.  The ANY Keyword 
    ```sql 
    SELECT *
    FROM clients 
    WHERE client_id = ANY(  -- EQUAL TO: WHERE client_id  IN (
            SELECT client_id
            FROM invoices 
            GROUP BY client_id
            HAVING COUNT(*) >= 2
    )
    ```

6.  Correlated Subqueries
    - compare with self in sub query
    ```SQL 
    SELECT *
    FROM employees e 
    WHERE salary > (
        SELECT AVG(salary)
        FROM employees
        WHERE office_id = e.office_id
    )
    ```

7.  The EXISTS Operator 
    ```SQL 
    -- IN
    SELECT *
    FROM clients
    WHERE client_id IN (        -- EQUALS TO, WHERE client_id IN (1,2,3,
        SELECT DISTINCT client_id
        FROM invoices
    )
    ```

    ```SQL 
    -- EXISTS   // performance better 
    SELECT *
    FROM clients c
    WHERE client_id EXISTS (    -- SUB QUERY DOES NOT RETURN VALUE
        SELECT DISTINCT client_id
        FROM invoices
        WHERE client_id = c.client_id
    )
    ```
    
8.  Subqueries in the SELECT Clause 
    ```SQL 
    SELECT 
        invoice_id, 
        invoice_total,
        (SELECT AVG(invoice_total)
            FROM invoices) AS invoice_average,
        invoice_total - (SELECT invoice_average) AS difference

    ```
    
9.  Subqueries in the FROM Clause
    ```SQL 

    ```

# Essential MySQL Functions

1. Numeric Functions
2. String Functions
3. Date Functions in MySQL
4. Formatting Dates and Times
5. Calculating Dates and Times
6. The IFNULL and COALESCE Functions
7. The IF Function
8. The CASE Operator 

# Views
1. Creating Views 
2. Altering or Dropping Views 
3. Updatable Views 
4. THE WITH OPTION CHECK Clause
5. Other Benefits of Views

# Stored Procedures

1. What are Stored Procedures
2. Creating a Stored Procedure 
3. Creating Procedures Using MySQLWorkbench 
4. Dropping Stored Procedures 
5. Parameters
6. Parameters with Default Value
7. Parameter Validation
8. Output Parameters 
9. Variables 
10. Functions 
11. Other Conventions 

# Triggers and Events

1. Triggers
2. Viewing Triggers
3. Dropping Triggers
4. Using Triggers for Auditing
5. Events
6. Viewing, Dropping and Altering Events

# Transactions and Concurrency

1. Transactions
2. Creating Transactions
3. Concurrency and Locking
4. Concurrency Problems
5. Transaction Isolation Levels
6. READ UNCOMMITTED Isolation Level
7. READ COMMITTED Isolation Level
8. REPEATABLE READ Isolation Level
9. SERIALIZABLE Isolation Level
10. Deadlocks

# Data Type

1. Introduction
2. String Types
3. Integer Types
4. Fixed-point and Floating-point Types
5. Boolean Types
6. Enum and Set Types
7. Date and Time Types
8. Blob Types
9. JSON Type 

# Designing Databases
 
 1. Introduction
 2. Data Modelling
 3. Conceptual Models
 4. Logical Models
 5. Physical Models
 6. Primary Keys
 7. Foreign Keys
 8. Foreign Key Constraints
 9. Normalization
 10. 1NF- First Normal Form
 11. Link Tables
 12. 2NF- Second Normal Form
 13. 3NF- Third Normal Form
 14. My Pragmatic Advice
 15. Don't Model the Universe
 16. Forward Engineering a Model
 17. Synchronizing a Model with a Database
 18. Reverse Engineering a Database
 19. Project- Flight Booking System
 20. Solution- Conceptual Model
 21. Solution- Logical Model
 22. Project - Video Rental Application
 23. Solution- Conceptual Model
 24. Solution- Logical Model
 25. Creating and Dropping Databases
 26. Creating Tables
 27. Altering Tables
 28. Creating Relationships
 29. Altering Primary and Foreign Key Constraints
 30. Character Sets and Collations
 31. Storage Engines

 # Indexing for High Performance
  
 1- Introduction
 2- Indexes
 3- Creating Indexes
 4- Viewing Indexes
 5- Prefix Indexes
 6- Full-text Indexes
 7- Composite Indexes
 8- Order of Columns in Composite Indexes
 9- When Indexes are Ignored
 10- Using Indexes for Sorting
 11- Covering Indexese
 12- Index Maintenance

 # Securing Databases
  
 1. Introduction
 2. Creating a User
 3. Viewing Users
 4. Dropping Users
 5. Changing Passwords
 6. Granting Privileges
 7. Viewing Privileges
 8. Revoking Privileges