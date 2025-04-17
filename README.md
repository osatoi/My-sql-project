~~~sql
-- PROFIT ANALYSIS


-- Question 1
-- Within the space of the last three years, whats was the profit worth of the breweries, inclusive of the Anglophone and the farncophone territories?

SELECT SUM(profit) as 'Total Profit'
FROM International_Breweries;


-- Question 2
-- Compare the total profits between this two territories for the country manager, Mr. Stone.

ALTER TABLE International_Breweries
ADD Languages VARCHAR(50);

UPDATE International_Breweries
SET Languages =
CASE WHEN countries IN ('Senegal', 'Togo', 'Benin') THEN 'Francophone Countries'
	ELSE 'Anglophone Countries'
	END;
SELECT DISTINCT Languages, SUM(profit) AS 'Total Profit by Language'
FROM International_Breweries
GROUP BY Languages;

-- Question 3
-- Which country generated the highest profit in 2019?

SELECT TOP 1 Countries, SUM(profit) AS 'Total Profit'
FROM International_Breweries
WHERE years = 2019
GROUP BY countries
ORDER BY 'Total Profit' DESC;

-- Question 4
-- Help him find the year with the highest profit.

SELECT years, SUM(profit) AS 'Total Profit'
FROM International_Breweries
GROUP BY years
ORDER BY 'Total Profit' DESC;

-- Question 5
-- Which year in the three years was the least profit generated?

SELECT months, years, SUM(profit) AS 'Total Profit'
FROM International_Breweries
GROUP BY months, years
ORDER BY months, years ASC







-- Sales Performance Analysis

		-- Question 1
--Show product demand by country – List highest and lowest orders per product

SELECT Brands, countries, MAX(quantity) AS 'Highest Order', MIN(quantity) AS 'Lowest Order' 
FROM International_Breweries
GROUP BY countries, brands
ORDER BY countries;

		--Queastion 2
--Monthly average sales per product over the 3-year period

SELECT DISTINCT Months, Brands, AVG(Quantity) AS 'Average Sales'
FROM International_Breweries
GROUP BY Months, Brands
ORDER BY Months, Brands ASC;

		--Question 3
--Where can we find the highest sales quarterly?

SELECT DISTINCT Brands,Years, DATEPART(QUARTER, Years) AS Quarters, MAX(Profit) AS 'Highest Sales'
FROM International_Breweries
GROUP BY Years, Brands
ORDER BY Years, Brands ASC;

		--Question 4
-- Show quarterly volume of sales by Sales Reps.

SELECT DISTINCT Sales_Rep, Years, DATEPART(QUARTER, Years) AS Quarters, SUM(Quantity) AS 'Quarterly Sales Volume'
FROM International_Breweries
GROUP BY Sales_rep, Years
ORDER BY Years, Sales_rep ASC;

		-- Question 5
--Which Sales rep made the highest profit in this period? (A candidate for bonus)

SELECT TOP 1 Sales_Rep, SUM(profit) AS 'Highest Profit'
FROM International_Breweries
GROUP BY profit, Sales_rep
ORDER BY profit DESC;


-- 1.	Analysis of sales by branch:

SELECT Branch, City, Total, Quantity, AVG(Rating) as AverageRating
FROM superMarketSales
GROUP BY Branch, City, Total, Quantity 

--In some database systems, you cannot reference an alias defined in the 
--SELECT clause within the same SELECT clause. In such cases, you would need to repeat the expression used to 
--calculate the average rating in the GROUP BY clause.

--2.	Customer segmentation:

SELECT [Customer type], Gender, City, [Product line], Payment, Total, Quantity, [gross income] 'Profit by Sales', Rating
FROM superMarketSales
GROUP BY [Customer type], Gender, Quantity, Total, Payment, [Product line], City, [gross income],Rating
ORDER BY [Profit by Sales] DESC, Total DESC, Quantity DESC

-- Customers by Payment Type

SELECT Payment, COUNT(Payment) 'Number by Payment Type'
FROM superMarketSales
GROUP BY Payment
ORDER BY 2 DESC

-- Customers by Product Line

SELECT DISTINCT([Product line]), COUNT([Product line]) 'Number by Product Line'
FROM superMarketSales
GROUP BY [Product line]
ORDER BY 2 DESC

-- Customers by City
SELECT DISTINCT(City), COUNT(City) 'Number by City'
FROM superMarketSales
GROUP BY City
ORDER BY 2 DESC

--3.	Understanding sales patterns by city:

SELECT City, SUM([gross income]) 'Profit Made', SUM(Total) 'Total on Sales', SUM(Quantity) 'Number of Sales', 
		 AVG(Rating) As 'Average Rating'
FROM superMarketSales
GROUP BY City
ORDER BY 2 DESC, 3 DESC, 4 DESC

