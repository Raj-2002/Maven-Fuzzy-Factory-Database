# Maven-Fuzzy-Factory-Database

## THE SITUATION
You’ve just been hired as an eCommerce Database Analyst for Maven Fuzzy Factory, an online 
retailer which has just launched its first product. 

## THE BRIEF
As a member of the startup team, you will work with the CEO, the Head of Marketing, and the 
Website Manager to help steer the business.
You will analyze and optimize marketing channels, measure and test website conversion 
performance, and use data to understand the impact of new product launches. 

## OBJECTIVE
 THE Use SQL to:
 • Access and explore the Maven Fuzzy Factory database
 • Become the data expert for the company and the go-to person for mission-critical analyses
 • Analyze and optimize the business’ marketing channels, website, and product portfolio


## Traffic Source Analysis and Optimization

### Task - 1 -  SITE TRAFFIC BREAKDOWN - April 12, 2012

#### Email

   > Good morning,
   > We've been live for almost a month now and we’re starting to generate sales. Can you help me understand where the bulk of our website sessions are coming 
   > from, through yesterday? I’d like to see a breakdown by UTM source, campaign and referring domain if possible. 
   >
   > Thanks!
   > -Cindy

#### MySQL Query
```
SELECT 
	utm_source,
    utm_campaign,
    http_referer,
    COUNT(website_session_id) AS total_sessions
FROM website_sessions
WHERE created_at < '2012-04-12'
GROUP BY
	utm_source,
    utm_campaign,
    http_referer ;
```
#### Final Result

| utm_source | utm_campaign | http_referer              | total_sessions |
|------------|--------------|---------------------------|-----------------|
| gsearch    | nonbrand     | https://www.gsearch.com  | 3613            |
| NULL       | NULL         | NULL                      | 28              |
| gsearch    | brand        | https://www.gsearch.com  | 26              |
| NULL       | NULL         | https://www.gsearch.com  | 27              |
| bsearch    | brand        | https://www.bsearch.com  | 7               |
| NULL       | NULL         | https://www.bsearch.com  | 7               |


## Task - 2 - Gsearch CVR - April 14 2012

### Email

> Hi there,
> Sounds like gsearch nonbrand is our major traffic source, but we need to understand if those sessions are driving sales.
> Could you please calculate the conversion rate (CVR) from session to order? Based on what we're paying for clicks, 
> we’ll need a CVR of at least 4% to make the numbers work. If we're much lower, we’ll need to reduce bids. If we’re 
> higher, we can increase bids to drive more volume.

### MySQL Query

```
SELECT 
	COUNT(ws.website_session_id) AS sessions,
    COUNT(o.website_session_id) AS orders,
	COUNT(o.website_session_id)/COUNT(ws.website_session_id) AS CVR
FROM website_sessions ws
LEFT JOIN orders o
ON o.website_session_id = ws.website_session_id
WHERE 
	ws.utm_source = 'gsearch'
    AND ws.utm_campaign = 'nonbrand'
    AND ws.created_at < '2012-04-14';
```

### Final Result

| sessions | orders | CVR    |
|----------|--------|--------|
| 3895     | 112    | 0.0288 |


## Task - 3 - Gsearch Volume Trends - May 12, 2012

### Email

>Based on your conversion rate analysis, we bid down gsearch nonbrandon 2012-04-15. Can you pull gsearch nonbrand trended session volume, by 
>week, to see if the bid changes have caused volume to drop at all?
>Thanks, Tom

### MySQL Query

```
SELECT 
	MIN(DATE(created_at)) AS start_of_week,
	COUNT(website_session_id) AS Sessions
FROM website_sessions ws
WHERE 
	utm_campaign = 'nonbrand'
    AND utm_source = 'gsearch'
    AND created_at < '2012-05-12'
GROUP BY YEARWEEK(created_at)   ;
```

### Final Result

| start_of_week | Sessions |
|---------------|----------|
| 19-03-2012    | 896      |
| 25-03-2012    | 956      |
| 01-04-2012    | 1152     |
| 08-04-2012    | 983      |
| 15-04-2012    | 621      |
| 22-04-2012    | 594      |
| 29-04-2012    | 681      |
| 06-05-2012    | 651      |



