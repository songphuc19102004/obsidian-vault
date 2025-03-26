`COUNT(DISTINCT order_date)`

to count distince(different value)

  

==`aggregation_function() OVER(PARTITION BY column _name) as [alias]`==

EX: SELECT Employee_name, Skill, Salary, AVG(Salary) OVER(PARTITION BY Skill) as AVG_Salary

AVG(Salary) OVER(PARTITION BY Skill) as AVG_Salary: This will mean that this column will show average salary based on the Skill column(C#, Java, React,…)

#### Query

|Employee_name|Skill|Salary|AVG_Salary|
|---|---|---|---|
|[[Phuc]]|ASP.NET|900|750|
|[[Minh]]|ASP.NET|800|750|
|[[Nam]]|Java|860|600|
|[[Phu]]|Java|500|600|

  
  

  

If we use group by:

```SQL
SELECT sales.customer_id, sales.order_date, menu.product_id, menu.price, AVG(menu.price) as AVG_VAL
FROM dannys_diner.sales
JOIN dannys_diner.menu ON sales.product_id = menu.product_id
GROUP BY sales.customer_id
```

We will have to group by all the column on the select, which will we not want this, and the ==aggregation value== might be wrong too.

|   |   |   |   |   |
|---|---|---|---|---|
|customer_id|order_date|product_id|price|avg_val|
|B|2021-02-01T00:00:00.000Z|3|12|12.0000000000000000|
|A|2021-01-10T00:00:00.000Z|3|12|12.0000000000000000|
|A|2021-01-11T00:00:00.000Z|3|12|12.0000000000000000|
|B|2021-01-11T00:00:00.000Z|1|10|10.0000000000000000|
|C|2021-01-07T00:00:00.000Z|3|12|12.0000000000000000|
|B|2021-01-04T00:00:00.000Z|1|10|10.0000000000000000|
|A|2021-01-01T00:00:00.000Z|2|15|15.0000000000000000|
|C|2021-01-01T00:00:00.000Z|3|12|12.0000000000000000|
|A|2021-01-01T00:00:00.000Z|1|10|10.0000000000000000|
|B|2021-01-16T00:00:00.000Z|3|12|12.0000000000000000|
|B|2021-01-02T00:00:00.000Z|2|15|15.0000000000000000|
|B|2021-01-01T00:00:00.000Z|2|15|15.0000000000000000|
|A|2021-01-07T00:00:00.000Z|2|15|15.0000000000000000|

  

### CTE(Common Table Expression)

Definition: temporary result set defined within the execution scope of a single `SELECT`, `INSERT`, `UPDATE`, or `DELETE` statement. It helps improve query readability and organization.