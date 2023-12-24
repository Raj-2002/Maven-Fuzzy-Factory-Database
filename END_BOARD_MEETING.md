### 1. First, I’d like to show our volume growth. Can you pull overall session and order volume, 
trended by quarter for the life of the business? Since the most recent quarter is incomplete, 
you can decide how to handle it.
 
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

### 2. Next, let’s showcase all of our efficiency improvements. I would love to show quarterly figures 
since we launched, for session-to-order conversion rate, revenue per order, and revenue per session. 


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

### 3. I’d like to show how we’ve grown specific channels. Could you pull a quarterly view of orders 
from Gsearch nonbrand, Bsearch nonbrand, brand search overall, organic search, and direct type-in?


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

### 4. Next, let’s show the overall session-to-order conversion rate trends for those same channels, 
by quarter. Please also make a note of any periods where we made major improvements or optimizations.

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

### 5. We’ve come a long way since the days of selling a single product. Let’s pull monthly trending for revenue 
and margin by product, along with total sales and revenue. Note anything you notice about seasonality.


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

### 6. Let’s dive deeper into the impact of introducing new products. Please pull monthly sessions to 
the /products page, and show how the % of those sessions clicking through another page has changed 
over time, along with a view of how conversion from /products to placing an order has improved.


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

### 7. We made our 4th product available as a primary product on December 05, 2014 (it was previously only a cross-sell item). 
Could you please pull sales data since then, and show how well each product cross-sells from one another?

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
