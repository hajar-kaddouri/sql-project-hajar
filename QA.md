What are your risk areas? Identify and describe them.

-Changing Null Values: Null values in a dataset represent missing or unknown data, 
  changing or imputing these values can introduce bias or inaccuracies if not done carefully.
Performing Calculations on Large Columns: This can be computationally intensive and may result in performance issues, 
  particularly we are dealing with a very large dataset.
Finding Unique Values: The main risk here is the potential presence of duplicates 
  or near-duplicates that can skew your results.
Relating Between Tables: When joining tables, there's a risk of creating Cartesian products , 
  which can lead to incorrect results and performance issues. 
  Other risks include issues with data integrity and consistency. 

QA Process:
Describe your QA process and include the SQL queries used to execute it.

Process 1 : : Data Profiling

-Get column data types and nullability for the all_sessions table

SELECT column_name, data_type, is_nullable
FROM information_schema.columns
WHERE table_name = 'all_sessions'; Yes All 32 colums

Count unique values for the fullvisitorid  in analitycs Table:
to look for Primary key

SELECT COUNT(DISTINCT fullvisitorid)
FROM analytics;120018

Process 2 : ata Validation

select units_sold from analytics where units_sold is not null = 60854

select productquantity from all_sessions where productquantity is not null =12127

select productprice from all_sessions where productprice is not null =

select total_ordered from sales_report where total_ordered  is not null = 454 and with null =454

Process 3:Data Cleansing
After deleting the duplicate from analytics:

select * from analytics = 4301122
SELECT DISTINCT * FROM analytics = 1739308
--------------------------------------

explore unique values
SELECT fullvisitorid FROM all_sessions =15134

SELECT Distinct fullvisitorid FROM all_sessions=14223
-------------------
ALTER TABLE all_session
ALTER COLUMN productrevenue TYPE DOUBLE PRECISION USING (productrevenue::DOUBLE PRECISION)

SELECT CAST(transactionrevenue AS float) AS transactionrevenue
FROM all_sessions;
-------------
--select distinct productsku from all_sessions =532
-----------
--SELECT as1.productsku 
--FROM all_sessions as1
--LEFT JOIN products p 
--ON as1.productsku = p.sku WHERE as1.productquantity is not null --== 44row
--------------------------------
Process 4 :Testing Process
to delete the duplicate from analytics:

To check that null value in integer column 
SELECT COUNT(*)
FROM all_sessions
WHERE totaltransactionrevenue = 0

SELECT COUNT(*)
FROM all_sessions
WHERE totaltran = 0

SELECT COUNT(*)
FROM all_sessions
WHERE = 0
