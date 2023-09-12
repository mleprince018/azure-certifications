# DP-203 Azure Data Engineering

![Overview of DEngr](./pictures/./DP-203/overview-data-engineering-azure.png)

# Intro to DEngr on Az

## Concepts

- DEngr integrate, transform & consolidate data from various structured/unstructured data systems into systems suitable for analytics
- ensure data pipelines & stores are HiPerf, efficient, organized, & reliable under biz requirements and org constraints
- **Types of Data**:
  - structured: tables, from RDBMS, or csv where rows/cols are consistent throughout dataset
  - semi-struct: JSON, from NoSQL servers, etc the schema is not strict and can vary
  - unstruct: pictures - typically binary stored files
- **Data Operations**:
  - *Data Integration*: est links between ops & analytical services & data sources to enable secure, reliable access to data
  - *Data Transformation*: transform using ELT|ETL processes to adjust to fit use-cases or storage reqs
  - *Data Consolidation*: process of combining data that has been extracted from multiple data sources into a consistent structure - generally to support analytics|reporting
- **Common Languages**: SQL, Python, PySpark, Scala, R, Java, .NET...

- **Operational vs Analytical Data**:
  - ops data is typically Tx data generated & stored by apps or LOB services typically in a RDBMS
  - analytics data is data optimized for analysis/reporting typically in DW
  - DEngr design, implement & manage solutions to integrate from these sources/systems etc into analytics store so that others can perform analysis
- **Streaming Data**:
  - perpetual source of data that generate data in RT often based on events (IoT & Social Media)
  - DEngr implement solutions to capture, ingest, query and store this data
- **Data Pipelines**:
  - orchestrate activities that transfer & transform data - primary way to organize, manipulate & schedule ETL|ELT activities
- **Data Lakes**:
  - storage repo that can hold large amounts of data (TB or PB) in raw/native formats of struct, semi-struct, unstruct
- **DW**:
  - central repo of integrated data from multiple sources for reporting/analytics use-cases
- **Apache Spark**:
  - in-memory parallel processing framwork that uses distr fs for big data transformation/analysis

### Azure Services that Support DEngr

- Data sources: Azure SQL DB, Azure Cosmos DB, Azure Dataverse, Azure Event Hubs
- Data transformations: Azure Synapse, Azure Stream analytics, AzDF, Azure Databricks, Az HD Insight
- Other storage: Azyre Synapse & DL Storage Gen2
- Reporting & Vizualization: PowerBI

## Az DL Storage (ADLS) Gen2

- Orgs have spent decades building DW & Biz Intelligence solutions on RDBMS - but they can't get insights from semi|unstruct data
- DL are the alternative to DW because it's a distr fs that can massively scale to any data type and feed analytics engines like Spark
- this underpins many analytics tools run in Azure
- "silver bullet" DL solution for all analytics use-cases, cost effective, scalable, secure...

- **Benefits**:
  - can perform tiering and data lifecycle mgt of blob storage, is HA, secure, durable and performant
  - can handle streaming & batch processing
  - Hadoop compatible access through HDFS - to access through Databricks, HDInsight, Synapse analytics without duplication
  - security through ACLs & POSIX (does not have cascading permission sets using POSIX)
    - set at folder/file level and configured through Hive/Spark or Az Storage Explorer
    - data stored is encrypted using MSFT or Customer Managed Keys (CMKs)
  - performant - can use a hierarchy of directories & subdirectories
  - Durability through data redundancy

- **Data Lake Design**: should consider type(s) of data to be stored, how it will be transformed, who will access the data, access patterns...
  - can help plan ACL design and avoid "Data Swamp" anit-pattern

- **ADLS vs Blob Storage**
  - BLOB: you can store large amounts of object data in a flat namespace
    - the '/' within folders can be used as logical separators but do not operate as folders do ~ more like underscores in a VERY long var name
    - accessed through HTTP/HTTPS
  - ADLS Gen2 expands on blob storage functionality
    - uses hierarchical namespace that optimizes I/O inside of directories & stores metadata there
    - folder rename/deletes can be done in one operation vs modifying EVERY file with that name in the "path" of the blob
    - uses APIs to access data

