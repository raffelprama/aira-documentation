# Connecting Your Tools to AIRA

AIRA works best when it's seamlessly integrated with the tools your team already uses. This guide explains the purpose of each core integration.

### Slack Integration

The Slack integration is how AIRA becomes a member of your team.

* **Purpose**: This connection allows AIRA to send messages to a specific Slack channel when an incident occurs, providing real-time updates on its analysis and actions. It also enables your team to provide feedback directly within Slack, which helps AIRA learn and improve.
* **How It Works**: When you set up the Slack credential in n8n, you are giving AIRA permission to post messages and listen for button clicks in your workspace.

### OpenAI Integration

OpenAI is the "brain" of AIRA.

* **Purpose**: This connection provides AIRA with its advanced AI and natural language processing capabilities. It allows AIRA to read an alert, understand its context, and then generate a clear, human-readable summary of the problem and a recommendation for a solution.
* **How It Works**: By providing your OpenAI API key, you are giving the workflow access to powerful AI models that perform the core analysis tasks.

### Postgres Integration

The Postgres integration serves as AIRA's long-term memory.

* **Purpose**: This is where AIRA stores a history of all incidents, including the original alert, the AI's analysis, and any actions taken. This data is crucial for building a knowledge base and for future learning.
* **How It Works**: The n8n workflow connects to your Postgres database to automatically log every step of an incident's lifecycle, from creation to resolution.

---

With your tools now connected, you're ready to see how AIRA automates your incident management.

---
<div style="display: flex; justify-content: space-between;">
  <a href="setup.md">⬅️ Previous</a>
  <a href="../user-guides/incident-management.md">Next ➡️</a>
</div>