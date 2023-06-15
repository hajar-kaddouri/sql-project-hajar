Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:

SELECT 
    city, 
    country, 
    SUM(transactionrevenue) AS total_revenue
FROM 
    xall_sessions2 
WHERE 
    city NOT IN ('not set', 'not available in demo dataset')
GROUP BY 
    city, 
    country
ORDER BY 
    total_revenue DESC;

Answer:

"Sunnyvale"	"United States"	200000079
"(not set)"	"Hong Kong"	100
"Irvine"	"United States"	55
"South San Francisco"	"United States"	25
"Kirkland"	"United States"	11
"(not set)"	"Singapore"	9
"Singapore"	"Singapore"	8

**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:

SELECT distinct a.country, a.city, cast(AVG(an.units_sold) as decimal(10,2)) AS averageProductCount
FROM all_sessions AS a
JOIN analytics AS an ON a.fullvisitorid = an.fullvisitorid
where units_sold is not null AND city <> null
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

SELECT 
    s.city, 
    s.country, 
    s.v2ProductCategory, 
    SUM(a.units_sold) AS total_units_sold
FROM 
    all_sessions AS s
JOIN 
    analytics AS a ON s.fullvisitorid = a.fullvisitorid
WHERE 
    s.city is not null AND a.units_sold IS NOT NULL
group by 
    s.city, 
    s.country, 
    s.v2ProductCategory
ORDER BY 
    total_units_sold DESC;
    
Answer:
"Mountain View"	"United States"	"Home/Accessories/Stickers/"	4972




**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

SELECT 
    s.city, 
    s.country, 
    SUM(a.revenue) AS total_revenue
FROM 
    all_sessions AS s
JOIN 
    analytics AS a ON s.fullvisitorid = a.fullvisitorid
WHERE 
    s.city NOT IN ('not set', 'not available in demo dataset')
    AND a.revenue IS NOT NULL
GROUP BY 
    s.city, 
    s.country
ORDER BY 
    total_revenue DESC;


Answer:

"Mountain View"	"United States"	1140891
"San Bruno"	"United States"	247435
"New York"	"United States"	104501
"Chicago"	"United States"	63969
"Sunnyvale"	"United States"	26457
"Charlotte"	"United States"	13870





