# Schema Reference

This document provides a technical reference for the key data schemas and JSON formats used by AIRA. Understanding these structures is crucial for debugging the workflow, building custom integrations, or creating new agents.

---

## 1. Agent Output Schemas

### A. Summarization & Recommendation Agent

The `Summarization & Recommendation` agent is designed to return a standardized JSON object that contains a simplified Root Cause Analysis (RCA) and a recommendation.

* **Purpose**: To provide a concise, machine-readable summary of the RCA.
* **Format**: JSON
* **Schema**:
    ```json
    {
      "rca": "<a short, simplified root cause description>",
      "recommendation": "<a clear, actionable recommendation>"
    }
    ```
* **Example Output**:
    ```json
    {
      "rca": "ImagePullBackOff due to a missing image tag or repository authentication failure.",
      "recommendation": "Check the image tag and repository credentials for the 'my-app' image. Consider restarting the deployment to trigger a new pull."
    }
    ```

---

## 2. Feedback Mapping Schema

The feedback ingestion flow normalizes user feedback from Slack into a standardized format before storing it in the database.

* **Purpose**: To convert human-readable feedback (e.g., a button label) into a consistent machine-readable reason.
* **Format**: JSON
* **Mapping Logic**:
    ```javascript
    const mapping = {
      "👍 Helpful": "helpful",
      "👎 Not Helpful": "not_helpful",
      "⚠️ Incorrect": "incorrect",
      "❌ Inappropriate": "inappropriate",
      "🚫 Misleading": "misleading"
    };
    ```
* **Output Schema**:
    ```json
    {
      "flag_id": "a-v4-uuid-for-the-flag",
      "original": "👍 Helpful",
      "mappedReason": "helpful",
      "comment": "<optional user comment>"
    }
    ```

---

## 3. Database Operations: Data Mapping

This section provides a detailed breakdown of how data is mapped to the Postgres database for each key operation.

### A. Insert New Ticket

This operation is triggered by a new `firing` alert and inserts a new ticket record into the `tickets` table.

| Field Name     | Source Data                                                |
|----------------|------------------------------------------------------------|
| `id`           | Alert fingerprint (`alerts[0].fingerprint`)                |
| `subject`      | Alert description (`alerts[0].annotations.description`)    |
| `description`  | Alert summary (`commonAnnotations.summary`)                |
| `priority`     | Alert severity (`alerts[0].labels.severity`)               |
| `createdAt`    | Alert start time converted to ISO string (`startsAt`)      |
| `updatedAt`    | Alert start time converted to ISO string (`startsAt`)      |
| `relatedTickets`| Fixed value `[]` (empty array)                            |

### B. Insert AI Analysis

After an RCA is performed, the analysis results are stored in the `ai_analysis` table.

| Field Name         | Source Data                                               |
|--------------------|-----------------------------------------------------------|
| `id`               | A newly generated UUID (`ai_analysis_id`)                 |
| `ticketId`         | Alert fingerprint (`alerts[0].fingerprint`)               |
| `description`      | RCA content from the analysis agent (`message.content.rca`) |
| `recommendation`   | Recommendation from the agent (`message.content.recommendation`) |
| `createdAt`        | Current timestamp converted to ISO string (`$now`)          |
| `updatedAt`        | Current timestamp converted to ISO string (`$now`)          |
| `severity`         | Fixed value `medium`                                      |

### C. Update Resolving Steps

This operation updates an existing ticket with the steps taken to resolve the incident.

| Field Name       | Source Data                                                 |
|------------------|-------------------------------------------------------------|
| **(Match ID)** | Alert fingerprint (`alerts[0].fingerprint`)                 |
| `resolvingSteps` | The execution steps from the agent's output (`execution_steps`) |

### D. Close Ticket

This operation marks a ticket as resolved and closed.

| Field Name       | Source Data                                                |
|------------------|------------------------------------------------------------|
| **(Match ID)** | Alert fingerprint (`alerts[0].fingerprint`)                |
| `resolvedAt`     | Alert end time converted to ISO string (`endsAt`)          |
| `closedAt`       | Alert end time converted to ISO string (`endsAt`)          |
| `updatedAt`      | Current timestamp converted to ISO string (`$now`)         |

### E. Insert Feedback Flag

This operation stores user feedback in the `feedback_flags` table.

| Field Name         | Source Data                                               |
|--------------------|-----------------------------------------------------------|
| `flag_id`          | A newly generated UUID (`flag_id`)                        |
| `aiAnalysisId`     | The ID of the AI analysis being flagged (`data.data.alertId`) |
| `flagType`         | The normalized feedback reason (`mappedReason`)           |
| `comment`          | Optional user comment (`comment`)                         |
| `createdAt`        | Current timestamp converted to ISO string (`$now`)        |
| `updatedAt`        | Current timestamp converted to ISO string (`$now`)        |

---
<div style="display: flex; justify-content: space-between;">
  <a href="2_cli.md">⬅️ Previous</a>
  <a href="../agent/1_kubernetes.md">Next ➡️</a>
</div>