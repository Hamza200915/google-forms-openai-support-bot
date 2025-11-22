# Google Forms AI Support Bot

A simple no-code customer support bot built with **Google Forms**, **Google Sheets**, **Make.com**, **OpenAI**, and **Gmail**.

When a customer submits a question through a Google Form, this automation:

1. Saves the question to a Google Sheet.
2. Sends the question to OpenAI (ChatGPT) with some support rules.
3. Writes the AI-generated reply back into the sheet.
4. Emails the reply to the customer automatically.

---

## Features

- Collects customer questions via Google Forms.
- Stores everything in a Google Sheet (`Form_Responses` tab).
- Uses OpenAI to generate a short, friendly support reply.
- Automatically sends the reply to the customer’s email via Gmail.
- Completely no-code (configured in Make.com).

---

## Tech Stack

- **Google Forms** – Input from users.
- **Google Sheets** – Data storage.
- **Make.com** – Automation scenario.
- **OpenAI** – Generates the support reply.
- **Gmail** – Sends the reply email.

---

## Data Structure

Google Sheet: `Inbox`

Tab: **Form_Responses 1** (created automatically by the Form, headers in row 1)

Columns:

| Column | Name / Example header          | Description                            |
| ------ | ------------------------------ | -------------------------------------- |
| A      | `Timestamp`                    | Auto-filled by Google Forms            |
| B      | `What is your question?`       | Customer’s question text               |
| C      | `Your email address`           | Customer’s email                       |
| D      | `AI_Reply`                     | AI-generated answer (filled by Make)   |

> Note: Column B/C header names can be changed, but the Make scenario must be updated to match.

---

## Make.com Scenario Overview

The scenario has **4 modules**:

1. **Google Sheets – Watch New Rows**  
   - Triggers whenever a new response is added to `Form responses 1`.
   - Spreadsheet: `Inbox`  
   - Sheet name: `Form responses 1`  
   - Row with headers: `A1:D1`  
   - Limit: `1`

2. **OpenAI – Create a Chat Completion**  
   - Model: `gpt-4o-mini` (or another GPT‑4o model)  
   - Messages:
     - **System**: describes the shop rules and how to answer (returns, shipping, store info, etc.).
     - **User**: `Customer question: {{1["What is your question?"]}}` (mapped from the Sheets module).

   Example system prompt:

   ```text
   You are a helpful support agent for an online sports shop. You answer customer questions clearly in 2–5 short sentences.

   Rules:
   - Returns: 30 days, item unused and in original packaging. Tell the customer they can contact you to start the return.
   - Shipping: Standard = 3–5 business days for $5.99, Express = 1–2 business days for $12.99, free standard shipping over $75, ship to US and Canada only.
   - Store info: Address 123 Sports Lane, Anytown, USA. Open Mon–Sat 10 AM–8 PM, Sun 11 AM–6 PM. Phone (555) 123‑4567.
   - If they ask for a recommendation, suggest 1–2 products and explain simply why they are good.
   - If you need more info (like shoe size, color, or order number), politely ask for it.
   - Always be short, friendly, and clear.
