[_Previous StudyNotes_](AssociateDE_StudyNotes1.md)
# Associate Data Engineering in SQL - PART II
### Table of contents - PART II

- [Database](#database)
   - Introduction to Relational Database in SQL
   - Database Design
- [Data Warehousing](#data-warehousing)
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
   Surrogate keys are sort of an artificial primary key. In other words, they are not based on a native column in your data, but on a column that just exists for the sake of having a primary key. 
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

 
 
 
 
 ![img](images/03_21.png)

- 




You will add a special type of primary key, a so-called __surrogate key__, to the table "professors" in the last part of this chapter.




#### 2.3. Glue together tables with foreign keys












## Database Design
# Data Warehousing

## Data Warehousing Concepts
# Understanding Data Visualization
## Project: Exploring London's Travel Network
# Tutorial: How to Install PostgreSQL
