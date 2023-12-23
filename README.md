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




