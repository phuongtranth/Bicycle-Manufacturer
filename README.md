 # [SQL] Sales Performance Analysis
## I. Introduction
This project analyzes a Bicycle Manufacturer's data using BigQuery. Eight queries examine sales, growth, territories, discounts, customer retention, inventory, and order statuses. The insights aim to inform strategic decisions and optimize operations across various aspects of the business.
## II. Dataset Access
The Bicycle Manufacturer dataset is stored in a public Google BigQuery dataset named "adventureworks2019". To access the dataset, follow these steps:
- Log in to your Google Cloud Platform account and create a new project.
- Navigate to the BigQuery console and select your newly created project.
- In the navigation panel, select "Add Data" and then "Search a project".

Dataset Schema: https://i0.wp.com/improveandrepeat.com/wp-content/uploads/2018/12/AdvWorksOLTPSchemaVisio.png?ssl=1
## III. Key Focus Areas
- **Sales and Growth**: Analyze subcategory performance, including sales metrics and year-over-year growth rates.
- **Market Analysis**: Evaluate top-performing territories and assess seasonal discount impacts by subcategory.
- **Customer and Order Insights**: Measure customer retention rates and examine pending order statuses.
- **Inventory Management**: Track stock levels, month-over-month changes, and stock-to-sales ratios.
## IV. Exploring the Dataset
### Query 1: Calc Quantity of items, Sales value & Order quantity by each Subcategory in L12M
```sql
WITH MaxModifiedDate AS (
  SELECT MAX(DATE(od.ModifiedDate)) AS max_date
  FROM `adventureworks2019.Sales.SalesOrderDetail` AS od
),
FilteredOrders AS (
  SELECT 
    od.ModifiedDate,
    ps.name,
    od.OrderQty,
    od.LineTotal,
    od.SalesOrderID	
  FROM 
    `adventureworks2019.Sales.SalesOrderDetail` AS od
  LEFT JOIN 
    `adventureworks2019.Sales.Product` AS p
  USING(ProductID)
  LEFT JOIN 
    `adventureworks2019.Production.ProductSubcategory` AS ps
    ON CAST(p.ProductSubcategoryID AS INT) = ps.ProductSubcategoryID
  WHERE 
    DATE(od.ModifiedDate) >= (SELECT DATE_SUB(max_date, INTERVAL 12 MONTH) FROM MaxModifiedDate)
)
SELECT 
  FORMAT_TIMESTAMP('%b %Y', TIMESTAMP(ModifiedDate)) AS period,
  name,
  SUM(OrderQty) AS qty_item,
  SUM(LineTotal) AS total_sales,
  COUNT(DISTINCT SalesOrderID) AS total_orders
FROM 
  FilteredOrders
GROUP BY 
  period, name
ORDER BY 
  period DESC, name;
```
| period | name              | qty_item | total_sales | total_orders |
| ------ | ----------------- | -------- | ----------- | ------------ |
| Sep-13 | Bike Racks        | 312      | 22828.51    | 71           |
| Sep-13 | Bike Stands       | 26       | 4134        | 26           |
| Sep-13 | Bottles and Cages | 803      | 4676.563    | 380          |
| Sep-13 | Bottom Brackets   | 60       | 3118.14     | 19           |
| Sep-13 | Brakes            | 100      | 6390        | 29           |
| Sep-13 | Caps              | 440      | 2879.483    | 203          |
| Sep-13 | Chains            | 62       | 752.928     | 24           |
| Sep-13 | Cleaners          | 296      | 1611.831    | 108          |
| Sep-13 | Cranksets         | 75       | 13955.85    | 20           |
| Sep-13 | Derailleurs       | 97       | 5972.07     | 23           |
| Sep-13 | Fenders           | 169      | 3714.62     | 169          |
| Sep-13 | Gloves            | 444      | 7552.246    | 146          |
| Sep-13 | Handlebars        | 131      | 6486.588    | 47           |
| Sep-13 | Helmets           | 1016     | 27354.84    | 480          |
| Sep-13 | Hydration Packs   | 249      | 9186.162    | 87           |
| Sep-13 | Jerseys           | 1402     | 48318.09    | 304          |
| Sep-13 | Locks             | 1        | 15          | 1            |
| Sep-13 | Mountain Bikes    | 1194     | 1244717     | 263          |
| Sep-13 | Mountain Frames   | 662      | 224304      | 34           |
| Sep-13 | Pedals            | 438      | 16565.11    | 76           |
| Sep-13 | Road Bikes        | 1398     | 1318889     | 319          |
| Sep-13 | Road Frames       | 264      | 110277      | 38           |
| Sep-13 | Saddles           | 170      | 4250.052    | 39           |
| Sep-13 | Shorts            | 713      | 30568.71    | 130          |
| Sep-13 | Socks             | 430      | 2438.963    | 88           |
| Sep-13 | Tights            | 5        | 243.7175    | 1            |
| Sep-13 | Tires and Tubes   | 1420     | 18561.39    | 787          |
| Sep-13 | Touring Bikes     | 1238     | 1214629     | 171          |
| Sep-13 | Touring Frames    | 337      | 154334.6    | 21           |
| Sep-13 | Vests             | 623      | 24100.47    | 102          |
| Sep-13 | Wheels            | 1        | 83.2981     | 1            |
| Oct-13 | Bike Racks        | 284      | 21181.2     | 70           |
| Oct-13 | Bike Stands       | 24       | 3816        | 24           |
| Oct-13 | Bottles and Cages | 845      | 5038.953    | 400          |
| Oct-13 | Bottom Brackets   | 132      | 7030.008    | 30           |
| Oct-13 | Brakes            | 142      | 9036.781    | 38           |
| Oct-13 | Caps              | 406      | 2738.841    | 208          |
| Oct-13 | Chains            | 93       | 1122.357    | 31           |
| Oct-13 | Cleaners          | 294      | 1612.756    | 108          |
| Oct-13 | Cranksets         | 118      | 19722.79    | 33           |
| Oct-13 | Derailleurs       | 135      | 8291.808    | 35           |
| Oct-13 | Fenders           | 170      | 3736.6      | 170          |
| Oct-13 | Gloves            | 447      | 7678.813    | 148          |
| Oct-13 | Handlebars        | 188      | 9196.104    | 56           |
| Oct-13 | Helmets           | 1127     | 30733.99    | 557          |
| Oct-13 | Hydration Packs   | 243      | 9207.614    | 86           |
| Oct-13 | Jerseys           | 1372     | 47761.26    | 324          |
| Oct-13 | Mountain Bikes    | 1231     | 1338739     | 305          |
| Oct-13 | Mountain Frames   | 632      | 221830.4    | 32           |
| Oct-13 | Pedals            | 374      | 14060.98    | 72           |
| Oct-13 | Road Bikes        | 1288     | 1217739     | 363          |
| Oct-13 | Road Frames       | 247      | 99189.75    | 40           |
| Oct-13 | Saddles           | 226      | 5991.374    | 56           |
| Oct-13 | Shorts            | 661      | 28533.35    | 122          |
| Oct-13 | Socks             | 358      | 2076.261    | 86           |
| Oct-13 | Tires and Tubes   | 1531     | 21152.66    | 849          |
| Oct-13 | Touring Bikes     | 1522     | 1497461     | 221          |
| Oct-13 | Touring Frames    | 283      | 137878.1    | 28           |
| Oct-13 | Vests             | 611      | 23255.74    | 93           |
...

