# Databases Cheat Sheet

## 📊 MongoDB — Quick Operations

**Connection**

```powershell
.\mongosh.exe "mongodb+srv://?/" --apiVersion 1 --username ?
```

**Basic Queries**

```javascript
db.inventory.insertMany([
  { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
  ...
]);

db.inventory.find({ item: { $in: ["paper"] } }, { item: 1 });
```

---

## 🐘 PostgreSQL — Monitoring

**Check Long-Running Queries**

```sql
SELECT now() - query_start, * 
FROM pg_catalog.pg_stat_activity 
WHERE state = 'active' 
  AND now() - query_start > interval '5 minute';
```

---

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