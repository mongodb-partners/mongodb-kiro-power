---
inclusion: manual
---

# Get Started with MongoDB

Follow these steps to connect to your MongoDB database and start working with your data.

## Communication Style

- Keep responses concise and action-oriented
- Show query results in readable table or JSON format
- Confirm destructive operations before executing
- Suggest indexes and optimizations proactively

## Step 1: Verify Prerequisites

Check that the user's environment is ready:

- Node.js v20.19.0+, v22.12.0+, or v23+ is installed
- For local development: Docker is installed (if using `atlas-local-create-deployment`)
- For Atlas: a Service Account with Client ID and Secret is available

## Step 2: Connect to MongoDB

Determine the user's setup and connect accordingly:

**If they have a connection string:**

Use `connect` with their connection string. This works for both local MongoDB and Atlas clusters.

**If they want a local development database:**

Use `atlas-local-create-deployment` to spin up a local MongoDB instance, then `atlas-local-connect-deployment` to connect to it. Requires Docker to be running.

**If they want to manage Atlas:**

Ensure `MDB_MCP_API_CLIENT_ID` and `MDB_MCP_API_CLIENT_SECRET` environment variables are set. Use `atlas-list-orgs` to verify connectivity.

## Step 3: Explore the Database

Once connected:

1. Run `list-databases` to see all available databases
2. Pick the relevant database and run `list-collections`
3. For each important collection, run `collection-schema` to understand the document structure
4. Run `collection-indexes` to see what indexes exist
5. Use `find` with a `limit` of 3-5 to sample actual documents

## Step 4: Understand the Data Model

After inspecting schemas and sample documents:

- Identify the primary collections and their relationships
- Note embedded vs referenced data patterns
- Check for any validation rules on collections
- Summarize the data model back to the user

## Step 5: Verify Access Level

Test that the configured access level matches user needs:

- Try a read operation (`find`) to confirm read access
- If writes are needed, confirm the user wants write access and test with a safe operation
- If Atlas management is needed, test with `atlas-list-projects`

## Step 6: What's Next

Based on the user's goals, suggest next steps:

- **Building queries** — Start with `find`, progress to `aggregate` for complex analysis
- **Schema changes** — Use `create-collection` with validation, `create-index` for performance
- **Performance tuning** — Use `explain` and `atlas-get-performance-advisor`
- **Data migration** — Follow the data migration workflow in POWER.md
- **Local development** — Set up a local deployment with test data

## Trigger Phrases

Load this steering file when users say things like:

- "Set up MongoDB"
- "Connect to my database"
- "Get started with Mongo"
- "Help me connect to Atlas"
- "I want to explore my MongoDB data"