The data reveals a clear hierarchy in product performance. High-ticket items (Road, Mountain, Touring Bikes) drive revenue, while accessories (Helmets, Jerseys, Tires) lead in unit sales. This pattern suggests a "halo effect" where bike sales likely boost accessory purchases. Seasonal trends are evident, with peak sales in spring/summer months. The company could optimize inventory and marketing strategies by leveraging these complementary product relationships and seasonal patterns.

### Query 2: Calc % YoY growth rate by SubCategory & release top 3 cat with highest grow rate
```sql
WITH a AS (
  SELECT 
    EXTRACT(YEAR FROM od.ModifiedDate) AS year,
    ps.name,
    SUM(od.OrderQty) AS qty_item
  FROM 
    `adventureworks2019.Sales.SalesOrderDetail` AS od
  LEFT JOIN 
    `adventureworks2019.Sales.Product` AS p
  USING(ProductID)
  LEFT JOIN 
    `adventureworks2019.Production.ProductSubcategory` AS ps
    ON CAST(p.ProductSubcategoryID AS INT) = ps.ProductSubcategoryID
  GROUP BY EXTRACT(YEAR FROM od.ModifiedDate), ps.name
),

prv AS (
  SELECT 
    year,
    name, 
    qty_item,
    LAG(qty_item) OVER(PARTITION BY name ORDER BY year) AS prv_qty
  FROM a
),

growth_calc AS (
  SELECT 
    name,
    qty_item, 
    prv_qty,
    ROUND(100.0 * (qty_item - prv_qty) / prv_qty, 2) AS growth_rate,
    DENSE_RANK() OVER (ORDER BY ROUND(100.0 * (qty_item - prv_qty) / prv_qty, 2) DESC) AS rank
  FROM prv
  WHERE prv_qty IS NOT NULL
)

SELECT 
  name,
  qty_item, 
  prv_qty,
  growth_rate
FROM growth_calc
WHERE rank <= 3
ORDER BY growth_rate DESC;
```