## Task - 4 - Gsearch device level performance - May 11, 2012

### Email

>Hi there,
>I was trying to use our site on my mobile device the other day, and the experience was not great. Could you pull conversion rates from session to order, by 
>device type? If desktop performance is better than on mobile we may be able to bid up for desktop specifically to get more volume?
>Thanks, Tom

### MySQL Query

```
SELECT 
    ws.device_type,
    COUNT(ws.website_session_id) AS sessions,
    COUNT(o.order_id) AS orders,
    COUNT(o.order_id)/COUNT(ws.website_session_id) AS CVR
 FROM website_sessions ws
 LEFT JOIN orders o
 ON o.website_session_id = ws.website_session_id
 WHERE 
    ws.utm_campaign = 'nonbrand'
    AND ws.utm_source = 'gsearch'
    AND ws.created_at < '2012-05-11'
 GROUP BY
 ws.device_type;
```

### Final Result

| device_type | sessions | orders | CVR    |
|-------------|----------|--------|--------|
| mobile      | 2492     | 24     | 0.0096 |
| desktop     | 3911     | 146    | 0.0373 |


## Task - 5 - Gsearch device-level trends - June 9, 2012
 
### Email

>Hi there,
>After your device-level analysis of conversion rates, we realized desktop was doing well, so we bid our search 
>nonbrand desktop campaigns up on 2012-05-19. Could you pull weekly trends for both desktop and mobile 
>so we can see the impact on volume? You can use 2012-04-15 until the bid change as a baseline.
>Thanks, Tom

### MySQL Query

```
 SELECT 
	DATE(DATE_SUB(created_at , INTERVAL (DAYOFWEEK(created_at) -2) DAY)) AS start_of_week,
    COUNT(CASE WHEN ws.device_type = 'mobile' THEN ws.website_session_id ELSE NULL END) AS mobile_sessions,
	COUNT(CASE WHEN ws.device_type = 'desktop' THEN ws.website_session_id ELSE NULL END) AS desktop_sessions
FROM website_sessions ws
WHERE
	ws.utm_campaign = 'nonbrand'
    AND ws.utm_source = 'gsearch'
    AND ws.created_at < '2012-06-09'  
    AND ws.created_at > '2012-04-15'
GROUP BY start_of_week;
```

### Final Result

| start_of_week | mobile_sessions | desktop_sessions |
|---------------|------------------|------------------|
| 16-04-2012    | 238              | 383              |
| 23-04-2012    | 234              | 360              |
| 30-04-2012    | 256              | 425              |
| 07-05-2012    | 282              | 430              |
| 14-05-2012    | 214              | 403              |
| 21-05-2012    | 190              | 661              |
| 28-05-2012    | 183              | 585              |
| 04-06-2012    | 157              | 582              |




## Task - 6 - Top Website Pages - June 09, 2012
 
### Email

>I’m Morgan, the new Website Manager. Could you help me get my head around the site by pulling 
>the most-viewed website pages, ranked by session volume?
>Thanks! , Morgan



### MySQL Query

```
SELECT 
	pageview_url,
    COUNT(DISTINCT website_pageview_id) AS sessions
FROM website_pageviews wp
WHERE created_at < '2012-06-09'
GROUP BY pageview_url
ORDER BY sessions DESC  ; 
```

### Final Result

| pageview_url                  | sessions |
|------------------------------|----------|
| /home                        | 10403    |
| /products                    | 4239     |
| /the-original-mr-fuzzy        | 3037     |
| /cart                        | 1306     |
| /shipping                    | 869      |
| /billing                     | 716      |
| /thank-you-for-your-order    | 306      |



## Task - 7 - Top Entry Pages

### Email

>Hi there!
>Would you be able to pull a list of the top entry pages? I want to confirm where our users are hitting the site.
>If you could pull all entry pages and rank them on entry volume, that would be great.
>Thanks!,Morgan


### MySQL Query

