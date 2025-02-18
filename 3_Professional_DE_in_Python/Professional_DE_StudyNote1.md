Professional Data Engineer in Python

Table of Content I
- [Inroduction to dbt](#inroduction-to-dbt)



# Understanding Modern Data Architecture
# Introduction to Shell

# Inroduction to dbt
You'll learn to build data warehouses, perform data modeling and transformations, and design tests to perform data validation.  learn how to generate documentation for your warehouse users
## Welcome to dbt
### What is dbt?
- What is dbt?
    - dbt, also known as the __data build tool__, is designed to simplify the management of data warehouses and transform the data within. This is __primarily the T, or transformation__, within ELT (or sometimes ETL) processes. \
    - It __allows for easy transition between data warehouse__ types (such as Snowflake, BigQuery, Postgres, or DuckDB. dbt is ideal for teams, including those with analysts and engineers.
    - dbt also __(provides source/code control)__ provides the ability to define SQL and transformations in source-controlled environments (where typically this is difficult to accomplish.)
- What does dbt do?
    - dbt __primarily defines data models and the transformations of those models using SQL__. (Newer versions of dbt can also use Python)\
    Here, a data model represents the structure of your data and how its elements relate. 
    - dbt __translates between SQL dialects__ as appropriate to connect to different data sources and warehouses.
    - It can __define the relationships between data models__ and manage the dependencies that arise when using them.
    - dbt actually __runs the transformation process (or processes) when requested__.
    - Finally, dbt can also __test and verify the data matches user-defined quality requirements__. We'll cover all of these in later videos.
- What does dbt look like?
    - Command-line tool, also known as dbt-core.
    - Adapters provide connections to different data warehouses: dbt-snowflake, dbt-bigquery, and dbt-sqlserver.  some managed as part of the project, others that are managed by third-party volunteers or companies. 
    - There is also a managed version of dbt known as dbt Cloud. 
- dbt subcommands
    - dbt  or dbt -h : Show help content
    - dbt <subcommand> -h : Help for subcommand
    - dbt init :Creates new dbt projects
    - dbt run : Runs the data generation/transformations process (and pushes updates to the warehouse. It should be run whenever there are model changes, or when the data process needs to be materialized.)
    - dbt test :Run the data quality tests available within dbt projects.
    - dbt debug : Can check connections to data warehouses or database.
    - See more in dbt -h or dbt documentations
### Creating a dbt project
- What is a dbt project?
    - Projects __encompass all the needed (and optional) components for working with data within dbt__.
        - __Project configuration__ includes the project name, folder names, etc.
        - __Data sources and destinations__
        - __SQL queries and templates__ that define how to access and transform the data into the desired formats.
        - A dbt project can also include documentation for the data and the relationships within it.
    - It's important to note that a dbt project is __implemented as a folder structure__ on a given machine. As such, it can be easily copied, modified, or placed into source control as needed.
    - ![img](images/03_01.png)
- How to create a new project? 
    - ![img](images/03_02.png)
    - dbt init will then create the top-level project folder and subfolders and configuration files for the project. 
- Defining configuration with project profiles
    - understand about dbt projects is the idea of a profile.
    - Within dbt, __a profile is most analogous to a given deployment scenario__. This can include __development, staging or testing, and production__.
    - Each profile can be defined as the user sees fit. __Multiple profiles can exist within a given dbt project__, allowing for different warehouse configurations based on the deployment scenario.
    - These profiles (ie, configurations) are __defined in the profiles.yml (or profiles dot yaml) file__, which is not automatically created on a new project.
    - This is an example profiles.yml file with two deployment types (dev and prod), and the default, as set by the option target, is currently set to dev. ![img](images/03_03.png)
    - DuckDB is useful for development and testing locally, while Snowflake would be better used in production as other users will likely need to access the data.
- YAML
    - YAML stands for __Yet Another Markup Language__.
    - It is __a text based file format, but whitespace indentation(spacing) matters__, much like Python.
    - YAML is __used in many development scenarios, often for configuration__, due to its relatively human-readable format.
    - The __rules for writing or modifying YAML can be tricky, but maintain indentation as illustrated in examples__. Be aware of the formatting requirements if you need to create one from scratch.
- DuckDB: is an __open-source serverless database, similar to sqlite__(This means there is not a server process required, in contrast to postgresql or mysql). It is __designed for analytics__, ie, data warehouses, and is __fast due to its vectorized nature__. __easy to use__, dbt-duckdb
- Practice
    - Project Name: nyc_yellow_taxi     Database type: duckdb
    - initializing a dbt project: bash: dbt init nyc_yellow_taxi   --> (top-level folder (nyc_yellow_taxi) and subfolders and configuration files are created: analyses, macros, snapshots, models, seeds, tests, README.md, dbt_project.yml)
    - Creating a project profile shown as below: nyc_yellow_taxi/profiles.yml. Then bash: dbt debug  :to verify there are no errors(generated two files: .user.yml, dbt.duckdb)
      ```
      nyc_yellow_taxi:
          outputs:
              dev:
                  type: duckdb
                  path: dbt.duckdb
          target: dev
      ```
### Working with a first project
- Workflow for dbt (A workflow for dbt depends somewhat on the user's needs, but typically follows as belwo)
    1. __Create project (dbt init__ or copy a working project from another location)
    2. __Define or update its configuration (profiles.yml file)__. 
    3. __Create /use models/ templates__ (you'll spend most of your time developing with dbt - defining and using the data models. basic transform data)
    4. Once the models are defined, we need to __instantiate them using the `dbt run` subcommand__. (This command will take the source SQL code you've written, translate it as necessary for your deployment target (aka, profile), and actually execute the transformation process.)
    5. When complete, you'll need to __verify and test your data and, if necessary, troubleshoot any issues.__
    6. Finally, this process is __repeated as necessary__, typically going back to the model level when a new data source is required.

Please note that in this circumstance, materialized has a specific meaning in dbt. It means to execute the transformations on the source data and place the results into tables or views in data warehouse. (e.g. You've successfully created a project and defined the required parameters to connect to your data warehouse. Your manager would like the data to be materialized into the data warehouse from the initial data source, using some example configurations a colleague tested with previously. Note that your colleagues have provided a test script called datacheck to validate the contents of the data warehouse. )
- table vs view
    - a table is __an object__ within a database or warehouse that actually holds data. These objects take up space within the database, relative to the size of data inserted into the database.
    - A view is usually defined as __a select query__ against another table or tables. As such, the content in the response is generated with each query.
## dbt models
### What is a dbt model?
### Updating dbt models
### Hierachical models in dbt
### Model troubleshooting
## Testing & Documentation
### Introduction to testing in dbt
### Creating singular tests
### Creating custom reusable tests
### Creating ang generating dbt documentation
## Implementing dbt in production
### dbt sources
### dbt seeds
### SCD2 with dbt snapshots
### Automating with dbt build
### Course review
