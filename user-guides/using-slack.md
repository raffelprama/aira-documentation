# Interacting with AIRA in Slack

Slack is where you and your team will interact with AIRA the most. This guide explains what to expect from AIRA's messages and how you can respond to them.

### What to Expect from AIRA

When AIRA detects an issue, it sends a clear and concise message to your designated Slack channel. This message is designed to give you all the information you need at a glance.

A typical message from AIRA includes:

* **The Alert**: A summary of the problem detected, such as "KubePodImagePullBackOff". It also provides a root cause, for example, a failing pod due to an image pull error caused by a typo.
* **The Diagnosis**: A plain-language explanation of the root cause that AIRA found, with a recommendation to fix it.
* **The Action**: What AIRA has done to try and fix the problem, like using a `kubectl_apply` command to update the image reference.

### How to Respond to AIRA

Your feedback is crucial! It helps AIRA learn and get smarter over time. You can respond to AIRA's messages in two simple ways:

* **Using Buttons**: AIRA's messages will include buttons at the bottom. The **"Flag"** button allows you to provide feedback on the response, which is then inserted into the database. The **"Runbook"** button gives you quick access to a full report or a pre-generated runbook for the incident.
* **Replying in the Thread**: You can also reply to AIRA's message in the thread to view a detailed report. This report shows the exact steps AIRA took, including the inputs and raw results of the commands it executed.

### Why This is Important

By using Slack to interact with AIRA, your team can:

* **Get real-time updates** without switching applications.
* **Collaborate on incidents** directly in the Slack thread.
* **Train AIRA** and improve its future responses with just a few clicks or a simple reply.

---

<div style="display: flex; justify-content: space-between;">
  <a href="incident-management.md">⬅️ Previous</a>
  <a href="providing-feedback.md">Next ➡️</a>
</div>
