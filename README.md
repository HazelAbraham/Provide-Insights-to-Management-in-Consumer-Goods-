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

#### 4.





#### 5





