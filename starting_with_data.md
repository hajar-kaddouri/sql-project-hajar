Question 1: find all duplicate records in analytics Table :

SQL Queries:

WITH duplicates AS (
  SELECT visitnumber, visitid, visitstarttime, date, fullvisitorid, channelgrouping, socialengagementtype, 
  units_sold, pageviews, timeonsite, bounces, revenue, unitprice,
  ROW_NUMBER() 
  OVER (PARTITION BY visitnumber, visitid, visitstarttime, date, fullvisitorid, channelgrouping, socialengagementtype, units_sold, pageviews, timeonsite, bounces, revenue,   unitprice ORDER BY visitnumber) 
  AS dup
  FROM public.analytics
)
SELECT visitnumber, visitid, visitstarttime, date, fullvisitorid, channelgrouping, socialengagementtype, 
  units_sold, pageviews, timeonsite, bounces, revenue, unitprice
FROM duplicates
WHERE dup > 1;
Answer: 
the rest of analytic DATA is 1 666 134


Question 2: find the total number of unique visitors (fullVisitorID) in analytics table that they have high number of units_sold 

SQL Queries:
SELECT COUNT(DISTINCT fullVisitorID) 
FROM analytics
WHERE units_sold > (
                   SELECT AVG(units_sold)
                   FROM analytics)



Answer:

1072

Question 3: Can you list all unique products that were viewed more than once by each visitor in the year 2017?

SQL Queries:
SELECT DISTINCT a.fullVisitorID, s.productsku
FROM all_sessions s
JOIN analytics a 
ON s.fullVisitorID = a.fullVisitorID
WHERE DATE_PART('year', s.date) = 2017 AND a.pageviews > 1
Answer:

3057

Question 4: 

SQL Queries:

Answer:



Question 5: 

SQL Queries:

Answer:
