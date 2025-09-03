# Components

This document provides a detailed breakdown of the main components that make up **AIRA**.  
Each component is modular and can be extended or replaced without impacting the rest of the system.


---

## 1. Orchestration (LangGraph)

- **Role**: Coordinates the execution of all agents and integrations.
- **Responsibilities**:
  - Defines the workflow graph.
  - Manages execution order, retries, and branching.
  - Maintains context state across nodes.
- **Why LangGraph?**
  - Declarative workflow design.
  - Supports async and parallel execution.
  - Extensible for custom nodes.

---

## 2. Agents

Specialized AI agents that handle specific tasks within the incident lifecycle.

### A. RCA Agent
- **Purpose**: Analyze Kubernetes alerts and identify potential root causes.
- **Input (user prompt)**:
  ```text
  {{ $json.body.alerts[0].annotations.description }}

  {{ $json.body.alerts[0].annotations.summary }}
    ```

* **System prompt**:

  ```text
  You are an RCA Kubernetes assistant.
  If the namespace isn’t defined by user, find the resources using `kubectl_search` tools, 
  and then use the other Kubernetes tools to analyze the inquiry.
  Never ask back to the user on what to do. Do the action directly!
  ```
* **Integrations**: Uses **MCP: Kubernetes API**.
* **Output**: Root cause analysis in structured form.

---

### B. Execution Agent

* **Purpose**: Apply Kubernetes changes to resolve identified issues.
* **Input (user prompt)**:

  ```text
  Below is the RCA found by RCA Agent:
  {{ $('RCA Agent1').item.json.output }}

  Using your execution tools like kubectl_apply, solve this problem
  ```
* **Integrations**: Executes actions through **MCP: Kubernetes API**.
* **Output**: Confirmation of executed steps (e.g., deployment applied, pod restarted).

---

### C. LLM: Summarization & Recommendation

* **Purpose**: Provide concise RCA and recommended next steps.
* **System prompt**:

  ```text
  You are a summarization agent
  ```
* **User prompt**:

  ```text
  Below is the result from analysis agent:
  {{ $json.output }}

  Based on the RCA, please return:

  - a simplified RCA (short)
  - a recommendation

  Return in JSON format with keys: rca, recommendation

  Example output: {"rca": "<rca>", "recommendation": "<recommendation>"}
  ```
* **Output**: JSON containing `rca` and `recommendation`.

---

### D. Incident Summary Agent

* **Purpose**: Generate final resolution messages for Slack or tickets.
* **User prompt**:

  ````text
  You are a technical incident response assistant called Respona. 
  Generate a professional "Issue Resolved" message based on the provided analysis data.

  **Context**: An alert has been resolved and we have analysis data showing the root cause and resolution steps.

  **Input Data**: 
  json
  {{ JSON.stringify($json, null, 2) }}
  

  **Instructions**:

  1. Create a concise, professional "Issue Resolved" message
  2. Include the root cause from the analysis
  3. Mention what was done to resolve it (based on the recommendation)
  4. Keep it under 150 words
  5. Use a positive, reassuring tone
  6. Include the ticket ID for reference

  **Format**: Plain text message suitable for Slack or similar platforms.

  **Output**: Generate only the resolved issue message, no additional commentary.
  ````
* **Output**: Polished text summary, ready for Slack or ticket updates.

---

## 3. Integrations

AIRA connects with external systems for communication, storage, and context.

* **A. Slack Integration**

  * Two-way interaction (alerts in, recommendations out).
  * Supports feedback loops for model improvement.
* **B. Postgres Database**

  * Stores incident history, feedback, and runbook traces.
  * Provides persistence for reproducibility.
* **C. HTTP Requests / External APIs**

  * Supports generic outbound calls (e.g., ServiceNow, custom tools).
* **D. MCP: Kubernetes API**

  * Exposed to RCA and Execution agents only.
  * Provides safe, structured access to cluster operations.

---

## 4. Data & Persistence

* **Central State Store**: LangGraph maintains context across nodes.
* **Database (Postgres)**: Persists RCA outputs, recommendations, and execution traces.
* **Audit Logs**: Records actions for compliance and debugging.
* **Feedback Collection**: User feedback on recommendations is logged for iterative improvement.

---

## Summary

* **LangGraph** = backbone of orchestration.
* **Agents** = RCA, Execution, Summarization, and Incident Summary.
* **Integrations** = Slack, Postgres, HTTP APIs, plus Kubernetes MCP.
* **Persistence** = state + history + feedback for learning.

---
<div style="display: flex; justify-content: space-between;">
  <a href="2_workflow-graph.md">⬅️ Previous</a>
  <a href="4_data-flow.md">Next ➡️</a>
</div>