| Row | name            | qty_item | prv_qty | growth_rate |
| --- | --------------- | -------- | ------- | ----------- |
| 1   | Mountain Frames | 3168     | 510     | 521.18      |
| 2   | Socks           | 2724     | 523     | 420.84      |
| 3   | Road Frames     | 5564     | 1137    | 389.36      |

Mountain Frames, Socks, and Road Frames have shown remarkable increases. Such dramatic growth suggests either a successful product revamp, expansion into new markets, or a shift in consumer preferences. It's crucial to investigate the drivers behind this surge to sustain and potentially replicate this success across other product lines.
### Query 3: Ranking Top 3 TeritoryID with biggest Order quantity of every year. If there's TerritoryID with same quantity in a year
```sql
WITH raw AS
    (SELECT
          EXTRACT( YEAR FROM od.ModifiedDate) AS year,
          oh.TerritoryID,
          SUM(OrderQty) AS order_cnt
          
    FROM adventureworks2019.Sales.SalesOrderDetail as od
    LEFT JOIN adventureworks2019.Sales.SalesOrderHeader as oh
    USING(SalesOrderID)
    GROUP BY EXTRACT( YEAR FROM od.ModifiedDate), oh.TerritoryID),

addrk AS
    (SELECT *,
          DENSE_RANK() OVER(PARTITION BY year ORDER BY order_cnt DESC) AS rk
    FROM raw)

SELECT year,
       TerritoryID,
       order_cnt,
       rk
FROM addrk
WHERE rk<=3
ORDER BY year, rk;
```
| Row | year | TerritoryID | order_cnt | rk |
| --- | ---- | ----------- | --------- | -- |
| 1   | 2011 | 4           | 3238      | 1  |
| 2   | 2011 | 6           | 2705      | 2  |
| 3   | 2011 | 1           | 1964      | 3  |
| 4   | 2012 | 4           | 17553     | 1  |
| 5   | 2012 | 6           | 14412     | 2  |
| 6   | 2012 | 1           | 8537      | 3  |
| 7   | 2013 | 4           | 26682     | 1  |
| 8   | 2013 | 6           | 22553     | 2  |
| 9   | 2013 | 1           | 17452     | 3  |
| 10  | 2014 | 4           | 11632     | 1  |
| 11  | 2014 | 6           | 9711      | 2  |
| 12  | 2014 | 1           | 8823      | 3  |

TerritoryIDs 4, 6, and 1 are the top performers across the period (2011-2014). All territories experienced significant growth from 2011 to 2013, with a notable decline in 2014. This pattern suggests a need to investigate the factors behind the 2014 decrease and potentially replicate the success strategies of Territory 4 in other areas.
### Query 4: Calc Total Discount Cost belongs to Seasonal Discount for each SubCategory
```sql
 SELECT
      EXTRACT( YEAR FROM od.ModifiedDate) AS year,
      ps.name,
      SUM(so.DiscountPct*od.UnitPrice*od.OrderQty) as total_discount

FROM 
    `adventureworks2019.Sales.SalesOrderDetail` AS od
  LEFT JOIN 
    `adventureworks2019.Sales.Product` AS p
  USING(ProductID)
  LEFT JOIN 
    `adventureworks2019.Production.ProductSubcategory` AS ps
    ON CAST(p.ProductSubcategoryID AS INT) = ps.ProductSubcategoryID
  LEFT JOIN 
     `adventureworks2019.Sales.SpecialOffer` AS so
    ON od.SpecialOfferID = so.SpecialOfferID
WHERE lower(so.Type) like '%seasonal discount%' 
GROUP BY year,ps.name;
```
| Row | year | name    | total_discount |
| --- | ---- | ------- | -------------- |
| 1   | 2012 | Helmets | 827.64732      |
| 2   | 2013 | Helmets | 1606.041       |

