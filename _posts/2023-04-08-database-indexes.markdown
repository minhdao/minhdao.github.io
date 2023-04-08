---
layout: post
title: "Database indexes"
date: 2023-04-08
categories:
  - database
description: Database tables grow overtime. How to reduce query time?
---

Have some understanding about indexes might help you optimize and improve the speed of your query.

In this video Hussein explains about indexes and some bad pratices to avoid when writing queries. 

`https://www.youtube.com/watch?v=-qNSXK7s7_w`

This note captures the key points discussed in the video. On top of that, some more disadvantages of indexing is also added for consideration.

## What is indexes?

An analogy is a yellow page phone book. Even though the phone book is really big and thick, you do not need to go to every contact in the book to find the contact you want. You can go to the category where your contact is likely to be located, then further narrow it down (maybe by using the phone book lexical ordering) until you can pinpoint the location of your contact.

Indexing is a way to quickly locate the data/row you want from a tables. It is done by build a data structure "on top" of the actual data that provide some pointers to the actual data. The data structure for indexing is normally b-tree or lsm-tree. This data structure will likely give you an `O(log(n))` instead of the usual `O(n)` runtime.

## Indexes in action

For example, we have a table called users with millions rows, the table will have 2 columns: id and name. The id column is indexed, while the name column is not indexed yet. Let’s run some query to see the difference between indexed and non-indexed columns.

### Running query to retrieve data from an indexed column
This seems to be the best case that can happen, because the indexed columns are fast to locate and the data is in the same place, then the query speed is very fast.
```
 SELECT id FROM users WHERE id = 1234
```

### Running query to retrieve data from a table with an indexed column:
This query will still be quite fast, but definitely be slower than the query above. This is because even though the data can be located on the table quickly, the database still needs to retrieve the data in the actual table from the disk.
```
SELECT name FROM users WHERE id =1234
```

### Running query to retrieve data from a table with non-indexed column
This query seems to be the slowest because it requires a full table scan (row by row) to find the row on the actual table.

```
SELECT name FROM users WHERE name = ’minh’
```
A way to improve the query speed is to create indexes for the `name` column. 
However, we need to also be careful with the query filter, because a query with `like` filter will still result in a slow speed (even on indexed columns) because it needs to do matching for every row of the table to find the expression defined in `like`.

## Summary

Creating indexes for columns is a good way to improve lookup time for the specific columns.  That does not guarantee a faster speed for your query, but instead the database relies on the programmer to give it hints on how to plan and execute the query. `like` filter will definitely result in a slower query. `select *` from a table also will result in a slower query. Therefore, think about what data you need and write your queries to only get what you need.

Another disadvantage with indexes is that it affects database performance, because not only more space is taken, but also I/O operations like writing to the database  are required to keep the index updated.


