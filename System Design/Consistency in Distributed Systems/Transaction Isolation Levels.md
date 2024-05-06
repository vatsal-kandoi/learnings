## Transactions

Collection of queries to perform a unit of work. 
Atomic, i.e. all pass or all fail

* BEGIN -> start of transaction
* COMMIT -> completion of transaction
* ROLLBACK -> revert all changes till now

2 transactions are isolated if they can run concurrently without affecting each other

