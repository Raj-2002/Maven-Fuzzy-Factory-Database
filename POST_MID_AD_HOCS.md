## Expanded Channel Portfolio - November 29, 2012 

### Email

>Hi there, With gsearch doing well and the site performing better, we launched a second paid search channel, bsearch, around 
?August 22. Can you pull weekly trended session volume since then and compare to gsearch nonbrandso I can get a sense for how 
>important this will be for the business?
>Thanks, Tom

### MySQL Query

```
SELECT 
	MIN(DATE(created_at)) AS week_start_date,
    COUNT(CASE WHEN utm_source = 'Gsearch' THEN 1 ELSE NULL END) AS Gsearch,
    COUNT(CASE WHEN utm_source = 'Bsearch' THEN 1 ELSE NULL END) AS Bsearch
FROM website_sessions
WHERE 
	created_at > '2012-08-22' 
	AND created_at < '2012-11-29'
    AND utm_campaign = 'nonbrand'
GROUP BY
	YEARWEEK(created_at);
```
### Results

| week_start_date | Gsearch | Bsearch |
|------------------|---------|---------|
| 22-08-2012       | 590     | 197     |
| 26-08-2012       | 1056    | 343     |
| 02-09-2012       | 925     | 290     |
| 09-09-2012       | 951     | 329     |
| 16-09-2012       | 1151    | 365     |
| 23-09-2012       | 1050    | 321     |
| 30-09-2012       | 999     | 316     |
| 07-10-2012       | 1002    | 330     |
| 14-10-2012       | 1257    | 420     |
| 21-10-2012       | 1302    | 431     |
| 28-10-2012       | 1211    | 384     |
| 04-11-2012       | 1350    | 429     |
| 11-11-2012       | 1246    | 438     |
| 18-11-2012       | 3508    | 1093    |
| 25-11-2012       | 2286    | 774     |



## Comparing Our channels -  November 30, 2012

### Email

>Hi there, I’d like to learn more about the bsearchnonbrand campaign. Could you please pullthe percentage of traffic coming on 
>Mobile, and compare that to gsearch? Feel free to dig around and share anything else you find 
>interesting. Aggregate data since August 22nd is great, no need to show trending at this point.


### MySQL Query    

```
SELECT 
	utm_source,
	COUNT(CASE WHEN device_type = 'mobile' THEN 1 ELSE NULL END) AS Mobile,
    COUNT(website_session_id) AS total_sessions
FROM website_sessions ws 
WHERE 
	created_at > '2012-08-22'   
	AND created_at < '2012-11-30'
    AND utm_campaign = 'nonbrand'
GROUP BY utm_source    ;
```
### Results

| utm_source | Mobile | total_sessions |
|------------|--------|----------------|
| gsearch    | 4921   | 20073          |
| bsearch    | 562    | 6522           |


## Multi channel Bidding - December 15, 2012    

### Email

>Hi there,
>I’m wondering if bsearch nonbrand should have the same bids as gsearch. Could you pull nonbrand conversion rates 
>from session to order for gsearch and bsearch, and slice the data by device type?
>Please analyze data from August 22 toSeptember 18; we ran a special pre-holiday campaign for gsearch starting on 
>September 19th, so the data after that isn’t fair game.
>Thanks, Tom

### MySQL Query

```
SELECT 
	device_type, 
    utm_source,
    COUNT(ws.website_session_id) AS total_sessions,
    COUNT(o.order_id) AS total_orders,
    COUNT(o.order_id)/COUNT(ws.website_session_id) AS CVR
FROM website_sessions ws
LEFT JOIN orders o  
ON o.website_session_id = ws.website_session_id 
WHERE 
	ws.created_at > '2012-08-22'
    AND ws.created_at < '2012-09-19'
    AND ws.utm_campaign = 'nonbrand'
    AND (ws.utm_source = 'bsearch' OR ws.utm_source = 'gsearch')
GROUP BY device_type, utm_source
ORDER BY device_type ;
```

