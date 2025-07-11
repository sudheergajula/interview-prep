# How to approach System design question?
## Step1 - Requirements Gathering 
1. Functional Requirements
2. NonFunctional Requirements

---
## Capacity Planning
## Step2 - Estimation
### How many Daily Active Users?
This can be critical to identify total number of users accessing system. Always round this to nearest 0's.

### What will be peak traffic? 30%?
| DAU  | Zeros | Storage |    Requests/Sec |  30%Peak/Hr |
|:-----|:------|---------|----------------:|------------:|
| K    | 3     | KB      |       1 req/sec |             |
| M    | 6     | MB      |      12 req/sec |  90 req/sec |
| 5M   | 76    | 5MB     |      60 req/sec | 120 req/sec |
| 10M  | 7     | 10MB    |     120 req/sec | 300 req/sec |
| 100M | 8     | 100MB   |    1200 req/sec |             |
| B    | 9     | GB      |   12000 req/sec |             |
| T    | 12    | TB      | 12*10^6 req/sec |             |
| Q    | 15    | PB      | 12*10^6 req/sec |             |
---
### Determine if system is read heavy vs write heavy?
Identify total reads vs writes based on capacity.
10: 1
Write Payload??
1. Traffic Estimation - 12req/sec
2. Storage Estimation - 10^6 * writes/sec
3. Network Estimation - 10^6 * ingress & 10^6*egress

---
## API Spec
Always start with
1. *Versioning API's*
2. *Authentication*
3. *Error Codes*
4. *Response* 
```declarative
/v1/auth <username>/<password>
Response 
200OK JWT
400 Bad REq
500 Internal Server Error
```

```
/v1/user 
Body - ??
Query Params -??
Path Params??
Authorisation : Bearer <>
```

## DB Design 
[Databases](database/databases.MD)

Start with choice of your database

1. For Read-Heavy Systems
   - _Relational Databases (SQL)_: MySQL, PostgreSQL — Use efficient indexing to optimize query performance.
   - _Key-Value Stores_: Redis, Memcached — Excellent for ultra-fast, in-memory data retrieval.
   - _Search Databases_: Elasticsearch — Ideal for full-text search and query-heavy systems.
   - _Replication Strategies_: Employ read replicas to distribute load and improve availability.
2. For Write-Heavy Systems
   - _NoSQL Databases_: MongoDB, Cassandra — Designed for horizontal scaling and high write throughput.
   - _Time-Series Databases_: InfluxDB, TimescaleDB — Optimized for time-stamped data, perfect for continuous writes.
   - _Columnar Databases_: HBase, Bigtable — Handle analytical workloads with frequent writes.
   - _Queue-Based Systems_: Kafka, RabbitMQ — Buffer writes using queues to manage throughput efficiently.

Start listing most important tables.
```sql
CREATE TABLE IF NOT EXISTS users(
    user_id SERIAL PRIMARY KEY,
    first_name varchar,
    last_name varchar,
    display_name varchar,
    created_ts timestamp without timezone,
    updated_ts timestamp without timezone 
    CONSTRAINT uniq_fname_lname UNIQUE(first_name, last_name),
    CONSTRAINT fk_user FOREIGN_KEY(display_name) refrences employee on delete (NO ACTION/ CASCADE/ SET NULL/RESTRICT)
)

CREATE INDEX user_id_idx on users(user_id) using BTREE 
```
## Indexes in Postgres
### B-tree index
B-tree is the default index type in PostgreSQL. B-tree stands for balanced tree.
B-tree indexes maintain the sorted values, making them efficient for exact matches and range queries.

### Hash index
Hash indexes maintain 32-bit hash code created from values of the indexed columns.

Therefore, hash indexes can only handle simple equality comparisons (=).

### GIN index
GIN indexes are inverted indexes that are suitable for composite values such as arrays, JSONB data, and full-text search.

Since a GIN index stores a separate entry for each component, 
it can handle queries that check for the existence of a specific component.

### GiST index
GiST indexes are versatile and support a wide range of data types, including geometric and full-text data.

GiST indexes allow various search strategies such as nearest-neighbor and partial match searches, 
making them useful for specialized applications.

### SP-GiST index
SP-GiST indexes are useful for indexing data with hierarchical structures or complex data types.

SP-GiST indexes partition the index space into non-overlapping regions, offering efficient search capabilities 
for specialized data structures.

### BRIN (Block Range Index) index
BRIN indexes are designed for very large tables where indexing every row is impractical.
A BRIN index divides the table into ranges of pages and stores summarized information about each range, 
making them efficient for range queries on large datasets while using minimal space.


### Caching

[Caching](database/caching.MD)

HLD

Tradeoffs


BloomFilter
Consistent Hashing
Storage - LSM, Erasure Encoding, Storage Pools, Metadata mmt
