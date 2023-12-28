## Investor Meeting Next Week

> Now that we’ve been in market for 3 years, we’ve generated enough growth to raise a much larger round of venture one of the best West Coast firms.  capital funding. We’re close to securing a large round from I need your analytical skills to help me paint a picture of high growth, and data-driven performance optimization.


### TASK 1 

First, I’d like to show our volume growth. Can you pull overall session and order volume, trended by quarter for the life of the business? Since the most recent quarter is incomplete, you can decide how to handle it.


```
SELECT 
    YEAR(ws.created_at) AS Year,
    CONCAT('Q', QUARTER(ws.created_at)) AS Month,
    COUNT(DISTINCT ws.website_session_id) AS TotalSessions,
    COUNT(DISTINCT o.order_id) AS TotalOrders
FROM 
    website_sessions ws
LEFT JOIN 
    orders o ON ws.website_session_id = o.website_session_id
GROUP BY 
    Year, Month
ORDER BY 
    Year, Month;
```
### Results

| Year | Month | TotalSessions | TotalOrders |
|------|-------|---------------|-------------|
| 2012 | Q1    | 1879          | 60          |
| 2012 | Q2    | 11433         | 347         |
| 2012 | Q3    | 16892         | 684         |
| 2012 | Q4    | 32266         | 1495        |
| 2013 | Q1    | 19833         | 1273        |
| 2013 | Q2    | 24745         | 1718        |
| 2013 | Q3    | 27663         | 1840        |
| 2013 | Q4    | 40540         | 2616        |
| 2014 | Q1    | 46779         | 3069        |
| 2014 | Q2    | 53129         | 3848        |
| 2014 | Q3    | 57141         | 4035        |
| 2014 | Q4    | 76373         | 5908        |
| 2015 | Q1    | 64198         | 5420        |


### TASK 2 

Next, let’s showcase all of our efficiency improvements. I would love to show quarterly figures since we launched, for session-to-order conversion rate, revenue per order, and revenue per session. 


```
SELECT 
    YEAR(ws.created_at) AS Year,
    CONCAT('Q', QUARTER(ws.created_at)) AS Quarter, 
    COUNT(DISTINCT o.order_id) / COUNT(DISTINCT ws.website_session_id) AS SessionToOrderConversionRate, 
    SUM(o.price_usd) / COUNT(DISTINCT o.order_id) AS RevenuePerOrder, 
    SUM(o.price_usd) / COUNT(DISTINCT ws.website_session_id) AS RevenuePerSession
FROM 
    website_sessions ws
LEFT JOIN 
    orders o ON ws.website_session_id = o.website_session_id
GROUP BY 
    Year, Quarter
ORDER BY 
    Year, Quarter;
```
### Results

| Year | Quarter | SessionToOrderConversionRate | RevenuePerOrder | RevenuePerSession |
|------|---------|-----------------------------|------------------|-------------------|
| 2012 | Q1      | 0.0319                      | 49.99            | 1.596275          |
| 2012 | Q2      | 0.0304                      | 49.99            | 1.517233          |
| 2012 | Q3      | 0.0405                      | 49.99            | 2.024222          |
| 2012 | Q4      | 0.0463                      | 49.99            | 2.316217          |
| 2013 | Q1      | 0.0642                      | 52.142396        | 3.346809          |
| 2013 | Q2      | 0.0694                      | 51.538312        | 3.578211          |
| 2013 | Q3      | 0.0665                      | 51.734533        | 3.441114          |
| 2013 | Q4      | 0.0645                      | 54.715688        | 3.530741          |
| 2014 | Q1      | 0.0656                      | 62.160684        | 4.078136          |
| 2014 | Q2      | 0.0724                      | 64.374207        | 4.662462          |
| 2014 | Q3      | 0.0706                      | 64.494949        | 4.554298          |
| 2014 | Q4      | 0.0774                      | 63.793497        | 4.934885          |
| 2015 | Q1      | 0.0844                      | 62.799917        | 5.301965          |



### TASK 3 