### Results 

| device_type | utm_source | total_sessions | total_orders | CVR    |
|-------------|------------|----------------|--------------|--------|
| desktop     | bsearch    | 1162           | 44           | 0.0379 |
| desktop     | gsearch    | 3011           | 136          | 0.0452 |
| mobile      | bsearch    | 130            | 1            | 0.0077 |
| mobile      | gsearch    | 1015           | 13           | 0.0128 |


## Impact of Bid Changes - December 22, 2012 
 
### Email

>Hi there,
>Based on your last analysis, we bid down bsearch nonbrand on Can you pull weekly session volume for gsearch and search 
>nonbrand, broken down by device, since November 4th? If you can include a comparison metric to show bsearchas a 
>percent of gsearchfor each device, that would be great too.

### MySQL Query

```
SELECT 
	MIN(DATE(created_at)) AS start_of_week,
    COUNT(CASE WHEN ws.device_type = 'mobile' AND ws.utm_source = 'gsearch' THEN 1 ELSE NULL END) AS gs_mob,
    COUNT(CASE WHEN ws.device_type = 'mobile' AND ws.utm_source = 'bsearch' THEN 1 ELSE NULL END) AS bs_mob,
    COUNT(CASE WHEN ws.device_type = 'desktop' AND ws.utm_source = 'gsearch' THEN 1 ELSE NULL END) AS gs_desk,
    COUNT(CASE WHEN ws.device_type = 'desktop' AND ws.utm_source = 'bsearch' THEN 1 ELSE NULL END) AS bs_desk,
	COUNT(CASE WHEN ws.device_type = 'mobile' AND ws.utm_source = 'bsearch' THEN 1 ELSE NULL END)/
    COUNT(CASE WHEN ws.device_type = 'mobile' AND ws.utm_source = 'gsearch' THEN 1 ELSE NULL END) AS bs_mob_pct_of_gs,
    COUNT(CASE WHEN ws.device_type = 'desktop' AND ws.utm_source = 'gsearch' THEN 1 ELSE NULL END)/
    COUNT(CASE WHEN ws.device_type = 'desktop' AND ws.utm_source = 'bsearch' THEN 1 ELSE NULL END) AS bs_desk_pct_of_gs
FROM website_sessions ws
WHERE created_at > '2012-11-04'
AND created_at < '2012-12-22'
AND utm_campaign = 'nonbrand'
GROUP BY YEARWEEK(created_at);
```

### Results

| start_of_week | gs_mob | bs_mob | gs_desk | bs_desk | bs_mob_pct_of_gs | bs_desk_pct_of_gs |
|---------------|--------|--------|---------|---------|-------------------|-------------------|
| 04-11-2012    | 323    | 29     | 1027    | 400     | 0.0898            | 2.5675            |
| 11-11-2012    | 290    | 37     | 956     | 401     | 0.1276            | 2.384             |
| 18-11-2012    | 853    | 85     | 2655    | 1008    | 0.0996            | 2.6339            |
| 25-11-2012    | 692    | 62     | 2058    | 843     | 0.0896            | 2.4413            |
| 02-12-2012    | 396    | 31     | 1326    | 517     | 0.0783            | 2.5648            |
| 09-12-2012    | 424    | 46     | 1277    | 293     | 0.1085            | 4.3584            |
| 16-12-2012    | 376    | 41     | 1270    | 348     | 0.109             | 3.6494            |


## Site traffic breakdown -  December 23, 2012 

### Email

Good morning,
>A potential investor is asking if we’re building any momentum with our brand or if we’ll need to keep relying 
>on paid traffic.  Could you pull organic search, direct type in, and paid brand search sessions by month, and show those sessions 
>as a % of paid search nonbrand?
>-Cindy 

### MySQL Query