- **Configuring ADLS**
    1. During storage account creation, select the "Enable Hierarchical Namespace" on the Advanced tab
    2. take an existing storage account and use the upgrade button to upgrade it (non-reversible - once hierarchical namespace is enabled it cannot be undone)

### ADLS Use-Cases

- can be used in EDW, Big Data Processing & Analytics, Data Science & AI/ML, RT Streaming
  - **EDW**:
    - Typically ADLS *feeds* EDW inside of DW tool
    - DL can turn into a "lakehouse" where a metadata layer can nbe placed over files in data lake - so it can be used directly for analytical queries/reports
  - **Big Data processing & Analytics (Spark)**:
    - "volume - variety - velocity" of data for Synapse, Databricks, HDInsight for MPP compute/analytical tasks on distr data
  - **RT Analytics (Streaming)**:
    - streaming is a growing use-case for IoT, Logs, Social Media & variety of other producers
    - solution needs to be able to capture a "boundless" stream of events as they occur
    - generally captured in a queue for processing (Az Event Hub/Kafka...) or ADLS file, processed & then output delivered to a "sink" like ADLS
  - **DS & ML**:
    - statistical analysis of large vols of data - using spark, python etc...
    - ML is a subarea of DS that deals with training predictive models on vast amounts of data
    - can use Az ML to code python in nnotebooks
- in general 4 stages:
    1. Ingest: acquire source data from files|logs|unstruct... and you use different services to ingest data as needed
    2. Store: ID where ingested data should be stored (ADLS is a great choice here...)
    3. Prep & Train: perform data prep & model training/scoring for ML - Synapse, Databricks, HDInsight, AzML
    4. Model & Serve: Visualization tools to present data to users through analytical stores or various combinations of tools

## Az Synapse

