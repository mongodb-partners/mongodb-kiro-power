# MongoDB Kiro Power

This repository contains a [Kiro Power](https://kiro.dev/docs/powers/) for MongoDB that gives the Kiro AI agent on-demand access to MongoDB databases and Atlas clusters through the official [MongoDB MCP Server](https://github.com/mongodb-js/mongodb-mcp-server).

## What's Included

```
mongodb/
├── POWER.md                          # Power metadata, tool catalog, and usage examples
├── mcp.json                          # MCP server configuration
└── steering/
    ├── getting-started.md            # Interactive setup walkthrough
    └── mongodb-best-practices.md     # Schema, indexing, and security guidance
```

| File | Purpose |
|------|---------|
| `POWER.md` | Entry point for the power — defines keywords, lists all 39 available tools, and provides workflows and best practices |
| `mcp.json` | Configures the `mongodb-mcp-server` MCP server (runs via `npx` over stdio) |
| `steering/getting-started.md` | Guides the agent through connecting, exploring, and verifying database access |
| `steering/mongodb-best-practices.md` | Teaches the agent schema design patterns, indexing strategies, aggregation tips, and security practices |

## Prerequisites

- **Node.js** v20.19.0+, v22.12.0+, or v23+
- **Docker** (optional — required only for local Atlas deployments or the Docker-based MCP server)
- **MongoDB Atlas cluster** or a self-hosted MongoDB instance
- **Atlas Service Account** (optional — only needed for Atlas management operations)

## Testing Locally

### 1. Verify the MCP Server Runs

Before installing the power, confirm the MCP server starts correctly:

```bash
# Check Node.js version
node --version   # Must be v20.19.0+, v22.12.0+, or v23+

# Run the server directly (it communicates over stdio, so it will hang waiting for input — Ctrl+C to stop)
npx -y mongodb-mcp-server@latest
```

If you see no errors and the process starts, the server is working.

### 2. Test With a Connection String

Set the environment variable and run the server to verify it can connect to your database:

```bash
export MDB_MCP_CONNECTION_STRING="mongodb://localhost:27017"
# or for Atlas:
# export MDB_MCP_CONNECTION_STRING="mongodb+srv://user:password@cluster.mongodb.net/mydb"

npx -y mongodb-mcp-server@latest
```

### 3. Test With Docker (Alternative)

```bash
export MDB_MCP_CONNECTION_STRING="mongodb+srv://user:password@cluster.mongodb.net/mydb"

docker run --rm -i \
  -e MDB_MCP_CONNECTION_STRING \
  mongodb/mongodb-mcp-server:latest
```

### 4. Test in Read-Only Mode

To prevent accidental writes during testing:

```bash
npx -y mongodb-mcp-server@latest --readOnly
```

### 5. Test as an HTTP Server

The MCP server also supports HTTP transport, which is useful for debugging with tools like `curl`:

```bash
npx -y mongodb-mcp-server@latest --transport http --httpPort=8080
```

## Using With Kiro IDE

### Install From Local Path

1. Clone this repository:
   ```bash
   git clone https://github.com/anujpanchal57/MongoDB-Kiro-Power.git
   ```
2. Open Kiro IDE
3. Go to the **Powers** panel
4. Click **Add power from Local Path**
5. Select the `mongodb/` directory inside the cloned repository
6. Click **Install**
7. When prompted, set the required environment variables:
   - `MDB_MCP_CONNECTION_STRING` — your MongoDB connection URI
   - `MDB_MCP_API_CLIENT_ID` — Atlas Service Account Client ID (optional)
   - `MDB_MCP_API_CLIENT_SECRET` — Atlas Service Account Secret (optional)
8. Use **Try the power** to run through the onboarding flow

### Install From GitHub

1. Open Kiro IDE → **Powers** panel
2. Click **Add power from GitHub**
3. Enter the repository URL: `https://github.com/anujpanchal57/MongoDB-Kiro-Power`
4. Click **Install** and set environment variables when prompted

### Verify Installation

Mention any of the power's keywords in a conversation to trigger activation:

- "Connect to my MongoDB database"
- "Help me query my Mongo collections"
- "Set up MongoDB"
- "Explore my Atlas cluster"

Kiro will automatically load the power and its MCP tools based on these keywords.

## Using With Kiro CLI

### Option A: Add via `kiro-cli mcp add`

```bash
kiro-cli mcp add \
  --name "mongodb" \
  --scope global \
  --command "npx" \
  --args "-y mongodb-mcp-server@latest" \
  --env "MDB_MCP_CONNECTION_STRING=\${MDB_MCP_CONNECTION_STRING}"
```

Make sure the environment variable is exported in your shell before starting the CLI:

```bash
export MDB_MCP_CONNECTION_STRING="mongodb+srv://user:password@cluster.mongodb.net/mydb"
```

### Option B: Add via Configuration File

Create or edit the MCP configuration file:

- **Per-project**: `<project-root>/.kiro/settings/mcp.json`
- **Global**: `~/.kiro/settings/mcp.json`

```json
{
  "mcpServers": {
    "mongodb": {
      "command": "npx",
      "args": ["-y", "mongodb-mcp-server@latest"],
      "env": {
        "MDB_MCP_CONNECTION_STRING": "${MDB_MCP_CONNECTION_STRING}",
        "MDB_MCP_API_CLIENT_ID": "${MDB_MCP_API_CLIENT_ID}",
        "MDB_MCP_API_CLIENT_SECRET": "${MDB_MCP_API_CLIENT_SECRET}"
      }
    }
  }
}
```

### Verify in Kiro CLI

Inside an interactive Kiro CLI session, run:

```
/mcp
```

This shows all loaded MCP servers and confirms the MongoDB server is connected.

## Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `MDB_MCP_CONNECTION_STRING` | Yes (for DB access) | MongoDB connection URI (e.g., `mongodb://localhost:27017` or `mongodb+srv://...`) |
| `MDB_MCP_API_CLIENT_ID` | No | Atlas Service Account Client ID (for Atlas management operations) |
| `MDB_MCP_API_CLIENT_SECRET` | No | Atlas Service Account Secret (for Atlas management operations) |

> **Security**: Always use environment variables for credentials. Never hardcode connection strings or API keys in configuration files that get committed to version control.

## Troubleshooting

### Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| `Connection refused` | MongoDB is not running or the connection string is wrong | Verify the connection string and that the MongoDB instance is reachable. For local instances, ensure `mongod` is running. |
| `Authentication failed` | Invalid username/password | Double-check the credentials in your connection string or Atlas API keys. |
| `Atlas API 403` | Service Account lacks permissions | Ensure the Service Account has the required project/org role in Atlas. |
| `Namespace not found` | Database or collection doesn't exist | Use `list-databases` and `list-collections` to verify the names. |
| `Index build failed` | Duplicate key violation or invalid key spec | Check for duplicate values with `find` and verify field paths exist. |
| `Timeout on aggregation` | Pipeline too complex or collection too large | Add early `$match` stages to reduce documents processed; add indexes on filtered fields. |
| `ECONNREFUSED on local deployment` | Docker is not running | Start Docker Desktop before using `atlas-local-create-deployment`. |
| `npx: command not found` | Node.js / npm not installed or not in PATH | Install Node.js v20.19.0+ and ensure `npx` is available (`npm install -g npx` if needed). |
| `Could not find or load module mongodb-mcp-server` | Network issue or npm registry unreachable | Check your internet connection. If behind a proxy, configure npm: `npm config set proxy http://proxy:port`. |
| MCP server starts but no tools appear | Environment variables not set | Ensure `MDB_MCP_CONNECTION_STRING` is exported in the shell where Kiro runs. |

### Debugging Steps

1. **Check Node.js version**:
   ```bash
   node --version
   ```
   Must be v20.19.0+, v22.12.0+, or v23+.

2. **Run the MCP server manually** to see raw error output:
   ```bash
   npx -y mongodb-mcp-server@latest 2>&1
   ```

3. **Test your connection string directly** with `mongosh` (the MongoDB shell):
   ```bash
   mongosh "mongodb://localhost:27017"
   # or
   mongosh "mongodb+srv://user:password@cluster.mongodb.net/mydb"
   ```
   If `mongosh` can't connect, the MCP server won't be able to either.

4. **Verify environment variables are set**:
   ```bash
   echo $MDB_MCP_CONNECTION_STRING
   echo $MDB_MCP_API_CLIENT_ID
   ```

5. **Check Docker is running** (for local Atlas deployments):
   ```bash
   docker info
   ```

6. **Use the MCP server's built-in debug resource**: Once connected through Kiro, ask the agent to access `debug://mongodb` for connectivity diagnostics.

7. **Check loaded servers in Kiro CLI**: Use the `/mcp` command in an interactive session to see which servers are loaded and their status.

8. **Review Kiro CLI logs**: If the MCP server fails to start, Kiro CLI will output the error in the session. Look for startup failures or configuration parsing errors.

## Resources

- [Kiro Powers Documentation](https://kiro.dev/docs/powers/)
- [Create Your Own Power](https://kiro.dev/docs/powers/create/)
- [Kiro CLI MCP Configuration](https://kiro.dev/docs/cli/mcp/configuration/)
- [MongoDB MCP Server Documentation](https://www.mongodb.com/docs/mcp-server/get-started/)
- [MongoDB MCP Server GitHub](https://github.com/mongodb-js/mongodb-mcp-server)
- [MongoDB Atlas Documentation](https://www.mongodb.com/docs/atlas/)