```
WITH channel_group AS (
    SELECT
        website_session_id,
        created_at,
        CASE
            WHEN utm_source IS NULL AND http_referer IN ('https://www.gsearch.com','https://www.bsearch.com') THEN 'organic_search'
            WHEN utm_campaign = 'nonbrand' THEN 'paid_nonbrand'
            WHEN utm_campaign = 'brand' THEN 'paid_brand'
            WHEN utm_source IS NULL AND http_referer IS NULL THEN 'direct_type_in'
        END AS channel_group
    FROM website_sessions
    WHERE created_at < '2012-12-23'
)

SELECT
    YEAR(created_at) AS yr,
    MONTH(created_at) AS mo,
    COUNT(DISTINCT CASE WHEN channel_group = 'paid_nonbrand' THEN website_session_id ELSE NULL END) AS nonbrand,
    COUNT(DISTINCT CASE WHEN channel_group = 'paid_brand' THEN website_session_id ELSE NULL END) AS brand,
    COUNT(DISTINCT CASE WHEN channel_group = 'paid_brand' THEN website_session_id ELSE NULL END) /
        COUNT(DISTINCT CASE WHEN channel_group = 'paid_nonbrand' THEN website_session_id ELSE NULL END) AS brand_pct_of_nonbrand,
    COUNT(DISTINCT CASE WHEN channel_group = 'direct_type_in' THEN website_session_id ELSE NULL END) AS direct,
    COUNT(DISTINCT CASE WHEN channel_group = 'direct_type_in' THEN website_session_id ELSE NULL END) /
        COUNT(DISTINCT CASE WHEN channel_group = 'paid_nonbrand' THEN website_session_id ELSE NULL END) AS direct_pct_of_nonbrand,
    COUNT(DISTINCT CASE WHEN channel_group = 'organic_search' THEN website_session_id ELSE NULL END) AS organic,
    COUNT(DISTINCT CASE WHEN channel_group = 'organic_search' THEN website_session_id ELSE NULL END) /
        COUNT(DISTINCT CASE WHEN channel_group = 'paid_nonbrand' THEN website_session_id ELSE NULL END) AS organic_pct_of_nonbrand
FROM channel_group cg
GROUP BY
    YEAR(created_at),
    MONTH(created_at);
```

### Final Results

| yr   | mo | nonbrand | brand | brand_pct_of_nonbrand | direct | direct_pct_of_nonbrand | organic | organic_pct_of_nonbrand |
|------|----|----------|-------|------------------------|--------|-------------------------|---------|--------------------------|
| 2012 | 3  | 1852     | 10    | 0.0054                 | 9      | 0.0049                  | 8       | 0.0043                   |
| 2012 | 4  | 3509     | 76    | 0.0217                 | 71     | 0.0202                  | 78      | 0.0222                   |
| 2012 | 5  | 3295     | 140   | 0.0425                 | 151    | 0.0458                  | 150     | 0.0455                   |
| 2012 | 6  | 3439     | 164   | 0.0477                 | 170    | 0.0494                  | 190     | 0.0552                   |
| 2012 | 7  | 3660     | 195   | 0.0533                 | 187    | 0.0511                  | 207     | 0.0566                   |
| 2012 | 8  | 5318     | 264   | 0.0496                 | 250    | 0.047                   | 265     | 0.0498                   |
| 2012 | 9  | 5591     | 339   | 0.0606                 | 285    | 0.051                   | 331     | 0.0592                   |
| 2012 | 10 | 6883     | 432   | 0.0628                 | 440    | 0.0639                  | 428     | 0.0622                   |
| 2012 | 11 | 12260    | 556   | 0.0454                 | 571    | 0.0466                  | 624     | 0.0509                   |
| 2012 | 12 | 6643     | 464   | 0.0698                 | 482    | 0.0726                  | 492     | 0.0741                   |


## Understanding Seasonality -  January 02, 2013 

### Email

