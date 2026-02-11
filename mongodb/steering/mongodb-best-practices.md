---
inclusion: manual
---

# MongoDB Best Practices

## Schema Design

### Embedding vs Referencing

**Embed** when:

- The child data is always accessed with the parent
- The relationship is one-to-few (e.g., addresses in a user document)
- The embedded data doesn't change independently
- The embedded array won't grow unboundedly

**Reference** when:

- The child data is accessed independently
- The relationship is one-to-many or many-to-many
- The referenced documents are large or change frequently
- You need to avoid the 16MB document size limit

### Common Patterns

**Attribute Pattern** — For documents with many similar fields that need querying:

```json
{
  "attributes": [
    { "key": "color", "value": "red" },
    { "key": "size", "value": "large" }
  ]
}
```

Index: `{ "attributes.key": 1, "attributes.value": 1 }`

**Bucket Pattern** — For time-series or high-volume event data:

```json
{
  "sensorId": "abc",
  "date": "2025-01-15",
  "readings": [
    { "time": "08:00", "value": 72.1 },
    { "time": "08:05", "value": 72.3 }
  ],
  "count": 2
}
```

**Computed Pattern** — Pre-compute frequently read aggregations:

```json
{
  "productId": "p123",
  "totalOrders": 1547,
  "averageRating": 4.3,
  "lastUpdated": "2025-01-15T10:00:00Z"
}
```

**Polymorphic Pattern** — Store different entity types in one collection when they share common query patterns:

```json
{ "type": "blog_post", "title": "...", "body": "...", "author": "..." }
{ "type": "comment", "body": "...", "author": "...", "postId": "..." }
```

## Indexing Strategy

### Index Selection Guidelines

1. **Analyze your query patterns first** — Use `explain` to see what indexes queries use
2. **Create compound indexes** that support multiple queries — field order matters (equality, sort, range)
3. **Use the ESR rule** for compound index field order:
   - **E**quality fields first (exact match)
   - **S**ort fields second
   - **R**ange fields last
4. **Avoid over-indexing** — Each index adds write overhead and storage cost
5. **Use partial indexes** when queries always filter on a specific condition

### Index Types

| Index Type | Use Case | Example |
|-----------|----------|---------|
| Single field | Simple queries on one field | `{ email: 1 }` |
| Compound | Multi-field queries | `{ status: 1, createdAt: -1 }` |
| Multikey | Queries on array fields | `{ tags: 1 }` |
| Text | Full-text search | `{ description: "text" }` |
| TTL | Auto-expire documents | `{ createdAt: 1 }` with `expireAfterSeconds` |
| Unique | Enforce uniqueness | `{ email: 1 }` with `unique: true` |
| Partial | Index a subset of documents | `{ status: 1 }` with `partialFilterExpression` |

### Anti-Patterns

- Creating indexes on fields with very low cardinality (e.g., boolean fields alone)
- Having more than 5-10 indexes per collection without good reason
- Not including all fields needed for covered queries in the index
- Ignoring index intersection — a single compound index is usually better than two separate ones

## Aggregation Pipeline

### Performance Tips

1. **Place `$match` and `$limit` as early as possible** — Reduces documents flowing through the pipeline
2. **Use `$project` or `$addFields` early** to reduce document size in memory
3. **Avoid `$unwind` on large arrays** without preceding `$match` — it multiplies the document count
4. **Use `$group` with accumulators** instead of `$unwind` + `$group` when possible
5. **Consider `$merge` or `$out`** for materializing expensive aggregation results

### Common Pipeline Patterns

**Lookup with pipeline** (preferred over simple `$lookup`):

```json
{
  "$lookup": {
    "from": "orders",
    "let": { "userId": "$_id" },
    "pipeline": [
      { "$match": { "$expr": { "$eq": ["$userId", "$$userId"] } } },
      { "$sort": { "createdAt": -1 } },
      { "$limit": 5 }
    ],
    "as": "recentOrders"
  }
}
```

**Faceted search**:

```json
{
  "$facet": {
    "results": [{ "$skip": 0 }, { "$limit": 10 }],
    "totalCount": [{ "$count": "count" }],
    "categories": [{ "$group": { "_id": "$category", "count": { "$sum": 1 } } }]
  }
}
```

## Security

### Connection Security

- Always use `mongodb+srv://` connection strings for Atlas deployments
- Never hardcode connection strings in code — use environment variables
- Use TLS/SSL for all production connections
- Rotate database credentials regularly

### Access Control

- Follow the principle of least privilege for database users
- Use separate database users for different applications
- Use Atlas IP access lists to restrict network access
- Enable audit logging for compliance-sensitive workloads

### Data Validation

- Use JSON Schema validation on collections to enforce document structure:

```javascript
db.createCollection("users", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["email", "name"],
      properties: {
        email: { bsonType: "string", pattern: "^.+@.+$" },
        name: { bsonType: "string", minLength: 1 },
        role: { enum: ["admin", "user", "viewer"] },
      },
    },
  },
});
```

## Performance

### Read Optimization

- Use `$project` or `projection` to return only needed fields
- Add appropriate indexes for your query patterns
- Use `limit` when you don't need all results
- Use covered queries when possible (all fields in the index)

### Write Optimization

- Use `insert-many` for bulk inserts instead of individual inserts
- Use `update-many` with `$set` to update specific fields rather than replacing entire documents
- Use `bulkWrite` for mixed operations
- Consider write concern based on durability requirements

### Monitoring

- Use `db-stats` to track database growth
- Use `collection-storage-size` to identify oversized collections
- Use `explain` regularly to verify query plans
- Use `atlas-get-performance-advisor` for proactive index recommendations
