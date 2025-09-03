# API Reference

This document provides a technical reference for the HTTP endpoints and payloads that AIRA uses to integrate with external systems like Alert Manager and Slack.

---

## 1. Alert Manager Webhook

This is the primary incoming webhook endpoint that triggers the incident response workflow. It is designed to receive payloads from an Alert Manager instance.

* **Endpoint**: `/webhooks/alertmanager`
* **Method**: `POST`
* **Triggered by**: Alert Manager when an alert is in a `firing` or `resolved` state.
* **Payload Example**:
    ```json
    {
      "version": "4",
      "groupKey": "{...}",
      "truncatedAlerts": 0,
      "status": "firing",
      "receiver": "aira-receiver",
      "groupLabels": { "alertname": "KubePodImagePullBackOff" },
      "commonLabels": {
        "alertname": "KubePodImagePullBackOff",
        "severity": "critical",
        "namespace": "my-application"
      },
      "commonAnnotations": {
        "summary": "Pod failed to pull image",
        "description": "The pod 'my-app-pod-123' in namespace 'my-application' is in an 'ImagePullBackOff' state."
      },
      "externalURL": "http://localhost:9093",
      "alerts": [
        {
          "status": "firing",
          "labels": { "alertname": "KubePodImagePullBackOff" },
          "annotations": {
            "summary": "Pod failed to pull image",
            "description": "The pod 'my-app-pod-123' in namespace 'my-application' is in an 'ImagePullBackOff' state."
          },
          "fingerprint": "9c1b7e41-b84e-4f3d-82c8-7c8a3e790a6f"
        }
      ]
    }
    ```
* **Required Fields**: The system relies on the `status` and `fingerprint` fields to manage the incident lifecycle. The `annotations` are used by the RCA and Incident Summary agents.

---

## 2. Slack Feedback Webhook

This endpoint is used to receive user feedback, typically from interactive elements like buttons or emojis in a Slack message.

* **Endpoint**: `/webhooks/slack/feedback`
* **Method**: `POST`
* **Triggered by**: A user interacting with an AI-generated message in Slack. The application must be configured with Slack's Interactivity & Shortcuts to send this payload.
* **Payload Example**:
    ```json
    {
      "token": "...",
      "team_id": "...",
      "type": "block_actions",
      "user": { "id": "...", "username": "...", "name": "...", "team_id": "..." },
      "api_app_id": "...",
      "container": { "type": "message" },
      "trigger_id": "...",
      "response_url": "...",
      "actions": [
        {
          "action_id": "my_action_id",
          "block_id": "k7fatqdsdkdfmxirm0774oad",
          "text": { "type": "plain_text", "text": "👍 Helpful" },
          "type": "button",
          "value": "helpful",
          "action_ts": "1234567890.123456"
        }
      ]
    }
    ```
* **Required Fields**: The `block_id` or `value` within the `actions` array is used to identify the specific feedback and its type (`Helpful`, `Not Helpful`, etc.). The `Map feedback payload` node normalizes this input.

---

## 3. Outgoing Slack API Calls

The AIRA application communicates with Slack via HTTP POST requests to the Slack Web API.

* **Endpoint**: `https://slack.com/api/chat.postMessage`
* **Method**: `POST`
* **Purpose**: To post initial incident reports, RCA summaries, and resolution messages to a designated Slack channel.
* **Payload (Simplified Example)**:
    ```json
    {
      "channel": "C12345678",
      "text": "Incident #INC-001 has been reported: Pod is in ImagePullBackOff state. RCA has been initiated."
    }
    ```
* **Headers**: Requires an `Authorization` header with a Bearer Token (`xoxb-your-bot-token`).
* **Key Behavior**:
    * **Reply to Thread**: When a message is a reply, the `thread_ts` field is included in the payload to ensure it is correctly placed within the conversation thread.
    * **File Upload**: The `files.upload` API endpoint is used to upload files (e.g., for detailed RCA reports or resolving steps) to a specific channel or thread.

---
<div style="display: flex; justify-content: space-between;";align="center">
  <a href="../usage/3_extending.md">⬅️ Previous</a>
  <a href="2_cli.md">Next ➡️</a>
</div>