# Locking
Locks allow application to synchronize their execution, so that there is an consistent and coordinated way to apply a transaction. This also helps systems to share their resources and control access of resource.


## Types of Locking
1. ReadLocks
2. ReadWriteLocks
3. WriteExclusiveLocks
4. ShareLocks

## Locking in RDBMS
In ACID, Isolation plays in role in ensuring we can apply locking. Locking can be applied at DBLevel, Table, ROW Level and PageLevel
DB Level - Drop Database
Table Level - Alter statement/Update all rows
Row Locking - Lock specific row (Optimistic and Pessimistic)

Each database allows developers to choose the level of isolation their application demands. Various isolation levels are
1. Dirty Reads (Not preferred)
2. READ COMMITTED - Allow other transaction in flight to see changes of record.
3. NON REPEATABLE READ - Disallows/ Prohibits transaction if data has changed since its transaction started.
4. Serializable  - Highest level of locking.

## Locking in Distributed Systems

### Coarse Grained Locking 
    General locking for applications and used for primarily electing leader etc.
    - Chubby
    
### Fine Grained Locking
    Used for Low Level locking maybe at entity level/row level.
    - ZooKeeper
    - Consul
    - Etcd
    - HazelCast 