I’d like to show how we’ve grown specific channels. Could you pull a quarterly view of orders from Gsearch nonbrand, Bsearch nonbrand, brand search overall, organic search, and direct type-in?


```
SELECT 
    YEAR(ws.created_at) AS Year,
    CONCAT('Q', QUARTER(ws.created_at)) AS Quarter, 
    COUNT(DISTINCT CASE WHEN ws.utm_source = 'gsearch' AND ws.utm_campaign = 'nonbrand' THEN o.order_id ELSE NULL END) AS GoogleSearch_NonBrand_Orders, 
    COUNT(DISTINCT CASE WHEN ws.utm_source = 'bsearch' AND ws.utm_campaign = 'nonbrand' THEN o.order_id ELSE NULL END) AS BingSearch_NonBrand_Orders, 
    COUNT(DISTINCT CASE WHEN ws.utm_campaign = 'brand' THEN o.order_id ELSE NULL END) AS BrandSearch_Orders,
    COUNT(DISTINCT CASE WHEN ws.utm_source IS NULL AND ws.http_referer IS NOT NULL THEN o.order_id ELSE NULL END) AS OrganicSearch_Orders,
    COUNT(DISTINCT CASE WHEN ws.utm_source IS NULL AND ws.http_referer IS NULL THEN o.order_id ELSE NULL END) AS DirectTypeIn_Orders
FROM 
    website_sessions ws
LEFT JOIN 
    orders o ON ws.website_session_id = o.website_session_id
GROUP BY 
    Year, Quarter
ORDER BY 
    Year, Quarter;
```
### Results

| Year | Quarter | GoogleSearch_NonBrand_Orders | BingSearch_NonBrand_Orders | BrandSearch_Orders | OrganicSearch_Orders | DirectTypeIn_Orders |
|------|---------|-----------------------------|---------------------------|--------------------|----------------------|----------------------|
| 2012 | Q1      | 60                          | 0                         | 0                  | 0                    | 0                    |
| 2012 | Q2      | 291                         | 0                         | 20                 | 15                   | 21                   |
| 2012 | Q3      | 482                         | 82                        | 48                 | 40                   | 32                   |
| 2012 | Q4      | 913                         | 311                       | 88                 | 94                   | 89                   |
| 2013 | Q1      | 766                         | 183                       | 108                | 125                  | 91                   |
| 2013 | Q2      | 1114                        | 237                       | 114                | 134                  | 119                  |
| 2013 | Q3      | 1132                        | 245                       | 153                | 167                  | 143                  |
| 2013 | Q4      | 1657                        | 291                       | 248                | 223                  | 197                  |
| 2014 | Q1      | 1667                        | 344                       | 354                | 338                  | 311                  |
| 2014 | Q2      | 2208                        | 427                       | 410                | 436                  | 367                  |
| 2014 | Q3      | 2259                        | 434                       | 432                | 445                  | 402                  |
| 2014 | Q4      | 3248                        | 683                       | 615                | 605                  | 532                  |
| 2015 | Q1      | 3025                        | 581                       | 622                | 640                  | 552                  |


### TASK 4 

Next, let’s show the overall session-to-order conversion rate trends for those same channels, by quarter. Please also make a note of any periods where we made major improvements or optimizations.

