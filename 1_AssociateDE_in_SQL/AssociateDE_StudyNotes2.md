[_Previous (AssociateDE_StudyNotes1)_](AssociateDE_StudyNotes1.md) \
[_Next (AssociateDE_StudyNotes3)_](AssociateDE_StudyNotes3.md)

# Associate Data Engineering in SQL - PART II
### Table of contents - PART II

- [Database](#database)
   - [Introduction to Relational Database in SQL](#introduction-to-relational-database-in-sql)
        - [1.Your first database](#1-your-first-database)
        - [2.Better data quality with constraints](#2-better-data-quality-with-constraintsidea-of-database-and-rules) 
   - [Database Design](#database-design)
- [Data Warehousing (AssociateDE_StudyNotes3)](AssociateDE_StudyNotes3.md)
   - Data Warehousing Concepts
   - Introduction to Snowflakes
- [Understanding Data Visualization](#undersdanding-data-visualization)
     - [Project: Exploring London's Travel Network](#project-exploring-londons-travel-network)
- Tutorial: How to Install PostgreSQL


# Database

## Introduction to Relational Database in SQL
You can model different phenomena in your data, as well as the relationships between them. This gives your data structure and consistency, which results in better data quality. 
You'll learn how to create tables and specify their relationships, as well as how to enforce data integrity. You'll also discover other unique features of database systems, such as constraints.
create your very first database with a set of simple SQL commands. Next, you'll migrate data from existing flat tables into that database. You'll also learn how meta-information about a database can be queried.

### 1. Your first database
#### Why Database
why using relational databases has many advantages over using flat files like CSVs or Excel sheets?
- Story: _As a __data journalist__, I try to uncover corruption, misconduct and other newsworthy stuff with data. A couple of years ago I researched secondary employment of Swiss university professors. It turns out a lot of them have more than one side job besides their university duty, being paid by big companies like banks and insurances. So I discovered more than 1500 external employments and visualized them in an interactive graphic, shown on the left. For this story, I had to compile data from various sources with varying quality. Also, I had to account for certain specialties, for example, that a professor can work for different universities; or that a third-party company can have multiple professors working for them. In order to analyze the data, I needed to make sure its quality was good and stayed good throughout the process. That's why I stored my data in a database, whose quite complex design you can see in the right graphic. All these rectangles were turned into database tables._
       ![img](images/03_00.png)
   -  why did I use a database?
        - A database models real-life entities like professors and universities by storing them in tables.
        - Each table only contains data from a single entity type. __This reduces redundancy by storing entities only once__ – for example, there only needs to be one row of data containing the details of a certain company.
        - Lastly, a database can be used to model relationships between entities. You can define exactly how entities relate to each other. For instance, a professor can work at multiple universities and companies, while a company can employ more than one professor. ![img](03_01.png)
            
- information_schema
     - information_schema database is available in database management systems (PostgreSQL, MySQL or SQL Server)by default.
     - information_schema is a meta-database that holds information about your current database. information_schema has multiple tables you can query with the known SELECT * FROM syntax:
          - tables: information about all tables in your current database
          - columns: information about all columns in all of the tables in your current database.
          - schemata: Information about all databases (schemas).
          - key_column_usage: Information about columns used in keys (primary keys, foreign keys, etc.).
          - referential_constraints: Information about foreign key constraints.
          - table_constraints: Information about table-level constraints.
   - e.g. ![img](images/03_02.png) ![img](images/03_03.png)
   - e.g. query to get information such as the *table_name*, *table_schema*(the database name: ___'public'-> user-defined tables and databases___; The other types hold system information, e.g. 'information_schema' is the system database that holds metadata), *table_type* (whether it’s a BASE TABLE, VIEW, etc.), and more.
     ```
     SELECT * FROM information_schema.tables;
     ```
  - e.g. list all the tables information from the information_schema database
    ```
    SELECT table_name FROM information_schema.tables
    WHERE table_schema = 'information_schema';
    ```
    'table_schema' column specifies the database name, and 'information_schema' is the system database that holds metadata.
  - e.g. List all columns from all tables in information_schema database
    ```
    SELECT table_name, column_name, data_type FROM information_schema.columns
    WHERE table_schema = 'information_schema';
    ```
  - lists information about all tables in all databases
    ```
    SELECT * FROM information_schema.tables;
    --OR 
    SELECT * FROM information_schema.columns;    
    ``` 
  -  have a look at the columns information of table university_professors.
    ```
    SELECT column_name, data_type  FROM information_schema.columns
    WHERE table_name = 'university_professors' AND table_schema = 'public';
    ```
#### Create your first database
##### Tables - the core of database (Database modeling)
- redundancy in the "university_professors" table: this professor,  his university, the "ETH Lausanne" is repeated in the first three records. The reason for this is that the table actually contains entities of at least three different types: professors, universities, organizations. ![img](images/03_04.png)
- entity-relationship diagram: Squares denote so-called entity types, while circles connected to these denote attributes (or columns). ![img](images/03_05.png)
     - In the old entity-relationship diagram, we have only modeled one so-called entity type – "university_professors". However, we discovered that this table actually holds many different entity types.
     - New: It represents three entity types, "professors", "universities", and "organizations" in their own tables, with respective attributes. This reduces redundancy, as professors, unlike now, need to be stored only once. However, one original attribute, the "function", is still missing.  
     - __??????? A better database model with four entity types: this database contains affiliations of professors with third-party organizations. The attribute "function" gives some extra information to that affiliation. For instance, somebody might act as chairman for a certain third-party organization. So the best idea at the moment is to store these affiliations in their own table – it connects professors with their respective organizations, where they have a certain function.__
       ![img](images/03_06.png)
- We dropped the "university_shortname" column in affiliations before migrating data, bcz it is not even needed here. why? 
     - A professor is uniquely identified by firstname, lastname only.
     - _?????? I queried the "university_professors" table and saw that there are 551 unique combinations of first names, last names, and associated universities. I then queried the table again and only looked for unique combinations of first and last names. Turns out, this is also 551 records. This means that the columns "firstname" and "lastname" uniquely identify a professor._
     - _?????? So the "university_shortname" column is not needed in order to reference a professor in the affiliations table. You can remove it, and this will reduce the redundancy in your database again. In other words: The columns "firstname", "lastname", "function", and "organization" are enough to store the affiliation a professor has with a certain organization._

##### how to create such databases from scratch? (build and maintain databases)?
- Create new tables: create four empty tables for professors, universities, organizations, and affiliations.
  ```
  CREATE TABLE table_name(
  column_a data_type,
  column_b data_type
  );
  ```
     - data_type: text; numeric; char(5) : fixed-length character strings with 5 characters each;       
- adding columns to existing tables, especially if they're still empty.
  ```
  ALTER TABLE table_name
  ADD COLUMN column_name data_type;
  ```
- Rename a column
  ```
  ALTER TABLE table_name
  RENAME COLUMN old_name TO new_name;
  ```
- Delete a column, Dropping columns is straightforward when the tables are still empty.
  ```
  ALTER TABLE table_name
  DROP COLUMN column_name;
  ```
- Delete a table
  ```
  DROP TABLE table_name;
  ```
 
##### Migrate the data (update your database as the structure changes)
migrate data from 'university_professors' of old diagram to four new empty tables of our better database model, moving the respective entity types to their appropriate tables. In the end, delete the "university_professors" table.
One advantage of splitting up "university_professors" into several tables is the reduced redundancy.
- copy data from an existing table to a new one:
  ```
  INSERT INTO new_table_name
  SELECT DISTINCT colunm_name1, column_name2
  FROM old_table_name;
  ```
- insert values manually
  ```
  INSERT INTO table_name (column_a, column_b)
  VALUES ("value_a", "value_b");
  ```

### 2. Better data quality with constraints(idea of database and rules)
__the idea of a database__ is to push data into a certain structure – a pre-defined model, where you enforce data types, relationships, and other rules. Generally, these rules are called __integrity constraints__, although different names exist.
![img](images/03_07.png)  (enforce a database constraint)
- attribute constraints: e.g., a certain attribute, represented through a database column, could have the integer data type, allowing only for integers to be stored in this column.
- key constraints: e.g. Primary keys, uniquely identify each record, or row, of a database table.
- referential integrity constraints: In short, they glue different database tables together.
  
![img](images/03_08.png)
- So constraints give you consistency, meaning that a row in a certain table has exactly the same form as the next row, and so forth.

#### 2.1 Enforce data consistency with attribute constraints (data types)
After building a simple database, it's now time to make use of the features. You'll specify data types in columns, enforce column uniqueness, and disallow NULL values in this chapter.
Three concepts that help preserve data quality in databases: __constraints, keys, and referential integrity__. (use constraints, keys and referential integrity in order to assure data quality.)
![img](images/03_09.png)
data types in PostgreSQL. 
   - basic data types for numbers, such as "bigint"
   - strings of characters, such as "character varying".
   - more high-level data types like "cidr", which can be used for IP addresses.
   - Implementing such a type on a column would disallow anything that doesn't fit the structure of an IP.

![img](images/03_10.png)
use the "CAST" function to turn "wind_speed" (text type) into an integer.

Practice:
- e.g. create a fictional database table (transactions) that holds three records(rows). The columns have the data types date, integer, and text, respectively. (According to the PostgreSQL documentation, the column _transaction_date_ with __date__ type accepts values in the form of YYYY-MM-DD, DD/MM/YY, and so forth.)
         ```
         CREATE TABLE transactions (
         transaction_date date,
         amount integer,
         fee text
         );
          -- Let's add a record to the table
         INSERT INTO transactions (transaction_date, amount, fee) 
         VALUES ('2018-09-24', 5454, '30');  
         ```

##### Working with data types (--Attribute Constraints)
- Data types
     - Enforced on columns(i.e. attributes: data types are attribute constraints and are therefore implemented for single columns of a table.)
     - Define the so-called "domain" of a column.
     - Define what operations are possible (+ - * / doesn't work on text)
     - Enforce consistent storage of values (consistent storage is enforced, so a street number will always be an actual number, and a postal code will always have no more than 6 digits, according to your conventions. This greatly helps with data quality.)
- The most common types in PostgreSQL
     - ![img](images/03_11.png) ![img](images/03_12.png)  "integer" allows only whole numbers in a certain range. If that range is not enough for your numbers, there's also "bigint" for larger numbers.
     - e.g.
       ```
       CREATE TABLE students (
       ssn integer,
       name varchar64),
       dob date,
       average_grade numeric(3, 2), --e.g. 5.54
       tuition_paid boolean
       );
       -- Alter types after table creation
       ALTER TABLE students
       ALTER COLUMN name
       TYPE varchar(128);

       ALTER TABLE students
       ALTER COLUMN average_grade
       TYPE integer
       -- Turns 5.54 into 6, not 5, before type conversion
       USING ROUND(avergae_grade);
       ```
       ![img](images/03_13.png)

##### two special attribute constraints: The not-null and unique constraints
![img-e.g.](images/03_14.png)
![img-e.g.](images/03_15.png)

Attribute constraints didn't actually change the structure of the mode.
#### 2.2. Uniquely identify records with key constraints (primary/foreign key)


In the entity-relationship diagram, keys are denoted by underlined attribute names. Notice that you'll add a whole new attribute to the "professors" table, and you'll modify existing columns of the "organizations" and "universities" tables.
##### Keys and superkeys
- what is key? what is superkey? what is candidate key?
     - ![img](images/03_16.png)
          - Typically a database table has an attribute, or a combination of multiple attributes, whose values are unique across the whole table. Such attributes identify a record uniquely. Normally, a table, as a whole, only contains unique records, meaning that the combination of all attributes is a key in itself. However, __it's called a superkey if attributes from that combination can be removed, and the attributes still uniquely identify records.__
          - __If all possible attributes have been removed but the records are still uniquely identifiable by the remaining attributes, we speak of a minimal superkey, which is the actual _key_. So a key is always minimal.__
     - e.g. ![img](images/03_17.png)
          - __the combination of all attributes is a superkey.__ (If we remove the "year" attribute from the superkey, the six records are still unique, so it's still a superkey. ) Actually, __there are a lot of possible superkeys in this example.__
          - ![img](images/03_18.png) Remember that superkeys are minimal if no attributes can be removed without losing the uniqueness property. This is trivial for K1 to 3, as they only consist of a single attribute.
          - These four minimal superkeys are also called __candidate keys__.(why?-next video)

- Practice: Identify keys: Your database doesn't have any defined keys so far, and you don't know which columns or combinations of columns are suited as keys. There's a simple way of finding out whether __a certain column (or a combination)__ contains only unique values – and thus identifies the records in the table.
     - ```
       SELECT COUNT(DISTINCT(column_a, column_b, ...)) FROM table;
       ```

##### Primary keys
- What is Primary key?
     - ![img](images/03_19.png)
     - time-invariant, meaning that they must hold for the current data in the table – but also for any future data that the table might hold.
- Adding/specifying primary key upon table creation
     - ![img](images/03_20.png)
     - these two tables at the left accept exactly the same data, however, the latter has an explicit primary key specified.
     - you can designate more than one column(the combination of columns) as the primary key(see right in pic). Ideally, though, primary keys consist of as few columns as possible!
- Adding primary key constraints to existing tables.(you have to give the constraint a certain name)
     ```
     ALTER TABLE table_name
     ADD CONSTRAINT new_column_name PRIMARY KEY (column_name);
     ```
##### Surrogate keys
- What is surrogate key?
   Surrogate keys are sort of __an artificial primary key__. In other words, they are not based on a native column in your data, but on a column that just exists for the sake of having a primary key. 
- Why do we need surrogate key?
     - Primary keys should be built from a few columns as possible
     - Primary keys should never change over time.
     - If you can't find a good cadidate key as the primary key for an existing table(OR the existing attributes are not really suited as primary key), you can define an artificial primary key, ideally consisting of a unique number or string, which stays the same for each record. Other attributes might change, but the primary key always has the same value for a given record.  
- Adding a surrogate key with __serial__ data type: a special data type in PostgreSQL that allows the addition of auto-incrementing numbers to an existing table. Once you add a column with the "serial" type, all the records in your table will be numbered. Whenever you add a new record(don't provide value for surrogate key) to the table, it will automatically get a number that does not exist yet. 
     ```
     ALTER TABLE cars
     ADD COLUMN id serial PRIMARY KEY
     INSERT INTO cars
     VALUES('Volk', 'Blitz', 'black');
     ```
- Another strategy for creating a surrogate key: combine two existing columns into a new one.  
     - we first add a new column with the "varchar" data type. We then "UPDATE" that column with the concatenation of two existing columns. The "CONCAT" function glues together the values of two or more existing columns. Lastly, we turn that new column into a surrogate primary key.
       ```
       ALTER TABLE table_name
       ADD COLUMN column_c varchar(256);
       UPDATE table_name
       SET column_c = CONCAT(column_a, column_b);
       ALTER TABLE table_name
       ADD CONSTRAINT pk PRIMARY KEY(column_c);
       ```
#### 2.3. Glue together tables with foreign keys
In the final chapter, you'll leverage foreign keys to connect tables and establish relationships that will greatly benefit your data quality. And you'll run __ad hoc analyses__ on your new database.
##### Model 1:N relationships with foreign keys
- model a so-called relationship type between "professors" and "universities". 
     -  ![img](images/03_21.png)
     -  In the ER diagram, this is drawn with a rhombus. The small numbers specify the cardinality of the relationship: a professor works for at most one university, while a university can have any number of professors working for it – even zero.
- Implement relationships with foreign keys
     - __A foreign key(FK) points to the primary key(PK) of another table__ (i.e What is foreign key?    Foreign keys are designated columns that point to a primary key of another table.)
     - __Domain of FK must be equal to domain of PK.__ (One of three restrictions for foreign keys: the domain and the data type must be the same as one of the primary key)
     - __Each value of FK must exist in PK of the other table(FK constraint or "referential integrity").__ (One of three restrictions for foreign keys: only foreign key values are allowed that exist as values in the primary key of the referenced table. This is the actual foreign key constraint, also called "referential integrity".)
     - __FKs are not actual keys.__ (One of three restrictions for foreign keys: a foreign key is not necessarily an actual key, because duplicates and "NULL" values are allowed. )
- Specifying foreign keys - when creating new tables
     -  ![img](images/03_22.png)
     -  As each car is produced by a certain manufacturer, it makes sense to also add a foreign key to this table. We do that by writing the "REFERENCES" keyword, followed by the referenced table and its primary key in brackets. From now on, only cars with valid and existing manufacturers may be entered into that table. Trying to enter models with manufacturers that are not yet stored in the "manufacturers" table won't be possible, thanks to the foreign key constraint.
- Specifying foreign keys - adding foreign keys to existing tables
      ```
       ALTER Table a
       ADD CONSTRAINT a_fkey FOREIGN KEY (b_id) REFERENCES b (id);
       ```
     - Pay attention to the __naming convention__ employed here: Usually, a foreign key referencing another primary key with name id is named x_id, where x is the name of the referencing table in the singular form.

- Practice:
     - WHEN insert a foreign key value that doesn't exist in reference table, ERROR: insert or update on table "professors" violates foreign key constraint "professors_fkey" .    DETAIL:  Key (university_id)=(MIT) is not present in table "universities".
  
##### Model more complex relationships
- relationship between organizations and professors: an N:M relationship.
     - ![img](images/03_23.png)  ![img](images/03_24.png)
     -  a professor can be affiliated with more than one organization and vice versa. Also, this an N:M relationship has an own attribute, the function. Remember that each affiliation comes with a function, for instance, "chairman". The second thing you'll notice is that the affiliations entity type disappeared altogether. For clarity, I still included it in the diagram, but it's no longer needed. However, you'll still have four tables: Three for the entities "professors", "universities" and "organizations", and one for the N:M-relationship between "professors" and "organizations".

- Implement N:M-relationships
     - ![img](images/03_25.png)
     - Two foreign keys: one pointing to the "professors.id" column, and one pointing to the "organizations.id" column.
     - additional attributes, in this case "function", need to be included.
     - Code shown in above pic of creating that relationship table from scratch. Note that "professor_id" is stored as "integer", as the primary key it refers to has the type "serial", which is also an integer. On the other hand, "organization_id" has "varchar(256)" as type, conforming to the primary key in the "organizations" table.
     - One last thing: Notice that no primary key is defined here because a professor can theoretically have multiple functions in one organization. One could define the combination of all three attributes as the primary key in order to have some form of unique constraint in that table, but that would be a bit over the top.

- Migrate data: Since you already have a pre-populated affiliations table, things are not going to be so straightforward. You'll need to link and migrate the data to a new table to implement this relationship.
     - You're going to transform the affiliations table in-place, i.e., without creating a temporary table to cache your intermediate results.
       ```
       -- Add a professor_id column
       ALTER TABLE affiliations
       ADD COLUMN professor_id integer REFERENCES professors (id);

       -- Rename the organization column to organization_id
       ALTER TABLE affiliations
       RENAME COLUMN organization TO organization_id;
       
       -- Add a foreign key on organization_id so that it references the id column in organizations
       ALTER TABLE affiliations
       ADD CONSTRAINT affiliations_organization_fkey FOREIGN KEY (organization_id) REFERENCES organizations (id);
       ```
     - So far, professor_id in table affiliations is null. __Update the professor_id column with the corresponding value of the id column in professors.__ "Corresponding" means rows in professors where the firstname and lastname are identical to the ones in affiliations.
       ```
       -- Set professor_id to professors.id where firstname, lastname correspond to rows in professors
       UPDATE affiliations
       SET professor_id = professors.id
       FROM professors
       WHERE affiliations.firstname = professors.firstname AND affiliations.lastname = professors.lastname;       
       ```
     - Drop firstname, lastname columns from the affiliations table
       ```
       -- Drop the firstname column
       ALTER TABLE affiliations
       DROP COLUMN firstname;
       -- Drop the lastname column
       ALTER TABLE affiliations
       DROP COLUMN lastname;
       ```
 
##### Referential integrity
- What is referential integrity?
     - states that __a record referencing another record in another table must always refer to an existing record.__ In other words: A record in table A cannot point to a record in table B that does not exist.
     - Referential integrity is a constraint that __always concerns two tables__,
     - and is __enforced through foreign keys__ 
     - e.g. if you define a foreign key in the table "professors" referencing the table "universities", referential integrity is held from "professors" to "universities".

- Referential integrity violations (two ways).
     - __if a record in table B that is referenced from a record in table A is deleted.__
     - __if a record in table A referencing a non-existing record from table B is inserted.__
     - Referential integrity from table A to table B will be violated. And __Foreign keys prevent violations__- they will __throw errors__ and stop you from accidentally doing these things.

- How to deal with violations?  
     - throwing an error is not the only option. If you specify a foreign key on a column, you can actually tell the database system what should happen if an entry in the referenced table is deleted.
     - __By default, the "ON DELETE NO ACTION" keyword is automatically appended to a foreign key definition__, like in the example here. This means that if you try to delete a record in table B which is referenced from table A, the system will throw an error. However, there are other options.
     - __there's the "CASCADE" option__, which will ___first allow the deletion of the record in table B, and then will automatically delete all referencing records in table A.___ So that deletion is cascaded.
     -  ![img](images/03_26.png)
     -  More options:
          - ![img](images/03_27.png)         - 
          - __The "RESTRICT" option is almost identical to the "NO ACTION" option.__ The differences are technical and beyond the scope of this course.
          - More interesting is __the "SET NULL" option__. It will set the value of the foreign key for this record to "NULL".
          - __The "SET DEFAULT" option__ only works if you have specified a default value for a column. It automatically changes the referencing column to a certain default value if the referenced record is deleted. Setting default values is also beyond the scope of this course, but this option is still good to know.
            
- Practice: Change the referential integrity behavior of a key.
     - story: So far, you implemented three foreign key constraints:
       **** SET professors.university_id = universities.id ****
       **** SET affiliations.organization_id = organizations.id ****
       **** SET affiliations.professor_id = professors.id ****
       These foreign keys currently have the behavior ON DELETE NO ACTION. Here, you're going to change that behavior for the column referencing organizations from affiliations --> If an organization is deleted, all its affiliations (by any professor) should also be deleted.
     - How to alter a key constraint: DROP the key constraint and then ADD a new one with a different ON DELETE behavior.
     ```
     -- Have a look at the existing foreign key constraints by querying table_constraints in information_schema
     SELECT constraint_name, table_name, constraint_type
     FROM information_schema.table_constraints
     WHERE constraint_type = 'FOREIGN KEY';

     -- Drop the right foreign key constraint
     ALTER TABLE affiliations
     DROP CONSTRAINT affiliations_organization_id_fkey;

     -- Add a new foreign key constraint from affiliations to organizations which cascades deletion
     ALTER TABLE affiliations
     ADD CONSTRAINT affiliations_organization_id_fkey FOREIGN KEY (organization_id) REFERENCES organizations (id) ON DELETE CASCADE;         
     ```
##### Roundup (Review And ad-hoc analysis)
- Review: Transform a table(flat files like CSVs or excel files) into the database schema-only by executing SQL queries:
     - Define column data types
     - Key constraints (add primary/foreign keys, specify relationships between tables)

- The DBMS exposes a query interface where you can run ad-hoc analyses and queries with SQL. However, you can also access this interface through other client applications. You could, for example, program a Python script that connects to the database, loads data from it, and visualizes it.  ![img](images/03_28.png)
  
- After this, you'll no longer manipulate data in your database system, but employ some analysis queries on your database.

## Database Design
A good database design is crucial for a high-performance application. \
how your data will be stored beforehand. a well-designed database ensures ease of access and retrieval of information. While choosing a design, a lot of considerations have to be accounted for. \
In this course, you'll learn how to process, store, and organize data in an efficient way. You'll see how to structure data through normalization and present your data with views. Finally, you'll learn how to manage your database and all of this will be done on a variety of datasets from book sales, car rentals, to music reviews.

### Processing, Storing, and Organizing Data
two approaches to data processing, OLTP and OLAP. the basics of data modeling.
#### OLTP and OLAP
- How should we organize and manage data?
     -  ![img](images/03_29.png)
     -  we have to consider the different schemas, management options, and objects that make up a database.  These topics all affect the way data is stored and accessed. Some enable faster query speeds. Some take up less memory than others. And notably, some cost more money than others.
     -  there is no one right answer to this motivating question. It will come down to how the data will be used.
- Approaches to processing data (OLTP vs OLAP)
     - They help define the way data is going to flow, be structured, and stored. If you figure out which fits your business case, designing your database will be much easier.
     - OLTP stands for __Online Transaction Processing__, which is oriented around transactions. application-oriented,like for bookkeeping for example. 
     - OLAP stands for __Online Analytical Processing__. which is oriented around analytics. are oriented around a certain subject that's under analysis, like last quarter's book sales. 
     - Use cases: OLTP focus on supporting day-to-day operations, while OLAP tasks are vaguer and focus on business decision making. ![img](images/03_30.png)  ![img](images/03_31.png)  
- OLTP and OLAP working together
     - OLTP data is usually stored in an operational database that is pulled and cleaned to create an OLAP data warehouse. Analyses from OLAP systems are used to inform business practices and day-to-day activity, thereby influencing the OLTP databases.
#### Storing data
- Data can be stored in three different levels: Structured data(SQL, tables in a relational database), Unstructured data(photos, chat logs, MP3), Semi-structured data(NoSQL, XML, JSON). \
  The semi-structured data does not follow a larger schema, rather it has an ad-hoc self-describing structure.
- Databases: Traditional database(operational database, used for OLTP), Data warehouse(OLAP), Data lake.
     - ![img](images/03_32.png) \
       We use the term "traditional databases" because many people consider data warehouses and lakes to be a type of database
     - ![img](images/03_33.png) \
       Data warehouses are optimized for __read-only__ analytics. In their database design, they typically use dimensional modeling and a denormalized schema. \
       Amazon, Google, and Microsoft all offer data warehouse solutions, known as Redshift, Big Query, and Azure SQL Data Warehouse, respectively. \
       A data mart is a subset of a data warehouse dedicated to a specific topic. Data marts allow departments to have easier access to the data that matters to them.  
     - Data Lakes
       ![img](images/03_34.png) \
       why lower cost in data lakes? Data Lake storage is cheaper because it uses object storage as opposed to the traditional block or file storage. This allows massive amounts of data to be stored effectively of all types, from streaming data to operational databases. \
       Lakes are massive because they store all the data that might be used. Data lakes are often petabytes in size - that's 1,000 terabytes! Unstructured data is the most scalable, which permits this size. \
       __Lakes are schema-on-read__, meaning the schema is created as data is read. __Warehouses and traditional databases are classified as schema-on-write__ because the schema is predefined. \
       Data lakes have to be organized and cataloged well; otherwise, it becomes an aptly named "data swamp." Data lakes aren't only limited to storage. It's becoming popular to run analytics on data lakes. This is especially true for tasks like deep learning and data discovery, which needs a lot of data that doesn't need to be that "clean." \
- Two different approaches for describing data flow: ETL and ELT
     - ![img](images/03_35.png) 
     - ETL is the more traditional approach for warehousing and smaller-scale analytics. In ETL, data is transformed before loading into storage - usually to follow the storage's schema, as is the case with warehouses.
     - ELT has become common with big data projects. __In ELT, the data is stored in its native form__ in a storage solution like a data lake. __Portions of data are transformed for different purposes, from building a data warehouse to doing deep learning.__
#### Database design
- What is database design?
     - ![img](images/03_36.png) \
       Database design determines how data is logically stored. This is crucial because it affects how the database will be queried, whether for reading data or updating data. \
       There are two important concepts to know when it comes to database design: Database models and schemas. \
       Database models are high-level specifications for database structure. The relational model, which is the most popular, is the model used to make relational databases. It defines rows as records and columns as attributes. It calls for rules such as each row having unique keys. There are other models that exist that do not enforce the same rules. \
       A schema is a database's blueprint. In other words, __the implementation of the database model. It takes the logical structure more granularly by defining the specific tables, fields, relationships, indexes, and views a database will have.__ Schemas must be respected when inserting structured data into a relational database.
- __Data modeling__
     - ![img](images/03_37.png) \
       The first step to database design is data modeling. This is the abstract design phase, where we define a data model for the data to be stored. \
       There are three levels to a data model:
          - A conceptual data model describes what the database contains, such as its entities, relationships, and attributes.
          - A logical data model decides how these entities and relationships map to tables.
          - A physical data model looks at how data will be physically stored at the lowest level of abstraction. \
       These three levels of a data model ensure consistency and provide a plan for implementation and use.
- An example: where we want to store songs?     
     - ![img](images/03_38.png) \
        In this case, the entities are songs, albums, and artists with various pink attributes. Their relationships are denoted by blue rhombuses. Here we have a conceptual idea of the data we want to store. \
       Here is a corresponding schema using the relational model. The fastest way to create a schema is to translate the entities into tables. But just because it's the easiest, doesn't mean it's the best. \
     - some other ways this ER diagram could be converted. \
       ![img](images/03_39.png) \
       you could opt to have one table because you don't want to have to run so many joins to get song information. \
       Or, you could add tables for genre and label. Many songs share these attributes, and having one place for them helps with data integrity. \
       The biggest difference here is how the tables are determined. There are different pros and cons to these three examples I've shown. The next chapter on normalization and denormalization will expand on this.
- __Dimensional modeling__ (beyond relational model)
     - ![img](images/03_40.png) \
       Dimensional modeling is an adaptation of the relational model specifically for data warehouses. It's optimized for OLAP type of queries that aim to analyze rather than update. To do this, it uses the star schema. The schema of a dimensional model tends to be easy to interpret and extend. This is a big plus for analysts working on the warehouse.
     - ![img](images/03_41.png) \
       Dimensional models are made up of two types of tables: __fact and dimension tables__.
       What the fact table holds is decided by the business use-case. It contains records of a key metric, and this metric changes often. Fact tables also hold foreign keys to dimension tables. \
       Dimension tables hold descriptions of specific attributes and these do not change as often. \
       ![img](images/03_42.png) \
       What does that mean? e.g. The turquoise table is a fact table called songs. It contains foreign keys to purple dimension tables. These dimension tables expand on the attributes of a fact table, such as the album it is in and the artist who made it. The records in fact tables often change as new songs get inserted. Albums, labels, artists, and genres will be shared by more than one song - hence records in dimension tables won't change as much. __Summing it up, to decide the fact table in a dimensional model, consider what is being analyzed and how often entities change.__

### Database Schemas and Normalization
#### Star and snowflake schema
- Star schema
     - ![img](images/03_43.png) \
       The star schema is the simplest form of the dimensional model. Some use the terms "star schema" and "dimensional model" interchangeably.
     - e.g. you work for a company that sells books in bulk to bookstores across the US and Canada. You have a database to keep track of book sales. Let's take a look at the star schema for this database. \
       ![img](images/03_44.png) \       
       Excluding primary and foreign keys, the fact table holds the _sales amount_ and _quantity_ of books. \
       It's connected to dimension tables with details on the books sold, the time the sale took place, and the store buying the books. \
      The lines connecting these tables represent a one-to-many relationship. e.g., a store can be part of many book sales, but one sale can only belong to one store.    
- Snowflake schema
     - ![img](images/03_45.png) \
       The snowflake schema is an extension of the star schema.
       Off the bat, we see that it has more tables. The information contained in this schema is the same as the star schema. In fact, the fact table is the same, but the way the dimension tables are structured is different. 
     - The star schema extends one dimension, while the snowflake schema extends over more than one dimension. This is because the dimension tables are normalized.       
- What is normalization? 
     - ![img](images/03_46.png) \
       Normalization is a technique that divides tables into smaller tables and connects them via relationships. The goal is to reduce redundancy and increase data integrity.
       So how does this happen? There are several forms of normalization. But __the basic idea is to identify repeating groups of data and create new tables for them__. 
     - Let's go back to our example and to see how these tables were normalized. __i.e. how to normalize databases to different extents.__
          - book dimension:
            ![img](images/03_47.png)      ![img](images/03_48.png) \
            Here's the book dimension in the star schema. What could be repeating here? Primary keys are inherently unique.
            For book titles, although there is possible repeat here, it is not common. On the other hand, authors often publish more than one book, publishers definitely publish many books, and a lot of books share genres.
            We can create new tables for them, and it results in the following snowflake schema: these repeating groups now have their own table.
          - Store dimension:
            ![img](images/03_49.png)     ![img](images/03_50.png)  \
            store dimension: City, states, and countries can definitely have more than one book stores within them.
            Notice: the way we structure these repeating groups is a bit different from the book dimension.? An author can have published in different genres and with various publishers, hence why they were different dimensions. However, a city stays in the same state and country; thus, they extend each other over three dimensions.                  
          - Time dimension:
            ![img](images/03_51.png) \
            A day is part of a month that is part of a quarter, and so on!
- Practice:
     - Adding foreign keys(???): ![img](images/03_52.png)   ![img](images/03_53.png) 
     - Extending the book dimension(??): ![img](images/03_54.png)
            
#### Normalized and denormalized databases
- example: star schema with denormalized dimension tables, snowflake schema with normalized dimension tables. The normalized database looks way more complicated. And it is in some ways.
     - For example, let's say you wanted to get the quantity of all books by Octavia E. Butler sold in Vancouver in Q4 of 2018.
       Query based on star schema: 3 jions. ![img](images/03_55.png) \
       Query based on snaowflakes schema: 8 jions.  ![img](images/03_56.png)   ![img](images/03_57.png) \
       The normalized snowflake schema has considerably more tables. This means more joins, which means slower queries. 
- Why would we want to normalize a database? 
        ![img](images/03_59.png) 
     - __Normalization saves space__
       Denormalized databases enable __data redundancy__ (It has a lot of repeated information). Normalization eliminates data redundancy. 
     - __Normalization ensures better data integrity.__
       ![img](images/03_58.png)  \
       First, it enforces __data consistency__. Data entry can get messy, and at times people will fill out fields differently. For example, when referring to California, someone might enter the initials "CA". __Since the states are already entered in a table, we can ensure naming conventions through referential integrity.__
       Secondly, because duplicates are reduced, __modification of any data becomes safer and simpler__. Say in the previous example, you wanted to update the spelling of a state - you wouldn't have to find each record referring to the state, instead, you could make that change in the states table by altering one record. From there, you can be confident that the new spelling will be enacted for all stores in that state.
       Lastly, since tables are smaller and organized more by object, __its easier to alter the database schema.__ You can extend a smaller table without having to alter a larger table holding all the vital data.
- Advantages and disadvantages of database normalization:
     - ![img](images/03_59.png)
       Now normalization seems appealing, especially for database maintenance. However, normalization requires a lot more joins making queries more complicated, which can make indexing and reading of data slower.
       Deciding between normalization and denormalization comes down to __how read- or write- intensive your database is going to be__.
     - OLTP vs OLAP:
       ![img](images/03_60.png) \
       OLTP is write-intensive meaning we're updating and writing often. Normalization makes sense because we want to add data quickly and consistently.
       OLAP is read-intensive because we're running analytics on the data. This means we want to prioritize quicker read queries. 

#### Normal forms


### Database Views


### Database Management


![img](images/03_29.png)





 


