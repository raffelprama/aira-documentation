# Automated Incident Management with AIRA

AIRA’s core purpose is to turn a chaotic incident into a calm, automated process. This guide walks you through the typical lifecycle of an alert, from detection to resolution, and shows you how AIRA handles each step.

### Step 1: **Alert Detection**

The process begins the moment your monitoring tool (like a Kubernetes monitoring tool) detects a problem and sends an alert. AIRA is always listening for these signals.

### Step 2: **Instant AI Diagnostics**

As soon as an alert is received, AIRA springs into action. Using its integrated AI, it immediately performs a **full diagnostic**. It's like having an expert on standby who can analyze logs and **identify the root cause** in seconds, without a human ever having to look.

### Step 3: **Automated Action**

If AIRA's diagnosis points to a simple, known issue, it can **take direct action to fix it**. For example, if it finds that a service has become unresponsive, it can automatically restart it. This **solves the problem before your team even has a chance to react**, preventing it from escalating.

### Step 4: **Clear Communication**

Whether it takes action or not, AIRA sends a **clear, easy-to-read message** to your team's designated Slack channel. This message includes:

* A plain-language summary of the problem.
* What the root cause was.
* A report of the action it has taken.
* A recommendation for how to prevent it in the future.

### Step 5: **Incident Closed & Logged**

Once the issue is resolved, AIRA automatically **closes the incident and logs all the details** in your database. This creates a permanent, searchable record for every incident, building a valuable history of your system's health.

---

By automating these five steps, AIRA ensures that simple problems are handled instantly, and complex issues are immediately diagnosed and documented, saving your team countless hours and reducing stress.

---
<div style="display: flex; justify-content: space-between;">
  <a href="../getting-started/integrations.md">⬅️ Previous</a>
  <a href="using-slack.md">Next ➡️</a>
</div>