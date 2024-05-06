## Data Consistency Levels

### Linearizable

* the transactions are always ordered in this level
* show all changes in the database till the current read request
* achieved using a single-threaded single server
* useful for perfect consistency

### Eventual Consistency

* system can send stale data for a while, but eventually, must send correct data
* process read & write in parallel or concurrently for faster operations
* e.g. if 2 operations are performed concurrently, update x to 10, and read x where x is previously 5, it can return either 10 or 5, depending on which is executed first, but on later queries it would return 10


### Causal Consistency

* related operations need to be executed in sequence, i.e. if the previous operation is related to the current, it must be executed prior to it
* if you have 5 operations

    * update x to 20
    * update y to 10
    * read x
    * update x to 2
    * read y

    you can group these opersions into 1,3,4 in one thread/server and 2,5 in another
* better than eventual since operations for same key performed in sequence
* better then linearizable since unrelated keys dont hold back the entire job
* drawback is it fails on aggreation operaionts (such as sum * from table)

### Quorum

* multiple replicas, and return most appropriate value such as last updated / common
* eventually consistent in most cases, e.g. you update x = 40 in 1 node out of 3, and it crashes before sync, the value read is the old one
* can be made strongly consistency by specifying minimum number of replicas data is to be read from 

    * R + W > N where
    * R = minimum read replicares
    * W = minimum replicas written to
    * N = total replicas
    * if we cannot get data from a minmum of N - W + 1 nodes, we throw an error
* provides fault tolerance and can have a sysntem that is 
    * eventually consistent if R + W <= N
    * strongly consistent if R + W > N
* the cost is high
* if the replica count is even, we can get a split-brain problem