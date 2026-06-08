# MongoDB Kiro Power

A [Kiro](https://kiro.dev) power that connects your IDE directly to [MongoDB](https://mongodb.com) databases and [Atlas](https://www.mongodb.com/atlas) clusters. Query collections, design schemas, optimize indexes, translate natural language to queries, and manage Atlas infrastructure — all through natural language in your editor.

## What It Does

This power gives Kiro the ability to talk to your MongoDB deployments via the [MongoDB MCP Server](https://github.com/mongodb-js/mongodb-mcp-server), and brings in expert guidance through skills adapted from the [mongodb/agent-skills](https://github.com/mongodb/agent-skills) repository. Instead of switching between your IDE, MongoDB Compass, and the Atlas console, you can ask Kiro things like:

- "Show me all orders placed in the last 7 days with status pending"
- "Why is this aggregation pipeline slow?"
- "Should I embed reviews in my product documents or use a separate collection?"
- "Create a free Atlas cluster called staging-v2 in US East"
- "Set up vector search on my products collection"
- "Build a stream processing pipeline that reads from my Kafka orders topic"

Kiro will run the right queries, inspect your schema and indexes, and give you actionable advice grounded in [7 expert skills](#whats-included) for MongoDB.

## Quick Start

### Prerequisites

- [Kiro IDE](https://kiro.dev) installed
- Node.js v20.19.0+, v22.12.0+, or v23+
- A MongoDB Atlas cluster or self-hosted MongoDB deployment
- Docker (optional, for Atlas Local development)

### 1. Install the Power

Open Kiro and install the **MongoDB** power from the Powers panel, or add it directly from the GitHub URL:

```
https://github.com/mongodb/kiro-powers
```

### 2. Set Your Environment Variables

When prompted, configure your connection:

- `MDB_MCP_CONNECTION_STRING` — your MongoDB connection URI (e.g. `mongodb+srv://user:pass@cluster.mongodb.net/mydb`)
- `MDB_MCP_API_CLIENT_ID` / `MDB_MCP_API_CLIENT_SECRET` — (optional) Atlas Service Account credentials for cluster management

### 3. Start Querying

A natural entry point is to explore your databases and collections:

```
> "List my databases and collections"
> "Show me the schema for the orders collection"
```

Then query, optimize, and build:

```
> "Find the top 10 customers by total spend this month"
> "Why is this query doing a COLLSCAN?"
> "Create a compound index for this filter and sort pattern"
> "Translate this question into an aggregation pipeline: revenue by region for Q1"
```

## What's Included

### MCP Tools

**Query & Discovery**

| Tool | Description |
|------|-------------|
| `find` | Run a find query with filter, projection, sort, and limit |
| `aggregate` | Execute an aggregation pipeline |
| `count` | Count documents matching a filter |
| `collection-schema` | Inspect the inferred schema of a collection |
| `collection-indexes` | List all indexes on a collection |
| `list-databases` | List all databases |
| `list-collections` | List collections in a database |
| `db-stats` | Get database statistics |
| `collection-storage-size` | Get the size of a collection in MB |
| `explain` | Analyze query execution plan |
| `export` | Export query or aggregation results as EJSON |
| `mongodb-logs` | Return recent mongod log events |

**Write Operations**

| Tool | Description |
|------|-------------|
| `insert-many` | Insert an array of documents |
| `update-many` | Update all documents matching a filter |
| `delete-many` | Delete all documents matching a filter |
| `create-collection` | Create a new collection |
| `drop-collection` | Remove a collection and its indexes |
| `drop-database` | Remove a database |
| `rename-collection` | Rename a collection |
| `create-index` | Create a standard or vector search index |
| `drop-index` | Drop an index by name |

**Connection Management**

| Tool | Description |
|------|-------------|
| `connect` | Connect to a MongoDB instance or Atlas cluster |
| `switch-connection` | Switch to a different MongoDB connection |

**Atlas Management**

| Tool | Description |
|------|-------------|
| `atlas-list-orgs` | List Atlas organizations |
| `atlas-list-projects` | List Atlas projects |
| `atlas-create-project` | Create an Atlas project |
| `atlas-list-clusters` | List Atlas clusters |
| `atlas-inspect-cluster` | Inspect cluster metadata |
| `atlas-create-free-cluster` | Create a free-tier Atlas cluster |
| `atlas-list-db-users` | List database users |
| `atlas-create-db-user` | Create a database user |
| `atlas-inspect-access-list` | View IP access list |
| `atlas-create-access-list` | Add IPs/CIDR ranges to access list |
| `atlas-list-alerts` | List active Atlas alerts |
| `atlas-get-performance-advisor` | Get index recommendations from Performance Advisor |

**Atlas Local Development**

| Tool | Description |
|------|-------------|
| `atlas-local-create-deployment` | Spin up a local Atlas deployment via Docker |
| `atlas-local-list-deployments` | List local deployments |
| `atlas-local-connect-deployment` | Connect to a local deployment |
| `atlas-local-delete-deployment` | Delete a local deployment |

### Skills: 7 Expert Guidance Modules

The power ships with skills adapted from [mongodb/agent-skills](https://github.com/mongodb/agent-skills) — the canonical best-practice guidance used across MongoDB agent integrations. Reference a skill by name in Kiro chat to activate it (e.g. `#mongodb-schema-design`).

**Schema Design**
- Choose between embedding and referencing based on access patterns and document growth
- Apply proven patterns: bucket, computed, polymorphic, outlier, archive, extended reference
- Detect anti-patterns: excessive lookups, unnecessary collections, redundant indexes
- Enforce structure with JSON Schema validation

**Query Optimization**
- Diagnose slow queries using `explain` plans and Atlas Performance Advisor
- Build compound indexes following the ESR (Equality → Sort → Range) rule
- Eliminate COLLSCAN plans and identify unused indexes

**Natural Language Querying**
- Translate plain-English questions into schema-validated `find` queries and aggregation pipelines
- Index-aware optimization: suggests filters that align with existing indexes

**Connection Configuration**
- Tune connection pool sizes, timeout values, and TLS/SSL settings
- Profiles for serverless, OLTP, OLAP, and bursty workloads
- Covers all supported driver languages

**MCP Server Setup**
- Configure the MongoDB MCP Server with connection strings or Atlas Service Account credentials
- Set up Atlas Local deployments for development
- Enable read-only mode for safer access

**Atlas Stream Processing**
- Build real-time pipelines connecting Kafka, S3, Kinesis, and Lambda to MongoDB
- Provision Atlas Stream Processing workspaces and choose the right tier
- Debug processors and optimize pipeline performance

**Search & AI**
- Create Atlas Search indexes for full-text, autocomplete, and faceted search
- Build `$vectorSearch` pipelines for semantic similarity and RAG applications
- Implement hybrid search combining lexical and vector scoring

## Project Structure

```
.
├── POWER.md                          # Power manifest — capabilities, tools, workflows, config
├── mcp.json                          # MCP server configuration
├── LICENSE                           # Apache 2.0
├── steering/                         # Skills (loaded on demand in Kiro)
│   ├── atlas-stream-processing/
│   │   ├── skill.md                  # Stream processing skill
│   │   └── references/               # Connection configs, pipeline patterns, sizing guides
│   ├── mongodb-connection/
│   │   ├── skill.md                  # Connection optimization skill
│   │   └── references/               # Monitoring guide
│   ├── mongodb-mcp-setup/
│   │   └── skill.md                  # MCP server setup skill
│   ├── mongodb-natural-language-querying/
│   │   └── skill.md                  # NLQ skill
│   ├── mongodb-query-optimizer/
│   │   ├── skill.md                  # Query optimization skill
│   │   └── references/               # Indexing principles, aggregation optimization, anti-patterns
│   ├── mongodb-schema-design/
│   │   ├── SKILL.md                  # Schema design skill
│   │   └── references/               # 18 pattern, fundamental, and anti-pattern reference files
│   └── mongodb-search-and-ai/
│       ├── SKILL.md                  # Search & AI skill
│       └── references/               # Vector search, lexical search, hybrid search guides
├── tests/                            # Property-based and integration tests
│   ├── skills.test.js                # Validates skill file structure and POWER.md integrity
│   ├── skill-invocation-syntax.pbt.test.js
│   ├── *-eval-data.js                # Evaluation datasets per skill
│   ├── *-eval-coverage.test.js       # Coverage tests per skill
│   └── *-validation-report.md        # Validation reports
└── .kiro/                            # Kiro workspace config (hooks, specs, settings)
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Connection refused | Verify the connection string and that the MongoDB instance is reachable |
| Authentication failed | Check username/password in the connection string or Atlas API credentials |
| Atlas API 403 | Ensure the Service Account has the required project/org role in Atlas |
| `Namespace not found` | Use `list-databases` and `list-collections` to verify exact names |
| Index build failed | Check for duplicate key violations with `find`; verify field paths |
| Slow aggregation | Add an early `$match` stage to reduce documents processed; add indexes |
| COLLSCAN on `explain` | Create a compound index aligned to your filter and sort fields using the ESR rule |
| Docker not running | Start Docker Desktop before using `atlas-local-create-deployment` |
| MCP server not connecting | Reconnect from Kiro's MCP panel; verify environment variables are set correctly |

## Resources

- [MongoDB MCP Server Documentation](https://www.mongodb.com/docs/mcp-server/get-started/)
- [MongoDB MCP Server GitHub](https://github.com/mongodb-js/mongodb-mcp-server)
- [MongoDB Atlas Documentation](https://www.mongodb.com/docs/atlas/)
- [MongoDB Aggregation Pipeline Reference](https://www.mongodb.com/docs/manual/reference/operator/aggregation/)
- [mongodb/agent-skills](https://github.com/mongodb/agent-skills)
- [Kiro IDE](https://kiro.dev)

## Legal & Support

- **License:** [Apache-2.0](./LICENSE)
- **MCP Server License:** [Apache-2.0](https://github.com/mongodb-js/mongodb-mcp-server/blob/main/LICENSE)
- **Privacy Policy:** [MongoDB Privacy Policy](https://www.mongodb.com/legal/privacy/privacy-policy)
- **Support:** [GitHub Issues](https://github.com/mongodb/kiro-powers/issues) | [MongoDB Community Forums](https://www.mongodb.com/community/forums/)