```
SELECT 
    YEAR(ws.created_at) AS Year,
    CONCAT('Q', QUARTER(ws.created_at)) AS Quarter, 
    COUNT(DISTINCT CASE WHEN ws.utm_source = 'gsearch' AND ws.utm_campaign = 'nonbrand' THEN o.order_id ELSE NULL END)
        / COUNT(DISTINCT CASE WHEN ws.utm_source = 'gsearch' AND ws.utm_campaign = 'nonbrand' THEN ws.website_session_id ELSE NULL END) AS GoogleSearch_NonBrand_ConversionRate, 

    COUNT(DISTINCT CASE WHEN ws.utm_source = 'bsearch' AND ws.utm_campaign = 'nonbrand' THEN o.order_id ELSE NULL END) 
        / COUNT(DISTINCT CASE WHEN ws.utm_source = 'bsearch' AND ws.utm_campaign = 'nonbrand' THEN ws.website_session_id ELSE NULL END) AS BingSearch_NonBrand_ConversionRate, 

    COUNT(DISTINCT CASE WHEN ws.utm_campaign = 'brand' THEN o.order_id ELSE NULL END) 
        / COUNT(DISTINCT CASE WHEN ws.utm_campaign = 'brand' THEN ws.website_session_id ELSE NULL END) AS BrandSearch_ConversionRate,

    COUNT(DISTINCT CASE WHEN ws.utm_source IS NULL AND ws.http_referer IS NOT NULL THEN o.order_id ELSE NULL END) 
        / COUNT(DISTINCT CASE WHEN ws.utm_source IS NULL AND ws.http_referer IS NOT NULL THEN ws.website_session_id ELSE NULL END) AS OrganicSearch_ConversionRate,

    COUNT(DISTINCT CASE WHEN ws.utm_source IS NULL AND ws.http_referer IS NULL THEN o.order_id ELSE NULL END) 
        / COUNT(DISTINCT CASE WHEN ws.utm_source IS NULL AND ws.http_referer IS NULL THEN ws.website_session_id ELSE NULL END) AS DirectTypeIn_ConversionRate
FROM 
    website_sessions ws
LEFT JOIN 
    orders o ON ws.website_session_id = o.website_session_id
GROUP BY 
    Year, Quarter
ORDER BY 
    Year, Quarter;
```
### Results

| Year | Quarter | GoogleSearch_NonBrand_ConversionRate | BingSearch_NonBrand_ConversionRate | BrandSearch_ConversionRate | OrganicSearch_ConversionRate | DirectTypeIn_ConversionRate |
|------|---------|-------------------------------------|-----------------------------------|----------------------------|-----------------------------|-----------------------------|
| 2012 | Q1      | 0.0324                              | NULL                              | 0                          | 0                           | 0                           |
| 2012 | Q2      | 0.0284                              | NULL                              | 0.0526                     | 0.0359                      | 0.0536                      |
| 2012 | Q3      | 0.0384                              | 0.0408                            | 0.0602                     | 0.0498                      | 0.0443                      |
| 2012 | Q4      | 0.0436                              | 0.0497                            | 0.0531                     | 0.0539                      | 0.0537                      |
| 2013 | Q1      | 0.0612                              | 0.0693                            | 0.0703                     | 0.0753                      | 0.0614                      |
| 2013 | Q2      | 0.0685                              | 0.069                             | 0.0679                     | 0.076                       | 0.0735                      |
| 2013 | Q3      | 0.0639                              | 0.0697                            | 0.0703                     | 0.0734                      | 0.0719                      |
| 2013 | Q4      | 0.0629                              | 0.0601                            | 0.0801                     | 0.0694                      | 0.0647                      |
| 2014 | Q1      | 0.0693                              | 0.0704                            | 0.0839                     | 0.0756                      | 0.0765                      |
| 2014 | Q2      | 0.0702                              | 0.0695                            | 0.0804                     | 0.0797                      | 0.0738                      |
| 2014 | Q3      | 0.0703                              | 0.0698                            | 0.0756                     | 0.0733                      | 0.0702                      |
| 2014 | Q4      | 0.0782                              | 0.0841                            | 0.0812                     | 0.0784                      | 0.0748                      |
| 2015 | Q1      | 0.0861                              | 0.085                             | 0.0852                     | 0.0821                      | 0.0775                      |


### TASK 5

We’ve come a long way since the days of selling a single product. Let’s pull monthly trending for revenue and margin by product, along with total sales and revenue. Note anything you notice about seasonality.


