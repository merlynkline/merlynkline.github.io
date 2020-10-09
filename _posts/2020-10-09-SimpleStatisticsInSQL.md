---
layout: post
title:  "Simple statistics in SQL"
date:   2020-10-09
categories: codecraft
---
Suppose we have a SQL table describing products we keep in stock that includes columns like this (among others):

| Column    | Notes                                               |
| --------- | --------------------------------------------------- |
| code      | Primary key                                         |
| price     |                                                     |
| imported  | Indicated by 'Y' or 'N'                             |
| warehouse | Contrived CSV list of codes from 'N', 'S', 'E', 'W' |
| shape     | Shape code from 'R', 'S', 'T'                       |

A common task to be performed with a data model like this is to extract some statistics showing how many or what percentage of products fall into different categories such as price ranges, imported/domestic, which warehouses they are kept in and what shapes they are. An approach to this that might present itself immediately could be to use `COUNT` and `GROUP` to extract the required numbers, e.g:

```sql
SELECT imported
,      COUNT(*)
FROM   products
GROUP BY imported
```

Although this will work well as presented, unfortunately it will not scale to include more categorisations, requiring multiple queries to extract statistics for different columns, and will be much more difficult in non-trivial cases like the CSV list of warehouses.

A more flexible and scalable approach is to build a query that produces a row for each product, containing a `1` or `0` for each of the categories we are interested in to indicate whether or not the product falls into that category. We can then simply calculate the `SUM` of these values to get a counter for every category of interest in a single pass through the data, e.g:

```sql
SELECT SUM(CASE imported WHEN 'Y' THEN 1                        ELSE 0 END) AS imported
,      SUM(CASE imported WHEN 'N' THEN 1                        ELSE 0 END) AS domestic
,      SUM(CASE WHEN price < 10 THEN 1                          ELSE 0 END) AS cheap
,      SUM(CASE WHEN price >= 10 AND price < 50 THEN 1          ELSE 0 END) AS midrange
,      SUM(CASE WHEN price >= 50 THEN 1                         ELSE 0 END) AS expensive
,      SUM(CASE WHEN warehouse REGEXP_LIKE '(^|,)N(,|$)' THEN 1 ELSE 0 END) AS north
,      SUM(CASE WHEN warehouse REGEXP_LIKE '(^|,)S(,|$)' THEN 1 ELSE 0 END) AS south
,      SUM(CASE WHEN warehouse REGEXP_LIKE '(^|,)E(,|$)' THEN 1 ELSE 0 END) AS east
,      SUM(CASE WHEN warehouse REGEXP_LIKE '(^|,)W(,|$)' THEN 1 ELSE 0 END) AS west
,      SUM(CASE shape WHEN 'R' THEN 1                           ELSE 0 END) AS round
,      SUM(CASE shape WHEN 'S' THEN 1                           ELSE 0 END) AS square
,      SUM(CASE shape WHEN 'T' THEN 1                           ELSE 0 END) AS triangular
FROM products
```

This design confers many benefits: It is flexible and easy to maintain as requirements evolve. Execution time grows in a roughly linear relationship to the size of the data and memory requirements are fixed. The process can be seen as a kind of [MapReduce](https://en.wikipedia.org/wiki/MapReduce) process and as such is amenable to significant optimisation in the database engine. 



Merlyn Kline  
October 2020