Seasonal discounts were exclusively applied to the Helmets subcategory in 2012 and 2013. The total discount nearly doubled from $827.65 in 2012 to $1,606.04 in 2013, suggesting an intensified promotional strategy for helmets. This focused discounting approach may indicate efforts to boost helmet sales, potentially due to safety regulations or competitive pressures.
### Query 5: Retention rate of Customer in 2014 with status of Successfully Shipped (Cohort Analysis)
```sql
WITH firstmonth AS
      (SELECT 
            DISTINCT  oh.CustomerID,
            MIN(EXTRACT( MONTH FROM oh.ModifiedDate)) AS month_join

      FROM `adventureworks2019.Sales.SalesOrderDetail`  AS od
      LEFT JOIN `adventureworks2019.Sales.SalesOrderHeader` AS oh
      USING(SalesOrderID)
      WHERE oh.Status = 5
            AND EXTRACT( YEAR FROM oh.ModifiedDate) = 2014
            
      GROUP BY oh.CustomerID
      ORDER BY oh.CustomerID),

allmonth AS
    (SELECT 
                DISTINCT  oh.CustomerID,
                EXTRACT( MONTH FROM oh.ModifiedDate) AS month
          FROM `adventureworks2019.Sales.SalesOrderDetail`  AS od
          LEFT JOIN `adventureworks2019.Sales.SalesOrderHeader` AS oh
          USING(SalesOrderID)
          WHERE oh.Status = 5
                AND EXTRACT( YEAR FROM oh.ModifiedDate) = 2014
          ORDER BY oh.CustomerID)
SELECT f.month_join,
       CONCAT('M - ',a.month-f.month_join) AS month_diff,
       COUNT(CustomerID) 
FROM allmonth AS a 
LEFT JOIN firstmonth AS f
USING(CustomerID)
GROUP BY f.month_join, month_diff
ORDER BY f.month_join, month_diff;
```
| month_join | month_diff | f0_  |
| ---------- | ---------- | ---- |
| 1          | M - 0      | 2076 |
| 1          | M - 1      | 78   |
| 1          | M - 2      | 89   |
| 1          | M - 3      | 252  |
| 1          | M - 4      | 96   |
| 1          | M - 5      | 61   |
| 1          | M - 6      | 18   |
| 2          | M - 0      | 1805 |
| 2          | M - 1      | 51   |
| 2          | M - 2      | 61   |
| 2          | M - 3      | 234  |
| 2          | M - 4      | 58   |
| 2          | M - 5      | 8    |
| 3          | M - 0      | 1918 |
| 3          | M - 1      | 43   |
| 3          | M - 2      | 58   |
| 3          | M - 3      | 44   |
| 3          | M - 4      | 11   |
| 4          | M - 0      | 1906 |
| 4          | M - 1      | 34   |
| 4          | M - 2      | 44   |
| 4          | M - 3      | 7    |
| 5          | M - 0      | 1947 |
| 5          | M - 1      | 40   |
| 5          | M - 2      | 7    |
| 6          | M - 0      | 909  |
| 6          | M - 1      | 10   |
| 7          | M - 0      | 148  |