>Good morning,
>2012 was a great year for us. As we continue to grow, we should take a look at 2012’s monthly and weekly volume 
>patterns, to see if we can find any seasonal trends we should plan for in 2013.  If you can pull session volume and order volume, that 
>would be excellent.
>Thanks,
>Cindy
 

 ### MySQL Query

 ```
SELECT
YEAR(ws.created_at) AS Year,
MONTH(ws.created_at) AS Month, 
COUNT(DISTINCT ws.website_session_id) AS sessions, 
COUNT(DISTINCT o.order_id) AS orders
FROM website_sessions ws
LEFT JOIN orders o
ON ws.website_session_id = o.website_session_id
WHERE ws.created_at < '2013-01-01'
GROUP BY Year, Month;
```
### Results

## Data for Customer Service  January 05, 2013 
### Email

>Good morning,
>We’re considering adding live chat support to the website to improve our customer experience. Could you analyze 
>the average website session volume, by hour of day and by day week,so that we can staff appropriately? 
>Let’s avoid the holiday time period and use a date range of Sep 15 -Nov 15, 2013.
>
>Thanks, Cindy

### MySQL Query
0
```
WITH daily_hourly_sessions AS (
    SELECT
        DATE(created_at) AS created_date,
        WEEKDAY(created_at) AS weekday,
        HOUR(created_at) AS hour,
        COUNT(DISTINCT website_session_id) AS website_sessions
    FROM website_sessions
    WHERE created_at BETWEEN '2012-09-15' AND '2012-11-15'
    GROUP BY created_date, weekday, hour
)

SELECT
    hour,
    ROUND(AVG(CASE WHEN weekday = 0 THEN website_sessions ELSE NULL END), 1) AS mon,
    ROUND(AVG(CASE WHEN weekday = 1 THEN website_sessions ELSE NULL END), 1) AS tue,
    ROUND(AVG(CASE WHEN weekday = 2 THEN website_sessions ELSE NULL END), 1) AS wed,
    ROUND(AVG(CASE WHEN weekday = 3 THEN website_sessions ELSE NULL END), 1) AS thu,
    ROUND(AVG(CASE WHEN weekday = 4 THEN website_sessions ELSE NULL END), 1) AS fri,
    ROUND(AVG(CASE WHEN weekday = 5 THEN website_sessions ELSE NULL END), 1) AS sat,
    ROUND(AVG(CASE WHEN weekday = 6 THEN website_sessions ELSE NULL END), 1) AS sun
FROM daily_hourly_sessions
GROUP BY hour;
```
### Results

## Sales Trends January 04, 2013

### Email

>Good morning,
>We’re about to launch a new product, and I’d like to do a deep dive on our current flagship product.
>Can you please pull monthly trends to date for number of sales, total revenue, and total margin generatedfor the business?

>Cindy 

### MySQL Query

```
SELECT
	YEAR(created_at) AS Year,
	MONTH(created_at) AS Month,
	COUNT(DISTINCT order_id) AS number_of_sales,
	SUM(price_usd) AS total_revenue,
	SUM(price_usd - cogs_usd) AS total_margin
FROM orders
WHERE created_at < '2013-01-04'
GROUP BY
	YEAR(created_at),
	MONTH(created_at);
 ```   
    
## Impact of New Product Launch  April 05, 2013 

### Email

>Good morning,
>We launched our second product back on January 6th. Can you pull together some trended analysis? 
>I’d like to see monthly order volume, overall conversion rates, revenue per session, and a breakdown of sales by 
>product, all for the time period since April 1, 2013.
>Thanks,
>Cindy
 
### MySQL Query

