---
title: "SQL - Second highest distinct value"
date: "2025-08-19T20:59:40+01:00"
Description: ""
Categories: ["SQL queries"]
Author: "Lmbf"
toc:
  enable: false
---

**Problem**: given a table `my_table` with an numeric column `col`, we want to return the second highest distinct value. In case there is no second highest (for example, when the table only has one row or all the rows in the table have the same value), we want to return `null`.

**Solution**:

```sql
SELECT MAX(col)
FROM my_table 
WHERE col NOT IN ( 
    SELECT MAX(col) 
        FROM my_table
    );
```

**Explanation**: This is a nifty trick. The subquery simply produces a temporary table with one single value, the maximum of the column `col` of table `my_table`. Then we again select the maximum value of column `col` of `my_table` where the value is **not** in the temporary table we've produced, *i.e.*, the maximum of the values that include every value except the actual maximum is the second highest value. Additionally, if there is no second highest value, there is nothing to select so we naturally produce a `null` value.


