---
title: 'Turn RFM Segmentation Into Actionable Insights'
author: Package Build
date: '2024-08-15'
categories:
  - Analysis
tags:
  - tableau
lastmod: '2024-08-16T17:09:25+07:00'
draft: no
show_in_homepage: yes
show_description: no
featured_image: '/images/pareto.png'
comment: yes
toc: no
autoCollapseToc: yes
math: no
---

<!--more-->

Originally published on [medium](https://medium.com/@idrusfachr/whats-next-after-rfm-exploring-e-commerce-dataset-4bc42e053c79)

### Background
The marketing team at The Look E-commerce faced challenges in optimizing their customer retention strategy. Despite efforts, the existing approach, which treated all customers uniformly, yielded suboptimal results. To address this, the team sought the expertise of a data analyst to understand customer behavior better.

It was determined that a more personalized approach, tailored to individual customer purchasing habits, was necessary. This article focuses on applying data analysis to uncover actionable insights that can inform targeted retention strategies, rather than delving into the technical aspects of customer segmentation methodologies.

> Note : In this article, we will focus only on deriving actionable insights or answering business questions rather than showing the technical and theoretical side of RFM and how to calculate them. There are more than enough resources talked about it available on internet.

### Problem Statement
How can we improve customer retention rates and increase customer lifetime value by developing a targeted retention strategy based on customer purchase behavior analysis?

### Data Preparation
below are the SQL query to calculate RFM from The Looker E-commerce dataset. Scopes :

* Filter data only include order whit status Shipped orComplete and transaction date before August 2024.
* Recency is calculated as per 2024–08–01


``` sql
-- Create user transaction info as base table
WITH base_tbl AS (
  SELECT u.id AS user_id,
        u.age,
        u.gender,
        u.country,
        u.traffic_source AS source,
        o.order_id, 
        o.num_of_item AS quantity,
        p.category AS product_category,
        p.department AS product_department,
        DATE(o.created_at) AS created_at,
        ROUND(o.num_of_item  * p.retail_price, 2) as amount
  FROM `bigquery-public-data.thelook_ecommerce.users` u
  LEFT JOIN `bigquery-public-data.thelook_ecommerce.orders` o on u.id = o.user_id
  LEFT JOIN `bigquery-public-data.thelook_ecommerce.order_items` i on u.id = i.user_id
  LEFT JOIN `bigquery-public-data.thelook_ecommerce.products` p on product_id = p.id
  WHERE i.status IN ('Shipped' , 'Completed')
  AND DATE(o.created_at) <= '2024-07-31'
)

-- Calculate Frequency & Monetery
, fm_tbl AS (
  SELECT user_id,
        age,
        gender,
        country,
        MAX(created_at) AS recent_date,
        COUNT(DISTINCT order_id) AS frequency,
        SUM(amount) AS monetary
  FROM base_tbl
  GROUP BY 1,2,3,4
)

-- Calculate Recency
,  rfm_tbl AS (
    SELECT 
      user_id,
      age,
      gender,
      country,
      DATE_DIFF('2024-08-01', recent_date, DAY) AS recency,  -- recency as per 2024-08-01
      frequency,
      monetary,
    FROM fm_tbl
)

-- Determine quintiles for each RFM metric
,  percentile_tbl AS (
    SELECT
      rfm.*,
      --All percentiles for MONETARY
      m.percentiles[offset(20)] AS m20, 
      m.percentiles[offset(40)] AS m40,
      m.percentiles[offset(60)] AS m60, 
      m.percentiles[offset(80)] AS m80,
      m.percentiles[offset(100)] AS m100,    
      --All percentiles for FREQUENCY
      f.percentiles[offset(20)] AS f20, 
      f.percentiles[offset(40)] AS f40,
      f.percentiles[offset(60)] AS f60, 
      f.percentiles[offset(80)] AS f80,
      f.percentiles[offset(100)] AS f100,    
      --All percentiles for RECENCY
      r.percentiles[offset(20)] AS r20, 
      r.percentiles[offset(40)] AS r40,
      r.percentiles[offset(60)] AS r60, 
      r.percentiles[offset(80)] AS r80,
      r.percentiles[offset(100)] AS r100
    FROM 
      rfm_tbl rfm,
      (SELECT APPROX_QUANTILES(monetary, 100) percentiles FROM
  rfm_tbl) AS m,
      (SELECT APPROX_QUANTILES(frequency, 100) percentiles FROM
      rfm_tbl) AS f,
      (SELECT APPROX_QUANTILES(recency, 100) percentiles FROM
      rfm_tbl) AS r
  )

-- Assign scores for each RFM metric
,  rfm_score AS (
    SELECT *, 
    CAST(ROUND((f_score + m_score) / 2, 0) AS INT64) AS fm_score
    FROM (
        SELECT *, 
        CASE WHEN monetary <= m20 THEN 1
            WHEN monetary <= m40 AND monetary > m20 THEN 2 
            WHEN monetary <= m60 AND monetary > m40 THEN 3 
            WHEN monetary <= m80 AND monetary > m60 THEN 4 
            WHEN monetary <= m100 AND monetary > m80 THEN 5
        END AS m_score,
        CASE WHEN frequency <= f20 THEN 1
            WHEN frequency <= f40 AND frequency > f20 THEN 2 
            WHEN frequency <= f60 AND frequency > f40 THEN 3 
            WHEN frequency <= f80 AND frequency > f60 THEN 4 
            WHEN frequency <= f100 AND frequency > f80 THEN 5
        END AS f_score,
        --Recency scoring is reversed
        CASE WHEN recency <= r20 THEN 5
            WHEN recency <= r40 AND recency > r20 THEN 4 
            WHEN recency <= r60 AND recency > r40 THEN 3 
            WHEN recency <= r80 AND recency > r60 THEN 2 
            WHEN recency <= r100 AND recency > r80 THEN 1
        END AS r_score,
        FROM percentile_tbl
        )
  )

-- Define the RFM segments using the scores obtained from rfm_score cte
,  rfm_segment AS (
    SELECT
      user_id,
      age,
      gender,
      country,
      recency,
      frequency,
      monetary,
      r_score,
      f_score,
      m_score,
      fm_score,
      CASE WHEN (r_score = 5 AND fm_score = 5) 
              OR (r_score = 5 AND fm_score = 4) 
              OR (r_score = 4 AND fm_score = 5) 
          THEN 'Champions'
          WHEN (r_score = 5 AND fm_score =3) 
              OR (r_score = 4 AND fm_score = 4)
              OR (r_score = 3 AND fm_score = 5)
              OR (r_score = 3 AND fm_score = 4)
          THEN 'Loyal Customers'
          WHEN (r_score = 5 AND fm_score = 2) 
              OR (r_score = 4 AND fm_score = 2)
              OR (r_score = 3 AND fm_score = 3)
              OR (r_score = 4 AND fm_score = 3)
          THEN 'Potential Loyalists'
          WHEN r_score = 5 AND fm_score = 1 THEN 'Recent Customers'
          WHEN (r_score = 4 AND fm_score = 1) 
              OR (r_score = 3 AND fm_score = 1)
          THEN 'Promising'
          WHEN (r_score = 3 AND fm_score = 2) 
              OR (r_score = 2 AND fm_score = 3)
              OR (r_score = 2 AND fm_score = 2)
          THEN 'Customers Needing Attention'
          WHEN r_score = 2 AND fm_score = 1 THEN 'About to Sleep'
          WHEN (r_score = 2 AND fm_score = 5) 
              OR (r_score = 2 AND fm_score = 4)
              OR (r_score = 1 AND fm_score = 3)
          THEN 'At Risk'
          WHEN (r_score = 1 AND fm_score = 5)
              OR (r_score = 1 AND fm_score = 4)        
          THEN 'Cant Lose Them'
          WHEN r_score = 1 AND fm_score = 2 THEN 'Hibernating'
          WHEN r_score = 1 AND fm_score = 1 THEN 'Lost'
          END AS rfm_segment 
    FROM rfm_score
  )

SELECT * FROM rfm_segment;
```

### Segment Overview

![](/images/segment_overviews.png)

Initial analysis reveals a concerningly low purchase frequency among customers, even within high-performing segments. On average, top-tier customers make only three purchases over five years, indicating a significant opportunity to increase customer engagement and drive repeat business. Boosting purchase frequency is a primary focus for the marketing team.

### Segment Mapping
To visualize customer segments based on spending behavior and purchase recency, we plotted each customer’s spending score (a combination of monetary value and purchase frequency) against their recency score. This allowed us to identify distinct customer groups and understand their relative positions within the customer lifecycle.

![](/images/segment_mapping.png)

The graph above helps us to better know segment behavior and plan the best strategies. We can categorize segment based on 4 quadrants.

**Valuable users zone**

This quadrant encompasses our top-performing customers, characterized by high spending and recent activity. It includes Champions, Loyal Customers, and Potential Loyalists. Our primary goal is to retain and nurture these valuable customers. this segment. Proposed strategies :
1. **Upselling and Cross-Selling**: Offer complementary products or premium upgrades to maximize customer value.
2. **Premium Tier Engagement**: Prioritize these customers for premium memberships or subscription offerings.
3. **Enhanced Customer Experience**: Provide exceptional customer support and personalized interactions to foster loyalty.
4. **Relationship Building**: Implement customer lifecycle programs (e.g., birthday greetings, loyalty rewards) to strengthen emotional connections.

**Activate users zone**

This is our big spender users but have not doing any purchase for a while. Proposed strategies :
1. **Win-back campaign**. Implement targeted campaigns offering incentives or exclusive promotions to encourage repeat purchases.
2. **Check buying experience**. Conduct further analysis for this users segments about how is their buying experience. Did they cancel or return last purchase, did they give low reviews or other issues might happen during their purchases.

**Churn users zone**

This quadrant represents customers at high risk of churn due to low spending and purchase inactivity. Retaining these customers is critical. Proposed strategies :
1. **Offer discount or incentive**.
2. **Review their buying experience**. Did they had bad experience in buying (return or cancel) or customer service.

**Potential users zone**

This segment exhibits recent purchase activity but lower spending compared to other groups. They represent a growth opportunity with the potential to become loyal customers. Proposed growth Strategies:
1. **Product Education**: Clearly communicate product benefits and value propositions to encourage increased spending.
2. **Bundling and Promotions**: Offer product bundles or limited-time promotions to stimulate larger purchases and improve average order value.
3. **Cross-Selling**: Introduce complementary products to expand the customer’s product basket.

### Resource Allocation
Ideal condition is that we can run all strategies targeting all segments or quadrant. But, we often have constraints on budget, time or other resource. Data Analyst can help to prioritize which segment should be targeted to get the optimum results.

There are many possibilities to prioritize which segments to be focused on. The domain knowledge or experience judgement from marketing and business team also can be very useful input to decide which segments to be prioritized. In term of data, we can use Pareto principle to objectively decide the segments. Basically, Pareto principle focus on small amount of variables (20%) that significantly contribute (80%) to the outcome.
![](/images/pareto.png)

In this case, we want to know which segments contribute to 80% of the total revenue. Based the Pareto chart, it’s known that 4 segments are contributing 83% of the total revenue. These segments should be the priority for marketing/retention efforts. If we look on the 4 segments, 2 segments (loyal customers and champions) are high value customer which generally more cost-effective to retain and grow.

If we can only choose 1 segment, it should be At Risk segment. This customers have high spending and significant contribution toward total revenue. Losing this users will has significant impact to revenue.

### Segment Profiling

To effectively target and engage customers, creating detailed segment profiles is essential. By understanding the characteristics, behaviors, and preferences of each customer segment, businesses can tailor marketing efforts and improve overall performance.

```{}
-- Assign user segment into order data for user profiling
SELECT b.*,
       r.rfm_segment 
FROM base_tbl b
LEFT JOIN rfm_segment r ON b.user_id = r.user_id;
```
For example, we want to look at the users profile on `At Risk` segment.

![](/images/user_profile.png)

Key Takeaways:

1. The at-risk segment is diverse in age and geography, requiring varied retention strategies.
2. Focus on improving engagement in top categories like Intimates and Jeans.
3. Tailor retention efforts to the peaks in the 20–30 and 50–60 age groups.
4. Consider region-specific strategies for China and the US, which have the most at-risk users.
5. Investigate why these users are classified as “at risk” and develop targeted re-engagement campaigns.

### Conclusions
Based on our analysis, we can conclude some take aways :

* The average purchase users are poor, even for most performing users segment. Increase purchase frequency for all users should be the first priority.
* Segment can be categorized into 4 quadrant based on spending and recency score. Each quadrant needs custom treatment based their behavior.
* 4 segments contribute to 83% of the revenue. It should be the priority for retention effort. If it can only choose 1 segment, we recommend At Risk segment to be choosen based on the potential revenue contribution and users base size.
* Users profile for each segment can help marketing team plan retention strategies.


Full Tableau dashboard for this analysis can be accessed here : https://public.tableau.com/shared/TKH8W2R2S?:display_count=n&:origin=viz_share_link


