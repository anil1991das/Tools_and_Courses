---
title: "SQL Constraints and Keys Explained: PRIMARY KEY, FOREIGN KEY, UNIQUE, and CHECK"
description: "Understand relational database keys and constraints, learn how they protect data integrity, and see practical SQL examples."
slug: "sql-constraints-and-keys-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Databases"
tags:
  - "SQL"
  - "Database Design"
  - "Primary Key"
  - "Foreign Key"
  - "Constraints"
relatedTools: []
relatedArticles:
  - "database-normalization-guide"
  - "sql-joins-explained"
draft: false
---

# SQL Constraints and Keys Explained: PRIMARY KEY, FOREIGN KEY, UNIQUE, and CHECK

Relational databases use keys and constraints to maintain valid relationships and prevent inconsistent data. They allow the database to reject invalid records rather than relying entirely on application code.

Understanding primary keys, foreign keys, unique constraints, and check constraints is essential when designing reliable database schemas.

## What is a primary key?

A primary key uniquely identifies each row in a table.

~~~sql
CREATE TABLE customers (
  customer_id BIGINT PRIMARY KEY,
  name VARCHAR(100) NOT NULL
);
~~~

A primary-key column or set of columns must be unique and cannot contain NULL values.

A table has one primary-key constraint, though it can contain multiple columns as a composite primary key.

## What is a foreign key?

A foreign key enforces a relationship between rows in related tables.

~~~sql
CREATE TABLE orders (
  order_id BIGINT PRIMARY KEY,
  customer_id BIGINT NOT NULL,
  total_amount DECIMAL(12, 2) NOT NULL,
  CONSTRAINT fk_orders_customer
    FOREIGN KEY (customer_id)
    REFERENCES customers(customer_id)
);
~~~

The referenced key must satisfy the database's requirements. A foreign key prevents an order from referring to a customer row that does not exist, unless the constraint is configured or deferred in a way that permits the operation temporarily.

## What is a UNIQUE constraint?

A UNIQUE constraint prevents duplicate key values according to the database's comparison and NULL rules.

~~~sql
CREATE TABLE users (
  user_id BIGINT PRIMARY KEY,
  email VARCHAR(255) UNIQUE
);
~~~

The treatment of NULL values in unique constraints differs between database systems and can also depend on configuration. Check the target engine's documentation.

## What is NOT NULL?

NOT NULL requires a column to contain a non-NULL value.

~~~sql
CREATE TABLE products (
  product_id BIGINT PRIMARY KEY,
  name VARCHAR(200) NOT NULL
);
~~~

It is useful when a value is required for every row. NOT NULL does not by itself prevent empty strings or whitespace-only text.

## What is CHECK?

A CHECK constraint enforces a Boolean condition on inserted or updated row values.

~~~sql
CREATE TABLE products (
  product_id BIGINT PRIMARY KEY,
  price DECIMAL(10, 2) NOT NULL,
  CONSTRAINT chk_product_price
    CHECK (price >= 0)
);
~~~

The exact treatment of NULL within CHECK expressions depends on SQL's three-valued logic and database behavior. Combine CHECK with NOT NULL when a value must exist and satisfy the condition.

## Composite keys

A composite key uses more than one column to identify a row.

~~~sql
CREATE TABLE order_items (
  order_id BIGINT,
  product_id BIGINT,
  quantity INTEGER NOT NULL,
  PRIMARY KEY (order_id, product_id)
);
~~~

This design allows each product to appear once per order. If the business requires multiple separate line items for the same product, an additional line-item identifier may be more appropriate.

## Primary key versus unique constraint

Both can enforce uniqueness, but they have different roles:

| Primary key | Unique constraint |
|---|---|
| Main row identifier | Enforces uniqueness for another key |
| One primary-key constraint per table | Multiple unique constraints may be defined |
| Does not allow NULL | NULL behavior depends on the database |
| Often referenced by foreign keys | Can also be referenced where supported |

A primary key is a logical identifier for a row. A unique constraint enforces a separate uniqueness rule.

## Referential actions

Foreign keys can define what happens when a referenced row is deleted or updated.

Common actions include:

- RESTRICT or NO ACTION: prevent an operation that would violate the relationship, with timing details depending on the engine.
- CASCADE: propagate the delete or update to dependent rows.
- SET NULL: set the referencing columns to NULL when allowed.
- SET DEFAULT: use the column default when supported and valid.

Use cascading actions deliberately. Deleting a parent record can affect many child records.

## Constraints and application validation

Application validation improves user experience, but database constraints protect data integrity at the persistence boundary.

Both are valuable. For example, an application can show a friendly message when an email is already used, while a database unique constraint protects against concurrent requests that attempt to create the same value.

## Common mistakes

### Relying only on application validation

Two requests may pass application checks at the same time. Database constraints provide the final integrity guarantee.

### Choosing a mutable natural key as the primary key

Values such as email addresses can change. Consider whether a stable surrogate identifier is more appropriate.

### Using CASCADE without reviewing the impact

Cascading deletion can remove more data than expected. Understand the relationship graph and business retention requirements.

### Assuming every database handles NULL identically

Unique constraints and check expressions can differ across engines. Verify the behavior of your selected database.

## Frequently asked questions

### Can a table have multiple primary keys?

A table has one primary-key constraint, but that key can be composite and contain multiple columns.

### Is a foreign key automatically indexed?

Some database systems or schema tools create an index in certain cases; others do not automatically index the referencing columns. Check the database documentation and query workload.

### Can a unique column contain NULL?

This depends on the database implementation and constraint definition. Do not assume identical behavior across engines.

## Further reading

- [PostgreSQL: Constraints](https://www.postgresql.org/docs/current/ddl-constraints.html)
- [MySQL: Constraints](https://dev.mysql.com/doc/refman/8.4/en/constraints.html)
- [SQLite: CREATE TABLE](https://www.sqlite.org/lang_createtable.html)

---
