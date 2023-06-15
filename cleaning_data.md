What issues will you address by cleaning the data?

Step 1: Retrieve sample data
   From all Tables
Step 2:replacing null values with correct information :

-update productquantity
-update productrevenue
-updates the transaction column 
-calculate totaltransactionrevenue 
-change the nulls to USD currency
-update quantity and revenue of item 
-replace NULL values with 0 in a specific integer column
-update the city values
-Update the table by dividing the values by 1,000,000 and rounding to two decimal places

Step 3 : Type transformations:

-function created to change from seconde to time format 
-funtion that can be used to switch from milisecond an integer value to time 
-function to switch from varchar to interval by minute or period of time 

Step 4 : Delete duplicate rows from analytics 





Queries:
Below, provide the SQL queries you used to clean your data.
                


------------------------------------------------------------------------------------
--UPDATE all_sessions
--SET productquantity = products.stocklevel
--FROM products
--WHERE all_sessions.productsku = products.sku AND all_sessions.productquantity IS NULL
------------------------------------------------------------------------------------
=udate product revenue 
UPDATE all_sessions
SET productrevenue = COALESCE(productrevenue, (productprice * 
		(SELECT total_ordered 
		FROM sales_report 
		WHERE sales_report.productsku = all_sessions.productsku)))
WHERE productrevenue IS NULL;
--------------------------------------------------------------------------------------
WITH analytics_update_transaction2 AS (
    SELECT fullvisitorid
    FROM analytics
    WHERE units_sold > 0
)
UPDATE xall_sessions2
SET transactions = 1
FROM analytics_update_transaction2
WHERE xall_sessions2.fullvisitorid = analytics_update_transaction2.fullvisitorid;
-----------------------------------------------------------------------------------
SELECT CAST(productrefundamt AS float) AS productrefundmount
FROM xall_sessions2;

UPDATE xall_sessions2
SET totaltransactionrevenue = (transactionrevenue - productrefundamt) * transactions 
----------------------------------------------------------------------------------

UPDATE xall_sessions2
SET currencycode = 'USD';
------------------------------------------------------------------------------------
UPDATE all_sessions
SET itemquantity = productquantity
WHERE itemquantity IS NULL;

UPDATE xall_sessions2
SET itemrevenue = productrevenue
WHERE itemrevenue IS NULL;
---------------------------------------------------------------------------------
UPDATE xall_sessions2
SET productrevenue = COALESCE(productrevenue, 0);

UPDATE xall_sessions2
SET productquantity = COALESCE(productquantity, 0);

UPDATE xall_sessions2
SET totaltransactionrevenue = COALESCE(totaltransactionrevenue, 0);

UPDATE xall_sessions2
SET itemquantity = COALESCE(itemquantity, 0);

UPDATE xall_sessions2
SET productrefundamt = COALESCE(productrefundamt, 0);

UPDATE xall_sessions2
SET transactionrevenue = COALESCE(transactionrevenue, 0);
--------------------------------------------------------------------------------
UPDATE 
    all_sessions 
SET 
    city = (
        SELECT 
            city 
        FROM 
            all_sessions AS valid_city
        WHERE 
            valid_city.fullvisitorid = all_sessions.fullvisitorid
            AND valid_city.city NOT IN ('(not set)', 'not available in demo dataset') 
        LIMIT 1
    )
WHERE 
    city IN ('(not set)', 'not available in demo dataset');
---------------------------------------------------------------------------
UPDATE analytics
SET unitprice = CAST(unitprice / 1000000.0 AS numeric(10, 2));
------------------------------------------------------------------------------------
functions  CREATE OR REPLACE FUNCTION change_timesecond_format(table_name text, column_name text) RETURNS VOID AS $$
BEGIN
    EXECUTE format('ALTER TABLE %I ALTER COLUMN %I TYPE VARCHAR USING LPAD(%I::VARCHAR, 6, ''0'');', table_name, column_name, column_name);

    EXECUTE format('UPDATE public.%I SET %I = SUBSTR(%I, 1, 2) || '':'' || SUBSTR(%I, 3, 2) || '':'' || SUBSTR(%I, 5, 2);', table_name, column_name, column_name, column_name, column_name);

    EXECUTE format('UPDATE public.%I SET %I = TIME ''00:00:00'' + INTERVAL ''1 hour'' * CAST(SUBSTR(%I, 1, 2) AS INTEGER) + INTERVAL ''1 minute'' * CAST(SUBSTR(%I, 4, 2) AS INTEGER) + INTERVAL ''1 second'' * CAST(SUBSTR(%I, 7, 2) AS INTEGER);', table_name, column_name, column_name, column_name, column_name);

    EXECUTE format('ALTER TABLE public.%I ALTER COLUMN %I TYPE TIME USING %I::TIME;', table_name, column_name, column_name);
END;
$$ LANGUAGE plpgsql;
----------------------------------------------------------------------------------------------

CREATE OR REPLACE FUNCTION change_time_format(table_name text, column_name text) RETURNS VOID AS $$
BEGIN
    EXECUTE format('ALTER TABLE %I ADD COLUMN temp_col TIME;', table_name);
    
    EXECUTE format('UPDATE %I SET temp_col = (make_interval(secs := %I::INTEGER / 1000.0))::TIME;', table_name, column_name);
    
    EXECUTE format('ALTER TABLE %I DROP COLUMN %I;', table_name, column_name);
    
    EXECUTE format('ALTER TABLE %I RENAME COLUMN temp_col TO %I;', table_name, column_name);
END;
$$ LANGUAGE plpgsql;
---------------------------------------------------------------------------------------------
CREATE FUNCTION change_durationInterval_format(table_name text, column_name text) RETURNS VOID AS $$
BEGIN
    EXECUTE format('ALTER TABLE %I ADD COLUMN temp_col INTERVAL;', table_name);
    
    EXECUTE format('UPDATE %I SET temp_col = %I::INTEGER * INTERVAL ''1 minute'';', table_name, column_name);
    
    EXECUTE format('ALTER TABLE %I DROP COLUMN %I;', table_name, column_name);
    
    EXECUTE format('ALTER TABLE %I RENAME COLUMN temp_col TO %I;', table_name, column_name);
END;
$$ LANGUAGE plpgsql;
---------------------------------------------------------------------------------------------

WITH duplicates AS (
  SELECT ctid,
  ROW_NUMBER() OVER (
    PARTITION BY visitnumber, visitid, visitstarttime, date, fullvisitorid, channelgrouping, socialengagementtype, 
    units_sold, pageviews, timeonsite, bounces, revenue, unitprice 
    ORDER BY visitnumber
  ) AS dup
  FROM public.analytics
)
DELETE FROM public.analytics 
WHERE ctid IN (
  SELECT ctid 
  FROM duplicates 
  WHERE dup > 1
-----------------------------------------------------------------------------------------------------
