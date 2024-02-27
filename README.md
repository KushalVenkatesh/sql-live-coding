### Surfalytics | Mock Interview: SQL technical round for Data Analyst

This table records subscription details for customers of a B2B SaaS company. Each row represents a unique subscription, including its duration, plan type, and associated fees.

Subscriptions Table
+----------------+---------+          
| Column Name    | Type    | 
+----------------+---------+
| subscription_id| varchar | 
| customer_id    | varchar |     
| start_date     | date    |   
| end_date       | date    |   
| plan_type      | varchar |     
| monthly_fee    | decimal |      
+----------------+---------+

end_date: Date when the subscription ends. If NULL, the subscription is considered ongoing.
plan_type: Type of subscription plan (e.g., Basic, Premium, Enterprise).


The UsageLogs table tracks usage activities for each subscription, including the type of activity and the amount of data consumed during the activity.

UsageLogs Table
+-----------------+---------+          
| Column Name     | Type    | 
+-----------------+---------+
| log_id          | varchar | 
| subscription_id | varchar |     
| activity_date   | date    |   
| activity_type   | varchar |   
| data_consumed_gb| decimal |     
+-----------------+---------+

activity_type: The type of activity (e.g., data upload, data download, streaming).

Task 1: Find the total number of active subscriptions as of the current date.

Task 2: Determine the average monthly data consumed for active vs. inactive subscriptions.

Task 3: Identify the month-over-month growth rate in the number of new subscriptions.

Task 4: For each plan type, find the customer who has the highest average data consumption.

Task 5: Calculate the retention rate of subscriptions from the start date to the end date, segmented by quarter of the start date. Define retention rate as the percentage of subscriptions that were not renewed (i.e., have an end_date) by the end of their term.
