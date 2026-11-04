---
title: "ACID Transactions Explained: Atomicity, Consistency, Isolation, and Durability"
description: "Understand the four ACID properties, why database transactions matter, and how commit, rollback, and isolation protect data integrity."
slug: "acid-transactions-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Databases"
tags:
  - "Database"
  - "SQL"
  - "ACID"
  - "Transactions"
relatedTools: []
relatedArticles:
  - "sql-indexes-explained"
draft: false
---

# ACID Transactions Explained: Atomicity, Consistency, Isolation, and Durability

A database operation may involve several related changes. A bank transfer, for example, must debit one account and credit another. If only one part succeeds, the data can become incorrect.

Database transactions group operations so that they are treated as one logical unit. The ACID properties describe key guarantees associated with reliable transaction processing.

## What is a transaction?

A transaction is a sequence of database operations that the database treats as one unit of work.

A typical transaction has this lifecycle:

1. Begin the transaction.
2. Perform one or more reads or writes.
3. Commit if the operation succeeds.
4. Roll back if the operation must be cancelled.

A successful commit makes the transaction's changes permanent according to the database's durability guarantees. A rollback cancels uncommitted changes within the transaction, subject to the database's semantics.

## Atomicity

Atomicity means that the transaction is treated as all-or-nothing.

Consider a transfer of ₹500:

- Account A must be debited by ₹500.
- Account B must be credited by ₹500.

If the debit succeeds but the credit fails, the transaction should not leave the system in a state where the money has simply disappeared.

A simplified SQL example:

~~~sql
BEGIN;

UPDATE accounts
SET balance = balance - 500
WHERE account_id = 1;

UPDATE accounts
SET balance = balance + 500
WHERE account_id = 2;

COMMIT;
~~~

A real implementation must also check that the expected account rows were updated and that the withdrawal is valid. Error handling should roll back the transaction when a required condition fails.

## Consistency

Consistency means a transaction takes the database from one valid state to another, preserving defined constraints and invariants.

Examples include:

- A primary key remains unique.
- A foreign key refers to a valid row.
- A balance cannot become negative when a suitable constraint or application rule prevents it.
- Required columns satisfy their constraints.

The database can enforce many rules through constraints, but business invariants may also require correct application logic and transaction design.

## Isolation

Isolation concerns how concurrent transactions interact.

Suppose two users attempt to update the same inventory record at nearly the same time. Without appropriate concurrency control, they could overwrite one another's changes or observe inconsistent data.

Database systems provide isolation levels and concurrency mechanisms. Depending on the chosen level and engine, possible phenomena include dirty reads, non-repeatable reads, phantom reads, and serialization conflicts.

Higher isolation can provide stronger guarantees but may reduce concurrency or increase contention.

## Durability

Durability means that once a transaction has committed, its changes survive failures covered by the database's durability model.

Database systems commonly use write-ahead logging, recovery mechanisms, and storage guarantees to provide durability.

Actual guarantees depend on the database configuration, storage hardware, replication setup, and acknowledgement policy. A successful commit on one server does not automatically mean the data is backed up or replicated to every location.

## Commit and rollback

A transaction is committed when the database accepts the changes as complete.

If an error occurs before commit, the application may roll back:

~~~sql
BEGIN;

UPDATE inventory
SET quantity = quantity - 1
WHERE product_id = 42;

-- Perform other required operations.

ROLLBACK;
~~~

The example deliberately rolls back the change. In application code, use structured error handling to ensure that a failed operation does not leave a transaction open.

## Isolation levels

Common SQL isolation levels include:

| Level | General idea |
|---|---|
| Read Uncommitted | May allow dirty reads, depending on database implementation |
| Read Committed | Prevents dirty reads |
| Repeatable Read | Provides stronger repeatability for rows read within a transaction |
| Serializable | Aims to make concurrent transactions behave as if they ran serially |

Database engines differ in their implementation and exact behavior. Snapshot isolation and multiversion concurrency control (MVCC) are also important concepts in many systems.

Do not assume that the same isolation-level name provides identical behavior in every database.

## Transactions in application code

Applications commonly start a transaction through a database driver or ORM.

The application should:

- Keep transactions as short as practical.
- Avoid waiting for user input while a transaction remains open.
- Handle deadlocks and serialization failures appropriately.
- Retry only when the operation is safe to retry.
- Ensure external side effects, such as sending email, are coordinated with committed database state.

A database transaction cannot automatically roll back an email already sent or an external payment already processed. Such workflows may need an outbox pattern, idempotency keys, or compensating operations.

## Common mistakes

### Treating multiple queries as one transaction automatically

Many database drivers run each statement in its own transaction unless an explicit transaction is started or autocommit is changed.

### Holding transactions open too long

Long-running transactions can lock resources, retain old row versions, and reduce concurrency.

### Ignoring deadlocks

Concurrent transactions can acquire resources in different orders and deadlock. Applications should handle database deadlock errors with an appropriate retry policy.

### Assuming rollback undoes external operations

Rollback affects database transaction state. It does not reverse an API call, message delivery, or payment that has already happened.

## Frequently asked questions

### Are ACID transactions only used in relational databases?

The term is strongly associated with relational database systems, but transaction guarantees are also provided by many other data stores and distributed systems.

### Does ACID mean a database can never lose data?

No. Durability depends on the database's configured guarantees and underlying storage. Backups, replication, disaster recovery, and operational controls are still required.

### Is Serializable always the best isolation level?

Not necessarily. Stronger isolation can increase contention and reduce throughput. Choose an isolation level based on correctness requirements and the database's behavior.

## Further reading

- [PostgreSQL: Transactions](https://www.postgresql.org/docs/current/tutorial-transactions.html)
- [PostgreSQL: Transaction Isolation](https://www.postgresql.org/docs/current/transaction-iso.html)
- [Microsoft: Transactions](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/transactions-transact-sql)

---
