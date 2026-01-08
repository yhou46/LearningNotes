# System design

# Database and storage

- Query optimization / Denormalization / Partition/ Sharding
- N+1 Query
- Primary index vs secondary index vs global index

- How to prevent race condition
- How to prevent transaction overwrite (locking / lock free approaches)
- ACID / row lock / distributed lock

- MySQL vs NoSQL vs TimeSeries DB

- Consistent hashing

Storage?
- OLAP vs OLTP
- ElasticSearch
- ELK

# Backend and efficiency

- Speed up on read path
- speed up on write path
- caching strategies
- cache warm up
- sharing: push vs pull

- fanout vs hotspot (celebrity effects)

High availablity:
- Usage too high issues (connection count, CPU, memory, disk, storage, cache)
- CP vs AP (CAP theory)

Pagination (cursor vs offset)

# API design
- RESTful API vs GraphQL API vs gRPC
- API gateway

- talk to 3rd party API (backoff, retry, timeout, circuit breaker, random select, local fallback)

# Database

## SQL
SQL supports search by any columns without the need of creating indexes but indexes help the performance

## NoSQL
NoSQL doesn't support search by any attributes?

### NoSQL single table design
- Amazon Dynamo DB single table design:
Reference:
    - https://aws.amazon.com/blogs/compute/creating-a-single-table-design-with-amazon-dynamodb/
    - https://dynobase.dev/dynamodb-joins/


- Mongo DB
    - [Data duplication](https://medium.com/mongodb/data-duplication-in-mongodb-and-modern-applications-5a5c1836d114)
    - [The Single Collection Pattern](https://www.mongodb.com/developer/products/mongodb/single-collection-pattern/)

### Amazon Dynamo DB
- Core components: https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.CoreComponents.html
- Issue with Dynamo DB: keys and indexes have to be designed based on query pattern, like how you are going to query the data

# File upload and distribution
- Client upload vs Server upload
- pre-signed URL / Multipart upload on client

# Communication protocols:
- long term connection: websocket vs long polling vs server-sent events
- cookie vs local storage

# Python concurrency
- Process vs thread vs coroutine
- python when to use multiprocessing / threading / asyncio

- ASGI vs WSGI

# Serialization
- Serialization and deserialization
- Bloom Filter