The cohort analysis reveals a significant drop in customer retention after the initial purchase month across all cohorts. There's a consistent pattern of higher retention in the third month after joining (M-3), particularly for earlier cohorts. This suggests a potential 90-day repurchase cycle. The steep decline in retention rates indicates a need for improved customer engagement strategies, especially targeting the critical period between the first and third months post-purchase.
### Query 6: Trend of Stock level & MoM diff % by all product in 2011
```sql
WITH raw AS
(SELECT p.Name,
          EXTRACT(MONTH FROM wo.ModifiedDate) as month,
          EXTRACT(YEAR FROM wo.ModifiedDate) as year,
          SUM(wo.StockedQty) as Stock_current
  FROM 
    `adventureworks2019.Production.Product` AS p
  LEFT JOIN 
    `adventureworks2019.Production.WorkOrder` AS wo
  USING(ProductID)
  WHERE EXTRACT(YEAR FROM wo.ModifiedDate) = 2011
  GROUP BY Name, month, year),

addprv AS
  (SELECT *,
        LEAD(Stock_current) OVER(PARTITION BY name ORDER BY month DESC) as stock_prv
  FROM raw)

SELECT *, 
       ROUND(100.0*(Stock_current-stock_prv)/stock_prv,1) as diff
FROM addprv
ORDER BY Name, month DESC;
```
| Name            | month | year | Stock_current | stock_prv | diff   |
| --------------- | ----- | ---- | ------------- | --------- | ------ |
| BB Ball Bearing | 12    | 2011 | 8475          | 14544     | \-41.7 |
| BB Ball Bearing | 11    | 2011 | 14544         | 19175     | \-24.2 |
| BB Ball Bearing | 10    | 2011 | 19175         | 8845      | 116.8  |
| BB Ball Bearing | 9     | 2011 | 8845          | 9666      | \-8.5  |
| BB Ball Bearing | 8     | 2011 | 9666          | 12837     | \-24.7 |
| BB Ball Bearing | 7     | 2011 | 12837         | 5259      | 144.1  |
| BB Ball Bearing | 6     | 2011 | 5259          |           |        |
| Blade           | 12    | 2011 | 1842          | 3598      | \-48.8 |
| Blade           | 11    | 2011 | 3598          | 4670      | \-23   |
| Blade           | 10    | 2011 | 4670          | 2122      | 120.1  |
| Blade           | 9     | 2011 | 2122          | 2382      | \-10.9 |
| Blade           | 8     | 2011 | 2382          | 3166      | \-24.8 |
| Blade           | 7     | 2011 | 3166          | 1280      | 147.3  |
| Blade           | 6     | 2011 | 1280          |           |        |
| Chain Stays     | 12    | 2011 | 1842          | 3598      | \-48.8 |
| Chain Stays     | 11    | 2011 | 3598          | 4670      | \-23   |
| Chain Stays     | 10    | 2011 | 4670          | 2122      | 120.1  |
| Chain Stays     | 9     | 2011 | 2122          | 2341      | \-9.4  |
| Chain Stays     | 8     | 2011 | 2341          | 3166      | \-26.1 |
| Chain Stays     | 7     | 2011 | 3166          | 1280      | 147.3  |
| Chain Stays     | 6     | 2011 | 1280          |           |

