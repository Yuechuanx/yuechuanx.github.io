---
title: postgres-tutorial-summary
tags:
---

> 本篇是针对 PostgreSQL tutorial 的总结
>
> [postgresqltutorial](http://www.postgresqltutorial.com/) 是一个非常好的入门文档，提供 `dvdrental` 数据库以及 E-R 图，可以尽情在上面操作与玩耍。

~~~sql
			 
 -- Json
 CREATE TABLE orders (
   ID serial NOT NULL PRIMARY KEY,
   info json NOT NULL
);
-- insert json data 
INSERT INTO orders (info)
VALUES
   (
      '{ "customer": "John Doe", "items": {"product": "Beer","qty": 6}}'
   );
-- insert multiple rows 
INSERT INTO orders (info)
VALUES
   (
      '{ "customer": "Lily Bush", "items": {"product": "Diaper","qty": 24}}'
   ),
   (
      '{ "customer": "Josh William", "items": {"product": "Toy Car","qty": 1}}'
   ),
   (
      '{ "customer": "Mary Clark", "items": {"product": "Toy Train","qty": 2}}'
   );
-- query json data
SELECT info FROM orders;
-- OPERATOR -> returns json object filed by key. 
SELECT info -> 'customer' FROM orders;
-- The operator ->> returns JSON object field by text.
SELECT info ->> 'customer' AS customer FROM orders;
-- you can chain it with the operator ->> to retrieve a specific node
SELECT info -> 'items' ->> 'product' AS product FROM orders ORDER BY product;
-- Use JSON operator in WHERE clause
SELECT info ->> 'customer' AS customer FROM orders WHERE info -> 'items' ->> 'product' = 'Diaper';
-- find out who bought two products at a time
SELECT info ->> 'customer' AS customer, info -> 'items' ->> 'product' AS product FROM orders 
WHERE CAST(info -> 'items' ->> 'qty' AS INTEGER) = 2
-- Apply aggregate functions to JSON data
SELECT min(CAST(info -> 'items' ->> 'qty' AS INTEGER)), 
max(CAST(info -> 'items' ->> 'qty' AS INTEGER)),
sum(CAST(info -> 'items' ->> 'qty' AS INTEGER)),
avg(CAST(info -> 'items' ->> 'qty' AS FLOAT))
FROM orders;
-- JSON functions
SELECT json_each(info) FROM orders;
SELECT json_object_keys(info->'items') FROM orders;
SELECT json_typeof(info->'items') FROM orders;
~~~

