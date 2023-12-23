
## Board Meeting Next Week 


>Good morning, I need some help preparing a presentation for the board meeting next week. 
>The board would like to have a better understanding of our growth story over our first 8 months. 
>This will also be a good excuse to show off our analytical capabilities a bit.
>Cindy


### REQUEST 1 

Gsearch seems to be the biggest driver of our business. Could you pull monthly trends for gsearch sessions and orders so that we can showcase the growth there?

```
SELECT 
	YEAR(ws.created_at) AS Year,
    MONTH(ws.created_at) AS Month,
    COUNT(DISTINCT ws.website_session_id) AS Sessions,
    COUNT(DISTINCT o.order_id) AS Orders,
    COALESCE(COUNT(DISTINCT o.order_id) / NULLIF(COUNT(DISTINCT ws.website_session_id), 0), 0)*100 AS CVR_PCT
        
FROM website_sessions ws
LEFT JOIN orders o 
ON o.website_session_id = ws.website_session_id

WHERE ws.created_at < '2012-11-27' AND ws.utm_source = 'gsearch'
GROUP BY Year, Month;
```


### REQUEST 2 

Next, it would be great to see a similar monthly trend for Gsearch, but this time splitting out nonbrand and 
brand campaigns separately. I am wondering if brand is picking up at all. If so, this is a good story to tell.

```
SELECT 
    YEAR(ws.created_at) AS year,
    MONTH(ws.created_at) AS month,
    COUNT(DISTINCT CASE WHEN ws.utm_campaign = 'nonbrand' THEN ws.website_session_id ELSE NULL END) AS non_brand_sessions,
    COUNT(DISTINCT CASE WHEN ws.utm_campaign = 'nonbrand' THEN o.order_id ELSE NULL END) AS non_brand_orders,
    COUNT(DISTINCT CASE WHEN ws.utm_campaign = 'brand' THEN ws.website_session_id ELSE NULL END) AS brand_sessions,
    COUNT(DISTINCT CASE WHEN ws.utm_campaign = 'brand' THEN o.order_id ELSE NULL END) AS brand_orders
FROM 
    website_sessions ws
LEFT JOIN 
	orders o
ON 
	o.website_session_id = ws.website_session_id
WHERE 
	ws.created_at < '2012-11-27'  AND ws.utm_source = 'gsearch'  
GROUP BY 
    YEAR(ws.created_at),
    MONTH(ws.created_at);
```


### REQUEST 3 
While we’re on Gsearch, could you dive into nonbrand, and pull monthly sessions and orders split by device 
type? I want to flex our analytical muscles a little and show the board we really know our traffic sources. 

```
SELECT 
    YEAR(ws.created_at) AS year,
    MONTH(ws.created_at) AS month,
	COUNT(o.order_id) AS nonbrand_orders,
	COUNT(CASE WHEN ws.device_type = 'mobile' THEN o.order_id ELSE NULL END ) AS mobile_orders,
	COUNT(CASE WHEN ws.device_type = 'desktop' THEN o.order_id ELSE NULL END ) AS desktop_orders
-- 	SUM(CASE WHEN ws.device_type = 'mobile' THEN 1 ELSE NULL END) AS mobile_orders,
-- 	SUM(CASE WHEN ws.device_type = 'desktop' THEN 1 ELSE NULL END) AS desktop_orders


FROM 
    website_sessions ws
LEFT JOIN 
	orders o
ON 
	o.website_session_id = ws.website_session_id
WHERE 
	ws.created_at < '2012-11-27' 
    AND ws.utm_campaign = 'nonbrand'
    AND utm_source = 'gsearch'
GROUP BY 
    YEAR(ws.created_at),
    MONTH(ws.created_at);
```


### Request 4 

I’m worried that one of our more pessimistic board members may be concerned about the large % of traffic from 
Gsearch. Can you pull monthly trends for Gsearch, alongside monthly trends for each of our other channels? 

~~~
SELECT DISTINCT utm_source,
utm_campaign,
http_referer
FROM website_sessions
WHERE created_at < '2012-11-27';    

