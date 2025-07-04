# DSA-CAPSTONE-PROJECT-CASE-STUDY-2-
My round up project for my beginner training with The Incubator Hub 
SQL QUERY :

select * from KMS_Cleaned_for_SQL

												27/06/2025
------which product category has the highest sales      QUESTION 1

SELECT TOP 1
	Product_Category,
	SUM(Sales) AS TotalSales
FROM KMS_Cleaned_For_SQL
GROUP BY Product_Category
ORDER BY TotalSales DESC;


----Top 3 and Bottom 3 regions in terms of Sales   QUESTION 2

TOP 3
select top 3 
	Region,
	SUM(Sales) AS TotalSales
FROM KMS_Cleaned_For_SQL
GROUP BY Region
ORDER BY TotalSales DESC;

BOTTOM 3
select top 3 
	Region,
	SUM(Sales) AS TotalSales
FROM KMS_Cleaned_For_SQL
GROUP BY Region
ORDER BY TotalSales ASC;



------WHAT WERE THE TOTAL SALES OF APPLIANCES IN ONTARIO   QUESTION 3

SELECT Region,
SUM(Sales) AS TotalSales
FROM KMS_Cleaned_For_SQL
WHERE Product_Sub_Category = 'Appliances'
AND Province = 'Ontario'
GROUP BY Region


--------WHAT WERE THE TOTAL SALES OF OFFICE SUPPLIES IN ONTARIO
SELECT
	SUM(Sales) AS TotalSales
FROM KMS_Cleaned_For_SQL
WHERE
	Product_Category = 'office Supplies'
	AND Province = 'Ontario';


------ADVICE THE MANAGEMENT OF KMS ON WHAT TO DO TO INCREASE THE REVENUE FROM THE BOTTOM 10 CUSTOMERS    QUESTION 4

BOTTOM 10 CUSTOMERS BY SALES

SELECT TOP 10
	Customer_Name,
	SUM(Sales) As TotalSales
FROM KMS_Cleaned_For_SQL
GROUP BY Customer_Name
ORDER BY TotalSales ASC

1. CUSTOMER ENGAGEMENT AND FEEDBACK
2. PERSONALIZED OFFERS AND DISCOUNTS
3. CUSTOMER EDUCATION
4. DEDICATED RELATIONSHIP MANAGER OR SUPPORT
5. BUNDLE PRODUCTS
6. MINIMUM ORDER INCENTIVES
7. TRACK AND REVIEW PROGRESS MONTHLY
8. GEO-SPECIFIC PROMOTIONS

------KMS INCURRED THE MOST SHIPPING COST USING WHICH SHIPPING METHOD QUESTION 5

SELECT
	Ship_Mode,
	SUM(TRY_CAST(Shipping_Cost AS FLOAT)) AS TotalShippingCost
FROM KMS_Cleaned_For_SQL
GROUP BY Ship_Mode
ORDER BY TotalShippingCost DESC;  


------- WHO ARE THE MOST VALUABLE CUSTOMERS, AND WHAT PRODUCTS OR SERVICES DO THEY TYPICALLY PURRCHASE?  QUESTION 6

--CUSTOMER WITH THE HIGHEST TOTAL SALES

SELECT TOP 10
	Customer_Name,
	SUM(Sales) AS TotalSales
FROM KMS_Cleaned_For_SQL
GROUP BY Customer_Name
ORDER BY TotalSales DESC;

-- PRODUCTS OR CATEGORY THEY BUY MOST

WITH TopCustomers as (
	SELECT TOP 10
		Customer_Name,
		SUM(Sales) AS TotalSales
	FROM KMS_Cleaned_For_SQL
	GROUP BY Customer_Name
	ORDER BY TotalSales DESC
)
SELECT
	tc.Customer_Name,
	sd.Product_Category,
	COUNT(*) AS PurchaseCount,
	SUM(sd.Sales) AS TotalSpent
FROM TopCustomers tc
JOIN KMS_Cleaned_For_SQL sd
	ON tc.Customer_Name = sd.Customer_Name
GROUP BY tc.Customer_Name, sd.Product_Category
ORDER BY tc.Customer_Name, TotalSpent DESC;

-------WHICH SMALL BUSINESS CUSTOMER HAD THE HIGHEST SALES      QUESTION 7

SELECT TOP 1
	Customer_Name,
	SUM(Sales) AS TotalSales
FROM KMS_Cleaned_For_SQL
WHERE Customer_Segment = 'Small Business'
GROUP BY Customer_Name
ORDER BY TotalSales DESC;

------WHICH CORPORATE CUSTOMER PLACED THE MOST NUMBER OF ORDERS IN 2009-2012?    QUESTION 8

SELECT TOP 1
	Customer_Name,
	COUNT(DISTINCT Order_ID) AS NumberOfOrders
FROM KMS_Cleaned_For_SQL
WHERE Customer_Segment = 'Corporate'
AND TRY_CAST(Order_Date AS DATE) BETWEEN '2009-01-01' AND '2012-12-31'
GROUP BY Customer_Name
ORDER BY NumberOfOrders DESC;

--------WHICH CONSUMER CUSTOMER WAS THE MOST PROFITABLE ONE?       QUESTION 9

SELECT TOP 1
	Customer_Name,
	sum(TRY_CAST(Profit AS FLOAT)) AS TotalProfit
FROM KMS_Cleaned_For_SQL
WHERE Customer_Segment = 'Consumer'
GROUP BY Customer_Name
ORDER BY TotalProfit DESC;

--------WHICH CUSTOMER RETURNED ITEMS, AND WHAT SEGMENT DO THEY BELONG TO?      QUESTION 10
CUSTOMERS WITH POSSIBLE RETURNS (BASED ON NEGATIVE PROFIT OR ZERO SALES)

SELECT
	Customer_Name,
	Customer_Segment,
	Sales,
	Profit
FROM KMS_Cleaned_For_SQL
WHERE
	TRY_CAST(Profit AS FLOAT) < 0
	OR TRY_CAST (Sales AS FLOAT) = 0; 


-------IF THE DELIVERY TRUCK IS THE MOST ECONOMICAL BUT THE SLOWEST SHIPPING METHOD					QUESTION 11
	   AND EXPRESS AIR IS THE FASTEST BUT THE MOST EXPENSIVE ONE, DO YOU THINK THE 
	   COMPANY APPROPRIATELY SPENT SHIPPING COSTS BASED ON THE ORDER PRIORITY? EXPLAIN YOUR ANSWER

MATCH ORDER PRIORITY WITH SHIPPING METHOD TO CHECK ALIGNMENT

SELECT 
	[Order_Priority],[Ship_Mode],
	COUNT([Order_ID]) AS OrderCount,
	ROUND(SUM(Sales - Profit), 2) AS
	EstimatedShippingCost,
	AVG(DATEDIFF(day, [Order_Date], [Ship_Date])) AS AvgShipDays
	FROM KMS_Cleaned_For_SQL
	GROUP BY [Order_Priority],[Ship_Mode]
	ORDER BY [Order_Priority],[Ship_Mode] DESC;
