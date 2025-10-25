## Full Execution Guide (SQL in Google Big Query) 

### Section 2 Question 1: Create table & insert dummy data
You may change the project name according to your's.
```bash
CREATE OR REPLACE TABLE `massive-physics-476110-f0.MRDIY_de.sql_test_raw` (
  month STRING,
  product STRING,
  store_code STRING,
  category STRING,
  sales_qty INT64,
  sales_amt FLOAT64,
  sales_cost FLOAT64
);

INSERT INTO `massive-physics-476110-f0.MRDIY_de.sql_test_raw`
(month, product, store_code, category, sales_qty, sales_amt, sales_cost)
VALUES
('Jan-25','ProdA','S001','Beverage',120,600,360),
('Jan-25','ProdB','S001','Beverage',140,700,420),
('Jan-25','ProdC','S002','Snack',180,720,450),
('Jan-25','ProdD','S003','Snack',150,600,370),
('Jan-25','ProdE','S004','Dairy',200,1000,650),
('Jan-25','ProdF','S005','Dairy',100,500,320),
('Feb-25','ProdA','S001','Beverage',130,650,390),
('Feb-25','ProdB','S002','Beverage',120,600,360),
('Feb-25','ProdC','S003','Snack',170,680,420),
('Feb-25','ProdD','S004','Snack',160,640,400),
('Feb-25','ProdE','S005','Dairy',190,950,600),
('Feb-25','ProdF','S006','Dairy',110,550,350),
('Mar-25','ProdA','S001','Beverage',140,700,420),
('Mar-25','ProdB','S002','Beverage',150,750,450),
('Mar-25','ProdC','S003','Snack',200,800,500),
('Mar-25','ProdD','S004','Snack',170,680,410),
('Mar-25','ProdE','S005','Dairy',210,1050,670),
('Mar-25','ProdF','S006','Dairy',120,600,360),
('Apr-25','ProdA','S002','Beverage',160,800,480),
('Apr-25','ProdB','S003','Beverage',130,650,390),
('Apr-25','ProdC','S004','Snack',190,760,460),
('Apr-25','ProdD','S005','Snack',180,720,440),
('Apr-25','ProdE','S006','Dairy',220,1100,700),
('Apr-25','ProdF','S007','Dairy',130,650,400),
('May-25','ProdA','S001','Beverage',150,750,450),
('May-25','ProdB','S002','Beverage',160,800,480),
('May-25','ProdC','S003','Snack',210,840,520),
('May-25','ProdD','S004','Snack',190,760,460),
('May-25','ProdE','S005','Dairy',230,1150,720),
('May-25','ProdF','S006','Dairy',140,700,430),
('Jun-25','ProdA','S002','Beverage',170,850,510),
('Jun-25','ProdB','S003','Beverage',140,700,420),
('Jun-25','ProdC','S004','Snack',200,800,480),
('Jun-25','ProdD','S005','Snack',180,720,430),
('Jun-25','ProdE','S006','Dairy',220,1100,680),
('Jun-25','ProdF','S007','Dairy',130,650,390),
('Jul-25','ProdA','S001','Beverage',160,800,480),
('Jul-25','ProdB','S002','Beverage',150,750,450),
('Jul-25','ProdC','S003','Snack',210,840,500),
('Jul-25','ProdD','S004','Snack',190,760,460),
('Jul-25','ProdE','S005','Dairy',240,1200,750),
('Jul-25','ProdF','S006','Dairy',150,750,450),
('Aug-25','ProdA','S001','Beverage',170,850,510),
('Aug-25','ProdB','S002','Beverage',160,800,480),
('Aug-25','ProdC','S003','Snack',220,880,530),
('Aug-25','ProdD','S004','Snack',200,800,470),
('Aug-25','ProdE','S005','Dairy',250,1250,780),
('Aug-25','ProdF','S006','Dairy',140,700,420);
```


### Section 2 Question 2a: Add profit column 

```bash
CREATE OR REPLACE TABLE `massive-physics-476110-f0.MRDIY_de.added_profit` AS
SELECT
  month,
  product,
  store_code,
  category,
  sales_qty,
  sales_amt,
  sales_cost,
  (sales_amt - sales_cost) AS profit
FROM `massive-physics-476110-f0.MRDIY_de.sql_test_raw`;
```

### Section 2 Question 2b: Contribution by category
```bash
CREATE OR REPLACE TABLE `massive-physics-476110-f0.MRDIY_de.category_total` AS
SELECT
  month,
  category,
  SUM(sales_qty)  AS total_qty,
  SUM(sales_amt)  AS total_amt,
  SUM(sales_cost) AS total_cost,
  SUM(profit)     AS total_profit
FROM `massive-physics-476110-f0.MRDIY_de.added_profit`
GROUP BY month, category;

```

### Section 2 Question 2c: Join + compute contributions
```bash
CREATE OR REPLACE TABLE `massive-physics-476110-f0.MRDIY_de.contribution_by_catogary` AS
WITH joined AS (
  SELECT
    p.month,
    p.product,
    p.category,
    p.sales_qty,
    p.sales_amt,
    p.sales_cost,
    p.profit,
    t.total_qty,
    t.total_amt,
    t.total_cost,
    t.total_profit
  FROM `massive-physics-476110-f0.MRDIY_de.added_profit` p
  JOIN `massive-physics-476110-f0.MRDIY_de.category_total` t
    USING (month, category)
)
SELECT 
  month,
  product,
  category,
  FORMAT('%.0f%%', SAFE_DIVIDE(sales_qty,  total_qty)   * 100) AS qty_contrib,
  FORMAT('%.0f%%', SAFE_DIVIDE(sales_amt,  total_amt)   * 100) AS amt_contrib,
  FORMAT('%.0f%%', SAFE_DIVIDE(sales_cost, total_cost)  * 100) AS cost_contrib,
  FORMAT('%.0f%%', SAFE_DIVIDE(profit,     total_profit)* 100) AS profit_contrib
FROM joined;
```
### Section 2 Question 2d: Expected pivot table
```bash
CREATE OR REPLACE TABLE `massive-physics-476110-f0.MRDIY_de.sql_test_expected` AS
SELECT *
FROM (
  SELECT product, category, month,
         qty_contrib, amt_contrib, cost_contrib, profit_contrib
  FROM `massive-physics-476110-f0.MRDIY_de.contribution_by_catogary`
)
PIVOT (
  MAX(qty_contrib)   AS qty_contrib,
  MAX(amt_contrib)   AS amt_contrib,
  MAX(cost_contrib)  AS cost_contrib,
  MAX(profit_contrib) AS profit_contrib
  FOR month IN ('Jan-25','Feb-25','Mar-25','Apr-25','May-25','Jun-25','Jul-25','Aug-25')
);
```