SELECT 
    YEAR(ws.created_at) AS Year,
    MONTH(ws.created_at) AS Month,
    COUNT(DISTINCT CASE WHEN ws.utm_source = 'gsearch' THEN ws.website_session_id ELSE NULL END) AS gsearch_paid_sessions,
    COUNT(DISTINCT CASE WHEN ws.utm_source = 'bsearch' THEN ws.website_session_id ELSE NULL END) AS bsearch_paid_sessions,
    COUNT(DISTINCT CASE WHEN ws.utm_source IS NULL AND ws.http_referer IS NOT NULL THEN ws.website_session_id ELSE NULL END) AS organic_search_sessions,
    COUNT(DISTINCT CASE WHEN ws.utm_source IS NULL AND ws.http_referer IS NULL THEN ws.website_session_id ELSE NULL END) AS direct_type_in_sessions
FROM website_sessions ws
LEFT JOIN orders o ON o.website_session_id = ws.website_session_id
WHERE ws.created_at < '2012-11-27'
GROUP BY Year, Month;
~~~

### REQUEST 5 

I’d like to tell the story of our website performance improvements over the course of the first 8 months. 
Could you pull session to order conversion rates, by month? 

~~~
SELECT 
	YEAR(ws.created_at) AS Year,
    MONTH(ws.created_at) AS Month,
    COUNT(DISTINCT ws.website_session_id) AS Sessions,
    COUNT(DISTINCT o.order_id) AS Orders,
    COALESCE(COUNT(DISTINCT o.order_id) / NULLIF(COUNT(DISTINCT ws.website_session_id), 0), 0)*100 AS CVR_PCT
        
FROM website_sessions ws
LEFT JOIN orders o 
ON o.website_session_id = ws.website_session_id

WHERE ws.created_at < '2012-11-27' 
GROUP BY Year, Month;
~~~

### REQUEST 6 

For the gsearch lander test, please look at the increase in CVR 
from the test (Jun 19 – Jul 28), and use nonbrand sessions 

```
SELECT website_pageview_id 
FROM website_pageviews 
WHERE pageview_url = '/lander-1' 
LIMIT 1;

CREATE TEMPORARY TABLE landing_page_ids
SELECT
	wp.website_session_id AS sessions,
    MIN(website_pageview_id) AS pagview_id,
    wp.pageview_url AS pageview_url, 
    o.order_id AS order_id
FROM website_pageviews  wp
JOIN website_sessions ws
ON ws.website_session_id = wp.website_session_id
LEFT JOIN orders o  
ON o.website_session_id = wp.website_session_id 
WHERE 
	website_pageview_id >= 23504 
    AND wp.created_at < '2012-07-28'
    AND (pageview_url = '/home' OR pageview_url = '/lander-1')
    AND ws.utm_source = 'gsearch'
    AND ws.utm_campaign = 'nonbrand'
GROUP BY wp.website_session_id,wp.pageview_url, o.order_id;


--  DROP TABLE landing_page_ids

SELECT 
	pageview_url AS lander_page, 
	COUNT(sessions) AS sessions, 
    COUNT(order_id) AS orders,
    CASE 
    WHEN COUNT(sessions) = 0 THEN 0
    ELSE ROUND(COUNT(order_id) * 100 / NULLIF(COUNT(sessions), 0), 2)
  END AS CVR
FROM landing_page_ids
GROUP BY pageview_url;
```


### REQUEST 7 

For the landing page test you analyzed previously, it would be great to show a full conversion funnel from each 
of the two pages to orders. You can use the same time period you analyzed last time (Jun 19 – Jul 28). 


