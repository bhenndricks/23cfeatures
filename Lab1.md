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

1. Create the first table. Table 1 would be inventory_no_reservation (normal table)


````
<copy>
CREATE TABLE s1.inventory_no_reservation (
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
-- Inserting rows into inventory_no_reservation table
INSERT INTO s1.inventory_no_reservation (id, product_name, quantity, budget)
VALUES (1, 'Product A', 10, 200);

INSERT INTO s1.inventory_no_reservation (id, product_name, quantity, budget)
VALUES (2, 'Product B', 5, 200);
</copy>
````

2. Insert data into the second table:

````
<copy>
-- Inserting rows into inventory_reservations table
INSERT INTO s1.inventory_reservations (id, product_name, quantity, budget)
VALUES (1, 'Product C', 8, 200);

INSERT INTO s1.inventory_reservations (id, product_name, quantity, budget)
VALUES (2, 'Product D', 3, 200);
</copy>
````

# Lab 2: Grant Schema Privledges 

## Task 1: Grant Priveledges to users

1. Grant select/insert/update/delete  priveldges on the inventory_no_reservation table to u1

````
<copy>
GRANT SELECT, INSERT, UPDATE, DELETE ON s1.inventory_no_reservation TO u1;
</copy>
````
 
2. Grant schema priveledges select/insert/update/delete to u2

````
<copy>
GRANT SELECT, INSERT, UPDATE, DELETE ON SCHEMA s1 TO u2;
</copy>
````

## Task 2: Login to Users and Test the new feature Schema Priveledges versus the Select Grants Feature

At this point we know we gave user 1 the limited ability to access table 1 from the schema. Whereas u2 has schema level access that we defined earlier. The main difference is that in the past if you have a schema with many new tables being created and you want to grant u1 account SELECT permission on these tables you would usually have to manually regrant access to that user to view new tables. Fast forward to now, 23c allows DBA to grant perma access to all tables under the schema. You will see this in action during this lab.

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
select * from s1.inventory_no_reservation;
</copy>
````

* Query the second table from s1 schema and be mindful of what happens.

````
<copy>
select * from s1.inventory_no_reservation;
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
select * from s1.inventory_no_reservation;
</copy>
````
* Query the second table from s1 schema and be mindful of what happens.

````
<copy>
select * from s1.inventory_reservations;
</copy>
````

This is great u2 can view the data from the new table, and this will be applied automatically to newly created tables in the future, which we will explore in the next lab after this. I will create a third table based on s1 inventory table and u2 will be able to view the data whereas u1 can not because it does not have schema priveledges enabled in its data definition:

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

Now lets move on to the next lab and test how we can test the new feature Lock-Free Reservation Feature by updating the numeric column, `budget` from the second table `inventory_reservations` where we created a table with a reserved column earlier. We will demonstrate this by way of contrast. So we will first take user 2 and update columns on the non lock-free table we created, `no_inventory_reservations`. Then we will test the update columns with user 1 by updating columns in the `inventory_reservations` table. You will begin to see how the user that tries to update the first table will result in a hanging commit due to the one in one out nature of normal tables, they lock until a commit has been made by a previous user. This is what causes delay in the user from making multiple commits to the table. We will view here how Lock-Free Reservations solve that issue. 


# Lab 3: Lock-Free Reservations

## Task 1: Normal Update 

1. Open 3 windows u2, u2, u2

2. In Window 1 update table1(inventory_no_reservation) decrease a record by 100 but don’t commit

````
<copy>
UPDATE s1.inventory_no_reservation
SET budget = budget - 100;
</copy>
````


3. Window 2 update t1 and decrease the same record by 100 and show that the session just hangs

````
<copy>
UPDATE s1.inventory_no_reservation
SET budget = budget - 100;
</copy>
````

4. Window 3 update t1 and decrease the same record by 200 going below the “threshold” and the session just hangs

````
<copy>
UPDATE s1.inventory_no_reservation
SET budget = budget - 200;
</copy>
````

5. In Window 1 please commit the statement below which releases one of the other two windows

````
<copy>
UPDATE s1.inventory_no_reservation
SET budget = budget - 100;
COMMIT;
</copy>
````

6. The window that freed up commit

--insert screen shot 

7. The window that freed up commit giving the error

--insert screenshot

An inssufficient budget amount causes an abort. 

## Task 2: Lock-Free Reservations

* using the same 3 windows

1. Window 1 update t1(inventory_reservation) decrease a record by 100 but don’t commit

````
<copy>
UPDATE s1.inventory_reservation
SET budget = budget - 100;
</copy>
````

2. Window 2 update t2 and decrease the same record by 100 and show that the session doesn’t hang.

````
<copy>
UPDATE s1.inventory_reservation
SET budget = budget - 100;
</copy>
````

3. Window 3 update t2 and decrease the same record by 200 going below the “threshold” and the session errors

````
<copy>
UPDATE s1.inventory_reservation
SET budget = budget - 200;
</copy>
````

4. Commit window 1 

````
<copy>
UPDATE s1.inventory_reservation
SET budget = budget - 100;
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
UPDATE s1.inventory_reservation
SET budget = budget - 200;
COMMIT;
</copy>
````