```
SELECT
    YEAR(created_at) AS Year,
    MONTH(created_at) AS Month,
    SUM(CASE WHEN product_id = 1 THEN price_usd ELSE NULL END) AS MrFuzzy_Revenue,
    SUM(CASE WHEN product_id = 1 THEN price_usd - cogs_usd ELSE NULL END) AS MrFuzzy_Margin,
    SUM(CASE WHEN product_id = 2 THEN price_usd ELSE NULL END) AS LoveBear_Revenue,
    SUM(CASE WHEN product_id = 2 THEN price_usd - cogs_usd ELSE NULL END) AS LoveBear_Margin,
    SUM(CASE WHEN product_id = 3 THEN price_usd ELSE NULL END) AS BirthdayBear_Revenue,
    SUM(CASE WHEN product_id = 3 THEN price_usd - cogs_usd ELSE NULL END) AS BirthdayBear_Margin,
    SUM(CASE WHEN product_id = 4 THEN price_usd ELSE NULL END) AS MiniBear_Revenue,
    SUM(CASE WHEN product_id = 4 THEN price_usd - cogs_usd ELSE NULL END) AS MiniBear_Margin,
    SUM(price_usd) AS Total_Revenue,
    SUM(price_usd - cogs_usd) AS Total_Margin
FROM 
    order_items
GROUP BY 
    Year, Month
ORDER BY 
    Year, Month;
```
### Results

