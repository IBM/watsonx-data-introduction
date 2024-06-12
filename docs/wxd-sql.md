# SQL Examples

This section contains all of the SQL examples used throughout the lab. You can copy the SQL from this chapter to use in the Query Workspace, rather than having to cut and paste them from documentation or type them in.

Each SQL statement will reference the chapter in the PDF documentation.

## Chapter 7 - Access Control

!!! abstract "7.1 Enter this text into the SQL window and Run the code"

    ```
    SELECT
      *
    FROM
      "hive_data"."ontime"."ontime"
    LIMIT
      10;
    ``` 

!!! abstract "7.2 Enter this text into the SQL window and Run the code"

    ```
    SELECT
      flightdate, reporting_airline, flight_number_reporting_airline, originairportid, destairportid
    FROM
      "hive_data"."ontime"."ontime"
    LIMIT
      10;
    ``` 

## Chapter 8 - Query Workspace

!!! abstract "8.1 Retrieve 10 rows from the ontime table"

    ```
    SELECT
      *
    FROM
      "hive_data"."ontime"."ontime"
    LIMIT
      10;
    ``` 

!!! abstract "8.2 Copy the following SQL and place it into the SQL window"

    ```
    SELECT * FROM "hive_data"."ontime"."ontime" LIMIT 10;
    SELECT * FROM "hive_data"."ontime"."ontime" LIMIT 20;
    ``` 

## Chapter 9 - PrestoDB SQL

!!! abstract "9.1 Display the catalogs"
    ```
    show catalogs;
    ```

!!! abstract "9.2 Show schemas in tpch"
    ```
    show schemas in tpch;
    ```

!!! abstract "9.3 Set the catalog to tpch.tiny"
    ```
    USE tpch.tiny;
    ```

!!! abstract "9.4 View tables in the current schema"
    ```
    show tables; 
    ```

!!! abstract "9.5 Set the catalog to tpch.tiny and show the tables"
    ```
    USE tpch.tiny;
    show tables;
    ```

!!! abstract "9.6 Inspect schema of the customer table"
    ```
    use tpch.tiny;
    describe customer;
    ```

!!! abstract "9.7 Show the customer table columns using an alternate format"
    ```
    show columns from tpch.tiny.customer;
    ```

!!! abstract "9.8 Inspect available Date functions"
    ```
    show functions like 'date%';
    ```

!!! abstract "9.9 View the contents of the sf1 schema"
    ```
    show tables in tpch.sf1;
    ```

!!! abstract "9.10 Query data from customer table"
    ```
    select * from tpch.sf1.customer limit 5;
    ```

!!! abstract "9.11 Gather statistics on the customer table"
    ```
    show stats for tpch.sf1.customer;
    ```

## Chapter 10 - Analytic Workloads

!!! abstract "10.1 Create the workshop schema in the iceberg_data catalog"
    ```
    CREATE SCHEMA IF NOT EXISTS iceberg_data.workshop 
    with (location='s3a://iceberg-bucket/');
    ```

!!! abstract "10.2 Create the workshop customer table"
    ```
    create table iceberg_data.workshop.customer as 
      select * from tpch.tiny.customer;
    ```

!!! abstract "10.3 Run a simple scan query which selects customer names and market segment"
    ```
    use iceberg_data.workshop; 
    select 
      name, mktsegment 
    from 
      customer 
    limit 3;
    ```

!!! abstract "10.4 Run an explain against the previous statement"
    ```
    use iceberg_data.workshop;
    explain select name, mktsegment from customer;
    ```

!!! abstract "10.5 Explain the query and focus on IO operations"
    ```
    use iceberg_data.workshop;
    explain (type io) select name, mktsegment from customer;
    ```

!!! abstract "10.6 Explain physical execution plan for the query"
    ```
    use iceberg_data.workshop;
    explain (type distributed) select name, mktsegment from customer;
    ```

!!! abstract "10.7 Enter the following query into the SQL window and press the Explain button"
    ```
    select 
      name, mktsegment 
    from 
      iceberg_data.workshop.customer 
    ```

!!! abstract "10.8 Create a partitioned table"
    ```
    create table iceberg_data.workshop.part_customer 
      with (partitioning = array['mktsegment']) 
    as select * from tpch.tiny.customer;
    ```

!!! abstract "10.9 Run a query against the partitioned table"
    ```
    select
      * 
    from 
      iceberg_data."workshop".part_customer 
    where 
      mktsegment='MACHINERY';
    ```

!!! abstract "10.10 Create the Orders Table"
    ```
    create table iceberg_data.workshop.orders as 
      select * from tpch.tiny.orders;
    ```

!!! abstract "10.11 SQL with a Windowing function"
    ```
    SELECT 
      orderkey, clerk, totalprice, 
      rank() OVER (PARTITION BY clerk ORDER BY totalprice DESC) AS rnk 
    FROM 
      iceberg_data.workshop.orders 
    ORDER BY 
      clerk, rnk;
    ```

!!! abstract "10.12 Save a query as a prepared statement"
    ```
    prepare 
      customer_by_segment
    from 
      select * from iceberg_data.workshop.customer where mktsegment=?;
    ```

!!! abstract "10.13 Execute prepared statement using parameters"
    ```
    prepare 
      customer_by_segment
    from 
      select * from iceberg_data.workshop.customer where mktsegment=?;    
    execute customer_by_segment using 'FURNITURE';
    ```

## 11 - Advanced Functions

!!! abstract "11.1 Concatenate two strings"
      ```
      select 
         concat(cast(custkey as varchar),'-',name) 
      from 
         iceberg_data.workshop.customer
      limit 2;
      ```

