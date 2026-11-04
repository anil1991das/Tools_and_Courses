---
title: "SQL Joins Explained: INNER, LEFT, RIGHT, and FULL JOIN"
description: "Understand how SQL joins combine rows from related tables, compare common join types, and avoid duplicate or missing results."
slug: "sql-joins-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Databases"
tags:
  - "SQL"
  - "Database"
  - "Joins"
  - "Data Analysis"
relatedTools: []
relatedArticles:
  - "sql-query-formatting-guide"
draft: false
---

# SQL Joins Explained: INNER, LEFT, RIGHT, and FULL JOIN

Relational databases store information in tables. A customer may be stored in one table and their orders in another. A SQL JOIN combines rows from these tables using a relationship, such as a customer identifier.

Joins are among the most important SQL concepts because they let an application query related information without storing every detail in one large table.

## What is a SQL JOIN?

A JOIN combines rows from two or more tables based on a join condition.

Imagine two tables:

**customers**

| customer_id | name |
|---:|---|
| 1 | Asha |
| 2 | Ravi |
| 3 | Meera |

**orders**

| order_id | customer_id | amount |
|---:|---:|---:|
| 101 | 1 | 500 |
| 102 | 1 | 300 |
| 103 | 2 | 750 |

The customer_id column connects orders to customers.

## INNER JOIN

An INNER JOIN returns rows where the join condition matches in both tables.

~~~sql
SELECT
  customers.name,
  orders.order_id,
  orders.amount
FROM customers
INNER JOIN orders
  ON customers.customer_id = orders.customer_id;
~~~

This returns orders for Asha and Ravi. Meera is not included because she has no matching order.

Use INNER JOIN when the result should contain only matching records.

## LEFT JOIN

A LEFT JOIN returns every row from the left table and matching rows from the right table. If there is no match, right-side columns contain NULL.

~~~sql
SELECT
  customers.name,
  orders.order_id,
  orders.amount
FROM customers
LEFT JOIN orders
  ON customers.customer_id = orders.customer_id;
~~~

This includes Meera with NULL order fields.

LEFT JOIN is useful when a report must include all customers, including those who have not placed an order.

## RIGHT JOIN

A RIGHT JOIN preserves every row from the right table and includes matching rows from the left table. It is less frequently used because the same relationship can often be written by swapping the table order and using LEFT JOIN.

Always make the preserved side clear when reading a query.

## FULL OUTER JOIN

A FULL OUTER JOIN returns matched rows and unmatched rows from both sides. Missing values on either side appear as NULL.

Not every database engine supports FULL OUTER JOIN directly. Check the SQL dialect before using it.

## CROSS JOIN

A CROSS JOIN returns the Cartesian product of the two tables. Every row in the first table is paired with every row in the second.

If one table has 3 rows and another has 4, the result has 12 combinations.

CROSS JOIN can be useful for generating combinations, but an accidental missing join condition can create a very large result.

## Joining more than two tables

Queries can combine several related tables:

~~~sql
SELECT
  customers.name,
  orders.order_id,
  products.product_name
FROM customers
JOIN orders
  ON orders.customer_id = customers.customer_id
JOIN order_items
  ON order_items.order_id = orders.order_id
JOIN products
  ON products.product_id = order_items.product_id;
~~~

Each join introduces another relationship. Make sure the join keys reflect the actual schema and cardinality.

## Common join problems

### Duplicate rows

A customer with several orders appears once per matching order. That is expected for a one-to-many relationship. Use aggregation when the requirement is one row per customer.

### Missing records

INNER JOIN excludes unmatched rows. Use LEFT JOIN when unmatched records should remain visible.

### Incorrect join condition

Joining on a non-unique or unrelated field can produce incorrect combinations. Use the correct primary and foreign key relationship.

### Filtering a LEFT JOIN in WHERE

A condition on the right-side table in the WHERE clause can remove rows where the right side is NULL, effectively changing the result. Consider whether the condition belongs in the ON clause.

## JOIN versus UNION

JOIN combines columns from related rows. UNION combines result rows from separate queries with compatible column counts and types.

They solve different problems and should not be confused.

## Frequently asked questions

### Which join is most commonly used?

INNER JOIN and LEFT JOIN are commonly used in application queries and reporting. The correct type depends on whether unmatched records should appear.

### Can I join tables without a foreign key?

Yes. SQL can join on any condition, but a declared relationship and appropriate constraints help preserve data integrity.

### Why does a join return more rows than expected?

A join may be one-to-many or many-to-many. Check whether the join keys are unique and whether each relationship produces multiple matches.

## Further reading

- [PostgreSQL: Table Expressions and Joins](https://www.postgresql.org/docs/current/queries-table-expressions.html)
- [SQL Server: Joins](https://learn.microsoft.com/en-us/sql/relational-databases/performance/joins)

---