```    
SELECT
	YEAR(ws.created_at) AS Year, 
    MONTH(ws.created_at) AS Month,
	COUNT(DISTINCT ws.website_session_id) AS sessions,
	COUNT(DISTINCT o.order_id) AS orders,
	COUNT(DISTINCT o.order_id)/COUNT(DISTINCT ws.website_session_id) AS conv_rate,
	SUM(o.price_usd)/COUNT(DISTINCT ws.website_session_id) AS revenue_per_session,
	COUNT(DISTINCT CASE WHEN primary_product_id = 1 THEN order_id ELSE NULL END) AS product_1_orders,
	COUNT(DISTINCT CASE WHEN primary_product_id = 2 THEN order_id ELSE NULL END) AS product_2_orders
FROM website_sessions ws
LEFT JOIN orders o
ON ws.website_session_id = o.website_session_id
WHERE ws.created_at < '2013-04-05' 
AND ws.created_at > '2012-04-01' 
GROUP BY Year, Month ;   
```
### Results

##  Help w/ User Pathing -  April 06, 2014 
 
### Email

>Hi there! 
>Now that we have a new product, I’m thinking about our user path and conversion funnel. Let’s look at sessions which 
>hit the /products page and see where they went next. Could you please pull clickthrough rates from /products 
>since the new product launch on 2013-01-06, by product, and compare to the 3 months leading up to launch as a baseline? 
>
>Thanks, Morgan

### MySQL Query

```
CREATE TEMPORARY TABLE products_pageviews
SELECT
    website_session_id, 
    website_pageview_id, 
    created_at,
    CASE
        WHEN created_at < '2013-01-06' THEN 'Pre_Product_2'
        WHEN created_at >= '2013-01-06' THEN 'Post_Product_2'
        ELSE NULL
    END AS time_period
FROM website_pageviews wp
WHERE created_at BETWEEN '2012-10-06' AND '2013-04-06' AND pageview_url = '/products';

CREATE TEMPORARY TABLE next_pageview_id
SELECT
    pp.time_period,
    pp.website_session_id,
    MIN(wp.website_pageview_id) AS immediate_next_pageview_id,
    MIN(wp.pageview_url) AS next_pageview_url
FROM products_pageviews pp
LEFT JOIN website_pageviews wp
ON
    wp.website_session_id = pp.website_session_id
    AND wp.created_at > pp.created_at
GROUP BY  pp.time_period, pp.website_session_id;

SELECT
    time_period,
    COUNT(DISTINCT website_session_id) AS sessions,
    COUNT(DISTINCT CASE WHEN next_pageview_url IS NOT NULL THEN website_session_id END) AS w_next_pg,
    COUNT(DISTINCT CASE WHEN next_pageview_url IS NOT NULL THEN website_session_id END) / COUNT(DISTINCT website_session_id) AS pct_w_next_pg,
    COUNT(DISTINCT CASE WHEN next_pageview_url = '/the-original-mr-fuzzy' THEN website_session_id END) AS to_mr_fuzzy,
    COUNT(DISTINCT CASE WHEN next_pageview_url = '/the-original-mr-fuzzy' THEN website_session_id END) / COUNT(DISTINCT website_session_id) AS pct_to_mrfuzzy,
    COUNT(DISTINCT CASE WHEN next_pageview_url = '/the-forever-love-bear' THEN website_session_id END) AS to_lovebear,
    COUNT(DISTINCT CASE WHEN next_pageview_url = '/the-forever-love-bear' THEN website_session_id END) / COUNT(DISTINCT website_session_id) AS pct_to_lovebear
FROM next_pageview_id
GROUP BY time_period;
```
### Results

##   Product Conversion Funnels  April 10, 2014 */

### Email

>Hi there! 
>I’d like to look at our two products since January 6th and analyze the conversion funnels from each product page to 
>conversion.It would be great if you could produce a comparison between the two conversion funnels, for all website traffic.
>
>Thanks! -Morgan  

 ### MySQL Query

