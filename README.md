# SkyLink Resolution Desk

A customer-facing airline disruption resolution agent developed for **AIONOS Agentic AI Factory – Assignment 3**.

The prototype helps customers resolve common flight disruption situations such as cancellations, delays, refunds, rebooking, compensation, hotel requests and escalation cases.

**Live Prototype:** https://skylink-resolution-agent.vercel.app/

**GitHub Repository:** https://github.com/Tamanna006/skylink-resolution-agent

**Demo Video:** Paste your Google Drive link here

---

## What the Agent Does

SkyLink Resolution Desk is designed around a simple principle:

> **The response layer communicates the decision; it does not decide the entitlement.**

The agent:

- Understands the customer's request
- Identifies the relevant customer and booking context
- Checks the supplied airline policies
- Determines whether an action should be executed, declined, escalated or clarified
- Explains the decision to the customer
- Handles multiple requests in the same message
- Maintains the conversation and action history
- Records escalation cases
- Provides an exportable case record

The goal is to make the resolution process transparent and policy-driven instead of allowing the response layer to invent compensation or exceptions.

---

## Running the Prototype

The final prototype is a standalone web application.

### Requirements

- Modern web browser
- No API key
- No external database
- No build process
- No package installation

### Option 1 — Open Directly

Open `index.html` in a browser.

### Option 2 — Run Using a Local Server

```bash
git clone https://github.com/Tamanna006/skylink-resolution-agent.git
cd skylink-resolution-agent
python3 -m http.server 8000
