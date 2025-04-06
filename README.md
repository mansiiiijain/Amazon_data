# Problem Statement
Imagine you are an analyst at AMAZON and you want to work on some product feature of incentivizing discounts for different customers.
In order to understand what can be the strategy for incentivizing the discounts, analyze the trend of spending in different customer segments (like Occasional shoppers, Regular Buyers, and VIP Customers) over the months.

# Input Tables
- orders.csv
- customer.csv

# Instructions - Sub-problems
1. First segment the customers on the basis of their spending
   (Calculate total Sales for each Customer in each Month) , (Categorize each customer’s each month’s spend)
2. Observe the trend in percentage of different customer segments each month
   (Find the percentage of customer segments in each month)
3. Trend in average total spend value for a customer in each segment over the months
   (Trend in average order value for each segment over the month)

# Solution
WITH CTE_1 AS (
SELECT customer_id, extract(month from order_date) as order_month , SUM(total_amount) AS monthly_spending,
CASE 
WHEN SUM(total_amount) < 1000 THEN "Occasional Shoppers"
WHEN SUM(total_amount) > 1000 and SUM(total_amount) < 5000  THEN "Regular Buyers"
ELSE "VIP customers" 
END AS customer_segments
FROM orders 
group by order_month, customer_id
order by order_month, customer_id),

CTE_2 AS (
SELECT order_month, customer_segments, COUNT(customer_id) AS n_customers
FROM CTE_1
GROUP BY order_month, customer_segments),

CTE_3 AS (
SELECT extract(month from order_date) as order_month, COUNT(DISTINCT customer_id) AS total_cx
FROM orders
GROUP BY order_month)

SELECT CTE_2.order_month, CTE_2.customer_segments, round((CTE_2.n_customers / CTE_3.total_cx)*100,2) as percentage
from CTE_2 
inner join CTE_3
on CTE_2.order_month = CTE_3.order_month;

# Output table
- OUTPUT_TABLE.csv

# Insights on Customer Segment Trends:

1. Regular Buyers Dominate: Across most months (8, 9, 11, 12), Regular Buyers consistently form the largest percentage of customers.
2. VIP Customer Presence Varies: VIP customers show a significant presence in month 7 (50%) but a much smaller presence in months 12 (20%). They are absent in other months in this snapshot.
3. Occasional Shoppers Fluctuate: The percentage of Occasional Shoppers varies, peaking in month 10 (28.57%) but generally remaining lower than Regular Buyers.

# Potential Discount Incentive Strategies:

1. Focus on Retaining Regular Buyers: Since they are the most frequent customer segment, loyalty programs and consistent discounts could be highly effective in maintaining their engagement.
2. Re-engage Occasional Shoppers: Targeted discounts or promotions, especially during months where their numbers are higher (like month 10), could incentivize them to become more frequent buyers. Consider offering limited-time offers or discounts on specific product categories they've previously purchased.
3. Strategically Incentivize VIP Customers:
- Month 7 Focus: Analyze what might have driven the high VIP customer percentage in month 7 and consider replicating those factors.
- Re-activation in Month 12: Explore targeted campaigns to re-engage VIP customers in months where their presence is lower (like month 12). This could involve exclusive offers or early access to new products.
4. Tiered Discount System: Implement a tiered discount system that rewards increased spending and frequency. This could encourage Occasional Shoppers to become Regular Buyers and Regular Buyers to potentially become VIP customers.
5. Month-Specific Promotions: Tailor promotions to the dominant customer segment in each month. For example, focus heavily on Regular Buyer incentives in months 8, 9, and 11.
6. Personalized Discounts: Leverage customer purchase history to offer personalized discounts that resonate with each segment's preferences and spending habits.

# Source
- Newton School

