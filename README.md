# MongoDB Kiro Power

A [Kiro Power](https://kiro.dev) that brings MongoDB expertise directly into your AI-powered development environment — combining the official MongoDB MCP Server with curated steering skills adapted from the [mongodb/agent-skills](https://github.com/mongodb/agent-skills) repository.

## What is Kiro?

Kiro is an AI-powered development environment that helps developers design systems, explore solutions, and write code through conversational and spec-driven workflows. It supports tools, MCP servers, and "Powers" — packaged bundles of documentation, skills, and integrations that extend its capabilities in specific domains.

## What is a Kiro Power?

A Kiro Power packages three things together:

1. **MCP Server integration** — connects Kiro to external tools (in this case, the MongoDB MCP Server for querying databases, managing clusters, etc.)
2. **Steering files (skills)** — domain-specific guidance documents that Kiro loads on demand to provide expert-level assistance
3. **Documentation (POWER.md)** — a manifest describing capabilities, tools, workflows, and best practices

The benefit: instead of Kiro relying solely on general knowledge, a Power gives it deep, structured expertise in a specific domain. For MongoDB, this means Kiro can design schemas using proven patterns, optimize queries with explain plans, translate natural language into MQL, and manage Atlas infrastructure — all with the precision of a MongoDB specialist.

## Skills from mongodb/agent-skills

This Power adapts skills from the [mongodb/agent-skills](https://github.com/mongodb/agent-skills) repository into Kiro's steering file format. Each skill is a markdown file with YAML front-matter that Kiro loads on demand when you reference it in chat (e.g., `#mongodb-schema-design`).

| Skill | Source Concept | What It Does |
|-------|---------------|--------------|
| `mongodb-schema-design` | Schema design patterns & anti-patterns | Guides embed vs. reference decisions, applies patterns (bucket, computed, polymorphic, outlier, archive), detects anti-patterns, and recommends JSON Schema validation |
| `mongodb-query-optimizer` | Query optimization & indexing | Diagnoses slow queries using explain plans, recommends compound indexes following ESR (Equality-Sort-Range) principles, and leverages Atlas Performance Advisor |
| `mongodb-natural-language-querying` | Natural language to MQL | Translates plain-English data questions into schema-validated `find` queries and aggregation pipelines with index-aware optimization |
| `mongodb-connection` | Connection configuration | Optimizes connection pools, timeouts, and TLS settings for serverless, OLTP, OLAP, and bursty workloads across all driver languages |
| `mongodb-mcp-setup` | MCP server configuration | Guides setup of the MongoDB MCP server with connection strings, Atlas credentials, or Atlas Local deployments |
| `atlas-stream-processing` | Real-time data pipelines | Builds Atlas Stream Processing pipelines with Kafka, S3, Kinesis, and Lambda integrations; provisions workspaces and optimizes tier sizing |
| `mongodb-search-and-ai` | Search & vector capabilities | Implements full-text search, vector search (for RAG/semantic similarity), and hybrid search with Atlas Search index creation and query optimization |

### Kiro-Specific Format

Each skill follows this structure:

```markdown
---
name: skill-name
description: When and how to invoke this skill...
allowed-tools: mcp__mongodb__*
---

# Skill Title

[Detailed guidance, workflows, and reference links]
```

Skills can reference additional detail files in a `references/` subdirectory, keeping the top-level skill concise while providing deep-dive material when needed.

## Repository Structure

```
.
├── POWER.md                          # Power manifest — capabilities, tools, workflows, config
├── mcp.json                          # MCP server configuration for the MongoDB server
├── LICENSE                           # Apache 2.0
├── steering/                         # Steering skills (loaded on demand in Kiro)
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
│   │   └── references/               # 18 pattern/fundamental/anti-pattern reference files
│   └── mongodb-search-and-ai/
│       ├── SKILL.md                  # Search & AI skill
│       └── references/               # Vector search, lexical search, hybrid search guides
├── tests/                            # Property-based and integration tests
│   ├── skills.test.js                # Validates skill file structure and POWER.md integrity
│   ├── skill-invocation-syntax.pbt.test.js  # Property-based tests for invocation syntax
│   ├── *-eval-data.js                # Evaluation datasets per skill
│   ├── *-eval-coverage.test.js       # Coverage tests per skill
│   └── *-validation-report.md        # Validation reports
└── .kiro/                            # Kiro workspace config (hooks, specs, settings)
```

## Benefits for End-Users

### 1. Expert MongoDB Guidance Without Leaving Your Editor

Instead of switching between documentation tabs, Stack Overflow, and your IDE, you get contextual MongoDB expertise inline. Kiro reads your actual schema, indexes, and data before giving advice.

### 2. Faster Query Development

**Example:** You're building a reporting dashboard and need an aggregation pipeline for revenue by region over the last 30 days.

> You: "Show me revenue by region for the last 30 days from the orders collection"
>
> Kiro (with `#mongodb-natural-language-querying`): Fetches your schema, checks indexes, and generates a validated aggregation pipeline — complete with a `$match` on your date index for performance.

### 3. Performance Optimization with Real Data

**Example:** A query is taking 3 seconds in production.

> You: "Why is this query slow?" (paste your query)
>
> Kiro (with `#mongodb-query-optimizer`): Runs `explain`, identifies a COLLSCAN, checks existing indexes, and recommends a compound index following ESR principles — then creates it for you.

### 4. Schema Design That Avoids Common Pitfalls

**Example:** You're modeling an e-commerce catalog and unsure whether to embed reviews inside products.

> You: "Should I embed reviews in my product documents or use a separate collection?"
>
> Kiro (with `#mongodb-schema-design`): Analyzes your access patterns, estimates document growth, and recommends the extended reference pattern with a capped array of recent reviews — avoiding the 16MB limit while keeping reads fast.

### 5. Atlas Infrastructure Management from Chat

**Example:** You need to spin up a new cluster for a staging environment.

> You: "Create a free Atlas cluster called staging-v2 in US East"
>
> Kiro: Creates the cluster, adds your IP to the access list, and creates a database user — all through the MCP server without opening the Atlas console.

### 6. Real-Time Pipeline Development

**Example:** You need to stream order events from Kafka into MongoDB for real-time analytics.

> You: "Build a stream processing pipeline that reads from my Kafka orders topic and writes to MongoDB"
>
> Kiro (with `#atlas-stream-processing`): Generates the pipeline definition with proper connection config, windowing, and error handling — then helps you provision the right workspace tier.

### 7. Search and AI Integration

**Example:** You're adding semantic search to your product catalog.

> You: "Set up vector search on my products collection using the description_embedding field"
>
> Kiro (with `#mongodb-search-and-ai`): Creates the vector search index with the right dimensions and similarity function, then generates a `$vectorSearch` aggregation pipeline you can use in your application.

## Getting Started

1. Install the MongoDB Power in Kiro (via the Powers panel)
2. Set your environment variables when prompted:
   - `MDB_MCP_CONNECTION_STRING` — your MongoDB connection URI
   - `MDB_MCP_API_CLIENT_ID` / `MDB_MCP_API_CLIENT_SECRET` — (optional) for Atlas management
3. Start chatting — reference skills with `#skill-name` for domain-specific guidance

## Prerequisites

- Node.js v20.19.0+, v22.12.0+, or v23+
- A MongoDB Atlas cluster or self-hosted MongoDB deployment
- Docker (optional, for Atlas Local development)

## License

Apache 2.0 — see [LICENSE](./LICENSE)
