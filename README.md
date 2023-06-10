# Provide-Insights-to-Management-in-Consumer-Goods-
###  Domain:  Consumer Goods | Function: Executive Management

Atliq Hardwares (imaginary company) is one of the leading computer hardware producers in India and well expanded in other countries too.

However, the management noticed that they do not get enough insights to make quick and smart data-informed decisions. They want to expand their data analytics team by adding several junior data analysts. Tony Sharma, their data analytics director wanted to hire someone who is good at both tech and soft skills. Hence, he decided to conduct a SQL challenge which will help him understand both the skills.

Task:  

Imagine yourself as the applicant for this role and perform the following task

1.    Check ‘ad-hoc-requests.pdf’ - there are 10 ad hoc requests for which the business needs insights.
2.    You need to run a SQL query to answer these requests. 


# Tools : MySQL , Domain Knowledge


#### Ad-hoc Request Questions 
_____________________________________________________________________________________________________________________________________________________

### 1. Provide the list of markets in which customer "Atliq Exclusive" operates its business in the APAC region.
``` 
SELECT  DISTINCT MARKET FROM dim_customer
WHERE CUSTOMER="Atliq Exclusive" and  region="APAC";
```
![alt text](https://github.com/HazelAbraham/Provide-Insights-to-Management-in-Consumer-Goods-/blob/main/Q1%20SOL.png)


#### 2. What is the percentage of unique product increase in 2021 vs. 2020? The final output contains these fields, unique_products_2020 unique_products_2021 percentage_chg?
```
select *from fact_sales_monthly
SELECT X.B AS unique_product_2020, Y.A as unique_product_2021 , Round((A-B)*100/B, 2) AS percentage_change
FROM(
(SELECT COUNT(DISTINCT(PRODUCT_CODE)) AS B FROM  FACT_SALES_MONTHLY
WHERE FISCAL_YEAR="2020")X,
(SELECT COUNT(DISTINCT(PRODUCT_CODE)) AS A FROM  FACT_SALES_MONTHLY
WHERE FISCAL_YEAR="2021" )Y
)
```
![alt text](https://github.com/HazelAbraham/Provide-Insights-to-Management-in-Consumer-Goods-/blob/main/SOL%202.png)


#### 3. Provide a report with all the unique product counts for each segment  and sort them in descending order of product counts. The final output contains 2 fields,segment ,product_count
```
SELECT  segment , COUNT(DISTINCT PRODUCT_CODE) as product_count 
FROM dim_product
group by segment
order by product_count desc;
```
![alt text](https://github.com/HazelAbraham/Provide-Insights-to-Management-in-Consumer-Goods-/blob/main/SOL%203%20FINAL%20.png)

#### 4. Follow-up: Which segment had the most increase in unique products in 2021 vs 2020? The final output contains these fields,segment product_count_2020, product_count_2021 ,difference

```
With  unique_product as
(
SELECT y.segment  as segment, 
Count(distinct(Case when fiscal_year=2020 then x.product_code End))as product_count_2020,
Count(distinct(Case when fiscal_year=2021 then x.product_code End))as product_count_2021
from fact_sales_monthly as x
inner join dim_product as y
on x.product_code=y.product_code
group by y.segment
)
SELECT segment, product_count_2020, product_count_2021, (product_count_2021-product_count_2020) AS difference
FROM unique_product
ORDER BY difference DESC;
```

![alt text](https://github.com/HazelAbraham/Provide-Insights-to-Management-in-Consumer-Goods-/blob/main/Q4%20SOL.png)

#### 5. Get the products that have the highest and lowest manufacturing costs.The final output should contain these fields, product_code ,product manufacturing_cost.
```
SELECT F.product_code, D.product_code, F.manufacturing_cost
from fact_manufacturing_cost as F inner join dim_product as D
on F.product_code= D.product_code
where manufacturing_cost IN
(select max(manufacturing_cost) from fact_manufacturing_cost
UNION
    SELECT MIN(manufacturing_cost) FROM fact_manufacturing_cost
    ) 
ORDER BY manufacturing_cost DESC ;
````

![alt text](https://github.com/HazelAbraham/Provide-Insights-to-Management-in-Consumer-Goods-/blob/main/q5%20sol.png)


#### 6. Generate a report which contains the top 5 customers who received an average high pre_invoice_discount_pct for the fiscal year 2021 and in the Indian market. The final output contains these fields,customer_code,customer,average_discount_percentage
```
SELECT c.customer_code, c.customer, round(AVG(pre_invoice_discount_pct),4) as avg_discount_percentage
FROM  dim_customer AS c 
JOIN fact_pre_invoice_deductions AS d
ON c.customer_code=d.customer_code
WHERE c.market="INDIA" AND fiScal_year="2021"
GROUP BY customer_code
ORDER BY avg_discount_percentage DESC
LIMIT 5;
```
![alt text](https://github.com/HazelAbraham/Provide-Insights-to-Management-in-Consumer-Goods-/blob/main/Q6%20SOL.png)

#### 7.Get the complete report of the Gross sales amount for the customer “Atliq Exclusive” for each month. This analysis helps to get an idea of low and high-performing months and take strategic decisions. The final report contains these columns: Month Year Gross sales Amount*
```
select MONTH(date) as month, YEAR(date) as year, sum(gross_price*sold_quantity) as gross_sales_amt
from  fact_sales_monthly as f_s
inner join fact_gross_price as f_g
on f_s.product_code=f_g.product_code and f_s.fiscal_year=f_g.fiscal_year
inner join dim_customer as dc
on f_s.customer_code=dc.customer_code
where customer="Atliq Exclusive"
group by month, YEAR(date)
order by YEAR, month
```
![alt text](https://github.com/HazelAbraham/Provide-Insights-to-Management-in-Consumer-Goods-/blob/main/Q7%20SOL.png)

#### 8. In which quarter of 2020, got the maximum total_sold_quantity? The final output contains these fields sorted by the total_sold_quantity,Quarter total_sold_quantity

```
SELECT
CASE 
	WHEN DATE BETWEEN "2019-09-01" AND "2019-11-01" THEN CONCAT("[",1,"]",Monthname(date))
	WHEN DATE BETWEEN "2019-12-01" AND "2020-02-01" THEN CONCAT("[",2,"]",Monthname(date))
	WHEN DATE BETWEEN "2020-03-01" AND "2020-05-01" THEN CONCAT("[",3,"]",Monthname(date))
	WHEN DATE BETWEEN "2020-06-01" AND "2020-08-01" THEN CONCAT("[",4,"]",Monthname(date))
	END AS QUATERS,
	SUM(sold_quantity) as total_sold_quantity
FROM fact_sales_monthly
where fiscal_year=2020
group by QUATERS
```

![alt text](https://github.com/HazelAbraham/Provide-Insights-to-Management-in-Consumer-Goods-/blob/main/q8%20sol.png)

##### 9. Which channel helped to bring more gross sales in the fiscal year 2021 and the percentage of contribution? The final output contains these fields, channel gross_sales_mln percentag
```
SELECT  
dc.channel,
round(SUM(fs.sold_quantity * fg.gross_price) / 1000000, 2) AS gross_sales_in_millions,
round((SUM(fs.sold_quantity *fg.gross_price) / t.total_sales) * 100, 2) AS percentage 
FROM fact_sales_monthly fs
JOIN fact_gross_price fg on fs.product_code=fg.product_code AND fs.fiscal_year=2021
JOIN dim_customer dc on fs.customer_code =dc.customer_code
CROSS JOIN (
  SELECT SUM(s2.sold_quantity * g2.gross_price) AS total_sales
  FROM fact_sales_monthly s2 
  JOIN fact_gross_price g2 ON s2.product_code = g2.product_code
  WHERE s2.fiscal_year = 2021
) t
GROUP BY dc.channel, t.total_sales
ORDER BY gross_sales_in_millions DESC;
```
![alt text](https://github.com/HazelAbraham/Provide-Insights-to-Management-in-Consumer-Goods-/blob/main/q9%20sol.png)


#### 10.  Get the Top 3 products in each division that have a high total_sold_quantity in the fiscal_year 2021? The final output contains these fields, division ,product_code, product ,total_sold_quantity ,rank_order
```
WITH top_3 AS (
    select division, s.product_code, concat(p.product,"(",p.variant,")") AS product , sum(sold_quantity) AS total_sold_quantity,
    rank() OVER (partition by division order by sum(sold_quantity) desc) AS rank_order
 FROM
 fact_sales_monthly s
 JOIN dim_product p
 ON s.product_code = p.product_code
 WHERE fiscal_year = 2021
 GROUP BY product_code
)
SELECT * FROM top_3
WHERE rank_order IN (1,2,3);
```

![alt text](https://github.com/HazelAbraham/Provide-Insights-to-Management-in-Consumer-Goods-/blob/main/q10%20sol.png)

## Conclusion:
1.	 The "Retailer" channel recorded the highest sales, contributing 73.22% to the overall amount sold.
2.	 In contrast, the "Distributor" channel exhibited the lowest contribution at 11.31%.
3.	 These insights reveal essential metrics like Gross Profit, Average Gross Sales, and Average Manufacturing Cost, emphasizing the pivotal role of the           "Retailer" channel in driving Atliq Hardwares' sales.