```
CREATE TEMPORARY TABLE tp1
SELECT
    website_session_id, MIN(website_pageview_id) AS min_pageview_id
FROM website_pageviews
WHERE created_at < '2012-06-12'
GROUP BY
    website_session_id;

SELECT 
    ws.pageview_url, 
    COUNT(tp1.min_pageview_id) AS total_visits 
FROM website_pageviews ws 
LEFT JOIN tp1 ON tp1.min_pageview_id = ws.website_pageview_id 
GROUP BY ws.pageview_url 
ORDER BY total_visits DESC;
```

### Final Results

| website_session_id | min_pageview_id |
|---------------------|------------------|
| 1                   | 1                |
| 2                   | 2                |
| 3                   | 3                |
| 4                   | 4                |
| 5                   | 5                |
| 6                   | 6                |
| 7                   | 7                |
| 8                   | 12               |
| 9                   | 14               |
| 10                  | 15               |
| 11                  | 16               |
| 12                  | 17               |
| 13                  | 18               |
| 14                  | 19               |
| 15                  | 20               |
| 16                  | 21               |
| 17                  | 25               |   



| pageview_url | total_visits |
|--------------|--------------|
| /home        | 10714        |





## Task - 8 - Bounce Rate Analysis - June 14, 2012
 
### Email

>Hi there! 
>The other day you showed us that all of our traffic is landing on the homepageright now. We should check how that 
>landing page is performing. Can you pull bounce rates for traffic landing on the homepage? I would like to see three >numbers…Sessions, Bounced Sessions, and % of Sessions which Bounced (aka “Bounce Rate”).
>Thanks! -Morgan 


### MySQL Query

```
WITH CTE AS 
(
SELECT
	website_session_id, 
	count(website_pageview_id) as sess
FROM website_pageviews
WHERE created_at < '2012-06-14' 

GROUP BY
website_session_id
)

SELECT COUNT(website_session_id) AS sessions , 
COUNT(CASE WHEN sess = 1 THEN website_session_id ELSE NULL END) AS bounced_sessions
FROM CTE;
```

### Final Result

| website_session_id | sess |
|--------------------|------|
| 1                  | 1    |
| 2                  | 1    |
| 3                  | 1    |
| 4                  | 1    |
| 5                  | 1    |
| 6                  | 6    |
| 7                  | 1    |
| 8                  | 1    |
| 9                  | 1    |
| 10                 | 1    |
| 11                 | 1    |
| 12                 | 1    |
| 13                 | 1    |
| 14                 | 1    |
| 15                 | 3    |
| 16                 | 2    |
| 17                 | 1    |
| 18                 | 4    |
| 19                 | 1    |
| 20                 | 7    |
| 21                 | 1    |
| 22                 | 1    |
| 23                 | 1    |
| 24                 | 1    |
| 25                 | 1    |



| sessions | bounced_sessions |
|----------|------------------|
| 11048    | 6538             |



## Task - 9 Help Analyzing LP Test July 28, 2012*

### Email

