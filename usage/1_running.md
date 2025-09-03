# Running AIRA

This document provides instructions on how to run the AIRA application in different environments, from local development to production.

## 1. Local Development

Running AIRA locally is the best way to get started, test new features, or debug the workflow. The project uses the `langgraph` CLI for a seamless local development experience.

### Prerequisites

* Python 3.8+
* `pip`
* All dependencies listed in `requirements.txt`

### Step-by-Step Guide

1.  **Clone the Repository**
    Start by cloning the AIRA repository from your source control:
    ```bash
    git clone [https://github.com/your-org/aira.git](https://github.com/your-org/aira.git)
    cd aira
    ```

2.  **Install Dependencies**
    Install the required Python packages. For development, it's recommended to also install the `langgraph-cli`:
    ```bash
    pip install -r requirements.txt
    pip install "langgraph-cli[inmem]>=0.2.8"
    ```

3.  **Configure Environment**
    Create a `.env` file in the root directory of the project based on the example provided. This file will hold your configuration variables, such as API keys and MCP endpoints.
    ```env
    # LLM Configuration
    LLM_BASE_URL=[https://your-llm-endpoint.com/v1](https://your-llm-endpoint.com/v1)
    LLM_API_KEY=your-api-key
    LLM_MODEL=your-model-name
    LLM_TEMPERATURE=0.7

    # MCP Server Configuration
    MCP_SSE_URL=http://localhost:8024/mcp-server/sse
    MCP_SERVER_NAME=mcp_kubernetes

    # Database Configuration (for tickets and feedback)
    DB_HOST=localhost
    DB_PORT=5432
    DB_USER=your_user
    DB_PASS=your_password
    DB_NAME=aira_db

    # Slack Configuration
    SLACK_WEBHOOK_URL=[https://hooks.slack.com/services/](https://hooks.slack.com/services/)...
    ```

4.  **Run the Agent**
    The `langgraph dev` command starts the local LangGraph server and provides a web UI for visualizing and interacting with your graph.
    ```bash
    langgraph dev
    ```
    This command will start a server, typically at `http://localhost:2024`, and provide a URL to a web interface (LangGraph Studio) for debugging.

5.  **Running Tests**
    To ensure everything is working correctly, you can run the test suite:
    ```bash
    pytest tests/ -v
    ```

## 2. Production Deployment

For production, it is recommended to containerize the application and deploy it to a reliable hosting environment. This ensures consistency and scalability.

### Using Docker

The project's dependencies and structure are well-suited for a Dockerized environment. A typical `Dockerfile` for AIRA would look like this:

```dockerfile
# Use an official Python image
FROM python:3.10-slim

# Set the working directory
WORKDIR /app

# Copy the requirements file and install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy the rest of the application code
COPY . .

# Expose the port the LangGraph server will run on
EXPOSE 8000

# Command to run the application (e.g., using a production-ready server)
CMD ["gunicorn", "--bind", "0.0.0.0:8000", "src.main:app"]
```
---
<div style="display: flex; justify-content: space-between;";align="center">
  <a href="../architecture/4_data-flow.md">⬅️ Previous</a>
  <a href="2_configuration.md">Next ➡️</a>
</div>