| Year | Month | MrFuzzy_Revenue | MrFuzzy_Margin | LoveBear_Revenue | LoveBear_Margin | BirthdayBear_Revenue | BirthdayBear_Margin | MiniBear_Revenue | MiniBear_Margin | Total_Revenue | Total_Margin |
|------|-------|-----------------|----------------|-------------------|------------------|----------------------|----------------------|------------------|-----------------|---------------|--------------|
| 2012 | 3     | 2999.4          | 1830           | NULL              | NULL             | NULL                 | NULL                 | NULL             | NULL            | 2999.4        | 1830         |
| 2012 | 4     | 4949.01         | 3019.5         | NULL              | NULL             | NULL                 | NULL                 | NULL             | NULL            | 4949.01       | 3019.5       |
| 2012 | 5     | 5398.92         | 3294           | NULL              | NULL             | NULL                 | NULL                 | NULL             | NULL            | 5398.92       | 3294         |
| 2012 | 6     | 6998.6          | 4270           | NULL              | NULL             | NULL                 | NULL                 | NULL             | NULL            | 6998.6        | 4270         |
| 2012 | 7     | 8448.31         | 5154.5         | NULL              | NULL             | NULL                 | NULL                 | NULL             | NULL            | 8448.31       | 5154.5       |
| 2012 | 8     | 11397.72        | 6954           | NULL              | NULL             | NULL                 | NULL                 | NULL             | NULL            | 11397.72      | 6954         |
| 2012 | 9     | 14347.13        | 8753.5         | NULL              | NULL             | NULL                 | NULL                 | NULL             | NULL            | 14347.13      | 8753.5       |
| 2012 | 10    | 18546.29        | 11315.5        | NULL              | NULL             | NULL                 | NULL                 | NULL             | NULL            | 18546.29      | 11315.5      |
| 2012 | 11    | 30893.82        | 18849          | NULL              | NULL             | NULL                 | NULL                 | NULL             | NULL            | 30893.82      | 18849        |
| 2012 | 12    | 25294.94        | 15433          | NULL              | NULL             | NULL                 | NULL                 | NULL             | NULL            | 25294.94      | 15433        |
| 2013 | 1     | 17146.57        | 10461.5        | 2819.53           | 1762.5           | NULL                 | NULL                 | NULL             | NULL            | 19966.1       | 12224        |
| 2013 | 2     | 16796.64        | 10248          | 9718.38           | 6075             | NULL                 | NULL                 | NULL             | NULL            | 26515.02      | 16323        |
| 2013 | 3     | 15996.8         | 9760           | 3899.35           | 2437.5           | NULL                 | NULL                 | NULL             | NULL            | 19896.15      | 12197.5      |
| 2013 | 4     | 22945.41        | 13999.5        | 5639.06           | 3525             | NULL                 | NULL                 | NULL             | NULL            | 28584.47      | 17524.5      |
| 2013 | 5     | 24445.11        | 14914.5        | 4919.18           | 3075             | NULL                 | NULL                 | NULL             | NULL            | 29364.29      | 17989.5      |
| 2013 | 6     | 25144.97        | 15341.5        | 5399.1            | 3375             | NULL                 | NULL                 | NULL             | NULL            | 30544.07      | 18716.5      |
| 2013 | 7     | 25444.91        | 15524.5        | 5699.05           | 3562.5           | NULL                 | NULL                 | NULL             | NULL            | 31143.96      | 19087        |
| 2013 | 8     | 25494.9         | 15555          | 5879.02           | 3675             | NULL                 | NULL                 | NULL             | NULL            | 31373.92      | 19230        |
| 2013 | 9     | 26844.63        | 16378.5        | 5879.02           | 3675             | NULL                 | NULL                 | NULL             | NULL            | 32723.65      | 20053.5      |
| 2013 | 10    | 30143.97        | 18391.5        | 8098.65           | 5062.5           | NULL                 | NULL                 | NULL             | NULL            | 38242.62      | 23454        |
| 2013 | 11    | 36192.76        | 22082          | 10438.26          | 6525             | NULL                 | NULL                 | NULL             | NULL            | 46631.02      | 28607        |
| 2013 | 12    | 40891.82        | 24949          | 10978.17          | 6862.5           | 6392.61              | 4378.5               | NULL             | NULL            | 58262.6       | 36190        |
| 2014 | 1     | 36392.72        | 22204          | 10978.17          | 6862.5           | 9198                 | 6300                 | NULL             | NULL            | 56568.89      | 35366.5      |
| 2014 | 2     | 29194.16        | 17812          | 21056.49          | 13162.5          | 9703.89              | 6646.5               | 6057.98          | 4141            | 66012.52      | 41762        |
| 2014 | 3     | 39242.15        | 23942.5        | 11578.07          | 7237.5           | 11221.56             | 7686                 | 6147.95          | 4202.5          | 68189.73      | 43068.5      |
| 2014 | 4     | 45840.83        | 27968.5        | 12837.86          | 8025             | 12279.33             | 8410.5               | 7767.41          | 5309.5          | 78725.43      | 49713.5      |
| 2014 | 5     | 51489.7         | 31415          | 14757.54          | 9225             | 13751.01             | 9418.5               | 8937.02          | 6109            | 88935.27      | 56167.5      |
| 2014 | 6     | 44641.07        | 27236.5        | 14697.55          | 9187.5           | 13245.12             | 9072                 | 7467.51          | 5104.5          | 80051.25      | 50600.5      |
| 2014 | 7     | 48040.39        | 29310.5        | 14637.56          | 9150             | 12693.24             | 8694                 | 7917.36          | 5412            | 83288.55      | 52566.5      |
| 2014 | 8     | 47890.42        | 29219          | 14217.63          | 8887.5           | 13521.06             | 9261                 | 9086.97          | 6211.5          | 84716.08      | 53579        |
| 2014 | 9     | 52789.44        | 32208          | 15057.49          | 9412.5           | 14578.83             | 9985.5               | 9806.73          | 6703.5          | 92232.49      | 58309.5      |
| 2014 | 10    | 58638.27        | 35776.5        | 17037.16          | 10650            | 16924.32             | 11592                | 11306.23         | 7728.5          | 103905.98     | 65747        |
| 2014 | 11    | 72535.49        | 44255.5        | 22616.23          | 14137.5          | 19545.75             | 13387.5              | 13465.51         | 9204.5          | 128162.98     | 80985        |
| 2014 | 12    | 79184.16        | 48312          | 23216.13          | 14512.5          | 24788.61             | 16978.5              | 17634.12         | 12054           | 144823.02     | 91857        |
| 2015 | 1     | 69586.08        | 42456          | 23636.06          | 14775            | 20695.5              | 14175                | 18293.9          | 12505           | 132211.54     | 83911        |
| 2015 | 2     | 55638.87        | 33946.5        | 38633.56          | 24150            | 18625.95             | 12757.5              | 16314.56         | 11152           | 129212.94     | 82006        |
| 2015 | 3     | 43191.36        | 26352          | 13377.77          | 8362.5           | 12095.37             | 8284.5               | 10286.57         | 7031.5          | 78951.07      | 50030.5      |


### TASK 6