>Hi there! 
>Based on your bounce rate analysis, we ran a new custom landing page (/lander-1)  in a 50/50 test against the 
>homepage (/home)for our gsearch nonbrand traffic. Can you pull bounce rates for the two groupsso we can 
>evaluate the new page? Make sure to just look at the time period where /lander-1 was getting traffic, so that it is a >fair comparison.
>Thanks, Morgan 

 ```
SELECT website_pageview_id 
FROM website_pageviews 
WHERE pageview_url = '/lander-1' 
LIMIT 1;

CREATE TEMPORARY TABLE landing_page_ids
SELECT
	wp.website_session_id AS sessions,
    MIN(website_pageview_id) AS pageview_id,
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


-- DROP TABLE landing_page_ids

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

### Results

| website_pageview_id | 
|---------------------|
| 23504               |


| sessions | pageview_id | pageview_url | order_id |
|----------|-------------|--------------|----------|
| 11683    | 23504       | /lander-1    | NULL     |
| 11684    | 23505       | /home        | NULL     |
| 11685    | 23506       | /lander-1    | NULL     |
| 11686    | 23507       | /lander-1    | NULL     |
| 11687    | 23509       | /home        | NULL     |
| 11688    | 23510       | /home        | NULL     |
| 11689    | 23511       | /lander-1    | NULL     |
| 11690    | 23514       | /home        | NULL     |
| 11691    | 23515       | /lander-1    | NULL     |
| 11692    | 23517       | /lander-1    | NULL     |
| 11693    | 23518       | /lander-1    | NULL     |
| 11694    | 23521       | /lander-1    | NULL     |
| 11696    | 23526       | /home        | NULL     |
| 11697    | 23527       | /lander-1    | NULL     |
| 11698    | 23528       | /lander-1    | NULL     |
| 11699    | 23529       | /lander-1    | NULL     |
| 11700    | 23531       | /home        | NULL     |
| 11701    | 23532       | /lander-1    | NULL     |
| 11702    | 23534       | /lander-1    | NULL     |
| 11704    | 23536       | /lander-1    | NULL     |
| 11705    | 23537       | /home        | NULL     |
| 11706    | 23538       | /home        | NULL     |
| 11707    | 23539       | /lander-1    | NULL     |
| 11708    | 23540       | /lander-1    | NULL     |


| lander_page | sessions | orders | CVR   |
|-------------|----------|--------|-------|
| /lander-1   | 2316     | 94     | 4.06  |
| /home       | 2261     | 72     | 3.18  |



-------------------------------------------------------------------------------------------------
## Task - 10 - Landing Page Trend Analysis, August 31, 2012 */

### Email
>Hi there,
>Could you pull the volume of paid search nonbrand traffic landing on /home and /lander-1, trended weekly since June 
>1st? I want to confirm the traffic is all routed correctly.  Could you also pull our overall paid search bounce rate 
>trended weekly? I want to make sure the lander change has improved the overall picture.
>Thanks!

### MySQL Query
 ```
CREATE TEMPORARY TABLE landing_page_ids2
SELECT
    wp.website_session_id AS sessions,
    MIN(website_pageview_id) AS pageview_id,
    wp.pageview_url AS pageview_url, 
    o.order_id AS order_id
FROM website_pageviews  wp
JOIN website_sessions ws
ON ws.website_session_id = wp.website_session_id
LEFT JOIN orders o  
ON o.website_session_id = wp.website_session_id 
WHERE 
	-- website_pageview_id >= 23504 
    wp.created_at < '2012-08-31'
    AND wp.created_at > '2012-06-01'
    -- AND (pageview_url = '/home' OR pageview_url = '/lander-1')
    AND ws.utm_source = 'gsearch'
    AND ws.utm_campaign = 'nonbrand'
GROUP BY wp.website_session_id,wp.pageview_url, o.order_id;

--  DROP TABLE landing_page_ids2 
 
 WITH CTE2 AS
 (
 SELECT 
	lpi2.*, 
    DATE(DATE_SUB(wp.created_at , INTERVAL (DAYOFWEEK(created_at) -3) DAY)) AS week_start
 FROM landing_page_ids2 lpi2
 JOIN website_pageviews wp
 ON wp.website_pageview_id = lpi2.pageview_id
 
)

SELECT 
week_start,
COUNT(CASE WHEN pageview_url = '/home' THEN pageview_id ELSE NULL END) AS home_count,
COUNT(CASE WHEN pageview_url = '/lander-1' THEN pageview_id ELSE NULL END) AS lander_count
 FROM CTE2
 GROUP BY week_start;
