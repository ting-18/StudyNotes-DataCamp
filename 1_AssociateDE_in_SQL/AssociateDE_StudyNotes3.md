[_Previous (AssociateDE_StudyNotes1)_](AssociateDE_StudyNotes1.md)
[_Previous (AssociateDE_StudyNotes2)_](#AssociateDE_StudyNotes2.md)

# Associate Data Engineering in SQL - PART III

### Table of contents - PART III

- [Data Warehousing](#data-warehousing)
   - [Data Warehousing Concepts](#data-warehousing-concepts)
        - [Data Warehouse Basics](#data-warehouse-basics)
        - [Warehouse Architectures and Properties](#warehouse-architectures-and-properties)
        - [Data Warehouse Data Modeling](#data-warehouse-data-modeling)
        - [Implementation and Data Prep](#implementation-and-data-prep)
   - [Introduction to Snowflakes](#introduction-to-snowflakes)
- [Understanding Data Visualization](#undersdanding-data-visualization)
     - [Project: Exploring London's Travel Network](#project-exploring-londons-travel-network)
- Tutorial: How to Install PostgreSQL


# Data Warehousing

## Data Warehousing Concepts
### Data Warehouse Basics
#### What is a data warehouse?
- what is a data warehouse?
  A data warehouse is a computer system designed to store and analyze large amounts of data for an organization.  \
  The warehouse becomes __a central repository for clean and organized data for the organization.__ It does this by gathering data from different areas of an organization, integrating it, storing it, then making it available for analysis. \
  Organizations value data warehouses because they support business intelligence activity and enable analysis and decision-making, fostering data-driven innovation. Moreover, using data warehouse, the query will run on a large amount of data quicky. avoid slowing down transactional database.
- common scenarios
  Bravo, a hypothetical __publicly traded company__ that sells fancy home office furniture.  \
  Bravo might utilize its data warehouse for product forecasting. Their data warehouse aggregates their historical sales by customer and product, which is needed to forecast future demand. In addition, Bravo has specific regulations and governance it must adhere to as a publicly traded company. \
  Bravo's employees could prepare reports from the data warehouse to provide to auditors. The data warehouse could be used to confirm Bravo's adherence to the rules because the data warehouse is a store of financial transactions and customer information. \
  Finally, through analysis of their sales, Bravo noticed their sales growth is accelerating in Asia. Therefore, HR and Operations might use their production and employee data to prepare for hiring more staff to support their sales growth in Asia.

#### What's the difference between data warehouses and data lakes?
- database
  
- data warehouse
     -  a single data repository for analysis
     - They are built as a central data store for the entire organization, representing many departments.
     - it is more complicated to change because of upstream and downstream impacts
     ![img](images/04_01.png)
- data marts
     - A data mart only focuses on one department, such as just Finance.
     - A data mart is a relational database that stores an organization's transactional data for analysis.
     - Data marts and data warehouses both hold structured data.
  ![img](images/04_02.png)
- data lakes
     - Data lakes, similar to data warehouses, are built as a central store of data for the entire organization for analysis.
     - Data lakes are easier to change but may contain data with an unknown purpose. (because of their flexibility in storing unstructured data. This flexibility also allows storing data whose purpose may not be known today but may be helpful for future analysis. )  
       ![img](images/04_03.png)

#### Data warehouses support organizational analysis
- Data warehouse life cycle:
  Use Case: Companies are willing to invest a large amount of money into developing a data warehouse for the potential insights they can bring. 
1. Planning - the team begins to plan how to design the data warehouse to satisfy the organization's needs.
     - __business requirements__: understand the organization's needs. Who and how will they use the data warehouse?
       \ e.g. Data Analyst works closely with others in the business, and her analysis helps with decision-making. 
       \ Data Scientist's ML models automate decision-making and improve business processes. __DA and DS are closer to the business and know their final goals and requirements best.__
     - __data modeling__: how the team transforms data from different input sources and integrates it into our data warehouse. Crucial is that the team understands and links the relevant data sets.
       \ a data engineer is skilled at creating data pipelines. Data pipelines are automatic end-to-end processes that collect, modify, and deliver data.
       \ Data engineers and transactional database admin __plan data pipelines__ from the database systems to the warehouse.
       \ Also, DA and DS use their __business knowledge__ to support this stage by helping ensure the data model accurately represents the organization.
2. Implementation - the team builds the data warehouse
     - __ETL Design & Development__: designs and builds the ETL process from the different sources into the data warehouse. 
       \  DE is responsible for creating the pipelines, but she coordinates with Database Admin to extract data from the source systems.
     - __BI Application Development__: set up BI or business intelligence tools to interact with the data warehouse and create reports needed by the organization.
       \ BI tools are often how many users interact with the data warehouse. Some standard BI tools include Tableau, Power BI, or Google's Looker. At this point, DA and DS consult on the setup of the BI application.

3. Support/Maintenance - the team trains end users and maintain the warehouse.
     - __Maintenance__: update the warehouse table designs or make other necessary changes.
       \ __DE__ makes these changes if required.
     - __Test & Deploy__:
       \ DA and DS test the system to confirm their business requirements are met.
       \ Afterward, DE will deploy and make the warehouse available to the organization.
       \ After deployment, any significant changes will follow the same steps starting back at the planning phase.
- Persona matrix  ![img](images/04_04.png)

### Warehouse Architectures and Properties
#### What are the different layers of a data warehouse?
- Four layers: source, staging, storage, presentation
1. Data source layer: all data sources(Transactional database, log files, spreadsheets, files)
2. Data staging layer: contains ETL process and storage tables
      - the ETL process stages data or temporarily places it in tables so it is ready to be used in later steps of the process.
      - ETL process within data staging layer
        - transforms data by applying business rules(like averaging), it uses staging tables here to store the results between the various transformations temporarily.
        - Able to extract valid data from unstructured data source into rows and columns(structured data)
        - The staging layer outputs data that is ready to be stored. The data can be loaded to the next layer in batches or all at once.
3. Data storage layer: data is stored in warehouse and data marts
4. Data presentation layer: 
   - the information is made available to the end user in the data presentation layer.
   - BI or Business Intelligence tools, data mining tools, analysis tools, Reporting tools(dashboard), and direct user queries connect to the warehouse and allow the end user to interact with the data. 

#### The presentation layer: tools and how users interact with data in data warehouse
- Automated reporting/dashboarding tools
     - Goal: create reports needed for decision making; create dashboards using historical data
     - Users: analysts, citizen data scientist use tools to create reports automatically or update dashboards.
     - Tools: Tableau, Power BI, Google's Looker, and SAP BW(SAP Business Warehouse)
     - These tools tend to have graphical user interfaces with little coding required to use. 
- BI/data analytics: used to explore data and find patterns. These tools may have a graphical drag-and-drop interface.
     - Goal: BI and data mining tools are often used to explore the data and uncover patterns. 
     - Users: Analysts and Data Scientists use these tools to convert data into actionable insights. 
     - Tools: Oracle Data Mining, RapidMiner, Alteryx, and KNIME are focused on data analytics and mining.
- Direct query
     - Tools: SQL Server Management Studio, Azure Data Studio, and the popular programing tools of R and Python.
#### Data warehouse architectures
\ 
- Inmon - top-down: ETL -> Data Warehouse -> Data marts
     - Essential to this architecture is that the organization __must decide on the naming, the definition, which data is valid if there are conflicts, and all other data cleaning operations on all of the data before it enters the warehouse__.
     - this architecture __stores data in the warehouse in a normalized form__.
     - The data then moves to a department-focused data marts where end users and applications can query it.
- Pros and cons of Inmon - top-down
     -  ![img](images/04_05.png)
       \ ?? conforming the input sources into a single definition that the organization agrees upon makes the data warehouse an effective source of truth.   creating new data marts for reporting or analysis is relatively straightforward.
       \ ?? As you might imagine, gaining alignment by the organization on the data definitions can take a lot of upfront work resulting in a higher startup cost for warehouse projects. WHy? -> The top-down approach has longer upfront development time before users can deliver reports __due to aligning the organization on all data definitions and cleaning rules__.
- Klmaball - bottom-top: ETL -> Data marts -> Data Warehouse
     - Various data attributes, such as name and location, connect the data marts. The data marts are then integrated into a data warehouse. 
- Pros and cons of Klmaball - bottom-top
     - ![img](images/04_06.png)
       \ how fast it can get up and running by taking an incremental approach resulting in lower upfront costs on warehousing projects. Also, the denormalized data model makes the data easy to consume for users.
       \ However, denormalization increases the processing time within the ETL process and can create duplicate data when used across different data marts. Duplicate data can confuse and make the data warehouse less of a source of truth.
       \ An additional disadvantage is as the organization adds new departments or processes, more development will need to be done.(because of denormalized data)
       \ The bottom-up approach has a lower upfront cost but requires more upkeep than the top-down approach.
- Practice:
     - use cases:  imagine you work as a data warehousing consultant engaged with Northwestern insurance company. The company has seen much growth over time and wants to consolidate systems into a data warehouse. The company must connect the data from one department to all other departments and avoid data duplication, creating a single source of truth. Also, they would like to minimize the data storage size if possible. Finally, Northwestern's management wants a smooth implementation and would prefer the project team take the time needed to get it right the first time.
#### OLAP and OLTP systems
Another core component of Data warehouse implementations: OLAP, OLTP
- OLAP systems : Online analysis processing
     - OLAP is a tool for __performing multidimensional analysis at high speeds on large volumes of data from a data warehouse, data mart, or some other centralized data store__.
     - In data warehousing, most organizations have data __organized into different dimensions__, such as sales figures by country, state, and city. Another dimension example is time, broken into years, months, and days.
     - OLAP systems take this two-dimensional representation of data in rows and columns and __reorganize it into a multidimensional format__ that enables fast processing for analysis. This multidimensional format allows for what is commonly called "slicing and dicing" the data. Data scientists and analysts typically work with OLAP systems.
- OLAP cube
     - ![img](images/04_07.png)
     - At the core of the OLAP system is the OLAP data cube, a multidimensional database that makes it possible to process and analyze multiple data dimensions faster than a traditional relational database.
     - The data cube can __drill down or aggregate__ the total sales by each dimension.
     - Data cubes that have more than three dimensions are called hypercubes.
     - e.g. imagine we are interested in the organization's sales by region, year, and product. If we picture a cube, the cube's different edges, or height, width, and length, will represent one of these dimensions. We will have the total sales for those dimensions where these edges intersect. The data cube can drill down or aggregate the total sales by each dimension. In this example, the dimensions are region, year, and product, and total sales is the value that is aggregated or disaggregated based on the selected dimensions.
- OLTP: Online transaction processing
     - Typical uses of OLTP systems include cash terminals and reservation bookings. In these examples, the OLTP systems __processes simple queries to the database, like inserting, updating, and deleting rows__. Queries for OLTP systems tend to affect only a few rows of data within the database.
     - OLTP systems are often critical __for the business and not used for analysis__. Organizations often use them in __transactional databases or the source systems that feed into the data warehouse__.
- Example for a credit card company
     - DE is responsible for maintaining the OLTP system that tracks each customer's purchase and updates their current balance. This system was designed to __track thousands of purchases and their updates every second__.
     - For analyzing customer purchasing patterns. team uses a data warehouse and an OLAP system that __tracks purchases by year, customer age, location, and time of day__. Analysts within the company use this data to make business decisions.
  
### Data Warehouse Data Modeling
#### Data warehouse data modeling
- What is data modeling?
     \ __Data modeling refers to how we organize data in a database into tables and how to relate those tables if we want to join them.__
- Data models
     - 
- Fact table
- Dimension table
- Star Schema
- Snowflakes Schema
#### Kimball's four step process

#### Slowly changing dimensions

#### Row vs. column data store



### Implementation and Data Prep
#### ETL and ELT
#### Data cleaning
#### On premise and cloud data warehouses
#### Data warehouse design example
#### Wrap-up



## Introduction to Snowflakes





# Understanding Data Visualization
## Project: Exploring London's Travel Network
# Tutorial: How to Install PostgreSQL