--4.	Gender-based analysis: 

SELECT 
    Gender,
	SUM([gross income]) AS 'Profit on Sales',
	SUM(Total) AS 'Total on Sales',
	100.0 * SUM([gross income]) / (SELECT SUM([gross income]) FROM superMarketSales) AS 'Percentage Profit',
    SUM(Quantity) AS 'Number of sales', 
    AVG(Rating) AS 'Average Rating'   
FROM superMarketSales
GROUP BY Gender
ORDER BY 2 DESC, 3 DESC, 4 DESC



SELECT 
    CASE WHEN GROUPING(Gender) = 1 THEN 'Grand Total' ELSE Gender END AS Gender, 
    SUM([gross income]) AS 'Profit on Sales', 
    SUM(Total) AS 'Total on Sales', 
    SUM(Quantity) AS 'Number of sales', 
    AVG(Rating) AS 'Average Rating',
    CASE WHEN GROUPING(Gender) = 1 THEN 100.0 ELSE 100.0 * SUM([gross income]) / (SELECT SUM([gross income]) FROM superMarketSales) END AS 'Percentage Profit'
FROM superMarketSales
GROUP BY GROUPING SETS ((Gender), ())

--5.	Product line performance:
SELECT
	CASE WHEN GROUPING([Product line]) = 1 THEN 'Grand Total' ELSE [Product line] END AS Product, 
    SUM([gross income]) AS 'Profit on Sales', 
    SUM(Total) AS 'Total on Sales', 
    SUM(Quantity) AS 'Number of sales', 
    AVG(Rating) AS 'Average Rating',
    CASE WHEN GROUPING([Product line]) = 1 THEN 100.0 ELSE 100.0 * SUM([gross income]) / (SELECT SUM([gross income]) FROM superMarketSales) END AS 'Percentage Profit'
FROM superMarketSales
GROUP BY GROUPING SETS (([Product line]), ())
ORDER BY 2 DESC;


--6.	Time-based analysis: The "Date" and "Time" columns allow for analyzing sales trends over time, identifying peak sales hours,
--or understanding any temporal patterns in customer behavior.


--ALTER TABLE superMarketSales
--ADD SalesDate Date;

--UPDATE superMarketSales
--SET SalesDate = CONVERT(Date, Date);


--Alter Table superMarketSales
--Add SalesTime Time;

--UPDATE superMarketSales
--SET SalesTime = CONVERT(Time, Time);


--1. Which branch has the best results in the loyalty program?

SELECT TOP 1 Branch, AVG(Rating) as AverageRating,
	COUNT(Branch)OVER (PARTITION BY Branch) TotalBranches
FROM superMarketSales
GROUP BY Branch
ORDER BY Branch DESC;

--2.	Does the membership depend on customer rating?

SELECT [Customer type], AVG(Rating)
FROM superMarketSales
GROUP BY [Customer type]


--3.	Does gross income depend on the proportion of customers in the loyalty program? On payment method?

SELECT [Customer type], SUM([gross income]) [Average Income], AVG(Rating) AS AverageRating, Payment, COUNT([Customer type]) AS [Total by Customer Type]
FROM superMarketSales
GROUP BY [Customer type], Payment
ORDER BY 2 DESC;


--4.	Are there any differences in indicators between men and women?

SELECT 
    CASE WHEN GROUPING(Gender) = 1 THEN 'Grand Total' ELSE Gender END AS Gender, 
    SUM([gross income]) AS 'Profit on Sales', 
    SUM(Total) AS 'Total on Sales', 
    SUM(Quantity) AS 'Number of sales', 
    AVG(Rating) AS 'Average Rating',
    CASE WHEN GROUPING(Gender) = 1 THEN 100.0 ELSE 100.0 * SUM([gross income]) / (SELECT SUM([gross income]) FROM superMarketSales) END AS 'Percentage Profit'
FROM superMarketSales
GROUP BY GROUPING SETS ((Gender), ())


--5.	Which product category generates the highest income?

SELECT
	CASE WHEN GROUPING([Product line]) = 1 THEN 'Grand Total' ELSE [Product line] END AS Product, 
    SUM([gross income]) AS 'Profit on Sales', 
    SUM(Total) AS 'Total on Sales', 
    SUM(Quantity) AS 'Number of sales', 
    AVG(Rating) AS 'Average Rating',
    CASE WHEN GROUPING([Product line]) = 1 THEN 100.0 ELSE 100.0 * SUM([gross income]) / (SELECT SUM([gross income]) FROM superMarketSales) END AS 'Percentage Profit'
FROM superMarketSales
GROUP BY GROUPING SETS (([Product line]), ())
ORDER BY 2 DESC;

~~~