```

### Results

| week_start  | home_count | lander_count |
|-------------|------------|--------------|
| 29-05-2012  | 175        | 0            |
| 05-06-2012  | 792        | 0            |
| 12-06-2012  | 875        | 0            |
| 19-06-2012  | 492        | 350          |
| 26-06-2012  | 369        | 386          |
| 03-07-2012  | 392        | 388          |
| 10-07-2012  | 390        | 411          |
| 17-07-2012  | 429        | 421          |
| 24-07-2012  | 402        | 394          |
| 31-07-2012  | 33         | 995          |
| 07-08-2012  | 0          | 1087         |
| 14-08-2012  | 0          | 998          |
| 21-08-2012  | 0          | 1012         |
| 28-08-2012  | 0          | 833          |

 
## Task - 11 - Help Analyzing Conversion Funnels, September 05, 2012

### Email

>This analysis is really helpful! Looks like we should focus on the lander, Mr. Fuzzy page, 
>and the billing page, which have the lowest click rates. I have some ideas for the billing page that I think will make 
>customers more comfortable entering their credit card info. I’ll test a new page soon and will ask for help analyzing 
? performance.
>Thanks! -Morgan
 
 ### MySQL Query
 ```
 SELECT DISTINCT pageview_url FROM website_pageviews WHERE created_at < '2012-09-05' ;
 
 CREATE TEMPORARY TABLE cte as
 (
 SELECT
 wp.website_session_id,
 wp.website_pageview_id,
 CASE WHEN wp.pageview_url = '/home' THEN 1 ELSE NULL END AS viewed_homepage,
 CASE WHEN wp.pageview_url = '/lander-1' THEN 1 ELSE NULL END AS viewed_lander_page,
 CASE WHEN wp.pageview_url = '/products' THEN 1 ELSE NULL END AS viewed_products,
 CASE WHEN wp.pageview_url = '/the-original-mr-fuzzy' THEN 1 ELSE NULL END AS viewed_mr_fuzzy,
 CASE WHEN wp.pageview_url = '/cart' THEN 1 ELSE NULL END AS viewed_cart,
 CASE WHEN wp.pageview_url = '/shipping' THEN 1 ELSE NULL END AS viewed_shipping,
 CASE WHEN wp.pageview_url = '/billing' THEN 1 ELSE NULL END AS viewed_billing,
 CASE WHEN wp.pageview_url = '/thank-you-for-your-order' THEN 1 ELSE NULL END AS viewed_thank_you
 FROM website_pageviews wp
 JOIN website_sessions ws
 ON ws.website_session_id = wp.website_session_id
 WHERE 
	wp.created_at < '2012-09-05' 
    AND wp.created_at >= '2012-08-05'
    AND ws.utm_source = 'Gsearch'
    AND ws.utm_campaign = 'nonbrand'
 ORDER BY website_session_id
 );
 
 SELECT
		COUNT(DISTINCT cte.website_session_id) AS total_sessions,
        COUNT(cte.viewed_products) as viewed_products,
        COUNT(cte.viewed_mr_fuzzy) as viewed_mr_fuzzy,
        COUNT(cte.viewed_cart) as viewed_cart,
        COUNT(cte.viewed_shipping) as viewed_shipping,
        COUNT(cte.viewed_billing) as viewed_billing,
        COUNT(cte.viewed_thank_you) as viewed_thank_you
        
FROM cte;

SELECT
	COUNT(cte.viewed_products)/COUNT(DISTINCT cte.website_session_id) AS lander_ctr,
    COUNT(cte.viewed_mr_fuzzy)/COUNT(cte.viewed_products) AS products_ctr,
    COUNT(cte.viewed_cart)/COUNT(cte.viewed_mr_fuzzy) AS mr_fuzzy_ctr,
    COUNT(cte.viewed_shipping)/ COUNT(cte.viewed_cart) AS cart_ctr,
    COUNT(cte.viewed_billing)/COUNT(cte.viewed_shipping) AS shipping_ctr,
    COUNT(cte.viewed_thank_you)/COUNT(cte.viewed_billing) AS billing_ctr
FROM cte    ;
```	


## Task - 12 - Conversion Funnel Test Results - November 10, 2012

### Email
>Hello! 
>We tested an updated billing page based on your funnel analysis. Can you take a look and see whether /billing-2 is 
>doing any better than the original /billing page? We’re wondering what % of sessions on those pages end up 
>placing an order. FYI –we ran this test for all traffic, not just for our search visitors.
>Thanks! -Morgan


 ### MySQL Query
 ```
SELECT * from website_pageviews WHERE pageview_url = '/billing-2';   -- 53550

SELECT 
	pageview_url,
	COUNT(wp.website_session_id) as Total_Sessions,
	COUNT(o.order_id) AS Total_Orders,
	COUNT(o.order_id)/COUNT(wp.website_session_id) AS CVR
FROM website_pageviews wp 
LEFT JOIN orders o 
ON o.website_session_id = wp.website_session_id
WHERE 
	wp.website_pageview_id >= '53550'
    AND wp.created_at < '2012-11-10'
    AND (wp.pageview_url = '/billing' OR wp.pageview_url = '/billing-2')
GROUP BY pageview_url;
```


