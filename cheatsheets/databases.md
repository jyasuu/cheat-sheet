# Databases Cheat Sheet

## 🐘 PostgreSQL — Advanced Operations

**Connection**
```bash
psql -U username -d dbname -h host
```

**Backups**
```bash
pg_dump dbname > backup.sql
pg_restore -d dbname backup.sql
```

**Transactions**
```sql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

**User Management**
```sql
CREATE USER username WITH PASSWORD 'password';
GRANT ALL PRIVILEGES ON DATABASE dbname TO username;
```

## 🐘 PostgreSQL — Advanced Features

**Window Functions**
```sql
SELECT department, employee, salary, 
       AVG(salary) OVER (PARTITION BY department) as avg_dept_salary
FROM employees;
```

**Materialized Views**
```sql
CREATE MATERIALIZED VIEW sales_summary AS
SELECT product, SUM(quantity) FROM sales GROUP BY product;
REFRESH MATERIALIZED VIEW sales_summary;
```

**Extensions**
```sql
CREATE EXTENSION pg_trgm;  -- Fuzzy string matching
CREATE EXTENSION postgis;   -- Geospatial support
```


**Check Long-Running Queries**

```sql
SELECT now() - query_start, * 
FROM pg_catalog.pg_stat_activity 
WHERE state = 'active' 
  AND now() - query_start > interval '5 minute';
```

**Table Disk Size**

```sql

SELECT nspname || '.' || relname AS "relation",
    pg_size_pretty(pg_total_relation_size(C.oid)) AS "total_size"
  FROM pg_class C
  LEFT JOIN pg_namespace N ON (N.oid = C.relnamespace)
  WHERE nspname NOT IN ('pg_catalog', 'information_schema')
    AND C.relkind <> 'i'
    AND nspname !~ '^pg_toast'
  ORDER BY pg_total_relation_size(C.oid) DESC
  LIMIT 20;
```


**Others**

```sql
\x format display

SELECT jsonb_pretty(json_column::jsonb) FROM your_table;

SELECT *
FROM my_table
WHERE my_column ~ '[\u0009-\u000D\u00A0\u1680\u2000-\u200A\u2028\u2029\u202F\u205F\u3000]';
SELECT * FROM users WHERE name LIKE 'Leo%';
SELECT * FROM users WHERE name ILIKE 'leo%';
SELECT * FROM users WHERE name SIMILAR TO '(Leo|Luke)%';
SELECT * FROM logs WHERE message ~ 'error|fail';
SELECT * FROM logs WHERE message ~* 'error|fail';
SELECT * FROM logs WHERE message !~ 'success';
SELECT * FROM products WHERE tags @> ARRAY['electronics'];
SELECT * FROM products WHERE ARRAY['electronics'] <@ tags;
SELECT * FROM products WHERE tags && ARRAY['electronics', 'home'];
SELECT data->'name' FROM users;
SELECT data->>'name' FROM users;
SELECT data#>'{address,city}' FROM users;
SELECT * FROM events WHERE timerange @> now();
SELECT * FROM events WHERE timerange && '[2025-01-01,2025-12-31)'::tstzrange;
SELECT * FROM table1 WHERE col1 IS DISTINCT FROM col2;
SELECT * FROM users WHERE role IN ('admin', 'user');

set search_path=public;

