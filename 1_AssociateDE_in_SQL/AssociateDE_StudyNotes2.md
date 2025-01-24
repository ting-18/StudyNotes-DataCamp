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

### Why Database
why using relational databases has many advantages over using flat files like CSVs or Excel sheets?
- Story: _As a __data journalist__, I try to uncover corruption, misconduct and other newsworthy stuff with data. A couple of years ago I researched secondary employment of Swiss university professors. It turns out a lot of them have more than one side job besides their university duty, being paid by big companies like banks and insurances. So I discovered more than 1500 external employments and visualized them in an interactive graphic, shown on the left. For this story, I had to compile data from various sources with varying quality. Also, I had to account for certain specialties, for example, that a professor can work for different universities; or that a third-party company can have multiple professors working for them. In order to analyze the data, I needed to make sure its quality was good and stayed good throughout the process. That's why I stored my data in a database, whose quite complex design you can see in the right graphic. All these rectangles were turned into database tables._
       ![img](03_00.png)
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
   - e.g. ![img](03_02.png) ![img](03_03.png)
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
### Create your first database
#### Tables - the core of database (Database modeling)
- redundancy in the "university_professors" table: this professor,  his university, the "ETH Lausanne" is repeated in the first three records. The reason for this is that the table actually contains entities of at least three different types: professors, universities, organizations. ![img](03_04.png)
- entity-relationship diagram: Squares denote so-called entity types, while circles connected to these denote attributes (or columns). ![img](03_05.png)
     - In the old entity-relationship diagram, we have only modeled one so-called entity type – "university_professors". However, we discovered that this table actually holds many different entity types.
     - New: It represents three entity types, "professors", "universities", and "organizations" in their own tables, with respective attributes. This reduces redundancy, as professors, unlike now, need to be stored only once. However, one original attribute, the "function", is still missing.  
     - __??????? A better database model with four entity types: this database contains affiliations of professors with third-party organizations. The attribute "function" gives some extra information to that affiliation. For instance, somebody might act as chairman for a certain third-party organization. So the best idea at the moment is to store these affiliations in their own table – it connects professors with their respective organizations, where they have a certain function.__
       ![img](03_05.png)
- We dropped the "university_shortname" column in affiliations before migrating data, bcz it is not even needed here. why? 
     - A professor is uniquely identified by firstname, lastname only.
     - _?????? I queried the "university_professors" table and saw that there are 551 unique combinations of first names, last names, and associated universities. I then queried the table again and only looked for unique combinations of first and last names. Turns out, this is also 551 records. This means that the columns "firstname" and "lastname" uniquely identify a professor._
     - _?????? So the "university_shortname" column is not needed in order to reference a professor in the affiliations table. You can remove it, and this will reduce the redundancy in your database again. In other words: The columns "firstname", "lastname", "function", and "organization" are enough to store the affiliation a professor has with a certain organization._

#### how to create such databases from scratch? (build and maintain databases)?
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
 
#### Migrate the data (update your database as the structure changes)
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

### Enforce data consistency with attribute constraints
After building a simple database, it's now time to make use of the features. You'll specify data types in columns, enforce column uniqueness, and disallow NULL values in this chapter.
Three concepts that help preserve data quality in databases: __constraints, keys, and referential integrity__. (use constraints, keys and referential integrity in order to assure data quality.)

#### Better data quality with constraints




## Database Design
# Data Warehousing

## Data Warehousing Concepts
# Understanding Data Visualization
## Project: Exploring London's Travel Network
# Tutorial: How to Install PostgreSQL