The data reveals significant stock level fluctuations across products in 2011. A consistent pattern emerges: large increases in July and October (often >100%), followed by steady decreases in subsequent months. This cyclical trend suggests potential issues with inventory management or supply chain efficiency. The company should investigate these dramatic swings to optimize stock levels, reduce holding costs, and ensure product availability during peak demand periods.
### Query 7: Calc Ratio of Stock / Sales in 2011 by product name, by month
```sql
WITH sale AS        
        (SELECT EXTRACT( MONTH FROM od.ModifiedDate) AS month,
                EXTRACT( YEAR FROM od.ModifiedDate) AS year,
                od.ProductID,
                p.Name,
                SUM(od.OrderQty) AS total_sales
        FROM `adventureworks2019.Sales.SalesOrderDetail` od 
        LEFT JOIN `adventureworks2019.Production.Product` p
        USING(ProductID)
        WHERE EXTRACT( YEAR FROM od.ModifiedDate) = 2011
        GROUP BY EXTRACT( MONTH FROM od.ModifiedDate),
                EXTRACT( YEAR FROM od.ModifiedDate),
                od.ProductID,
                p.Name),
      stock AS
      (SELECT EXTRACT( MONTH FROM wo.ModifiedDate) AS month,
                EXTRACT( YEAR FROM wo.ModifiedDate) AS year,
                wo.ProductID,
                SUM(wo.StockedQty) AS total_stock
        FROM `adventureworks2019.Production.WorkOrder` AS wo
        WHERE EXTRACT( YEAR FROM wo.ModifiedDate) = 2011
        GROUP BY EXTRACT( MONTH FROM wo.ModifiedDate),
                EXTRACT( YEAR FROM wo.ModifiedDate),
                wo.ProductID) 
SELECT sa.Month,
        sa.Year,
        sa.ProductID,
        sa.Name,
        COALESCE(sa.total_sales, 0) AS total_sales,
        COALESCE(st.total_stock, 0) AS total_stock,
        ROUND(COALESCE(st.total_stock, 0) / COALESCE(sa.total_sales, 0), 1) AS ratio
FROM  sale AS sa
FULL JOIN stock AS st
ON sa.year = st.year
   AND sa.month = st.month 
   AND sa.ProductID = st.ProductID
ORDER BY month DESC,
         ratio DESC;
```
| Month | Year | ProductID | Name                           | total_sales | total_stock | ratio |
| ----- | ---- | --------- | ------------------------------ | ----------- | ----------- | ----- |
| 12    | 2011 | 745       | HL Mountain Frame - Black, 48  | 1           | 27          | 27    |
| 12    | 2011 | 743       | HL Mountain Frame - Black, 42  | 1           | 26          | 26    |
| 12    | 2011 | 748       | HL Mountain Frame - Silver, 38 | 2           | 32          | 16    |
| 12    | 2011 | 722       | LL Road Frame - Black, 58      | 4           | 47          | 11.8  |
| 12    | 2011 | 747       | HL Mountain Frame - Black, 38  | 3           | 31          | 10.3  |
| 12    | 2011 | 726       | LL Road Frame - Red, 48        | 5           | 36          | 7.2   |
| 12    | 2011 | 738       | LL Road Frame - Black, 52      | 10          | 64          | 6.4   |
| 12    | 2011 | 730       | LL Road Frame - Red, 62        | 7           | 38          | 5.4   |
| 12    | 2011 | 741       | HL Mountain Frame - Silver, 48 | 5           | 27          | 5.4   |
| 12    | 2011 | 725       | LL Road Frame - Red, 44        | 12          | 53          | 4.4   |
| 12    | 2011 | 729       | LL Road Frame - Red, 60        | 10          | 43          | 4.3   |
| 12    | 2011 | 732       | ML Road Frame - Red, 48        | 10          | 16          | 1.6   |
| 12    | 2011 | 750       | Road-150 Red, 44               | 25          | 38          | 1.5   |
| 12    | 2011 | 751       | Road-150 Red, 48               | 32          | 47          | 1.5   |
| 12    | 2011 | 775       | Mountain-100 Black, 38         | 23          | 28          | 1.2   |
| 12    | 2011 | 768       | Road-650 Black, 44             | 19          | 21          | 1.1   |
| 12    | 2011 | 773       | Mountain-100 Silver, 44        | 32          | 36          | 1.1   |
| 12    | 2011 | 778       | Mountain-100 Black, 48         | 25          | 27          | 1.1   |
| 12    | 2011 | 749       | Road-150 Red, 62               | 45          | 51          | 1.1   |

The stock-to-sales ratios reveal significant inventory imbalances across products. High-end mountain frames show excessive stock levels (ratios 10-27) compared to sales, while popular road bikes maintain healthier ratios (1.1-1.5). This suggests potential overproduction or slow turnover for certain mountain frames. The company should reassess its inventory management strategy, particularly for high-ratio items, to optimize stock levels and improve capital efficiency.
### Query 8:No of order and value at Pending status in 2014
```sql
SELECT 
       EXTRACT( YEAR FROM ModifiedDate) AS year,
       Status,
       COUNT(DISTINCT PurchaseOrderID) AS Order_cnt,
       SUM(TotalDue) AS Value
FROM `adventureworks2019.Purchasing.PurchaseOrderHeader` 
WHERE EXTRACT( YEAR FROM ModifiedDate) = 2014
      AND Status = 1
GROUP BY year, Status;
```
| Row | year | Status | Order_cnt | Value       |
| --- | ---- | ------ | --------- | ----------- |
| 1   | 2014 | 1      | 224       | 3873579.012 |

In 2014, there were 224 pending orders with a total value of $3,873,579.01. This high-value backlog suggests potential operational bottlenecks or capacity constraints. The average order value of $17,292.76 indicates these are likely big-ticket items. Addressing these pending orders promptly could significantly impact revenue recognition and customer satisfaction. Further investigation into the causes of delays is recommended.
## V. Conclusion
This project leveraged Google BigQuery to analyze a bicycle manufacturer's dataset, uncovering crucial insights into sales trends, product performance, and operational efficiency. Key findings include the dominance of high-ticket bike sales complemented by accessory volumes, exceptional growth in specific subcategories, and consistent top-performing territories. The analysis revealed opportunities for improvement in inventory management, customer retention strategies, and order fulfillment processes. By harnessing big data analytics, this project provides a solid foundation for data-driven decision-making, enabling the company to optimize product mix, refine marketing strategies, and enhance operational efficiency. These insights equip the manufacturer to make informed strategic choices, potentially driving growth and competitiveness in the dynamic bicycle market.

