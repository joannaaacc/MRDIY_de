## Full Execution Guide (SQL in Google Big Query) 
Declare at the very first.
```bash
DECLARE months ARRAY<DATE>;
DECLARE col_exprs STRING;
DECLARE sql STRING;
```
### Section 2 Question 1: Create table & insert dummy data
You may change the project name according to your's.
```bash
CREATE OR REPLACE TABLE `massive-physics-476110-f0.MRDIY_de.sql_test_raw` (
  month DATE,
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
INSERT INTO `massive-physics-476110-f0.MRDIY_de.sql_test_raw`
(month, product, store_code, category, sales_qty, sales_amt, sales_cost)
VALUES
(PARSE_DATE('%b-%y','Jan-25'),'ProdA','S001','Beverage',120,600,360),
(PARSE_DATE('%b-%y','Jan-25'),'ProdB','S001','Beverage',140,700,420),
(PARSE_DATE('%b-%y','Jan-25'),'ProdC','S002','Snack',180,720,450),
(PARSE_DATE('%b-%y','Jan-25'),'ProdD','S003','Snack',150,600,370),
(PARSE_DATE('%b-%y','Jan-25'),'ProdE','S004','Dairy',200,1000,650),
(PARSE_DATE('%b-%y','Jan-25'),'ProdF','S005','Dairy',100,500,320),
(PARSE_DATE('%b-%y','Feb-25'),'ProdA','S001','Beverage',130,650,390),
(PARSE_DATE('%b-%y','Feb-25'),'ProdB','S002','Beverage',120,600,360),
(PARSE_DATE('%b-%y','Feb-25'),'ProdC','S003','Snack',170,680,420),
(PARSE_DATE('%b-%y','Feb-25'),'ProdD','S004','Snack',160,640,400),
(PARSE_DATE('%b-%y','Feb-25'),'ProdE','S005','Dairy',190,950,600),
(PARSE_DATE('%b-%y','Feb-25'),'ProdF','S006','Dairy',110,550,350),
(PARSE_DATE('%b-%y','Mar-25'),'ProdA','S001','Beverage',140,700,420),
(PARSE_DATE('%b-%y','Mar-25'),'ProdB','S002','Beverage',150,750,450),
(PARSE_DATE('%b-%y','Mar-25'),'ProdC','S003','Snack',200,800,500),
(PARSE_DATE('%b-%y','Mar-25'),'ProdD','S004','Snack',170,680,410),
(PARSE_DATE('%b-%y','Mar-25'),'ProdE','S005','Dairy',210,1050,670),
(PARSE_DATE('%b-%y','Mar-25'),'ProdF','S006','Dairy',120,600,360),
(PARSE_DATE('%b-%y','Apr-25'),'ProdA','S002','Beverage',160,800,480),
(PARSE_DATE('%b-%y','Apr-25'),'ProdB','S003','Beverage',130,650,390),
(PARSE_DATE('%b-%y','Apr-25'),'ProdC','S004','Snack',190,760,460),
(PARSE_DATE('%b-%y','Apr-25'),'ProdD','S005','Snack',180,720,440),
(PARSE_DATE('%b-%y','Apr-25'),'ProdE','S006','Dairy',220,1100,700),
(PARSE_DATE('%b-%y','Apr-25'),'ProdF','S007','Dairy',130,650,400),
(PARSE_DATE('%b-%y','May-25'),'ProdA','S001','Beverage',150,750,450),
(PARSE_DATE('%b-%y','May-25'),'ProdB','S002','Beverage',160,800,480),
(PARSE_DATE('%b-%y','May-25'),'ProdC','S003','Snack',210,840,520),
(PARSE_DATE('%b-%y','May-25'),'ProdD','S004','Snack',190,760,460),
(PARSE_DATE('%b-%y','May-25'),'ProdE','S005','Dairy',230,1150,720),
(PARSE_DATE('%b-%y','May-25'),'ProdF','S006','Dairy',140,700,430),
(PARSE_DATE('%b-%y','Jun-25'),'ProdA','S002','Beverage',170,850,510),
(PARSE_DATE('%b-%y','Jun-25'),'ProdB','S003','Beverage',140,700,420),
(PARSE_DATE('%b-%y','Jun-25'),'ProdC','S004','Snack',200,800,480),
(PARSE_DATE('%b-%y','Jun-25'),'ProdD','S005','Snack',180,720,430),
(PARSE_DATE('%b-%y','Jun-25'),'ProdE','S006','Dairy',220,1100,680),
(PARSE_DATE('%b-%y','Jun-25'),'ProdF','S007','Dairy',130,650,390),
(PARSE_DATE('%b-%y','Jul-25'),'ProdA','S001','Beverage',160,800,480),
(PARSE_DATE('%b-%y','Jul-25'),'ProdB','S002','Beverage',150,750,450),
(PARSE_DATE('%b-%y','Jul-25'),'ProdC','S003','Snack',210,840,500),
(PARSE_DATE('%b-%y','Jul-25'),'ProdD','S004','Snack',190,760,460),
(PARSE_DATE('%b-%y','Jul-25'),'ProdE','S005','Dairy',240,1200,750),
(PARSE_DATE('%b-%y','Jul-25'),'ProdF','S006','Dairy',150,750,450),
(PARSE_DATE('%b-%y','Aug-25'),'ProdA','S001','Beverage',170,850,510),
(PARSE_DATE('%b-%y','Aug-25'),'ProdB','S002','Beverage',160,800,480),
(PARSE_DATE('%b-%y','Aug-25'),'ProdC','S003','Snack',220,880,530),
(PARSE_DATE('%b-%y','Aug-25'),'ProdD','S004','Snack',200,800,470),
(PARSE_DATE('%b-%y','Aug-25'),'ProdE','S005','Dairy',250,1250,780),
(PARSE_DATE('%b-%y','Aug-25'),'ProdF','S006','Dairy',140,700,420);
```


