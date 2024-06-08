## Maintaining Cache Consistency

### Write policy

Triggered upon a write operation in the cache, mostly on user requests for add / update / delete. If cache is source of truth, each operation affects system, and may require changes in DB or cache

#### Write back policy

* ##### Timeout based persistence

    Add a TTL to the cache entry. If the timestamp is greater than TTL the entry is removed from the cache and sent to the DB

* ##### Event based write back

    Keep an update count, and after a fixed count, update it in the cache

* ##### Replacement Write Back

    Whenever you need to bring another entry into the cache, you kick out the LRU / LFU / etc, and store its value in the database

* Key points

    * Highly efficient
    * Eventually consistent in most scenarios
    * Can be inconsistent on cache crashes
    * good for things like aggregated like count, comments, etc, something that can easily be rebuilt from the database


#### Write through policy

When a write request comes in, you kick it out the key from the cache, and update the DB. The key is reintroduced into the cache whenever there is a subsequent read request ( not immediately after the update is done ) 

*Special Condition*

* you get a write request for key 1 with 30 ( DB value is 20 )
* you remove 1, 30 from the cache and send the write request to the DB
* simultaneously, another read request comes in for 1, and it sees it is not in the cache and queries the DB
    * If you send the write request first to the DB, and the read request next, the write request would take a lock, and everything would be good ( you order the requests ) 
    * However, even with ordering, if you read uncommitted, it would give you old value, which will be stored in the cache, and served to users till it is updated again / kicked out


* Key points

    * High consistency
    * High persistence
    * not so efficient
    * Good for things like transaction data

#### Write around policy

If you get a write request, you dont touch the cache and update the DB with the new value. 

* Key points

    * good for high efficiency
    * persistence is guaranteed
    * It is eventually consistent always
    * Efficient as you dont have cache contention, you dont need to order writes / reads in the cache / DB


### Replacement policies

Triggered when something doesnt exist in your cache ( cache miss ) and you need to choose which entry you remove from the cache

#### LRU

Cache stores an attribute called last used, and when you need to kick something out, you see the last used value

#### LFU

Cache stores use count, and minimum usage count is kicked out

Continuous load and evicting from the cache. 

Thrashing => Lot of cache miss and lots of load & eviction. Happens when cache is low in size

Goal of cache: High hit-ratio & low miss-ratio

Usually LRU is better

#### Memcached ( Segmented LRU )

Maintains warm & cold region for high frequency data and low frequency data

Assuming 1 max size on warm & cold region

READ 1 -> Added to cold region
READ 1 -> Removed from cold & added to warm
READ 2 -> Added to cold region
READ 2 -> 1 is moved to cold region and 2 is moved to warm

When you run out of space in cold region, LRU is used to kick stuff out ( based on timestamp )

When you promote it warm region and warm region is out of space, LRU is used in warm region to kick it out of warm, and put it in cold region