Let’s dive deeper into the impact of introducing new products. Please pull monthly sessions to the /products page, and show how the % of those sessions clicking through another page has changed over time, along with a view of how conversion from /products to lacing an order has improved.


```
CREATE TEMPORARY TABLE products_pageviews AS
SELECT
    website_session_id,
    website_pageview_id,
    created_at AS saw_product_page_at
FROM
    website_pageviews
WHERE
    pageview_url = '/products';

-- Analyze user behavior related to product page views
SELECT
    YEAR(saw_product_page_at) AS Year,
    MONTH(saw_product_page_at) AS Month,
    COUNT(DISTINCT pp.website_session_id) AS Sessions_to_Product_Page,
    COUNT(DISTINCT wp.website_session_id) AS Clicked_to_Next_Page,
    COUNT(DISTINCT wp.website_session_id) / COUNT(DISTINCT pp.website_session_id) AS Clickthrough_Rate,
    COUNT(DISTINCT o.order_id) AS Orders,
    COUNT(DISTINCT o.order_id) / COUNT(DISTINCT pp.website_session_id) AS Products_to_Order_Rate
FROM
    products_pageviews pp
LEFT JOIN
    website_pageviews wp ON wp.website_session_id = pp.website_session_id
    AND wp.website_pageview_id > pp.website_pageview_id -- they had another page AFTER
LEFT JOIN
    orders o ON o.website_session_id = pp.website_session_id
GROUP BY
    Year, Month;
```
### Results
| Year | Month | Sessions_to_Product_Page | Clicked_to_Next_Page | Clickthrough_Rate | Orders | Products_to_Order_Rate |
|------|-------|---------------------------|-----------------------|-------------------|--------|-------------------------|
| 2012 | 3     | 743                       | 530                   | 0.7133            | 60     | 0.0808                  |
| 2012 | 4     | 1447                      | 1029                  | 0.7111            | 99     | 0.0684                  |
| 2012 | 5     | 1584                      | 1135                  | 0.7165            | 108    | 0.0682                  |
| 2012 | 6     | 1752                      | 1247                  | 0.7118            | 140    | 0.0799                  |
| 2012 | 7     | 2018                      | 1438                  | 0.7126            | 169    | 0.0837                  |
| 2012 | 8     | 3012                      | 2198                  | 0.7297            | 228    | 0.0757                  |
| 2012 | 9     | 3126                      | 2258                  | 0.7223            | 287    | 0.0918                  |
| 2012 | 10    | 4030                      | 2948                  | 0.7315            | 371    | 0.0921                  |
| 2012 | 11    | 6743                      | 4849                  | 0.7191            | 618    | 0.0917                  |
| 2012 | 12    | 5013                      | 3620                  | 0.7221            | 506    | 0.1009                  |
| 2013 | 1     | 3380                      | 2595                  | 0.7678            | 391    | 0.1157                  |
| 2013 | 2     | 3685                      | 2803                  | 0.7607            | 497    | 0.1349                  |
| 2013 | 3     | 3371                      | 2576                  | 0.7642            | 385    | 0.1142                  |
| 2013 | 4     | 4362                      | 3356                  | 0.7694            | 553    | 0.1268                  |
| 2013 | 5     | 4684                      | 3609                  | 0.7705            | 571    | 0.1219                  |
| 2013 | 6     | 4600                      | 3536                  | 0.7687            | 594    | 0.1291                  |
| 2013 | 7     | 5020                      | 3890                  | 0.7749            | 603    | 0.1201                  |
| 2013 | 8     | 5226                      | 3951                  | 0.756             | 608    | 0.1163                  |
| 2013 | 9     | 5399                      | 4072                  | 0.7542            | 629    | 0.1165                  |
| 2013 | 10    | 6038                      | 4564                  | 0.7559            | 708    | 0.1173                  |
| 2013 | 11    | 7886                      | 5900                  | 0.7482            | 861    | 0.1092                  |
| 2013 | 12    | 8840                      | 7026                  | 0.7948            | 1047   | 0.1184                  |
| 2014 | 1     | 7790                      | 6387                  | 0.8199            | 983    | 0.1262                  |
| 2014 | 2     | 7960                      | 6485                  | 0.8147            | 1021   | 0.1283                  |
| 2014 | 3     | 8110                      | 6669                  | 0.8223            | 1065   | 0.1313                  |
| 2014 | 4     | 9744                      | 7958                  | 0.8167            | 1241   | 0.1274                  |
| 2014 | 5     | 10261                     | 8465                  | 0.825             | 1368   | 0.1333                  |
| 2014 | 6     | 10011                     | 8260                  | 0.8251            | 1239   | 0.1238                  |
| 2014 | 7     | 10837                     | 8958                  | 0.8266            | 1287   | 0.1188                  |
| 2014 | 8     | 10768                     | 8980                  | 0.834             | 1324   | 0.123                   |
| 2014 | 9     | 11128                     | 9156                  | 0.8228            | 1424   | 0.128                   |
| 2014 | 10    | 12335                     | 10235                 | 0.8298            | 1609   | 0.1304                  |
| 2014 | 11    | 14476                     | 12020                 | 0.8303            | 1985   | 0.1371                  |
| 2014 | 12    | 17240                     | 14609                 | 0.8474            | 2314   | 0.1342                  |
| 2015 | 1     | 15217                     | 12992                 | 0.8538            | 2099   | 0.1379                  |
| 2015 | 2     | 14373                     | 12187                 | 0.8479            | 2067   | 0.1438                  |
| 2015 | 3     | 9022                      | 7723                  | 0.856             | 1254   | 0.139                   |


