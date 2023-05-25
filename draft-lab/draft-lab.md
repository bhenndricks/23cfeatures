# Lab 1: Setup

## Task 1: Create users 

1. Create two users

* Create user 1 and user 2

````
<copy>
CREATE USER u1 IDENTIFIED BY password1;

CREATE USER u2 IDENTIFIED BY password2;
</copy>
````

## Task 2: Create two tables under schema 1 

1. Create the first table. Table 1 would be inventory_no_reservations (normal table)


````
<copy>
CREATE TABLE s1.inventory_no_reservations (
  id NUMBER PRIMARY KEY,
  product_name VARCHAR2(50),
  quantity NUMBER,
  budget NUMBER
);
</copy>
````

2. Create a second table with lock-free reservations. Table 2 would be inventory_reservations (lock free reservation table)

* Modify the `CREATE TABLE` command to enable lock-free reservation as follows:  
 
````
<copy>
Create_table_command::={relational_table | object_table | XMLType_table }
Relational_table::= CREATE TABLE [ schema. ] table …;
relational_properties::= [ column_definition ] 
column_definition::= column_name datatype reservable [CONSTRAINT constraint_name check_constraint]
</copy>
````

* Create the table with the reservable keyword to enable lock free reservations to a specific column. In this case we bind it to the `budget` variable. 

````
<copy>
CREATE TABLE s1.inventory_reservations (
  id NUMBER PRIMARY KEY,
  product_name VARCHAR2(50),
  quantity NUMBER,
  budget NUMBER reservable CONSTRAINT minimum_balance CHECK (budget >= 400)

);
</copy>
````
## Task 3: Insert a few rows into each. 

1. Insert data into the first table:

````
<copy>
-- Inserting rows into inventory_no_reservations table
INSERT INTO s1.inventory_no_reservations (id, product_name, quantity, budget)
VALUES (1, 'Product A', 10, 700);
commit;
</copy>
````

````
<copy>
INSERT INTO s1.inventory_no_reservations (id, product_name, quantity, budget)
VALUES (2, 'Product B', 5, 200);
commit;
</copy>
````

2. Insert data into the second table:

````
<copy>
-- Inserting rows into inventory_reservations table
INSERT INTO s1.inventory_reservations (id, product_name, quantity, budget)
VALUES (1, 'Product C', 8, 700);
</copy>
````

````
<copy>
INSERT INTO s1.inventory_reservations (id, product_name, quantity, budget)
VALUES (2, 'Product D', 3, 200);
</copy>
````

# Lab 2: Grant Schema Privledges 

## Task 1: Grant Priveledges to users

1. Grant select/insert/update/delete  priveldges on the inventory_no_reservations table to u1

````
<copy>
GRANT SELECT, INSERT, UPDATE, DELETE ON s1.inventory_no_reservations TO u1;
</copy>
````
 
2. Grant schema priveledges select/insert/update/delete to u2

````
<copy>
GRANT SELECT, INSERT, UPDATE, DELETE ON SCHEMA s1 TO u2;
</copy>
````

## Task 2: Login to Users and Test the new feature Schema Priveledges versus the Select Grants Feature

In the current scenario, we have assigned user 1 with limited access to table 1 within the schema, while user 2 has schema-level access as defined earlier. The key distinction lies in the past practice where, when dealing with a schema containing numerous newly created tables, granting SELECT permission to user 1 on these tables necessitated manual access regrants each time new tables were added. However, with the advent of Oracle 23c, database administrators (DBAs) now have the ability to grant permanent access to all tables within a schema. During this lab, you will witness firsthand how this functionality operates, providing a seamless experience for managing access to tables within the schema.


1. Login to user 1

````
<copy>
sqlplus "username/password"@connect_identifier
</copy>
````

* Input the following to verify user:
````
<copy>
show user
</copy>
````

* Query the table 1 from the user.

````
<copy>
select * from s1.inventory_no_reservations;
</copy>
````

* Query the second table from s1 schema and be mindful of what happens.

````
<copy>
select * from s1.inventory_no_reservations;
</copy>
````

User 1 can not access this table because they do not have schema priveledges feature enabled. Watch what happens next with user 2.

2. Login to user 2

````
<copy>
sqlplus "username/password"@connect_identifier
</copy>
````

* Input the following to verify user:
````
<copy>
show user
</copy>
````

* Query the table 1 from the user.

````
<copy>
select * from s1.inventory_no_reservations;
</copy>
````
* Query the second table from s1 schema and be mindful of what happens.

````
<copy>
select * from s1.inventory_reservations;
</copy>
````


In this lab, we have enabled schema privileges for user u2, allowing them to view data from the new table. This privilege will also be automatically applied to any future tables created. In the upcoming lab, we will further explore this concept. As part of the current lab, I will create a third table based on the s1 inventory table. While u2 will be able to view the data from this new table, u1 won't have the necessary schema privileges enabled in its data definition, limiting their access.

