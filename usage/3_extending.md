# Extending AIRA

AIRA is built with a modular architecture that makes it easy to extend and customize. This guide will walk you through the process of adding a new specialized agent and modifying the core workflow graph to incorporate it.

## 1. Create a New Agent

The first step is to create a new Python module for your agent. This agent will contain its own LLM, tools, and logic, separate from other agents.

1.  **Create a new file** in the `src/nodes/agents/` directory. For this example, let's call it `my_agent.py`.
2.  **Define your agent's functionality.** Your agent should import the necessary components from the project and define its own LLM and tool set. It can optionally use a custom MCP server.

    ```python
    # src/nodes/agents/my_agent.py
    from src.nodes.agents import agent_node
    from src.mcp.servers import sync_get_tools
    from src.config import load_config
    from functools import partial
    from langgraph.prebuilt import ToolNode
    from langchain_openai import ChatOpenAI
    from langchain_core.messages import BaseMessage, HumanMessage

    # Configure for your specific LLM and optional MCP server
    config = load_config(
        mcp_sse_url="http://localhost:8025/my-mcp-server/sse",
        mcp_server_name="my_mcp_server"
    )

    # Initialize the LLM
    llm = ChatOpenAI(
        base_url=config.llm_base_url,
        api_key=config.llm_api_key,
        model=config.llm_model,
        temperature=config.llm_temperature
    )

    # Get tools specific to your agent via MCP
    tools = sync_get_tools(config)

    # Bind tools to the LLM
    llm_with_tools = llm.bind_tools(tools)

    # Create a ToolNode for tool execution
    my_tool_node = ToolNode(tools)

    # Create the agent function
    my_agent = partial(agent_node, llm_with_tools=llm_with_tools)

    # Define the conditional logic for the agent
    def should_continue(state: list[BaseMessage]):
        """Determines whether to call a tool or finish."""
        last_message = state[-1]
        # If the last message is a function call, route to tools
        if "tool_calls" in last_message.additional_kwargs:
            return "tools"
        # Otherwise, route to the END node
        return "end"
    ```
---
<div style="display: flex; justify-content: space-between;">
  <a href="2_configuration.md">⬅️ Previous</a>
  <a href="../reference/1_api.md">Next ➡️</a>
</div>