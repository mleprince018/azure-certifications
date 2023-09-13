# Handy SQL

## Data Query|Manipulation Language 

### Example Case where Title, MiddleName or Suffix could be NULL
- CASE is like the if/then/else structure that allows you to build multi-stage conditional expressions

```sql
SELECT TOP (1000) [CustomerID]
  ,[CompanyName] 
  , CAST(CustomerID AS varchar(8)) + ': ' + CompanyName  
  ,[Title]    
  ,[FirstName]    
  ,[MiddleName]    
  ,[LastName]    
  ,[Suffix]    
  , CASE         
    WHEN (Title IS NULL and MiddleName IS NULL and Suffix IS NULL)    THEN FirstName + ' ' + LastName        
    WHEN (Title IS NULL and MiddleName IS NULL)                     THEN FirstName + ' ' + LastName + ' ' + Suffix         
    WHEN (Title IS NULL and Suffix IS NULL)                         THEN FirstName + ' ' + MiddleName + ' ' + LastName        
    WHEN (MiddleName IS NULL and Suffix IS NULL)                    THEN Title + ' ' + FirstName + ' ' + LastName         
    WHEN (Title IS NULL)                                            THEN FirstName + ' ' + MiddleName + ' ' + LastName + ' ' + Suffix         
    WHEN (MiddleName IS NULL)                                       THEN Title + ' ' + FirstName + ' ' + LastName + ' ' + Suffix        
    WHEN (Suffix IS NULL)                                           THEN Title + ' ' + FirstName + ' ' + MiddleName + ' ' + LastName        
    ELSE Title + ' ' + FirstName + ' ' + MiddleName + ' ' + LastName + ' ' + Suffix     
  END as CustomerName    
  ,[SalesPerson]    
  , Phone    
  , EmailAddress    
  , COALESCE(EmailAddress, Phone) as ContactInfo  
FROM [adventureworks].[SalesLT].[Customer]
```

### Data Types in SQL

- SELECT clauses can perform calculations & manipulations which use operators to combine cols/values, etc through string or numeric functions *depending on col data type* 