1. Create the third table:

````
<copy>
CREATE TABLE s1.inventory_third_table (
  id NUMBER,
  product_name VARCHAR2(50),
  quantity NUMBER,
  budget NUMBER
);
</copy>
````

* Insert Data into the Third Table 
````
<copy>
INSERT INTO s1.inventory_third_table (id, product_name, quantity, budget)
VALUES (3, 'Product E', 7, 29.99);

INSERT INTO s1.inventory_third_table (id, product_name, quantity, budget)
VALUES (4, 'Product F', 12, 39.99);
</copy>
````

2. Login to u1 


````
<copy>
sqlplus "username/password"@connect_identifier
</copy>
````

* Input the following to verify user:
````
<copy>
show user
</copy>
````

* Query the third table for the user.

````
<copy>
select * from s1.inventory_third_table;
</copy>
````

Notice how there is no way user 1 can access the newly created table under the schema. 

3. Login to u2

````
<copy>
sqlplus "username/password"@connect_identifier
</copy>
````

* Input the following to verify user:
````
<copy>
show user
</copy>
````

* Query the third table for the user.

````
<copy>
select * from s1.inventory_third_table;
</copy>
````

This is awesome! We can tell that u2 is entitled to access all tables within the schema.

Now, let's proceed to the next lab and explore the functionality of the Lock-Free Reservation feature. We will accomplish this by updating the numeric column, budget, in the `inventory_reservations` table, which we previously created with a reserved column. This demonstration will provide a clear contrast between different scenarios. First, we will simulate user 2 updating columns in the non lock-free table, `no_inventory_reservations`. Next, we will test the update columns using user 1 in the `inventory_reservations` table. Through this exercise, you will observe how attempting to update the first table leads to a hanging commit due to the conventional one in, one out nature of regular tables. These tables remain locked until a commit is made by a preceding user, causing delays when multiple commits are made to the table. We will witness how Lock-Free Reservations effectively address this issue, providing a solution for improved performance and user experience.


# Lab 3: Lock-Free Reservations

## Task 1: Normal Update 

1. Open 3 windows u2, u2, u2

2. In Window 1 update table1(inventory_no_reservations) decrease a record by 100 but don’t commit

````
<copy>
UPDATE s1.inventory_no_reservations
SET budget = budget - 100 where ID = 1;
</copy>
````


3. Window 2 update t1 and decrease the same record by 100 and it shows that the session hangs. Due to the first update not being commited.

````
<copy>
UPDATE s1.inventory_no_reservations
SET budget = budget - 100 where ID = 1
commit;
</copy>
````

4. Window 3 update t1 and decrease the same record by 200 going below the “threshold”. The session should hang again.

````
<copy>
UPDATE s1.inventory_no_reservations
SET budget = budget - 200 WHERE ID = 1
commit;
</copy>
````

5. In Window 1 please commit the statement below which releases one of the other two windows

````
<copy>
UPDATE s1.inventory_no_reservations
SET budget = budget - 100 where ID = 1
COMMIT;
</copy>
````

6. Once a window is freed up, proceed to the next step without executing any additional instructions.

7. If the freed-up window encounters an error when committing, take note of the error message.

Note: An insufficient budget amount causes an abort.

## Task 2: Lock-Free Reservations

* using the same 3 windows

1. Window 1 update t1(inventory_reservations) decrease a record by 100 but don’t commit

````
<copy>
UPDATE s1.inventory_reservations
SET budget = budget - 100 where ID = 1;
</copy>
````

2. Window 2 update t2 and decrease the same record by 100 and show that the session doesn’t hang.

````
<copy>
UPDATE s1.inventory_reservations
SET budget = budget - 100 where ID = 1
commit;
</copy>
````

3. Window 3 update t2 and decrease the same record by 200 going below the “threshold” and the session errors

````
<copy>
UPDATE s1.inventory_reservations
SET budget = budget - 200 where ID = 1
commit;
</copy>
````

4. Commit window 1

````
<copy>
UPDATE s1.inventory_reservations
SET budget = budget - 100 where ID = 1
COMMIT;
</copy>
````

5. Roll back window 2
````
<copy>
ROLLBACK;
</copy>
````


5. Go to Window 3 and run the transaction again and it should succeed because we gave back the 100 to the reserved column, budget
````
<copy>
UPDATE s1.inventory_reservations
SET budget = budget - 200 where ID = 1
COMMIT;
</copy>
````

## **Acknowledgements**
* **Author(s)** - Blake Hendricks
* **Contributor(s)** - Vasudha Krishnaswamy 
* **Last Updated By/Date** - 5/25/2023