
# **Introduction**

Welcome to Live Labs, where we explore and delve into the fascinating realm of Oracle database management! In today's session, we will be focusing on two intriguing topics: Oracle Schema Level Privileges and Lock Free Column Reservations.

### **Schema Level Privileges**

Oracle Schema Level Privileges play a pivotal role in controlling access and security within an Oracle database. A schema, in Oracle, can be thought of as a logical container that holds database objects such as tables, views, procedures, and more. With schema level privileges, we can grant or revoke permissions to users or roles, ensuring that the right individuals have the appropriate level of access to the schema's objects.

During our exploration, we will dive deep into the concept of schema level privileges, understanding the various types of privileges available, such as SELECT, INSERT, UPDATE, DELETE, and more. We will learn how to grant and revoke these privileges to users and roles, ensuring that the principle of least privilege is upheld, thus enhancing the overall security posture of your Oracle database.

### **Lock Free Column Reservations**

Moving on, we will shift our focus to the fascinating world of Lock Free Column Reservations. When dealing with highly concurrent systems and multi-user environments, managing data consistency becomes crucial. Lock Free Column Reservations provide an efficient mechanism to achieve this without resorting to traditional locking mechanisms.

Throughout our exploration, we will unravel the inner workings of Lock Free Column Reservations, understanding how they allow multiple users to modify specific columns within a table concurrently, without causing conflicts or inconsistencies. We will discuss the advantages and considerations when implementing this approach, including the impact on performance and the precautions needed to ensure data integrity.

So, whether you're an Oracle enthusiast, a database administrator, or simply eager to expand your knowledge in the world of Oracle databases, join us in this Live Labs session as we uncover the intricacies of Oracle Schema Level Privileges and Lock Free Column Reservations. Let's embark on this enlightening journey together and unlock the power of Oracle's robust database management capabilities!

### **Key Benefits of Schema Level Privileges:**

* Granular Access Control: Schema level privileges allow for fine-grained control over access to database objects within a schema. It enables administrators to grant or revoke privileges at a more granular level, ensuring that users or roles only have access to the necessary objects.

* Improved Security: By implementing schema level privileges, you can adhere to the principle of least privilege, providing users with only the necessary permissions to perform their tasks. This helps in mitigating the risk of unauthorized access and potential data breaches.

* Data Integrity: Schema level privileges contribute to maintaining data integrity by controlling the actions users can perform on the schema's objects. By restricting certain operations, such as updates or deletions, you can prevent accidental or malicious modifications to critical data.

* Simplified Management: With schema level privileges, managing access control becomes more streamlined. Instead of granting permissions individually for each object, you can grant privileges at the schema level, simplifying the administration process.

* Flexibility and Scalability: Schema level privileges provide flexibility when managing user roles and permissions. As your database grows and evolves, you can easily modify privileges at the schema level, accommodating new requirements and ensuring the right level of access for different user groups.

### **Key Benefits of Lock Free Column Reservations:**

* Enhanced Concurrency: Lock Free Column Reservations allow multiple users to modify specific columns concurrently without causing conflicts or locking contention. This approach improves concurrency and scalability in highly concurrent systems, reducing wait times and enhancing system performance.

* Reduced Locking Overhead: Traditional locking mechanisms can introduce significant overhead, especially in scenarios where multiple users need to modify different columns within a table simultaneously. Lock Free Column Reservations eliminate the need for locks, minimizing contention and improving overall system efficiency.

* Improved Responsiveness: By eliminating locking, Lock Free Column Reservations enable users to make modifications to specific columns without waiting for other conflicting locks to be released. This leads to improved response times and a more interactive user experience.

* Consistent Data Access: Lock Free Column Reservations ensure consistent access to data by allowing concurrent modifications to specific columns. This helps in maintaining data integrity and preventing inconsistencies that could arise from locking conflicts.

* Simplified Transaction Management: Lock Free Column Reservations simplify transaction management by minimizing the need for explicit locks and reducing the complexity associated with deadlock detection and resolution. This can result in simpler and more robust transactional workflows.

Estimated Time: 30 Minutes


### **Workshop Objectives**

In this lab, you will:
* Create users and tables
* Grant Schema Priveledges
* Perform queries against Lock-Free Reservations

### **Prerequisites**

In order to do this workshop you need
* An Oracle 23c Free Developer Release Database or one running in a LiveLabs environment

### **Agenda**

1. Lab 1: Setup

* Task 1: Create Users
    In this task, you will create two users using SQL commands.

* Task 2: Create Two Tables Under Schema 1
    In this task, you will create two tables under the schema. The first table is a normal table, while the second table utilizes lock-free reservations for a specific column.

* Task 3: Insert a Few Rows into Each
    In this task, you will insert data into the previously created tables. Rows of data will be inserted into both the normal table and the table with lock-free reservations.

2. Lab 2: Grant Schema Privileges

* Task 1: Grant Privileges to Users
    In this task, you will grant select, insert, update, and delete privileges on specific tables to the users created in Lab 1.

* Task 2: Login to Users and Test the New Feature Schema Privileges versus the Select Grants Feature
    In this task, you will log in as the created users and test their access to tables based on the privileges granted. You will observe the difference in user experiences between limited table access and schema-level access.

3. Lab 3: Lock-Free Reservations

* Task 1: Normal Update
    In this task, you will simulate a scenario where multiple users attempt to update a table without lock-free reservations. You will observe the session hanging and delays caused by locking and committing issues.

* Task 2: Lock-Free Reservations
    In this task, you will repeat the scenario from Task 1, but this time utilizing the lock-free reservations feature. You will observe how lock-free reservations prevent session hanging and provide a more efficient user experience.

By completing these labs, you will gain practical experience in setting up a database environment, granting privileges, and utilizing the lock-free reservations feature in Oracle. Have a great learning experience!

Learn More

* [Lock-Free Reservations Documentation](https://docs.oracle.com/en/database/oracle/oracle-database/23/adfns/using-lock-free-reservation.html#GUID-60D87F8F-AD9B-40A6-BB3C-193FFF0E60BB)
* [Schema Priveledges](https://geodatamaster.com/2023/04/07/oracle-database-23c-schema-level-privileges/)

# **Acknowledgements**

* **Author(s)** - Blake Hendricks, Database Product Manager
* **Contributor(s)** - Vasudha Krishnaswamy 
* **Last Updated By/Date** - 5/25/2023
