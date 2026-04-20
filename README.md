# Gmail AI Job Agent — Full System Specification

## 🧠 Overview

Build a production-ready Node.js automation system that:

* Reads unread Gmail emails
* Uses OpenAI (ChatGPT) to classify job-related emails
* Takes actions:

  * Reply to remote jobs with resume
  * Reply to hybrid/on-site jobs with polite decline
  * Delete job alert emails
  * Ignore recruiter replies
* Attaches resume from Google Drive
* Runs automatically via GitHub Actions every 5 minutes
* Avoids duplicate processing via Gmail labels

---

## ⚙️ Tech Stack

* Node.js (ESM)
* Gmail API (googleapis)
* Google Drive API
* OpenAI API
* GitHub Actions
* dotenv

---

## 🔐 Environment Variables

Create `.env`:

```
OPENAI_API_KEY=
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
GOOGLE_REFRESH_TOKEN=
```

---

## 📁 Folder Responsibilities

### src/index.js

* Entry point
* Calls processor

### src/gmail.js

* Fetch unread emails
* Send replies
* Apply labels
* Delete emails

### src/drive.js

* Fetch resume from Google Drive

### src/openai.js

* classifyEmail()
* generateReply()

### src/processor.js

* Orchestrates full logic
* Decision engine

---

## 🧠 AI Logic

### classifyEmail()

Input:

* email subject + body

Output JSON:

```
{
  "action": "reply_remote | reply_hybrid | delete | ignore"
}
```

Rules:

* Remote → reply_remote
* Hybrid/on-site → reply_hybrid
* Job alerts → delete
* Replies → ignore

---

### generateReply()

Rules:

* Remote:

  * Express strong interest
  * Mention QA automation experience
* Hybrid:

  * Politely decline
  * Prefer remote roles
* Keep concise

---

## 📧 Gmail Processing Rules

### Fetch emails:

* is:unread
* exclude label: processed_by_ai

---

### Actions:

#### reply_remote

* Generate AI reply
* Attach resume
* Send email

#### reply_hybrid

* Generate AI reply
* Attach resume
* Send email

#### delete

* Move to trash

#### ignore

* Do nothing

---

### After processing:

* Add label: `processed_by_ai`
* Mark email as read

---

## 📎 Resume Handling

* Fetch from Google Drive
* File name contains: "Resume"
* Attach as base64

---

## 🏷 Labels

Create Gmail label manually:

```
processed_by_ai
```

---

## ⚠️ Safety Rules

* Do NOT reply to:

  * noreply emails
  * linkedin alerts
  * automated senders
* Skip emails with "Re:" in subject
* Limit to 10 emails per run

---

## ⚙️ GitHub Actions

Create:

`.github/workflows/automation.yml`

```
name: Gmail AI Job Agent

on:
  schedule:
    - cron: "*/5 * * * *"
  workflow_dispatch:

jobs:
  run:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 20

      - run: npm install

      - run: node src/index.js
        env:
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
          GOOGLE_CLIENT_ID: ${{ secrets.GOOGLE_CLIENT_ID }}
          GOOGLE_CLIENT_SECRET: ${{ secrets.GOOGLE_CLIENT_SECRET }}
          GOOGLE_REFRESH_TOKEN: ${{ secrets.GOOGLE_REFRESH_TOKEN }}
```

---

## 🧪 Testing Plan

### Test Cases:

1. Remote job email
2. Hybrid job email
3. Job alert email
4. Recruiter reply

Validate:

* Correct classification
* Correct action
* Resume attached
* Label applied

---

## 🔄 Future Enhancements

* Store processed emails in JSON/DB
* Track recruiter conversations
* Extract company + role
* Multi-resume support
* Slack notifications
* UI dashboard

---

## 🎯 Goal

A fully autonomous AI agent that:

* Filters inbox
* Applies intelligently
* Saves time
* Increases job response efficiency

---

## 🔥 Instructions for Codex

* Generate modular, clean code
* Use async/await
* Handle errors gracefully
* Add logging
* Keep code production-ready
* Use ES modules (import/export)

---

END OF SPEC
