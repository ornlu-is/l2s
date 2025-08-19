---
title: "SQL - Find duplicates"
date: "2025-08-19T21:13:22+01:00"
Description: ""
Categories: ["SQL queries"]
Author: "Lmbf"
toc:
  enable: false
---

**Problem**: given a table `my_table` with two columns: `id` representing a unique identifier and constituting the table's primary key and `col1` being a text column, we want to write a query that finds all values of `col1` of `my_table` that have one or more duplicates.

**Solution**:

```sql
SELECT col1
FROM my_table
GROUP BY col1 HAVING COUNT(col1) > 1
```

**Explanation**: this is pretty straightforward, we simply group by `col1` which will cause us to produce a table composed uniquely of the distinct values of `col1`. By specifying `HAVING COUNT(col1) > 1`, we are essentially filtering the table of distinct values of `col1` to restrict ourselves to the values that occur more than once.
