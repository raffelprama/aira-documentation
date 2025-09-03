# Data Flow

This document details the end-to-end data pipeline within AIRA, from the initial alert ingestion to the final resolution and feedback loops. Understanding this flow is crucial for monitoring, troubleshooting, and extending the system.

The core data flow can be visualized as a cycle:

**Alerts ➡️ Ingestion & Analysis ➡️ Resolution & Reporting ➡️ Feedback**

---

## 1. Alert Ingestion & Ticket Creation

The process begins with an incoming webhook from an **Alert Manager**. This webhook payload is the initial data input.

* **Source**: Alert Manager (via webhook).
* **Payload**: A JSON object containing alert details such as `labels`, `annotations` (e.g., `summary`, `description`), and `status` (`firing` or `resolved`).
* **Process**:
    1.  The `Alert Manager Webhook` node receives the payload.
    2.  The system checks the alert's `status` to determine the workflow (`firing` or `resolved`).
        ```sql
        SELECT * FROM ai_analysis where "ticketId"='{{ $json.body.alerts[0].fingerprint }}'
        ```
    3.  If `firing`, a check is performed against the **Postgres Database** to see if a ticket already exists for the alert.
    4.  If no ticket exists, a new record is created in the database, containing the alert details.
* **Output**: A new ticket record in the database and the alert payload passed to the next stage of the workflow.

---

## 2. Analysis & Recommendation

This stage focuses on processing the alert data to generate insights and actions. The data flow moves through specialized AI agents.

* **Input**: The Alert Manager payload and newly created ticket data.
* **Process**:
    1.  The `RCA Agent` ingests the alert data. It uses the `kubectl_search` and `kubectl_analyze` tools (via **MCP**) to gather additional context from the Kubernetes cluster.
    2.  The `RCA Agent`'s output (raw root cause analysis) is passed to the `Summarization & Recommendation Agent`.
    3.  This agent uses a prompt to transform the raw analysis into a simplified, human-readable summary and a structured JSON object with an `rca` and `recommendation` key.
    4.  The JSON output is then inserted as a new analysis record in the **Postgres Database**, linked to the original ticket ID.
* **Output**: A structured `rca` and `recommendation` in the database, ready to be used for reporting.

---

## 3. Resolution & Reporting

The system takes the analysis and either executes an action or reports the findings.

* **Input**: The `rca` and `recommendation` from the previous stage.
* **Process**:
    1.  Based on the problem type (e.g., `KubePodImagePullBackOff`), the `Execution Agent` may receive the `rca` data.
    2.  The `Execution Agent` uses a tool like `kubectl_apply` (via **MCP**) to apply the fix. The result of this action is logged back into the **Postgres Database**.
    3.  The `Summarization & Recommendation Agent`'s output is also formatted into a readable message.
    4.  This formatted message is sent to the designated **Slack channel** using an HTTP POST request.
    5.  Additionally, a formatted attachment containing detailed information is uploaded as a file to the Slack channel.
* **Output**:
    * An executed remediation step (if applicable).
    * A message and a file uploaded to Slack.
    * Updated records in the **Postgres Database**.

---

## 4. Resolved Alert & Lifecycle Management

When an alert's status changes to `resolved`, a separate flow is triggered to close the incident and report on the resolution.

* **Input**: An Alert Manager webhook with `status: resolved`.
* **Process**:
    1.  The ticket status in the **Postgres Database** is updated to `closed`.
    2.  The system queries the **Postgres Database** to get the associated ticket and its RCA data.
        ```sql
        SELECT * FROM ai_analysis where "ticketId"='{{ $json.id }}' order by "updatedAt" desc limit 1
        ```
    3.  The `Incident Summary Agent` uses the raw RCA and other context to generate a professional "Issue Resolved" message.
    4.  This message is sent as a reply to the **Slack thread** for the original alert.
    5.  A query is executed to retrieve the resolving steps from the database:
        ```sql
        SELECT "resolvingSteps" FROM tickets where id='{{ $('Alert Manager Webhook').item.json.body.alerts[0].fingerprint }}'
        ```
    6.  If the query returns data, the content is formatted and uploaded to the **Slack thread**. If no resolving steps exist, this step is skipped.
* **Output**:
    * A concise resolution message in Slack.
    * An optional file upload containing the resolving steps.
    * A closed ticket in the database.

**Note on Slack Replies vs. Uploads:**
* The `HTTP POST` request for the summary message replies to the original thread, ensuring the conversation is kept together.
* The subsequent file upload for the resolving steps is a separate action but is also directed to the same thread, making all relevant information easily accessible in one place.

---

## 5. Feedback Loop & Runbooks

The final stage closes the loop by capturing user feedback on the agent's performance.

* **Input**: A webhook from Slack containing a user's reaction (e.g., a "👍" emoji).
* **Process**:
    1.  The `Map feedback payload` node normalizes the feedback into a standardized format (`helpful`, `not_helpful`, etc.).
    2.  A new record, or "flag," is inserted into the **Postgres Database** to store the feedback.
    3.  If the feedback is `helpful`, the relevant content (e.g., the RCA and fix) is promoted and added to a **runbook** database or system, serving as a future reference for similar incidents.
* **Output**: A feedback record in the database and an updated runbook for future reference.

---
<div style="display: flex; justify-content: space-between;">
  <a href="3_components.md">⬅️ Previous</a>
  <a href="../usage/1_running.md">Next ➡️</a>
</div>