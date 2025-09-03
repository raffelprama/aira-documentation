# Setting Up Your AIRA Workflow

This guide will walk you through the simple, three-step process to get AIRA up and running in your n8n instance. No coding required!

### What You'll Need

* Access to an n8n instance (either self-hosted or cloud).
* The AIRA workflow file (`aira-workflow.json`) provided by your team.

### Step 1: Import the Workflow

1.  Log in to your n8n account.
2.  In the main dashboard, click on **"Workflows"** in the left-hand navigation panel.
3.  Click the **"New"** button in the top right corner.
4.  On the workflow canvas, click the three-dot menu at the top right and select **"Import from JSON"**.
5.  Upload the `aira-workflow.json` file you have.

The AIRA workflow should now appear on your canvas.

### Step 2: Configure Your Credentials

AIRA needs to be connected to your tools (like Slack or your monitoring system) to work. In n8n, these are called **Credentials**.

1.  In the left-hand navigation, click on **"Credentials"**.
2.  You will need to create and configure credentials for the following services:
    * **Slack**: To allow AIRA to send messages and receive feedback.
    * **OpenAI Account**: To provide AIRA with its AI and language capabilities.
    * **Postgres Account**: To allow AIRA to save incident tickets and feedback.
3.  Once the credentials are created, go back to your workflow canvas. Click on the individual nodes that use these services (e.g., the Slack, OpenAI, or Postgres nodes) and select the credentials you just created from the dropdown menu.

This step links the workflow to your specific accounts.

### Step 3: Activate the Workflow

Once you've imported the workflow and set up your credentials, you're ready to go live!

* In the top-right corner of the workflow canvas, you will see a toggle switch.
* Flip this toggle to **"Active"**.

Congratulations! AIRA is now listening for alerts and is ready to start automating your incident management.

---
<div style="display: flex; justify-content: space-between;">
  <a href="overview.md">⬅️ Previous</a>
  <a href="integrations.md">Next ➡️</a>
</div>