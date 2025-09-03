# Configuration

AIRA is configured entirely through environment variables, which simplifies deployment and secrets management. This page provides a complete reference for all available configuration options.

You can set these variables in a `.env` file for local development or as environment variables in your production environment (e.g., in a Kubernetes `Secret` or Docker `--env-file`).

## 1. LLM Settings

These variables are required to connect to the Language Model (LLM) that powers the agents.

| Environment Variable  | Description                                            | Required | Default   |
|-----------------------|--------------------------------------------------------|----------|-----------|
| `LLM_BASE_URL`        | Base URL for your LLM API endpoint (e.g., OpenAI, Vercel). | **Yes** | None      |
| `LLM_API_KEY`         | API key for authentication with the LLM provider.      | **Yes** | None      |
| `LLM_MODEL`           | The specific model name to use (e.g., `gpt-4`, `claude-3-opus`). | **Yes** | None      |
| `LLM_TEMPERATURE`     | The randomness of the LLM's output. A value between 0.0 and 1.0. | No       | `0.7`     |
| `LLM_TIMEOUT`         | The timeout for LLM API calls, in seconds.             | No       | `60`      |

## 2. MCP Server Configuration

The Model Context Protocol (MCP) server is essential for the Kubernetes-specific agents. The RCA and Execution agents rely on these settings.

| Environment Variable  | Description                                            | Required | Default                               |
|-----------------------|--------------------------------------------------------|----------|---------------------------------------|
| `MCP_SSE_URL`         | The Server-Sent Events (SSE) endpoint for the MCP server. | **Yes** | `http://localhost:8024/mcp-server/sse` |
| `MCP_SERVER_NAME`     | The name identifier for the specific MCP server being used. | **Yes** | `mcp_kubernetes`                      |

## 3. Database Configuration

These variables are used to connect to the PostgreSQL database for ticket management, feedback storage, and persistence of analysis results.

| Environment Variable  | Description                                            | Required | Default |
|-----------------------|--------------------------------------------------------|----------|---------|
| `DB_HOST`             | The hostname of the database server.                   | **Yes** | `localhost` |
| `DB_PORT`             | The port of the database server.                       | No       | `5432`  |
| `DB_USER`             | The username for database access.                      | **Yes** | None    |
| `DB_PASS`             | The password for the database user.                    | **Yes** | None    |
| `DB_NAME`             | The name of the database to connect to.                | **Yes** | `aira_db` |
| `DB_SSL_MODE`         | SSL mode for the database connection (e.g., `require`, `prefer`). | No       | `prefer`|

## 4. Slack Configuration

These variables are needed to enable communication with Slack for alerts, reporting, and feedback loops.

| Environment Variable  | Description                                            | Required | Default |
|-----------------------|--------------------------------------------------------|----------|---------|
| `SLACK_WEBHOOK_URL`   | The Incoming Webhook URL for posting messages to a Slack channel. | **Yes** | None    |
| `SLACK_TOKEN`         | The Slack Bot Token for more advanced API calls (e.g., file uploads, replying in threads). | **Yes** | None    |
| `SLACK_CHANNEL`       | The default channel ID or name for sending messages.  | No       | None    |

## 5. Agent-Specific Configuration

While most agents use the default `MCP_` settings, you can override them on a per-agent basis. This is handled directly in the agent's code, as shown in the `src/nodes/agents/` directory.

For example, to configure a custom agent to use a different MCP server:
```python
# src/nodes/agents/my_agent.py
config = load_config(
    mcp_sse_url="http://localhost:8025/my-custom-mcp/sse",
    mcp_server_name="my_custom_mcp"
)
```
---
<div style="display: flex; justify-content: space-between;">
  <a href="1_running.md">⬅️ Previous</a>
  <a href="3_extending.md">Next ➡️</a>
</div>