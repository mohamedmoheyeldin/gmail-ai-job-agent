# 🚀 Gmail AI Job Agent (FULL PRODUCTION SPEC)

---

## 🧠 Overview

This project builds a fully automated AI system that:

* Reads Gmail inbox
* Uses ChatGPT to **understand email content**
* Decides what to do:

  * Reply (remote job)
  * Reply (hybrid job)
  * Delete (job alerts)
  * Ignore (recruiter replies)
* Generates **custom AI replies per email**
* Attaches resume from Google Drive
* Runs automatically via GitHub Actions

---

## 🏗 Architecture

```
Gmail → Node.js → OpenAI (ChatGPT)
                   ↓
           classify + generate reply
                   ↓
          Gmail API → reply/delete
                   ↓
        Google Drive → attach resume
                   ↓
           GitHub Actions (scheduler)
```

---

## ⚙️ Tech Stack

* Node.js (ESM)
* Gmail API
* Google Drive API
* OpenAI API (ChatGPT)
* GitHub Actions
* dotenv

---

## 🔐 Google Cloud Setup

### Step 1: Create Project

Go to Google Cloud Console

---

### Step 2: Enable APIs

* Gmail API
* Google Drive API

---

### Step 3: Create OAuth Credentials

Type: **Desktop App**

Download:

```
credentials.json
```

---

### Step 4: Generate Refresh Token

Run script locally to get:

```
refresh_token
client_id
client_secret
```

👉 Required for production automation ([Google for Developers][1])

---

## 🔐 GitHub Secrets Setup

Go to:

Settings → Secrets → Actions

Add:

```
OPENAI_API_KEY
GOOGLE_CLIENT_ID
GOOGLE_CLIENT_SECRET
GOOGLE_REFRESH_TOKEN
```

👉 GitHub Actions uses secrets as env variables ([paulie.dev][2])

---

## 📁 Project Structure

```
src/
 ├── index.js
 ├── gmail.js
 ├── drive.js
 ├── openai.js
 ├── processor.js

utils/
 ├── logger.js

.github/workflows/
 └── automation.yml
```

---

## 🧠 AI Logic (CRITICAL)

### classifyEmail()

Input:

* subject + body

Output:

```json
{
  "action": "reply_remote | reply_hybrid | delete | ignore"
}
```

---

### generateReply()

⚡ IMPORTANT:

ChatGPT MUST generate dynamic reply based on email content.

---

### Prompt (VERY IMPORTANT)

```
You are Mohamed Moheyeldin, a Senior QA Automation Engineer.

Read the email and write a professional reply.

Rules:

IF remote job:
- Show strong interest
- Mention QA automation, Cypress, Playwright, CI/CD
- Keep concise

IF hybrid/on-site:
- Politely decline
- Say you are focusing on remote roles

Tone:
- Professional
- Confident
- Concise

DO NOT:
- Be generic
- Repeat same template
- Sound robotic
```

---

## 📧 Gmail Processing Rules

### Fetch Emails

```
is:unread -label:processed_by_ai
```

---

### Filtering Logic

#### 🚫 Delete

* "job alert"
* "recommended jobs"
* "today's jobs"
* LinkedIn alerts

---

#### 📩 Reply (Remote)

If AI detects remote job:

* Generate reply using ChatGPT
* Attach resume
* Send email

---

#### ⚠️ Reply (Hybrid)

* Generate polite decline
* Attach resume

---

#### 📌 Ignore

* Emails with "Re:"
* Ongoing conversations
* Recruiter replies

---

## 📎 Resume Handling

* Fetch from Google Drive
* File contains: "Resume"
* Convert to base64
* Attach to Gmail reply

---

## 🏷 Labels

Create manually in Gmail:

```
processed_by_ai
```

---

## 🔁 Post Processing

After each email:

* Mark as read
* Add label `processed_by_ai`

---

## ⚠️ Safety Rules (VERY IMPORTANT)

DO NOT reply if:

* sender contains "noreply"
* subject starts with "Re:"
* email looks automated

Limit:

```
max 10 emails per run
```

---

## ⚙️ GitHub Actions

File:

```
.github/workflows/automation.yml
```

---

### Workflow

```yaml
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

Send test emails:

1. "Remote QA job"
2. "Hybrid role"
3. "Job alert"
4. "Re: Follow up"

---

Validate:

* Correct classification
* Correct reply
* Resume attached
* Alerts deleted

---

## 🧠 Advanced Enhancements

* Store processed emails in JSON
* Track recruiter history
* Avoid duplicate replies
* Extract:

  * company
  * salary
  * job title

---

## 🎯 Goal

A fully autonomous AI agent that:

* Cleans inbox
* Applies intelligently
* Saves time
* Maximizes job opportunities

---

## 🔥 Instructions for Codex

When using this file:

1. Generate all files in modular structure
2. Use async/await
3. Add logging
4. Handle API failures
5. Ensure Gmail + OpenAI integration works
6. Use production-ready code

---

END OF SPEC

[1]: https://developers.google.com/identity/protocols/oauth2?utm_source=chatgpt.com "Using OAuth 2.0 to Access Google APIs | Authorization"
[2]: https://paulie.dev/posts/2025/02/how-to-send-email-using-github-actions/?utm_source=chatgpt.com "How to Send email using Gmail and GitHub Actions"