### Section 2 Question 2a: Add profit column 

```bash
CREATE OR REPLACE TABLE `massive-physics-476110-f0.MRDIY_de.added_profit` AS
SELECT *, 
(sales_amt - sales_cost) AS profit
FROM `massive-physics-476110-f0.MRDIY_de.sql_test_raw`;
```

### Section 2 Question 2b: Total Contribution 
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
CREATE OR REPLACE TABLE `massive-physics-476110-f0.MRDIY_de.contribution_by_category` AS
SELECT
  p.month, p.product, p.category,
  SAFE_DIVIDE(p.sales_qty,  t.total_qty)  AS qty_contrib,
  SAFE_DIVIDE(p.sales_amt,  t.total_amt)  AS amt_contrib,
  SAFE_DIVIDE(p.sales_cost, t.total_cost) AS cost_contrib,
  SAFE_DIVIDE(p.profit,     t.total_profit) AS profit_contrib
FROM `massive-physics-476110-f0.MRDIY_de.added_profit` p
JOIN `massive-physics-476110-f0.MRDIY_de.category_total` t
USING (month, category);
```
### Section 2 Question 2d: Expected pivot table
```bash
--  Collect unique months (DATE) sorted
SET months = (
  SELECT ARRAY_AGG(DISTINCT month ORDER BY month)
  FROM `massive-physics-476110-f0.MRDIY_de.contribution_by_category`
);

-- Build columns dynamically (each month → 4 metrics)
SET col_exprs = (
  WITH m AS (
    SELECT
      m AS month_date,
      FORMAT_DATE('%b-%y', m) AS label,
      FORMAT_DATE('%Y-%m-%d', m) AS iso
    FROM UNNEST(months) AS m
  )
  SELECT STRING_AGG(
    CONCAT(
      '\n  ,FORMAT("%.0f%%", MAX(IF(month = DATE "', iso, '", qty_contrib, NULL)) * 100) AS `', label, ' sales qty contribution by category`',
      '\n  ,FORMAT("%.0f%%", MAX(IF(month = DATE "', iso, '", amt_contrib, NULL)) * 100) AS `', label, ' sales amt contribution by category`',
      '\n  ,FORMAT("%.0f%%", MAX(IF(month = DATE "', iso, '", cost_contrib, NULL)) * 100) AS `', label, ' sales cost contribution by category`',
      '\n  ,FORMAT("%.0f%%", MAX(IF(month = DATE "', iso, '", profit_contrib, NULL)) * 100) AS `', label, ' profit contribution by category`'
    ),
    ' ' ORDER BY month_date
  )
  FROM m
);

--Build and execute main SQL
SET sql = '''
CREATE OR REPLACE TABLE `massive-physics-476110-f0.MRDIY_de.sql_test_expected` AS
SELECT
  product,
  category''' || col_exprs || '''
FROM `massive-physics-476110-f0.MRDIY_de.contribution_by_category`
GROUP BY product, category
ORDER BY product, category
''';

EXECUTE IMMEDIATE sql;
```
### Section 2 Question 2 Expected Outcome:
<img width="1579" height="239" alt="{C1BC7CFF-CC94-4702-A426-3485700D8DFD}" src="https://github.com/user-attachments/assets/579de036-d8bd-43cb-abd6-a88025d99a77" />