### TASK 7

We made our 4th product available as a primary product on December 05, 2014 (it was previously only a cross-sell item). Could you please pull sales data since then, and show how well each product cross-sells from one another?

```
CREATE TEMPORARY TABLE primary_products AS
SELECT 
    order_id, 
    primary_product_id, 
    created_at AS ordered_at
FROM 
    orders 
WHERE 
    created_at > '2014-12-05'; 

SELECT 
    primary_product_id, 
    COUNT(DISTINCT order_id) AS Total_Orders, 
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 1 THEN order_id END) AS CrossSold_P1,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 2 THEN order_id END) AS CrossSold_P2,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 3 THEN order_id END) AS CrossSold_P3,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 4 THEN order_id END) AS CrossSold_P4,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 1 THEN order_id END) / COUNT(DISTINCT order_id) AS P1_CrossSell_Rate,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 2 THEN order_id END) / COUNT(DISTINCT order_id) AS P2_CrossSell_Rate,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 3 THEN order_id END) / COUNT(DISTINCT order_id) AS P3_CrossSell_Rate,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 4 THEN order_id END) / COUNT(DISTINCT order_id) AS P4_CrossSell_Rate
FROM 
(
    -- Subquery to retrieve primary products and their cross-sell information
    SELECT
        pp.*,
        oi.product_id AS cross_sell_product_id
    FROM 
        primary_products pp
    LEFT JOIN 
        order_items oi ON oi.order_id = pp.order_id AND oi.is_primary_item = 0 -- Only bringing in cross-sells
) AS primary_w_cross_sell
GROUP BY 
    primary_product_id;
```
### Results

| primary_product_id | Total_Orders | CrossSold_P1 | CrossSold_P2 | CrossSold_P3 | CrossSold_P4 | P1_CrossSell_Rate | P2_CrossSell_Rate | P3_CrossSell_Rate | P4_CrossSell_Rate |
|--------------------|--------------|--------------|--------------|--------------|--------------|-------------------|-------------------|-------------------|-------------------|
| 1                  | 4467         | 0            | 238          | 553          | 933          | 0                 | 0.0533            | 0.1238            | 0.2089            |
| 2                  | 1277         | 25           | 0            | 40           | 260          | 0.0196            | 0                 | 0.0313            | 0.2036            |
| 3                  | 929          | 84           | 40           | 0            | 208          | 0.0904            | 0.0431            | 0                 | 0.2239            |
| 4                  | 581          | 16           | 9            | 22           | 0            | 0.0275            | 0.0155            | 0.0379            | 0                 |