!!! abstract "11.2 Select orders from the last 2 days"
      ```
      select
         orderdate 
      from 
         iceberg_data.workshop.orders 
      where 
         orderdate >  date '1998-08-02' - interval '2' day;
      ```

!!! abstract "11.3 Compute the number of orders by year"
      ```
      select 
         distinct year(orderdate), count(orderkey) 
      from 
         iceberg_data.workshop.orders 
      group by 
         year(orderdate);
      ```

!!! abstract "11.4 SQL Representation of Co-ordinates"
    ```
    select 
       ST_Point(-121.748360,37.195840)  as SVL, 
       ST_Point(-122.378952, 37.621311) as SFO;
    ```

!!! abstract "11.5 SQL Representation of a Line"
    ```
    select 
       ST_LineFromText('LINESTRING (-121.74294303079807 37.19665657093434, -121.73659072815602 37.20102399761407)');
    ```

!!! abstract "11.6 SQL Representation of a Polygon"
      ``` 
      select ST_Polygon('POLYGON (
         (-121.74418635253568 37.196001834113844, 
         -121.74499684288966 37.19668005184322,
         -121.74584008032835 37.19707784979194,  
         -121.74629035274705 37.197645197338105, 
         -121.74672425162339 37.198186455965086, 
         -121.74705172247337 37.19828427337538, 
         -121.74760023614738 37.19827775221884,  
         -121.74848440744239 37.19836252721197, 
         -121.74932764488139 37.19789300297414,  
         -121.75039192514376 37.19746260319114, 
         -121.75130884352407 37.19721479614175, 
         -121.75195559845278 37.1963670290329, 
         -121.75198015876644 37.19555185937345,  
         -121.7508585711051 37.19458016564036, 
         -121.74940132582242 37.19447582194559,  
         -121.74841891327239 37.1942866986312,
         -121.7474446874937 37.193556286900346, 
         -121.74418635253568 37.196001834113844))');
      ```

!!! abstract "11.7 Distance between SFO airport and IBM SVL"
      ```
      select 
         ST_Distance(to_spherical_geography(ST_Point(-122.378952, 37.621311)), 
         to_spherical_geography(ST_Point(-121.748360,37.195840)))*0.000621371 as distance_in_miles;
      ```

!!! abstract "11.8 What queries are currently running?"
      ```
      select * from "system".runtime.queries limit 5;
      ```

!!! abstract "11.9 What tasks make up a query and where is the task running?"
      ```
      select * from "system".runtime.tasks limit 5;
      ```

## 12 - Time Travel

!!! abstract "12.1 Check current snapshots – STARTING STATE"
      ```
      SELECT 
         * 
      FROM 
         iceberg_data.workshop."customer$snapshots" 
      ORDER BY 
         committed_at;
      ```

!!! abstract "12.2 Capture the first snapshot ID"
      ```
      SELECT 
         snapshot_id 
      FROM 
         iceberg_data.workshop."customer$snapshots" 
      ORDER BY 
         committed_at;
      ```

!!! abstract "12.3 Insert a new customer record into the table"
      ```
      insert into iceberg_data.workshop.customer 
         values(1501,'Deepak','IBM SVL',16,'123-212-3455',
                123,'AUTOMOBILE','Testing snapshots');
      ```
 
!!! abstract "12.4 View available snapshots"
      ```
      SELECT 
         * 
      FROM 
         iceberg_data.workshop."customer$snapshots" 
      ORDER BY 
         committed_at;
      ```

!!! abstract "12.5 Select customer Deepak"
      ```
      select * from iceberg_data.workshop.customer where name='Deepak';
      ```

!!! abstract "12.6 View the available snapshots"
      ```
      SELECT 
         snapshot_id 
      FROM 
         iceberg_data.workshop."customer$snapshots" 
      ORDER BY 
         committed_at;
      ```

!!! abstract "12.7 Rollback to previous snapshot - Replace the ID parameter with the snapshot ID previously found. Remember the closing parenthesis!"
      ```
      CALL iceberg_data.system.rollback_to_snapshot('workshop','customer',ID)
      ```

!!! abstract "12.8 Select customer Deepak"
      ```
      select * from iceberg_data.workshop.customer where name='Deepak';
      ```
 
## 13 Federation

!!! abstract "13.1 Select the names of employees in the GOSALES database with employee ids between 4000 and 5000"
      ```
      select 
        go."EMPLOYEE_KEY", go."FIRST_NAME", go."LAST_NAME" 
      from 
        gosales."GOSALESDW"."EMP_EMPLOYEE_DIM" go
      where 
        go."EMPLOYEE_KEY" between 4000 and 5000
      order by 
        go."EMPLOYEE_KEY"
      ```

!!! abstract "13.2 Select TPCH customers who are employees in GOSALES"
      ```
      select 
        t1.custkey, go."FIRST_NAME", go."LAST_NAME" 
      from 
        tpch.sf1.customer t1, gosales."GOSALESDW"."EMP_EMPLOYEE_DIM" go
      where 
         t1.custkey between 4000 and 5000 AND
         go."EMPLOYEE_KEY" = t1.custkey
      order by 
         t1.custkey
      ```

!!! abstract "13.3 Create a table in iceberg_data using the Db2 employee table"
    ```
    create table iceberg_data.workshop.db2employee AS 
      select 
        go."EMPLOYEE_KEY", go."FIRST_NAME", go."LAST_NAME" 
      from 
        gosales."GOSALESDW"."EMP_EMPLOYEE_DIM" go
      where 
        go."EMPLOYEE_KEY" between 4000 and 5000
      order by 
        go."EMPLOYEE_KEY"
    ```

!!! abstract "13.4 Query the contents of the new db2employee table"
    ```
    select * from iceberg_data.workshop.db2employee
    ```