```


## 📊 MongoDB — Comprehensive Guide

**Connection**

```powershell
.\mongosh.exe "mongodb+srv://?/" --apiVersion 1 --username ?
docker run -ti --rm alpine/mongosh mongosh "mongodb://user:pass@host:27017/db"
```

**Basic Queries**

```javascript
db.inventory.insertMany([
  { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
  ...
]);

db.inventory.find({ item: { $in: ["paper"] } }, { item: 1 });
```


**Indexes**
```javascript
db.collection.createIndex({ field: 1 });  // 1 for ascending, -1 for descending
db.collection.getIndexes();
```

**Aggregation**
```javascript
db.orders.aggregate([
  { $match: { status: "A" } },
  { $group: { _id: "$cust_id", total: { $sum: "$amount" } } }
]);
```

**Replica Set Status**
```bash
mongosh --eval "rs.status()"
```

## 📊 MongoDB — Expert Techniques

**Transactions**
```javascript
session = db.getMongo().startSession();
session.startTransaction();
try {
  db.orders.insertOne({ item: "book" }, { session });
  db.inventory.updateOne({ item: "book" }, { $inc: { qty: -1 } }, { session });
  session.commitTransaction();
} catch (e) {
  session.abortTransaction();
}
```

**Bulk Operations**
```javascript
const bulk = db.items.initializeUnorderedBulkOp();
bulk.insert({ item: "laptop" });
bulk.insert({ item: "monitor" });
bulk.execute();
```

**Text Search**
```javascript
db.articles.createIndex({ content: "text" });
db.articles.find({ $text: { $search: "database" } });
```

## 🔴 Redis — Key Operations

**Basic Commands**
```bash
redis-cli
SET key "value"
GET key
DEL key
EXPIRE key 60  # TTL in seconds
```

**Hashes**
```bash
HSET user:1000 name "John" age 30
HGETALL user:1000
```

**Publish/Subscribe**
```bash
# Terminal 1:
SUBSCRIBE channel

# Terminal 2:
PUBLISH channel "message"
```

## 🔴 Redis — Advanced Patterns

**Streams**
```bash
# Producer:
XADD mystream * sensor-id 1234 temp 19.8

# Consumer:
XREAD BLOCK 0 STREAMS mystream $
```

**Lua Scripting**
```bash
eval "return redis.call('GET', KEYS[1])..ARGV[1]" 1 mykey _suffix
```

**Cluster Management**
```bash
redis-cli --cluster create 127.0.0.1:7000 127.0.0.1:7001
redis-cli --cluster check 127.0.0.1:7000
```

## 🔍 Elasticsearch — CRUD Operations

**Create Index**
```bash
curl -X PUT "localhost:9200/index_name"
```

**Add Document**
```bash
curl -X POST "localhost:9200/index_name/_doc" -H 'Content-Type: application/json' -d'
{
  "field": "value"
}'
```

**Search**
```bash
curl -X GET "localhost:9200/index_name/_search?q=field:value"
```

**Delete Index**
```bash
curl -X DELETE "localhost:9200/index_name"
```

## 🔍 Elasticsearch — Advanced Usage

**Aggregations**
```bash
curl -X GET "localhost:9200/sales/_search" -H 'Content-Type: application/json' -d'
{
  "aggs": {
    "avg_price": { "avg": { "field": "price" } }
  }
}'
```

**Mappings**
```bash
curl -X PUT "localhost:9200/my_index" -H 'Content-Type: application/json' -d'
{
  "mappings": {
    "properties": {
      "date": { "type": "date" },
      "text": { "type": "text", "analyzer": "english" }
    }
  }
}'
```

**Reindex API**
```bash
curl -X POST "localhost:9200/_reindex" -H 'Content-Type: application/json' -d'
{
  "source": { "index": "old_index" },
  "dest": { "index": "new_index" }
}'
```

## Rabbitmq

```bash
rabbitmqadmin -u admin -p ? export broker
 
rabbitmqadmin -u admin -p ? import broker

rabbitmqadmin -u admin -p admin publish \
    exchange=ex \
    routing_key=key \
    payload='{"a": "","b":{"c": ""}}' \
    properties='{"headers": {"id": "6D933FBEEA6D42E2AD0E1FA479A5DABA"}}'
```



## Oracle

```sql
CREATE DATABASE LINK $LINK_NAME 
    CONNECT TO $DB_USER  IDENTIFIED BY "$DB_PASS"
    USING '(DESCRIPTION=
                (ADDRESS=(PROTOCOL=TCP)(HOST=$DB_HOST)(PORT=1521))
                (CONNECT_DATA=(SID=$DB_NAME))
            )';

DROP DATABASE LINK $LINK_NAME; 
```


## tbls

```bash
tbls doc postgres://postgres:postgres@localhost:5432/matdb?sslmode=disable
```