``` 
CREATE TEMPORARY TABLE product_seen
SELECT
    website_session_id,
    website_pageview_id,
    pageview_url AS product_page_seen
FROM website_pageviews 
WHERE created_at BETWEEN '2013-01-06' AND '2013-04-10'
    AND pageview_url IN ('/the-original-mr-fuzzy', '/the-forever-love-bear');
    

CREATE TEMPORARY TABLE session_product_level_made_it_flags AS
SELECT
  website_session_id,
  CASE
    WHEN product_page_seen = '/the-original-mr-fuzzy' THEN 'mrfuzzy'
    WHEN product_page_seen = '/the-forever-love-bear' THEN 'Lovebear'
    ELSE 'uh oh... check logic'
  END AS product_seen,
  SUM(cart_page) AS to_cart,
  SUM(shipping_page) AS to_shipping,
  SUM(billing_page) AS to_billing,
  SUM(thankyou_page) AS to_thankyou
FROM (
  SELECT
    ps.website_session_id,
    ps.product_page_seen,
    CASE WHEN pageview_url = '/cart' THEN 1 ELSE 0 END AS cart_page,
    CASE WHEN pageview_url = '/shipping' THEN 1 ELSE 0 END AS shipping_page,
    CASE WHEN pageview_url = '/billing-2' THEN 1 ELSE 0 END AS billing_page,
    CASE WHEN pageview_url = '/thank-you-for-your-order' THEN 1 ELSE 0 END AS thankyou_page
  FROM
    product_seen ps
    LEFT JOIN website_pageviews wp
      ON ps.website_session_id = wp.website_session_id
      AND wp.website_pageview_id > ps.website_pageview_id
  ORDER BY
    ps.website_session_id,
    wp.created_at
) AS funnel_steps
GROUP BY
  website_session_id,
  product_seen;
  
  SELECT
  product_seen,
  COUNT(website_session_id) AS sessions,
  SUM(to_thankyou) AS to_thankyou,
  SUM(to_cart) AS to_cart,
  SUM(to_shipping) AS to_shipping,
  SUM(to_billing) AS to_billing
FROM
  session_product_level_made_it_flags
GROUP BY
  product_seen
ORDER BY
  product_seen;

SELECT
product_seen, 
SUM(to_cart) /COUNT(website_session_id) AS product_page_click_rt, 
SUM(to_shipping)/SUM(to_cart) AS cart_click_rt, SUM(to_billing) / SUM(to_shipping) AS shipping_click_rt, SUM(to_thankyou)/SUM(to_billing) AS billing_click_rt
FROM session_product_level_made_it_flags
GROUP BY product_seen
ORDER BY product_seen;
```
### Results

##  Quality Issues & Refunds  October 15, 2014

### Email

>Good morning,  
>Our Mr. Fuzzy supplier had some quality issues which weren’t corrected until September 2013. Then they had a 
>major problem where the bears’ arms were falling off in Aug/Sep 2014. As a result, we replaced them with a new 
>supplier on September 16, 2014. Can you please pull monthly product refund rates, by product, and confirm our quality issues are now fixed?
>Cindy 

### MySQL Query

```
SELECT
    YEAR(oi.created_at) AS Year,
    MONTH(oi.created_at) AS Month,
    COUNT(DISTINCT CASE WHEN oi.product_id = 1 THEN oi.order_item_id END) AS p1_orders,
    COUNT(DISTINCT CASE WHEN oi.product_id = 1 THEN oir.order_item_id END) / COUNT(DISTINCT CASE WHEN oi.product_id = 1 THEN oi.order_item_id END) AS p1_refund_rt,
    COUNT(DISTINCT CASE WHEN oi.product_id = 2 THEN oi.order_item_id END) AS p2_orders,
    COUNT(DISTINCT CASE WHEN oi.product_id = 2 THEN oir.order_item_id END) / COUNT(DISTINCT CASE WHEN oi.product_id = 2 THEN oi.order_item_id END) AS p2_refund_rt,
    COUNT(DISTINCT CASE WHEN oi.product_id = 3 THEN oi.order_item_id END) AS p3_orders,
    COUNT(DISTINCT CASE WHEN oi.product_id = 3 THEN oir.order_item_id END) / COUNT(DISTINCT CASE WHEN oi.product_id = 3 THEN oi.order_item_id END) AS p3_refund_rt,
    COUNT(DISTINCT CASE WHEN oi.product_id = 4 THEN oi.order_item_id END) AS p4_orders,
    COUNT(DISTINCT CASE WHEN oi.product_id = 4 THEN oir.order_item_id END) / COUNT(DISTINCT CASE WHEN oi.product_id = 4 THEN oi.order_item_id END) AS p4_refund_rt
FROM
    order_items oi
LEFT JOIN
    order_item_refunds oir ON oi.order_item_id = oir.order_item_id
WHERE
    oi.created_at >= '2012-10-15'
GROUP BY
    Year, Month;
```


