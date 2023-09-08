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