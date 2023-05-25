# Lab 2: Schema Privilege Management

Welcome to Lab 2: Schema Privilege Management!

In this lab, we will explore the management of schema privileges in Oracle. Task 1 involves granting privileges to users for accessing tables within a schema. We will grant select, insert, update, and delete privileges on the inventory_no_reservations table to User 1 (u1) and schema privileges for select, insert, update, and delete operations to User 2 (u2) on Schema 1.

Task 2 focuses on testing the new schema privilege feature and comparing it with select grants. User 1, with limited access to Table 1 within the schema, will not have schema privileges enabled. User 2, on the other hand, will have schema-level access. During the lab, we will log in as each user and observe their access to different tables within the schema.

By logging in as User 1, we will verify the user and attempt to query Table 1. As expected, User 1 will not be able to access the inventory_no_reservations table due to the absence of schema privileges. However, when we log in as User 2, we will observe successful queries on Table 1 and also attempt to query the second table in the schema, inventory_reservations.

Additionally, we will create a third table, inventory_third_table, based on the existing inventory table in Schema 1. We will insert data into this new table and observe the access permissions for User 1 and User 2. While User 2 will have access to the new table, User 1 will not, highlighting the impact of schema privileges on user access.

The lab concludes by setting the stage for the next session, where we will explore the Lock-Free Reservation feature. We will update the inventory_reservations table and examine the difference in behavior between regular tables and lock-free reservation tables. This feature addresses the issue of session hangs caused by conventional tables, where multiple commits can lead to delays. Lock-Free Reservations offer improved performance and user experience.

Join us as we delve into Schema Privilege Management and gain a deeper understanding of its advantages in Oracle databases. Let's proceed to Lab 2 and get started!

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