Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:

SELECT city, country, SUM(transactionrevenue) as transactionrevenue
FROM xall_sessions2
GROUP BY city, country
ORDER BY transactionrevenue DESC

Answer:

"Sunnyvale"	"United States"	200000079
"Hong Kong"	"Hong Kong"	100
"Irvine"	"United States"	55
"South San Francisco"	"United States"	25
"Kirkland"	"United States"	119



**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:

SELECT distinct a.country, a.city, cast(AVG(an.units_sold) as decimal(10,2)) AS averageProductCount
FROM all_sessions AS a
JOIN analytics AS an ON a.fullvisitorid = an.fullvisitorid
where units_sold is not null AND city <> '(not set)'
GROUP BY a.country, a.city;

Answer:
"Ireland"	"Dublin"	1.00
"Israel"	"Tel Aviv-Yafo"	1.00
"Japan"	"Minato"	1.00
"Japan"	"not available in demo dataset"	1.58
"Japan"	"Osaka"	1.00
"Japan"	"Yokohama"	1.00
"Maldives"	"not available in demo dataset"	1.00


95rows

**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:

SELECT
    a.country as country,
    a.city as city,
    a.v2ProductCategory as productcategory,
    SUM(an.units_sold) as totalUnitsSold
FROM all_sessions AS a
JOIN analytics AS an ON a.visitId = an.visitId
JOIN products AS p ON a.productSKU = p.SKU
WHERE a.country != '(not set)'
    AND a.city != '(not set)'
    AND a.v2ProductCategory != '(not set)'
GROUP BY a.country, a.city, a.v2ProductCategory
HAVING SUM(an.units_sold) IS NOT NULL
ORDER BY a.country, a.city, totalUnitsSold DESC

Answer:

"United States"	"Sunnyvale"	"Housewares"
"United States"	"New York"	"Home/Bags/Backpacks/"



**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:



Answer:





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:



Answer:







