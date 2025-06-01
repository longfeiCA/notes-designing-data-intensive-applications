# Chapter 2
Data Models and Query Languages

## Data Model

### Relational Model

Data is organized into relations (called _tables_ in SQL), where each relation is an unordered collection of _tuples_ (rows in SQL). 

### Document Model

NoSQL database is latest attempt to overthrow the relational model's dominance. 

Reasons for the adoption of NoSQL: 

1. Greater scalability, including very large datasets or very high write throughput
2. Preference for free and open source software over commercial database products
3. Specialized query operations that are not well supported by relational model
4. Frustration with the restrictiveness of relational schemas, and a desire for a more dynamic and expressive data model

## Example: Save a LinkedIn Profile 

Fields: First name, last name, education (multiple entries), work experience (multiple entries)


#### Option 1: 

SQL was not able to store structured datatypes and XML data before 1999. The solution at the time was to store the education on another table with a foreign key refering to the user. 

#### Option 2: 

Later version of SQL standard added support for structured datatypes and XML data; this allowed multi-valued data to be stored within a single row, with support for querying and indexing inside those documents. These features are supported to **varying degrees** by Orable, IBM DB2, MS SQL Server, and PostgreSQL. A Json datatype is also supported by several databases, including IBM DB2, MySQL, and PostgreSQL. 

Most application development today is done in object-oriented programming languages, which leads to a common criticism of the SQL data model: if data is stored in relational tables, an awkward translation layer is required between the objects in the application code and the database model of tables, rows and columns. The disconnect between the models is sometimes called an _impedance mismatch_.

Object-relational mapping(ORM) frameworks like ActiveRecord and Hibernate reduce  the  amount  of  boilerplate  code  required  for  this  translation  layer,  but  they can’t completely hide the differences between the two models. 

#### Option 3:

A third option is to encode jobs, education as a JSON or XML document, store it on a text column in the database, and let the application interpret its structure and content. In this setup, you typically cannot use the database to query for values inside that encoded column. 

#### Option 4: (JSON model, NoSQL)

We may represent the LinkedIn profile as a JSON document. Some developers feel that the JSON model reduces the impedance mismatch between the application code and the storage layer. However, in Chapter 4, we shall see that there are also problems with JSON as a data encoding format. 

The JSON representation has better locality than the multi-table schema.  If you want to fetch a profile in the relational example, you need to either perform  multiple  queries  (query  each  table  by  user_id)  or  perform  a  messy  multi-way join between the users table and its subordinate tables. In the JSON representation, all the relevant information is in one place, and one query is sufficient.

## Data Normalization and Denormalization

ChatGPT's response: 

**Normalization** and **denormalization** are two opposite approaches to structuring data in relational databases. They impact how data is organized, stored, and accessed. Here's a clear explanation of both, with examples:

---

### **Normalization**

#### ➤ **Definition**:

Normalization is the process of organizing data to **reduce redundancy** and **improve data integrity** by dividing data into multiple related tables.

#### ➤ **Goal**:

* Eliminate duplication
* Ensure consistency
* Follow rules called "normal forms" (1NF, 2NF, 3NF, etc.)


####  **Example (Normalized)**

Suppose you’re storing data about students and the courses they take:

##### **Tables:**

**1. Students**

| student\_id | name  |
| ----------- | ----- |
| 1           | Alice |
| 2           | Bob   |

**2. Courses**

| course\_id | course\_name |
| ---------- | ------------ |
| 101        | Math         |
| 102        | English      |

**3. Enrollments**

| student\_id | course\_id |
| ----------- | ---------- |
| 1           | 101        |
| 1           | 102        |
| 2           | 101        |

##### ➤ **Benefits**:

* No duplicate data (course names stored once).
* Easy to update: changing a course name is done in one place.
* Ensures data integrity (no inconsistencies).

---

###  **Denormalization**

#### ➤ **Definition**:

Denormalization is the process of **combining tables** to reduce the number of joins needed in queries, at the cost of introducing **redundancy**.

#### ➤ **Goal**:

* Optimize read performance (especially in reporting or analytics)
* Reduce joins in complex queries

---

#### 🔹 **Example (Denormalized)**

One single table:

**StudentsWithCourses**

| student\_id | name  | course\_id | course\_name |
| ----------- | ----- | ---------- | ------------ |
| 1           | Alice | 101        | Math         |
| 1           | Alice | 102        | English      |
| 2           | Bob   | 101        | Math         |

##### ➤ **Trade-offs**:

* Redundant data: "Math" appears multiple times.
* Harder to maintain: If the course name "Math" changes, you must update every row where it appears.
* But: Queries are simpler and faster — no joins needed.

---

### 🔄 Summary Table

| Aspect            | Normalization                            | Denormalization                       |
| ----------------- | ---------------------------------------- | ------------------------------------- |
| Redundancy        | Reduced                                  | Increased                             |
| Read performance  | Slower (joins needed)                    | Faster (fewer joins)                  |
| Write performance | Faster (fewer updates on redundant data) | Slower (updates must touch more rows) |
| Storage           | Efficient                                | More storage needed                   |
| Data integrity    | High                                     | Lower (risk of inconsistency)         |
| Use cases         | OLTP (transactions, frequent updates)    | OLAP (analytics, reporting)           |

---

#### 📌 Rule of Thumb:

* **Normalize** for **transactional systems** where data integrity is key.
* **Denormalize** for **analytics/reporting systems** where read performance matters more.

## Limitation of Document Model

###  Some data structures naturally don't fit the Dacument Model. 

The document model is good at handling one-to-many tree structures. But normalization of data may require many-to-one relations that don't fit nicely into the document model. In relational databases, it is normal to refer to rows in other tables by ID, because joins are easy. In document databases, joins are not needed for one-to-many tree structures, and support for joins is often weak. 

### Data has a tendency of becoming more interconnected as features are added to applications. 

Even if the initial version of an application (for example, the LinkedIn profile example) fits well in a join-free document model, data has a tendency of becoming more interconnected as features are added to applications. Later, we may add a logos to schools or companies; or we may add a recommendation section where a user can write a recommendation for another user, which should have a reference to the referer's profile. 

#### Solution: The Network Model

The network model or the CODASYL model was a generalization of the hierarchical model. In the tree structure of the hierarchical model, every record has exactly one parent; in the network model, a record could have multiple parents. This allowed many-to-one and many-to-many relationships to be modeled. 

To date, document databases have not followed the path of CODASYL. 

#### Solution 2: The Relational Model

In a relational database, the query optimizer automatically decides which parts of the query to execute in which order,  and  which indexes to use.  If you want to query your data in new ways, you can just declare a new index, and queries will automatically use whichever indexes are most appropriate. You don't need to change your queries to take advantage of a new index. 

To be continued... Page 38