# RCA Kubernetes Agent

The `RCA Kubernetes` agent is a specialized AI agent designed to perform automated root cause analysis for common Kubernetes alerts. It is the primary node in the `Alert Firing Flow` and leverages the Model Context Protocol (MCP) to safely interact with a Kubernetes cluster.

## 1. Agent's Purpose

The main goal of this agent is to take a raw alert from a monitoring system (e.g., Alert Manager) and autonomously investigate the issue to identify the underlying cause. It is capable of:

* **Analyzing** pod, deployment, and service states.
* **Searching** for relevant Kubernetes resources.
* **Inspecting** logs, events, and configurations.
* **Generating** a structured root cause analysis output.

## 2. Prompts & Integrations

This section provides the prompts for both the **RCA Agent** and the **Execution Agent**, which work in sequence to analyze and resolve incidents.

### A. RCA Agent Prompts

This agent analyzes the initial alert to determine the root cause.

* **System Prompt**:
    ```text
    You are an RCA Kubernetes assistant.
    If the namespace isn’t defined by user, find the resources using `kubectl_search` tools, 
    and then use the other Kubernetes tools to analyze the inquiry.
    Never ask back to the user on what to do. Do the action directly!
    ```

* **Input (User Prompt)**:
    ```text
    {{ $json.body.alerts[0].annotations.description }}

    {{ $json.body.alerts[0].annotations.summary }}
    ```

* **Tools**: The RCA agent is bound to a toolset that includes:
    * `kubectl_search`: For finding resources (e.g., pods, deployments).
    * `kubectl_describe`: For getting detailed information about a resource.
    * `kubectl_logs`: For retrieving logs from a pod.

### B. Execution Agent Prompts

This agent takes the RCA output as input and applies a fix to solve the problem.

* **Input (User Prompt)**:
    ```text
    Below is the RCA found from RCA Agent:
    {{ $('RCA Agent1').item.json.output }}

    Using your execution tools like kubectl_apply, solve this problem.
    ```
* **Tools**: The Execution Agent is bound to a toolset that includes:
    * `kubectl_apply`: For applying changes to a Kubernetes cluster.
    * `kubectl_delete`: For deleting a resource.
    * `kubectl_restart`: For restarting a deployment or pod.

## 3. Workflow: `ImagePullBackOff`

This section walks through the step-by-step process the agents follow when they receive a common `ImagePullBackOff` alert.

### A. Root Cause Analysis (RCA)

1.  **Initial Analysis**: The **RCA Agent** receives the alert and uses its prompts to identify the problem from the input.
2.  **Tool Calls**: The agent uses its `kubectl` tools to investigate the issue (e.g., `kubectl_describe` on the affected pod).
3.  **Final RCA Generation**: The agent synthesizes all the gathered information to form the final root cause analysis.
* **Final Output (RCA Agent)**:
    ```text
    The pod 'my-app-pod-123' is in an ImagePullBackOff state. The root cause is a container image pull failure. This is likely due to an invalid image tag, an issue with the image registry, or incorrect image pull secrets.
    ```
This structured output is then passed to two parallel workflows: one for generating a user-facing summary and another for potential automated remediation.

### B. Problem Remediation (Execution Agent)

If the problem is solvable, the **Execution Agent** uses the RCA output to apply a fix.

1.  **Input**: The Execution Agent receives the RCA text as its input based on its prompt.
2.  **Tool Call**: It uses its `kubectl_apply` tool to restart the affected deployment, which should trigger a new image pull.
3.  **Final Output**: A message confirming the remediation action (e.g., "Deployment 'my-app-deployment' restarted successfully.").

This complete workflow shows how AIRA can not only identify issues but also take action to resolve them automatically.

---
<div style="display: flex; justify-content: space-between;";align="center">
  <a href="../reference/3_schema.md">⬅️ Previous</a>
  <a href="2_custom-agent.md">Next ➡️</a>
</div>