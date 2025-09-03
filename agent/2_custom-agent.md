# Guide for Adding Your Own Agent

AIRA is designed to be easily extensible. This guide provides a step-by-step process for adding your own custom agent to the workflow, allowing you to handle new types of alerts or integrate with different systems.

## 1. Core Concepts

Before you begin, ensure you are familiar with the following concepts:

* **Agents**: An agent combines a Language Model (LLM), a set of tools, and a defined prompt to perform a specific task.
* **Tools**: Functions that the agent can call to interact with external systems (e.g., APIs, databases, CLIs).
* **Model Context Protocol (MCP)**: The framework used to expose tools to the agents safely and securely.
* **LangGraph**: The state machine that orchestrates the flow between different agents and nodes.

## 2. Step 1: Create the Agent Module

Create a new Python file for your agent within the `src/nodes/agents/` directory. For this example, we will create a `database_agent.py` to handle alerts related to database performance.

```python
# src/nodes/agents/database_agent.py
from src.nodes.agents import agent_node
from src.mcp.servers import sync_get_tools
from src.config import load_config
from functools import partial
from langgraph.prebuilt import ToolNode
from langchain_openai import ChatOpenAI
from langchain_core.messages import BaseMessage, HumanMessage

# Define the configuration for your new agent.
# This can use the global config or specify a custom MCP server.
config = load_config(
    mcp_server_name="mcp_database",
    # If your tools are exposed via a separate MCP server, configure its URL here.
    # mcp_sse_url="http://localhost:8026/mcp-database/sse"
)

# Initialize the LLM for your agent.
llm = ChatOpenAI(
    base_url=config.llm_base_url,
    api_key=config.llm_api_key,
    model=config.llm_model,
    temperature=config.llm_temperature
)

# Get the tools that this agent is allowed to use.
# These tools should be exposed by the MCP server defined above.
tools = sync_get_tools(config)

# Bind the tools to the LLM.
llm_with_tools = llm.bind_tools(tools)

# Create a ToolNode for tool execution within the graph.
database_tool_node = ToolNode(tools)

# Define the agent function.
database_agent = partial(agent_node, llm_with_tools=llm_with_tools)

# Define the conditional logic for the agent's state.
def should_continue_database(state: list[BaseMessage]):
    """Determines whether the agent should call a tool or finish."""
    last_message = state[-1]
    # If the last message is a function call, route to tools
    if "tool_calls" in last_message.additional_kwargs:
        return "tools"
    # Otherwise, route to the END node
    return "end"
```
---
<div style="display: flex; justify-content: space-between;";align="center">
  <a href="1_kubernetes.md">⬅️ Previous</a>
  <a href="../index.md">Return ➡️</a>
</div>
