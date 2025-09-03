# Workflow Graph

This page documents the end-to-end flows that power AIRA.  
There are three primary workflows:

1. **Alert firing flow** — analyze the alert, optionally execute remediation, summarize, and notify.  
2. **Alert resolved flow** — close ticket, generate a concise resolution message, and publish any resolving steps.  
3. **Feedback ingestion flow** — capture user feedback and promote helpful content into the runbook.

---
## 1. Alert Flow
![Title](../src/main.png)


## A. Alert Firing Flow

```mermaid
flowchart LR
    Start([Start]) --> Firing{"Alert firing?"}

    %% Ticket check
    Firing -- Yes --> CheckTicket["Check if ticket exists"]
    CheckTicket -- No --> InsertTicket["Insert ticket"]
    CheckTicket -- Yes --> NoOp1(("Do nothing"))

    %% RCA branch
    Firing -- Yes --> RCA["RCA Agent"]
    RCA --> LLMRec["LLM flow: summarization and recommendation"]
    LLMRec --> GenId["Generate analysis_id"]
    GenId --> InsertDB1["Insert analysis into Postgres"]
    InsertDB1 --> FormatSlack1["Format message for Slack"]
    FormatSlack1 --> ReplySlack1["Reply in Slack"]
    ReplySlack1 --> FormatUpload1["Format attachment"]
    FormatUpload1 --> UploadSlack1["Upload file to Slack"]

    %% Solvable decision and execution
    RCA --> Solvable{"Problem solvable?"}
    Solvable -- No --> NoOp2(("Do nothing"))
    Solvable -- Yes --> Exec["Execution Agent via MCP K8s"]
    Exec --> FormatExec["Format execution result"]
    FormatExec --> InsertDB2["Insert execution result into Postgres"]

    %% If not firing
    Firing -- No --> SkipFiring(("Do nothing"))
```

**Notes**
- **RCA Agent** and **Execution Agent** issue Kubernetes operations **via MCP**.
- Summarization + recommendation run through a **single LLM flow**, then branch to outputs.
- Slack messages and file uploads are posted via HTTP integrations; database writes use Postgres.

---

## B. Alert Resolved Flow

```mermaid
flowchart LR
    RStart([Start]) --> Resolved{"Alert resolved?"}
    Resolved -- Yes --> CloseTicket["Close ticket in Postgres"]
    CloseTicket --> GetTicket["Get ticket data"]
    GetTicket --> IncSum["LLM incident summary"]
    IncSum --> FormatResolved["Format resolved message"]
    FormatResolved --> ReplySlackResolved["Reply in Slack"]

    ReplySlackResolved --> GetSteps["Query resolving steps from Postgres"]
    GetSteps --> StepsExist{"Resolving steps exist?"}
    StepsExist -- Yes --> FormatSteps["Format steps as attachment"]
    FormatSteps --> UploadSteps["Upload file to Slack"]
    StepsExist -- No --> NoOpR(("Do nothing"))

    Resolved -- No --> NoOpRN(("Do nothing"))
```

**Notes**
- The **incident summary** is concise and includes the RCA, the fix, and the ticket id.
- If recorded **resolving steps** exist, they are uploaded as a Slack file.

---

## 2. Feedback Ingestion Flow
![Title](../src/second.png)

```mermaid
flowchart LR
    FBStart([Start]) --> MapFB["Map feedback payload"]
    MapFB --> InsertFlag["Insert feedback flag into Postgres"]
    InsertFlag --> Helpful{"Is feedback marked helpful?"}
    Helpful -- Yes --> Runbook["Add content to runbook"]
    Helpful -- No --> NoOpFB(("Do nothing"))
```

**Notes**
- Incoming reactions are normalized (e.g., 👍 → `helpful`, 👎 → `not_helpful`).
- All feedback is stored; only `helpful` items are promoted to the runbook.


---
<div style="display: flex; justify-content: space-between;";align="center">
  <a href="1_overview.md">⬅️ Previous</a>
  <a href="3_components.md">Next ➡️</a>
</div>