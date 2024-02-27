# Mock SQL interview for Data Analyst

## Data Description

### Table 1: Subscriptions

This table records subscription details for customers of a B2B SaaS company. Each row represents a unique subscription, including its duration, plan type, and associated fees.

| Column Name     | Type    |
|-----------------|---------|
| subscription_id | varchar |
| customer_id     | varchar |
| start_date      | date    |
| end_date        | date    |
| plan_type       | varchar |
| monthly_fee     | decimal |

`end_date`: Date when the subscription ends. If NULL, the subscription is considered ongoing.

`plan_type`: Type of subscription plan (e.g., Basic, Premium, Enterprise).


### Table 2: UsageLogs

The UsageLogs table tracks usage activities for each subscription, including the type of activity and the amount of data consumed during the activity.

| Column Name      | Type    |
|------------------|---------|
| log_id           | varchar |
| subscription_id  | varchar |
| activity_date    | date    |
| activity_type    | varchar |
| data_consumed_gb | decimal |

`activity_type`: The type of activity (e.g., data upload, data download, streaming).

## Tasks

**Task 1: Find the total number of active subscriptions as of the current date.**

Answer: 

```sql
SELECT COUNT(*) AS active_subscriptions
FROM Subscriptions
WHERE start_date <= CURRENT_DATE
AND (end_date IS NULL OR end_date > CURRENT_DATE);
```

**Task 2: Determine the average monthly data consumed for active vs. inactive subscriptions.**

Answer: 

```sql
SELECT 
    CASE
        WHEN end_date IS NULL OR end_date > CURRENT_DATE THEN 'Active'
        ELSE 'Inactive'
    END AS subscription_status,
    AVG(data_consumed_gb) AS average_data_consumed
FROM Subscriptions S
JOIN UsageLogs U ON S.subscription_id = U.subscription_id
GROUP BY subscription_status;
```

**Task 3: Identify the month-over-month growth rate in the number of new subscriptions.**

Answer:

```sql
WITH MonthlySubscriptions AS (
    SELECT
        EXTRACT(YEAR FROM start_date) AS year,
        EXTRACT(MONTH FROM start_date) AS month,
        COUNT(subscription_id) AS new_subscriptions
    FROM Subscriptions
    GROUP BY year, month
),
GrowthCalculations AS (
    SELECT
        year,
        month,
        new_subscriptions,
        LAG(new_subscriptions, 1) OVER (ORDER BY year, month) AS previous_month_subscriptions
    FROM MonthlySubscriptions
)
SELECT
    year,
    month,
    new_subscriptions,
    COALESCE(((new_subscriptions - previous_month_subscriptions) / previous_month_subscriptions::float) * 100, 0) AS growth_rate
FROM GrowthCalculations;
```

**Task 4: For each plan type, find the customer who has the highest average data consumption.**

Answer:

```sql
WITH CustomerConsumption AS (
    SELECT
        S.plan_type,
        S.customer_id,
        AVG(U.data_consumed_gb) AS avg_consumption
    FROM Subscriptions S
    JOIN UsageLogs U ON S.subscription_id = U.subscription_id
    GROUP BY S.plan_type, S.customer_id
),
RankedConsumption AS (
    SELECT
        plan_type,
        customer_id,
        avg_consumption,
        RANK() OVER (PARTITION BY plan_type ORDER BY avg_consumption DESC) AS rank
    FROM CustomerConsumption
)
SELECT plan_type, customer_id, avg_consumption
FROM RankedConsumption
WHERE rank = 1;
```


**Task 5: Calculate the retention rate of subscriptions from the start date to the end date, segmented by quarter of the start date. Define retention rate as the percentage of subscriptions that were not renewed (i.e., have an end_date) by the end of their term.**

Answer:

```sql
WITH QuarterlySubscriptions AS (
    SELECT
        DATE_TRUNC('quarter', start_date) AS quarter,
        COUNT(*) AS total_subscriptions,
        COUNT(end_date) AS ended_subscriptions
    FROM Subscriptions
    GROUP BY quarter
),
RetentionRates AS (
    SELECT
        quarter,
        total_subscriptions,
        ended_subscriptions,
        (1 - (ended_subscriptions::FLOAT / total_subscriptions)) * 100 AS retention_rate
    FROM QuarterlySubscriptions
)
SELECT quarter, total_subscriptions, ended_subscriptions, retention_rate
FROM RetentionRates
ORDER BY quarter;
```