## Repeat Visitors - November 1, 2014

### Email

>Hey there,
>We’ve been thinking about customer value based solely on their first session conversion and revenue. Butif customers 
?have repeat sessions, they may be more valuable than we thought. If that’s the case, we might be able to spend a bit 
>more to acquire them. Could you please pull data on how many of our website visitors come back for another session? 2014 to date is good. 
>Thanks, Tom

### MySQL Query

```
WITH order_counts AS (
SELECT 
	user_id, 
    SUM(is_repeat_session) as repeatornot 
FROM website_sessions ws 
WHERE 
	created_at > '2014-01-01' 
    AND created_at<'2014-11-01'
    -- AND utm_source IS NOT NULL
    
GROUP BY user_id
ORDER BY repeatornot asc
)

SELECT repeatornot, count(user_id) AS tot from order_counts group by repeatornot;
```
### Results

##   Repeat Channel Mix November 5, 2014 

### Email

>Hi there,
>Let’s do a bit more digging into our repeat customers. Can you help me understand the channels they come back 
>through? Curious if it’s all direct type-in, or if we’re paying for these customers with paid search ads multiple times. 
>Comparing new vs. repeat sessions by channel would be really valuable, if you’re able to pull it! 2014 to date is great. 
>Thanks, Tom

### MySQL Query

```
SELECT
  CASE
    WHEN utm_source IS NULL AND http_referer IN ('https://www.gsearch.com', 'https://www.bsearch.com') THEN 'organic_search'
    WHEN utm_campaign = 'nonbrand' THEN 'paid_nonbrand'
    WHEN utm_campaign = 'brand' THEN 'paid_brand'
    WHEN utm_source IS NULL AND http_referer IS NULL THEN 'direct_type_in'
    WHEN utm_source = 'socialbook' THEN 'paid_social'
  END AS channel_group,
  COUNT(CASE WHEN is_repeat_session = 0 THEN website_session_id END) AS new_sessions,
  COUNT(CASE WHEN is_repeat_session = 1 THEN website_session_id END) AS repeat_sessions
FROM website_sessions
WHERE created_at >= '2014-01-01' AND created_at < '2014-11-05'
GROUP BY channel_group
ORDER BY repeat_sessions DESC;
```
### Results


## Top Website Pages  November 08, 2014 
 
### Email

>Hi there! 
>Sounds like you and Tom have learned a lot about our repeat customers. Can I trouble you for one more thing? 
>I’d love to do a comparison of conversion rates and revenue per session for repeat sessions vs new sessions. 
>Let’s continue using data from 2014, year to date. Thank you!
>Morgan
 
### MySQL Query

 ```
SELECT
  is_repeat_session,
  COUNT(DISTINCT website_sessions.website_session_id) AS sessions,
  COUNT(DISTINCT orders.order_id) / COUNT(DISTINCT website_sessions.website_session_id) AS conv_rate,
  SUM(price_usd) / COUNT(DISTINCT website_sessions.website_session_id) AS rev_per_session
FROM website_sessions
LEFT JOIN orders ON website_sessions.website_session_id = orders.website_session_id
WHERE website_sessions.created_at >= '2014-01-01' AND website_sessions.created_at < '2014-11-08'
GROUP BY is_repeat_session;
```
### Results
