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





## Task - 8 - 
 
### Email



### MySQL Query

```

```

### Final Result