```
CREATE TEMPORARY TABLE funnel
WITH cte1 AS (
    SELECT
        ws.website_session_id,
        wp.pageview_url,
        CASE WHEN pageview_url = '/home' THEN 1 ELSE 0 END AS homepage,
        CASE WHEN pageview_url = '/lander-1' THEN 1 ELSE 0 END AS custom_lander,
        CASE WHEN pageview_url = '/products' THEN 1 ELSE 0 END AS products_page,
        CASE WHEN pageview_url = '/the-original-mr-fuzzy' THEN 1 ELSE 0 END AS mrfuzzy_page,
        CASE WHEN pageview_url = '/cart' THEN 1 ELSE 0 END AS cart_page,
        CASE WHEN pageview_url = '/shipping' THEN 1 ELSE 0 END AS shipping_page,
        CASE WHEN pageview_url = '/billing' THEN 1 ELSE 0 END AS billing_page,
        CASE WHEN pageview_url = '/thank-you-for-your-order' THEN 1 ELSE 0 END AS thankyou_page
    FROM
        website_sessions ws
        LEFT JOIN website_pageviews wp ON ws.website_session_id = wp.website_session_id
    WHERE
        ws.utm_source = 'gsearch'
        AND ws.utm_campaign = 'nonbrand'
        AND ws.created_at < '2012-07-28'
        AND ws.created_at > '2012-06-19'
    ORDER BY
        ws.website_session_id,
        wp.created_at
)
SELECT
    website_session_id,
    MAX(homepage) AS saw_homepage,
    MAX(custom_lander) AS saw_custom_lander,
    MAX(products_page) AS till_product,
    MAX(shipping_page) AS till_shipping,
    MAX(billing_page) AS till_billing,
    MAX(thankyou_page) AS till_thankyou,
    MAX(mrfuzzy_page) AS till_mrfuzzy,
    MAX(cart_page) AS till_cart
FROM
    cte1
GROUP BY
    website_session_id;

SELECT
  CASE
    WHEN saw_homepage = 1 THEN 'saw_homepage'
    WHEN saw_custom_lander = 1 THEN 'saw_custom_lander'
    ELSE '404!'
  END AS segment,
  COUNT(DISTINCT website_session_id) AS sessions,
  COUNT(DISTINCT CASE WHEN till_product = 1 THEN website_session_id END) AS to_products,
  COUNT(DISTINCT CASE WHEN till_mrfuzzy = 1 THEN website_session_id END) AS to_mrfuzzy,
  COUNT(DISTINCT CASE WHEN till_cart = 1 THEN website_session_id END) AS to_cart,
  COUNT(DISTINCT CASE WHEN till_shipping = 1 THEN website_session_id END) AS to_shipping,
  COUNT(DISTINCT CASE WHEN till_billing = 1 THEN website_session_id END) AS to_billing,
  COUNT(DISTINCT CASE WHEN till_thankyou = 1 THEN website_session_id END) AS to_thankyou
FROM funnel
GROUP BY segment;  

SELECT
  CASE
    WHEN saw_homepage = 1 THEN 'saw_homepage'
    WHEN saw_custom_lander = 1 THEN 'saw_custom_lander'
    ELSE '404!'
  END AS segment,
  COUNT(DISTINCT CASE WHEN till_product = 1 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT website_session_id) AS lander_ctr,
  COUNT(DISTINCT CASE WHEN till_mrfuzzy = 1 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT CASE WHEN till_product = 1 THEN website_session_id ELSE NULL END) AS products_ctr,
  COUNT(DISTINCT CASE WHEN till_cart = 1 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT CASE WHEN till_mrfuzzy = 1 THEN website_session_id ELSE NULL END) AS mrfuzzy_ctr,
  COUNT(DISTINCT CASE WHEN till_shipping = 1 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT CASE WHEN till_cart = 1 THEN website_session_id ELSE NULL END) AS cart_ctr,
  COUNT(DISTINCT CASE WHEN till_billing = 1 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT CASE WHEN till_shipping = 1 THEN website_session_id ELSE NULL END) AS shipping_ctr,
  COUNT(DISTINCT CASE WHEN till_thankyou = 1 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT CASE WHEN till_billing = 1 THEN website_session_id ELSE NULL END) AS billing_ctr
FROM
  funnel
GROUP BY segment;
```



### REQUEST 8 

I’d love for you to quantify the impact of our billing test, as well. Please analyze the lift generated from the test 
(Sep 10 – Nov 10), in terms of revenue per billing page session, and then pull the number of billing page sessions 
for the past month to understand monthly impact.

```
WITH ctel AS (
    SELECT
        wp.website_session_id,
        
        wp.pageview_url AS billing_version_seen,
        o.order_id,
        o.price_usd
    FROM
        website_pageviews wp
        LEFT JOIN orders o ON o.website_session_id = wp.website_session_id
    WHERE
        wp.created_at > '2012-09-10' AND wp.created_at < '2012-11-10'
        AND wp.pageview_url IN ('/billing', '/billing-2')
)
SELECT
    billing_version_seen,
    COUNT(DISTINCT website_session_id) AS sessions,
    ROUND(SUM(price_usd) / COUNT(DISTINCT website_session_id), 2) AS revenue_per_billing_page_seen
FROM
    ctel
GROUP BY 1;


SELECT
    COUNT(website_session_id) AS billing_sessions_past_month
FROM
    website_pageviews
WHERE
    pageview_url IN ('/billing', '/billing-2')
    AND created_at BETWEEN '2012-10-27' AND '2012-11-27';
```