- Data generated by indv/orgs is growing and can provide basis for descriptive, diagnostic, predictive, prescriptive solutions
- There are many analytical tools available, you can use Azure Synapse for a single simple solution that works for all stakeholders
- [Synapse Documentation Link](https://learn.microsoft.com/en-us/azure/synapse-analytics/overview-what-is)

### Overview

- **Types of Analytics:**
  - Descriptive:  WHAT HAPPENED? - generally historical data in DW that gets reported on
  - Diagnostic:   WHY HAPPENED? - explore info that exists in DW but generally involves wider search for info to support analysis
  - Predictive:   WHAT WILL HAPPEN? - scenario analysis to PREDICT | FORECAST
  - Prescriptive: WHAT SHOULD I DO NEXT? - enables decision making on RT analysis of data to optimize for a particular decision
- all these analytics go through variety of storage, processing etc - and with synapse integration, can use SQL, spark, with central hub & consistent UI

### Using Synapse

- extensible through **linked services** to integrate with data stores, other processing platforms & visualization tools
- **Creating a workspace**
  - *Workspace*: an instance of synapse analytics service where you can manage the services & data resources for analytics
  - can be created in:
    - Az Portal GUI by clicking around
    - auto deploy using az powershell, CLI, ARM Template or Bicep Template
    - you login to perform tasks using *Synapse Studio*: web-based portal for synapse
- **Data Lakes in Synapse**
  - key resource for a workspace is the data lake - where data can be stored/processed at scale
  - workspace generally has a default datalake - implemented as a linked service to an ADLS Gen2 container
    - can add other data lakes or storage platforms as needed
- **Ingesting/Transforming Data**
  - Synapse helps you create, run & manage pipelines to orchestrate activities to pull & transform data and load data into targets
    - these can be scheduled to run at regular intervals using triggers
  - same tech as AzDataFactory
- **SQL in Synapse**
  - Can use 2 kinds of SQL "Pools" that are based on SQL Server RDBMS
    - Serverless pool: optimized for relational SQL semantics to query file-based data in DL
      - great for cheap & basic analysis
    - Dedicated SQL Pool: (this is the DW solution) host relational DW - for enterprise data modeling/reporting
  - uses distr query processing to parallelize SQL ops -> high scalable solution for processing
- **Spark in Synapse**
  - OSS platform for big data anlytics by processing DL files using Py, Scala, Java, SQL & C#
  - Notebooks described "analyze data using ABC coding language combined with text descriptions gained from the analysis" = Markdown in notebooks
  - can create 1+ spark pools and use interactive notebooks to combine code/notes as you build data analytics|ML|data viz notebooks
- **Data Explorer**
  - data processing engine in synapse that's based on Az's regular Data explorer service
  - uses Kusto Query Language KQL to enable HiPerf analysis of batch/streaming data
- **Integrations**
  - *Azure Synapse Link*: near RT synch btw ops data in Az CosmosDB, Az SQL DB, SQL Server, Dataverse, analytical storage...
  - *PowerBI*: enables data analysts to integrate PowerBI workspace into Synapse for data viz building in Synapse studio
  - *Purview*: enables orgs to catalog data assets in synapse - easier for Data Engr to find data, track lineage...
  - *Az ML*: enables data analysts & DS to integrate predictive model training & consumption into analytical solutions

- View the Data page, and note that there are two tabs containing data sources:
  - A Workspace tab containing databases defined in the workspace (including dedicated SQL databases and Data Explorer databases)
  - A Linked tab containing data sources that are linked to the workspace, including Azure Data Lake storage.
- View the Develop page, which is currently empty. This is where you can define scripts and other assets used to develop data processing solutions.
- View the Integrate page, which is also empty. You use this page to manage data ingestion and integration assets; such as pipelines to transfer and transform data between data sources.
- View the Monitor page. This is where you can observe data processing jobs as they run and view their history.
- View the Manage page. This is where you manage the pools, runtimes, and other assets used in your Azure Synapse workspace. View each of the tabs in the Analytics pools section and note that your workspace includes the following pools:
  - SQL pools:
    - Built-in: A serverless SQL pool that you can use on-demand to explore or process data in a data lake by using SQL commands.
    - sqlxxxxxxx: A dedicated SQL pool that hosts a relational data warehouse database.
  - Apache Spark pools:
    - sparkxxxxxxx: that you can use on-demand to explore or process data in a data lake by using programming languages like Scala or Python.
  - Data Explorer pools:
    - adxxxxxxxx: A Data Explorer pool that you can use to analyze data by using Kusto Query Language (KQL).

### Use-cases for Synapse

- Large Scale DW: integrate data for analytics/reporting independent of data loc or struct
- Advanced Analytics: perform predictive analytics with native features/integrations
- Data Exploration & Discovery: use serverless SQL pool to sql explore and do diagnostic analytics, exploratory analytics...
- RT Analytics: using Az Stream Analytics or Data explorer
- Data Integraiton: synapse pipelines for ingesting, prep model & serve data
- Integrated analytics: put services together into 1 cohesive solution by integrating analytics landscape into 1 service without backend IT mgt

# T-SQL Basics

## Introduction

- SQL standard was defined by ANSI - but each vendor has added variations & extensions
  - basics like SELECT|INSERT|UPDATE|DELETE are available no matter what the RDBMS - but extension functionality varies
  - MSFT uses T-SQL "Transact-SQL"
- a **DECLARATIVE** language: describe the output you want, and leave details of the steps required up to the execution engine
  - this is opposed to *procedural* where you give the computer instructions on what/how it should do
  - you describe results you want & query processor develops a query plan to retrieve it 
- most often used to query relational dbs, PKs & FKs, composite keys... 
- set-based processing: set-theory is fundamental to rdbms 
  - set: collection of definite, distinct objects considered as a whole
  - for SQL, the customer table represents a "set" and a SELECT statement also forms another set 
  - you operate on the "set" of data and not individual members - your actions/operations occur "all at once" 
  - no concept of "process ordering members" first,second,last row... don't matter for processing logic, just that they are members of a set that NEED to be processed
