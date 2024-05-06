## Transaction Isolation Levels

Collection of queries to perform a unit of work. 
Atomic, i.e. all pass or all fail

* BEGIN -> start of transaction
* COMMIT -> completion of transaction
* ROLLBACK -> revert all changes till now

2 transactions are isolated if they can run concurrently without affecting each other

Read Lock -> When a thread has a read lock on a row/table, no thread can update/insert/delete data from that table. (Even if the thread trying to write data doesn't require a write lock.)

Write Lock -> When a row/table has a write lock, it cannot be read by another thread if they have a read lock implemented in them but can be read by other threads if no read lock is implemented (i.e simple Select query)



### Read uncommitted

* No locks, All read and write operations execute with no locks
* uncommitted data can be read by transactions, leading to dirty reads
* high efficiency and low isolation
* can be implemented by single data entry and is overriten on update operations

### Read committed

* Read lock on read operations and write lock on write operations as command executes(as it fetches row), read lock will be released immediately once that read is complete but all write lock will be released on commit/rollback.
* only data post commit can be read
* causes non-repeatable reads, i.e. transaction reads same key twice but gets different data, since there has been a transaction committed on the key between the 2 reads
* local copy is maintained till the transaction is committed

### Repeatable reads

* Causal lock on condition. For example, SELECT * from Order; here the whole table gets read lock and will be released once the query gets executed. DELETE FROM Orders WHERE Status = 'CLOSED'; here write causal lock will be taken on status = 'CLOSED' condition. So any read or write request asking to either add or get or update the row where status='CLOSED' will have to wait till this causal write lock releases(on Commit/rollback).
* Read lock on read operations and write lock on write operations as it fetches/updates row. Both read and write locks will be released on commit/rollback.
* when a query is reading a row, the row remains unchanged during the transaction. 
* Snapshot isolation -> each transaction has a copy of its data, where they update, and only the update is persisted
* Optimistic concurrency control -> if 2 transactions concurrently change the same key to different values, we roll back
* Implemented by versioning the values, and maintaining a bbersion of them


### Serializable

* highest isolation
* operations are executed serailly or ensured no conflicts
* use it to avoid phantom reads -> 2 read queries executed and collection of rows returned is different, due to insertion of new row
* Queued locks, causal ordering. If they use queries for same key, they must be ordered


### References
* https://learn.microsoft.com/en-us/sql/odbc/reference/develop-app/transaction-isolation-levels?view=sql-server-ver16