**[SQL Server Data Types](https://learn.microsoft.com/en-us/sql/t-sql/data-types/data-types-transact-sql)**
| Exact   Numeric |     Approximate   Numeric |     Character |     Date/Time  |     Binary    |     Other          |
|-----------------|---------------------------|---------------|----------------|---------------|--------------------|
|     tinyint     |     float                 |     char      |     date       |     binary    |     cursor         |
|     smallint    |     real                  |     varchar   |     time       |     varbinary |     hierarchyid    |
| int             |                           | text          | datetime       | image         | sql_variant        |
| bigint          |                           | nchar         | datetime2      |               | table              |
| bit             |                           | nvarchar      | smalldatetime  |               | timestamp          |
| decimal/numeric |                           | ntext         | datetimeoffset |               | uniqueidentifier   |
| numeric         |                           |               |                |               | xml                |
| money           |                           |               |                |               | geography          |
| smallmoney      |                           |               |                |               | geometry           |

- Data type conversion can be explicit or implicit (SQL will try to fix it for you)
  - [View docs](https://learn.microsoft.com/en-us/sql/t-sql/data-types/decimal-and-numeric-transact-sql) for details on round up or truncate when you need certain precision level

- CAST & TRY_CAST: convert a value to a specified data type if value is compatible with target data type
  - CAST will fail if conversion doesn't fully work on all values, TRY_CAST will best effort convert what it can and leave failures as *NULL*
  - CONVERT & TRY_CONVERT are T-SQL only `CONVERT(nvarchar(30), SellStartDate, 126) AS ISO8601FormatDate`
    - *CONVERT includes an additional parameter that can be useful for formatting date and time values when converting them to text-based data.*

  ```sql
  -- size col contains values such as: (58, 92, S, M, L...)
  SELECT TRY_CAST(Size AS integer) As NumericSize
  FROM Production.Product;

  
  SELECT TOP (1000) [SalesOrderID]    
  ,[RevisionNumber]     
  -- NOTE the casting of PK & numeric as chars
  , 'SO' + CAST(SalesOrderID as varchar(8)) + ' (' + CAST(RevisionNumber AS varchar(2)) + ')' as FormattedSO    
  ,[OrderDate]    
  -- NOTE: Convert example with formatting
  , CONVERT(nvarchar(30),OrderDate, 102)    
  , shipdate    
  , CASE         
      WHEN ShipDate IS NULL THEN 'Awaiting Shipment'        
      ELSE 'Shipped'    
  END as ShippingStatus
  FROM [adventureworks].[SalesLT].[SalesOrderHeader]
  ```

- PARSE & TRY_PARSE: convert formatted str that represent numeric/datetime values ~ sas informat 
  ```sql
  SELECT PARSE('01/01/2021' AS date) AS DateValue,
   PARSE('$199.99' AS money) AS MoneyValue;
  ```

- STR: converts a numeric value to a varchar

### Null values in SQL 

- *NULL* is no value|unknown - cannot compare, <= add/subtract it... 
- ISNULL( col, if null then return result that aligns with col datatype)  i.e. `ISNULL(MiddleName, 'None')`
- COALESCE : can take a variable number of args, each of which is an expression. It will return the first expression in the list that is NOT NULL 
    ```sql
    SELECT EmployeeID,
          COALESCE(HourlyRate * 40,
                    WeeklySalary,
                    Commission * SalesQty) AS WeeklyEarnings
    FROM HR.Wages;
    ```

- NULLIF : return null under certain conditions 

- CONCAT treats NULL values as empty strings - whereas '+' adding NULL str creates a NULL: 
  ```sql
  SELECT TOP (1000) Name    
    , Color    
    , [Size]    
    , Color + ', ' + Size as ProductDetails    
    , CONCAT(Color, ', ', Size) as ProductDetails2 
  FROM [adventureworks].[SalesLT].[Product]
  ```
![sql concat null](./pictures/DP-203/sql-concat-null.png)

### Sorting Results 
`ORDER BY Category ASC, Price DESC`
- Columns by name. You can specify the names of the column(s) by which the results should be sorted. The results are returned in order of the first column, and then subsorted by each additional column in order.
- Column aliases. Because the ORDER BY is processed after the SELECT clause, it has access to aliases defined in the SELECT list.
- Columns by ordinal position in the SELECT list. 
  - Using the position isn't recommended in your applications, because of diminished readability and the extra care required to keep the ORDER BY list up to date. 
  - However, for complex expressions in the SELECT list, using the position number can be useful during troubleshooting.
- Columns not included in the SELECT list, but available from tables listed in the FROM clause. If the query uses a DISTINCT option, any columns in the ORDER BY list must be included in the SELECT list.

### Limiting Results returned
> TOP is proprietary to T-SQL
> Doesn't support skipping rows 
> Depends on ORDER BY - so you can only order output, not reading in of data

`SELECT TOP 10  <COL>...`
- Used in conjunction with ORDER BY so you can pick the "top 10" or "bottom 10"  
- WITH TIES : allows you to pull the "Tied" values i.e. if there are 5 folks tied for 8th place, and you wanted the "Top 10" 
  - **MUST USE ORDER BY CLAUSE**
  - WITH TIES would give you 13 rows to show you everyone that is "Tied" with the 10th place spot 

```sql
SELECT TOP 10 WITH TIES Name, ListPrice
FROM Production.Product
ORDER BY ListPrice DESC;
```
`SELECT TOP 10 PERCENT <COL>...` - does percentages rather than n rows (will round UP to nearest integer)

### OFFSET-FETCH 
- CANNOT use with TOP 
- return only a range of rows selected by your query - therefore goes at the END 
- you provide a starting point (offset) & a value to specify how many rows you would like to return (fetch) 
- allows you to "page" through your results one user-defined segment at a time 
  - if you do not specify the corresponding FETCH, it will return the remaining rows from offset through to the end. 

> Use ORDER BY when performing this to ensure deterministic result
> Without ORDER BY, SQL Server query optimizer can duplicate rows
  ```sql
  -- OFFSET { integer_constant | offset_row_count_expression } { ROW | ROWS }
  -- [FETCH { FIRST | NEXT } {integer_constant | fetch_row_count_expression } { ROW | ROWS } ONLY]

  SELECT ProductID, ProductName, ListPrice
  FROM Production.Product
  ORDER BY ListPrice DESC 
  OFFSET 0 ROWS --Skip zero rows
  FETCH NEXT 10 ROWS ONLY; --Get the next 10

  -- THEN in a subsequent query you start your offset at 10 and then get the following N rows
  SELECT ProductID, ProductName, ListPrice
  FROM Production.Product
  ORDER BY ListPrice DESC 
  OFFSET 10 ROWS --Skip 10 rows
  FETCH NEXT 10 ROWS ONLY; --Get the next 10
  ```

### Distinct 
- returns only ONE of each unique combo of cols in SELECT list 
> by default an implicit ALL is added to the beginning of each select statement. You use the distinct to override it. 

### Where Clause 
- = (equals)
- <> (not equals)
- \> (greater than)
- \>= (greater than or equal to)
- < (less than)
- <= (less than or equal to)
- IS NULL / IS NOT NULL
- can compound using AND | OR
- IN operator: `productid = 1 or productid = 2    ==  productid IN (1,2)` 
- BETWEEN operator: `Price >= 1 AND Price <= 10   ==  price BETWEEN 1 and 10`
  - great for date ranges!
- LIKE operator: use wildcard string filtering `WHERE Name LIKE '%mountain%'` ==> 'HL Mountain Frame - Silver, 42', 'Mountain-100 Silver, 38'
  - `%` is similar to the * where it is 0 or more chars
  - `_` is looking for 1 char 
  - basic regex `[0-9]`

# Nifty Items

**Using PK to randomize data values**
```sql
UPDATE SalesLT.Customer
SET EmailAddress = NULL
WHERE CustomerID % 7 = 1;
```
