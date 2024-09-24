[[Databases]]
# ACID Principles

## App Server Types

### Multithreaded (e.g., .NET Core)

* **Pros**: High performance due to multithreading.
* **Cons**: Lack of scalability. Avoid using shared memory across threads, as this introduces complexity and reduces scalability.

### Multiprocess (e.g., PHP, Node.js)

* **Pros**: Easier scalability across processes.
* **Cons**: Concurrency issues typically arise at the database level.

## SQL vs NoSQL

* **NoSQL** databases do not handle concurrency natively, requiring developers to manage it at the application level.
* NoSQL is ideal for **social media platforms**, where **speed and high throughput** are more important than consistency.
  * NoSQL often suffers from **Impedance Mismatch**, where allowing direct admin access to the database without triggers, permissions, or business rules causes a mismatch between the application logic and the database state.
  * To prevent this mismatch, rules and permissions should be applied at the application level, not directly in the database. In this model, direct database access should be restricted.
* **SQL** is better suited for **banking solutions**, where **data consistency** is critical. It prioritizes accuracy over speed, which can lead to slower but more reliable operations.
  * SQL updates occur sequentially, typically managed through transaction queues.

## ACID Properties

### 1. Atomicity

* Ensures that either all parts of a transaction are completed or none of them are. If any part of the transaction fails, the entire transaction is rolled back.
* A failure before a commit should trigger an automatic rollback.

### 2. Consistency

* The database must always remain in a consistent state from an external point of view.
* If multiple updates are happening within a single operation, the system should only be aware of the pre-transaction or post-transaction state. Partial updates should never be visible.

### 3. Isolation

* Transactions should be independent of each other, ensuring that concurrent transactions do not affect each other's intermediate states.

#### Pessimistic Locking

* Use `SELECT FOR UPDATE` to lock specific database records during a transaction. Other transactions attempting to access the locked record will wait for the lock to be released.
* Locks on other unrelated records are not affected.
* **Deadlock Prevention**: A common strategy is to lock resources in alphabetical order. This ensures that transactions don't enter a deadlock, where two processes are waiting on each other indefinitely.

#### Optimistic Locking

* Addresses the issue where data may change between the time it is loaded and submitted for update. To resolve this, you recheck the data before submitting updates.
* To handle simultaneous updates:
  * Retrieve both the **ID** and a **version number** of the record.
  * When updating, increment the version number (`UPDATE ... SET version = version + 1 WHERE id = ... AND version = ...`).
  * If the version number doesn't match, the update is not applied, and you handle this by either retrying the update or forcing it.
  * **Optimistic locking** is typically implemented via a flag in Object-Relational Mappers (ORMs).

### 4. Durability

* Once a transaction is committed, its changes should be permanent, ensuring that data is not lost, even in the event of a system failure.

## General Best Practices

* Using a **DB console** allows you to simulate concurrency by running queries in a specific order.
* The `BEGIN TRANSACTION` command ensures that operations remain isolated until the transaction is committed, allowing for independent execution.