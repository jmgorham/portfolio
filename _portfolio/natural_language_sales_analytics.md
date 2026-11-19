---
title: "Natural Language Sales Insights Agent"
excerpt: "Agentic AI for answering analytical questions about grocery delivery orders with SQL and data visualizations."
tools: [ "Databricks", "SQL", "Agentic AI", "Genie" ]
header:
  teaser: /assets/images/agent_landing.jpg
layout: archive
classes: wide
author_profile: true
sidebar:
order: 2
---

This project uses Databricks Genie AI to create a natural-language analytics agent. The agent uses SQL and data visualization to quickly and accurately answer ad-hoc questions about the data. The project uses anonymized data on over 3 million Instacart grocery orders, available through Kaggle <a href="https://www.kaggle.com/datasets/yasserh/instacart-online-grocery-basket-analysis-dataset" target="_blank">here</a>.

The Instacart Insights Agent can answer a wide range of questions, both simple and complex, about ordering trends, user preferences, and product popularity, allowing business users to explore ordering data without requiring in-depth knowledge of SQL or BI workflows. In doing so, it provides valuable business insights for retail and grocery marketing.

Please view the video below for a brief demo of the Instacart Insights Agent.

<iframe width="560" height="315" src="https://www.youtube.com/embed/Fol15zyS-Og?si=Cciw_jLnHPk1O56W" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

The first step in building the agent was preparing the data. Agents are most successful when provided with simple tables containing business-level aggregates and requiring minimal joins to answer common business questions. I chose to model the data using a star schema to provide this business-level analytics structure, and I utilized a medallion architecture to transform the data from the raw download source to the star schema target. 

As an example, the image below shows the lineage graph of the gold-level table dim_products used by the agent. The raw data downloads were uploaded without transformation in the bronze layer, cleaned and normalized in the silver layer, and aggregated in the gold layer.

![Lineage graph for gold layer table dim_products]({{ site.url }}{{ site.baseurl }}/assets/images/dim_products_lineage_graph.jpg){: .full}

(Please note: usernames containing personal contact information have been redacted on all screenshots.)

To configure the agent, I iteratively applied the following steps:

1. Add agent-level metadata, such as text descriptions for columns.
2. Provide the agent with examples of SQL queries used to answer specific analytical questions, join tables, and calculate common metrics.
3. Write text instructions to guide the agent's response behavior.
4. Evaluate the agent's performance using benchmarks.

Benchmarks evaluate the agent against sample questions. For each question, I wrote a SQL query to produce the exact result table expected from the agent. If an agent fails a benchmark, this may indicate that the agent is struggling to interpret user prompts, the available data, and custom instructions in context. I used benchmarks as a starting point for improving the agent's performance, and with this iterative approach, I successfully tuned the agent to respond to prompts more accurately and precisely.

![Benchmark evaluations of agent with improvement over time]({{ site.url }}{{ site.baseurl }}/assets/images/benchmarks.jpg){: .full}

This project required me to write complex SQL at every stage of agent development: data preparation and modeling, agent configuration with example queries, and benchmarking with ground-truth queries. I utilized multiple join types, subqueries, CTEs, and window functions to transform and analyze the data with SQL. One of these sample queries is included below as an example of my work.

This query answers the sample question: *What product categories are most commonly ordered together with baby supplies?*

{% highlight sql %}
-- Question: What product categories are most commonly ordered together with baby supplies?

-- CTE: get order IDs for orders that contain baby products
WITH baby_orders AS (
  SELECT DISTINCT
    f.order_id
  FROM
    `workspace`.`instacart_gold`.`fact_user_order_products` f
      JOIN `workspace`.`instacart_gold`.`dim_products` p
        ON f.product_id = p.product_id
  WHERE
    p.department = 'babies'
)
SELECT
  p.department,
  -- Calculation of percentage by department
  ROUND(
    COUNT(DISTINCT f.order_id)
      * 100.0
      / (
        SELECT
          COUNT(DISTINCT order_id)
        FROM
          baby_orders
      ),
    1
  ) AS pct_of_baby_orders
FROM
  `workspace`.`instacart_gold`.`fact_user_order_products` f
    JOIN `workspace`.`instacart_gold`.`dim_products` p
      ON f.product_id = p.product_id
WHERE
  -- Filter to include only orders that are in baby_orders CTE
  f.order_id IN (
    SELECT
      order_id
    FROM
      baby_orders
  )
  -- Do not calculate percentage overlap for babies department
  AND p.department != 'babies'
GROUP BY
  p.department
ORDER BY
  pct_of_baby_orders DESC
LIMIT 10
{% endhighlight %}

Executing this query reveals that 83.3% of orders that include baby supplies also contain produce items. The department with the second most overlap is dairy and eggs, with 82.5% of orders containing items from the babies department also including items from the dairy and eggs department.

While the Instacart Insights Agent excels at answering questions quickly, AI agents can make mistakes. Human analysts can monitor Databricks Genie agents, and business users can flag AI responses for human review. Tools like Genie help democratize access to data and analytics, though human expertise and judgment are required in building and maintaining agents and in solving analytical challenges that require higher-level creative and strategic thinking.