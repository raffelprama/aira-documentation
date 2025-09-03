# CLI Reference

This document provides a reference for the command-line tools used to run, debug, and test the AIRA application. The primary tool is the `langgraph` CLI, which is essential for development and visualization.

---

## 1. LangGraph CLI

The `langgraph` CLI is a powerful tool for local development and debugging of your LangGraph-based application. It provides a visual interface and a server to test your graph locally without a full-scale deployment. For more information, please refer to the [Official LangGraph CLI documentation](https://docs.langchain.com/langgraph-platform/cli).

### `langgraph dev`

This is the main command for local development. It starts a server that exposes your LangGraph workflow.

* **Command**: `langgraph dev`
* **Purpose**:
    * Starts a local server (typically on `http://localhost:2024`).
    * Compiles and serves your LangGraph workflow (`src/agent/graph.py`).
    * Provides a web-based UI (LangGraph Studio) at the server URL.
* **Usage**:
    ```bash
    # Run from the project's root directory
    langgraph dev
    ```
* **Key Features**:
    * **Live Reloading**: The server automatically reloads when you make changes to your Python code.
    * **Visualization**: The web UI shows a real-time, interactive diagram of your graph, allowing you to see the state transitions and node executions.
    * **Debug Console**: You can send inputs to your graph directly from the web UI and see the output of each node in the chain, which is invaluable for debugging.

### `langgraph run`

* **Command**: `langgraph run`
* **Purpose**: This command is used to run a graph from the command line, useful for quick tests or simple scripts.
* **Usage**:
    ```bash
    # Run the graph with a simple message input
    langgraph run -i "What is the status of pod 'my-app'?"
    ```

## 2. Testing CLI (`pytest`)

`pytest` is the standard testing framework used in this project. It is run from the command line to execute unit and integration tests.

### `pytest`

* **Command**: `pytest`
* **Purpose**: Discovers and runs all test files (`test_*.py` or `*_test.py`) within the `tests/` directory.
* **Usage**:
    ```bash
    # Run all tests with verbose output
    pytest tests/ -v

    # Run only integration tests
    pytest tests/integration_tests/ -v

    # Run only unit tests
    pytest tests/unit_tests/ -v
    ```
* **Options**:
    * `-v`: Enables verbose output, showing the name and status of each test.
    * `--asyncio-mode=auto`: Required for testing asynchronous functions.

## 3. Environment Variables

While not a CLI, setting environment variables is a crucial part of the command-line workflow. You can set them temporarily for a single command or use a `.env` file.

* **Example (Temporary)**:
    ```bash
    LLM_API_KEY="sk-12345" pytest tests/unit_tests/ -v
    ```
* **Example (using `dotenv`)**: The `python-dotenv` library allows you to load variables from a `.env` file, so you don't have to specify them each time.

---
<div style="display: flex; justify-content: space-between;";align="center">
  <a href="1_api.md">⬅️ Previous</a>
  <a href="3_schema.md">Next ➡️</a>
</div>