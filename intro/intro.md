

Lab Description:
Lab 1 consists of three tasks. In Task 1, you will create two users using SQL commands. Task 2 involves creating two tables under a schema. The first table is a normal table, while the second table utilizes lock-free reservations for a specific column. Finally, in Task 3, you will insert rows of data into each table.

Lab 1: Setup

Task 1: Create Users
In this task, you will create two users using SQL commands.

Task 2: Create Two Tables Under Schema 1
In this task, you will create two tables under the schema. The first table is a normal table, while the second table utilizes lock-free reservations for a specific column.

Task 3: Insert a Few Rows into Each
In this task, you will insert data into the previously created tables. Rows of data will be inserted into both the normal table and the table with lock-free reservations.

Lab 2: Grant Schema Privileges

Task 1: Grant Privileges to Users
In this task, you will grant select, insert, update, and delete privileges on specific tables to the users created in Lab 1.

Task 2: Login to Users and Test the New Feature Schema Privileges versus the Select Grants Feature
In this task, you will log in as the created users and test their access to tables based on the privileges granted. You will observe the difference in user experiences between limited table access and schema-level access.

Lab 3: Lock-Free Reservations

Task 1: Normal Update
In this task, you will simulate a scenario where multiple users attempt to update a table without lock-free reservations. You will observe the session hanging and delays caused by locking and committing issues.

Task 2: Lock-Free Reservations
In this task, you will repeat the scenario from Task 1, but this time utilizing the lock-free reservations feature. You will observe how lock-free reservations prevent session hanging and provide a more efficient user experience.

By completing these labs, you will gain practical experience in setting up a database environment, granting privileges, and utilizing the lock-free reservations feature in Oracle. Have a great learning experience!