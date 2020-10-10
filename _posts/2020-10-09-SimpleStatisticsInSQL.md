---
layout: post
title:  "Simple statistics in SQL"
date:   2020-10-09
categories: codecraft
---
Using SQL to extract simple statistics from a relational data model can all too easily result in complex and fragile code but there is a common pattern which makes it easy and reliable.

Suppose we have a table describing products we keep in stock that includes columns like this:

| Column    | Notes                                               |
| --------- | --------------------------------------------------- |
| code      | Primary key                                         |
| price     |                                                     |
| imported  | Indicated by 'Y' or 'N'                             |
| warehouse | Contrived CSV list of codes from 'N', 'S', 'E', 'W' |
| shape     | Shape code from 'C', 'S', 'T'                       |

A common task to be performed with a data model like this is to extract some counters so we can report how many or what percentage of products fall into different categories such as price ranges, imported/domestic, which warehouses they are kept in and what shapes they are. An approach to this that might present itself immediately could be to use `COUNT` and `GROUP` to extract the required numbers, e.g:

```sql
SELECT imported
,      COUNT(*)
FROM   products
GROUP BY imported
```

Although this will work well as presented unfortunately it will not scale to include more categorisations because multiple queries would be needed to extract statistics for different columns. It will also be much more difficult to use this approach in non-trivial cases like the CSV list of warehouses which, while a poor data model for this reason among others, is not an uncommon pattern. In addition to these issues the results produced might also require further processing to flatten them for a report as there might be multiple rows and the number of rows depends on the data as well as the query. E.g. this might be returned:

| imported | count |
| :------: | ----: |
|     Y    |    10 |
|     N    |   274 |

Or perhaps just this:

| imported | count |
| :------: | ----: |
|     Y    |    23 |

This problem only gets worse as we use more queries to extract more statistics.

A more flexible and scalable approach is to build a query that would produce a row for each product such that each row contains a `1` or `0` for each of the categories we are interested in, indicating whether or not the product falls into that category. Applying a simple aggregate using `SUM` will change this to produce a counter for every category of interest in a single pass through the data, e.g:

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
,      SUM(CASE shape WHEN 'C' THEN 1                           ELSE 0 END) AS circular
,      SUM(CASE shape WHEN 'S' THEN 1                           ELSE 0 END) AS square
,      SUM(CASE shape WHEN 'T' THEN 1                           ELSE 0 END) AS triangular
,      SUM(1                                                              ) AS counter
FROM products
```

Note the final column, selecting `SUM(1)` to produce a simple count of the number of products included in the results. This is useful if you are calculating (e.g.) percentages or you are applying a filter in a `WHERE` clause and need to know how many products passed the filter. The count is produced efficiently without the need for a separate query.

This design confers many benefits:

* It is flexible and easy to maintain as requirements evolve.
* The results are clearly named counters for each of the individual categories.
* If any data passes the filter the shape of the results is fixed; the query always produces a single row with a fixed number of columns that always contain numbers.
* Execution time grows in a linear relationship to the size of the data, and memory requirements are fixed; it is an O(n) process.
* The procedure is a type of [MapReduce](https://en.wikipedia.org/wiki/MapReduce) and as such is amenable to significant optimisation by the database engine.
* The repeating pattern and the tight encapsulation of knowledge about each category simplify dynamic generation of the required SQL by client software should that be required.

Although this design is somewhat counter-intuitive when considering the obvious options for statistics collection offered by common dialects of SQL it is easily understood and turns out to be a low cost and flexible solution.

Merlyn Kline  
October 2020