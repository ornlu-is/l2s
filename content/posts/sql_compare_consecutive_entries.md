---
title: "SQL - Compare consecutive entries"
date: "2025-08-25T20:54:29+01:00"
Description: ""
Categories: ["SQL queries"]
Author: "Lmbf"
toc:
  enable: false
---


**Problem**: given a table `my_table` with an `id` column, a date column called `my_date` and a numerically valued column `my_value`, return the IDs of the rows whose `my_value` value is larger than their predecessor. Assume that the table is sorted in descending order by `my_date` and that each entry corresponds to exactly one day.

**Solution**:

```sql
SELECT mt1.id
FROM my_table AS mt1
JOIN my_table AS mt2
ON mt1.my_date = mt2.my_date + INTERVAL '1 day'
WHERE mt1.my_value > mt2.my_value
```

**Explanation**: We simply join the table on itself by the `my_date` column while shifting all dates by one day and then use the `WHERE` clause to filter for the rows we want to keep. This trick is highly localized in the sense that it requires all days to exist, otherwise it would fail.
