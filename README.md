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

Architecture

The final runtime is implemented as a self-contained JavaScript application.

                  Customer Message
                         │
                         ▼
              ┌─────────────────────┐
              │  Interface / Chat   │
              │  Customer Context   │
              └──────────┬──────────┘
                         │
                         ▼
              ┌─────────────────────┐
              │ Intent Detection    │
              │ Structured Request  │
              │ Types / Mood        │
              └──────────┬──────────┘
                         │
                         ▼
              ┌─────────────────────┐
              │ Deterministic       │
              │ Policy Engine       │
              │                     │
              │ Customer + Booking │
              │ + Request + Rules   │
              └──────────┬──────────┘
                         │
              ┌──────────┼───────────┐
              ▼          ▼           ▼
           Execute     Decline    Escalate
              │          │           │
              └──────────┼───────────┘
                         ▼
              ┌─────────────────────┐
              │ Response Layer      │
              │ Customer-facing     │
              │ explanation         │
              └──────────┬──────────┘
                         │
                         ▼
              ┌─────────────────────┐
              │ Audit Trail & Case  │
              │ Record / Export     │
              └─────────────────────┘
Design Principle

The policy engine is the authority for:

Eligibility
Compensation
Refund conditions
Rebooking conditions
Fare-difference rules
Escalation requirements
Prohibited actions

The response layer communicates the resulting decision to the customer.

Runtime Flow

Each customer message follows this process:

Customer message is received.
The request is mapped to one or more structured intent types.
Customer and booking information is loaded from the grounding data.
The policy engine evaluates the request against the applicable rules.
A verdict is produced:
execute
decline
escalate
ask
The corresponding action or escalation is recorded.
A customer-facing response is generated from the decision.
The conversation and audit trail are updated.

This separation prevents customer-facing wording from changing the underlying entitlement.

Grounding Data

The prototype uses only the information supplied in the assignment data pack.

Customer Profiles
Customer	Tier	Booking	Flight	Situation
Priya Nair	Gold	SK4821X	SK-204 Delhi → Goa	Cancelled
Arvind Kulkarni	Silver	TR1190B	SK-118 Mumbai → Bengaluru	Delayed 4 hours
Meher Kaur	Platinum	WL7742	SK-305 Delhi → Hyderabad	Delayed 6 hours

The prototype does not require external web information to make these decisions.

Policy Engine

The decision engine uses the service rules and prohibited-action rules supplied in the assignment.

Customer Request	Condition	Decision	Rule
Rebooking	Airline cancellation	Execute, free	R1
Refund	Airline cancellation	Execute	R1 + R3
Refund to cash / another method	Any	Escalate	P5
Meal voucher	Delay	Execute ₹500	R2a
Lounge access	Delay > 3 hours	Execute	R2b
Hotel	Delay > 5 hours	Execute for delayed hours	R2c
Hotel	Delay ≤ 5 hours	Decline	R2c
Full-night hotel	Any	Escalate	P1
Free upgrade / goodwill	Any tier	Escalate	P1 + R5
Higher-fare flight	Voluntary change	Execute, customer pays	R4
Fare difference waiver	Above ₹1,500	Escalate	P2
Legal action / formal complaint	Any	Escalate immediately	P4
Disruption outside airline responsibility	Any	Escalate	P3
Example Customer Scenarios
1. Priya — Cancelled Flight

Priya's Delhi → Goa flight is cancelled.

The agent provides the two choices allowed by the cancellation policy:

Free rebooking on the next available option within 24 hours
Full refund to the original payment method

When Priya asks for a cash refund and a free business-class upgrade:

The eligible refund is processed according to policy
Cash refund is escalated because the original payment method is required
The free upgrade is escalated because it is not an available entitlement
Her Gold status is explained as priority rebooking rather than an automatic goodwill upgrade

When she mentions filing a formal complaint or taking legal action, the case is escalated immediately.

2. Arvind — Four-Hour Delay

Arvind's flight is delayed by four hours.

The policy automatically provides:

₹500 meal voucher
Lounge access

When he asks for a hotel, the request is declined because the hotel rule applies only when the delay is more than five hours.

The agent explains the applicable benefits instead of unnecessarily escalating the case.

3. Meher — Six-Hour Delay

Meher's flight is delayed by six hours.

The applicable benefits include:

₹500 meal voucher
Lounge access
Hotel coverage for the delayed hours

When she asks for a complete night's hotel stay, the additional request is escalated because it is outside the supplied entitlement.

She then asks to move to another flight without paying a ₹2,000 fare difference.

The higher-fare move itself is allowed under the voluntary-change rule, but waiving a difference above the ₹1,500 desk limit requires escalation.

Inputs, Sources and Assumptions
Inputs

The runtime uses:

Customer profile
Customer tier
Booking reference
Flight information
Delay/cancellation status
Customer message
Previous conversation state
Session/action state
Sources

The grounding data contains:

Customer profiles
Booking records
Service rules R1–R5
Prohibited actions P1–P5
₹1,500 fare-difference authority limit

The sample conversations are used as scenario demonstrations and tone references rather than as additional policy sources.

Assumptions
Customer identity is considered pre-verified.
The booking reference is already associated with the customer session.
A cancellation is handled using the cancellation rules rather than delay compensation rules.
“More than 5 hours” is treated as greater than 5 hours.
A policy-based decline is different from an escalation.
Actions are recorded by the prototype but are not connected to a real airline reservation or payment system.
AI Tools Used

AI tools were used during the development process to assist with implementation, debugging, testing, documentation and refinement.

Tool	Stage	Usage
Claude	Development	Assisted with implementation, debugging, rule-matrix refinement, test-case generation and UI/content iteration.
ChatGPT	Development	Used for requirement analysis, architecture discussion, debugging guidance, documentation refinement and demo preparation.
Vercel	Deployment	Used to deploy the final web prototype.
GitHub	Version Control	Used for source-code management and assignment submission.
Runtime AI Architecture

The final deployed prototype is self-contained and does not require an external AI API at runtime.

The runtime uses JavaScript-based intent matching, structured request types and a deterministic policy engine.

AI assistance was used to build and refine the application, but the deployed prototype does not depend on an external AI provider to operate.

This keeps the policy decisions predictable and prevents an AI response from inventing an entitlement, compensation amount or policy exception.

Conversation and Audit Trail

The application maintains a case record during the session.

The record includes:

Customer messages
Extracted requests
Policy decisions
Rule IDs
Actions taken
Escalation information
Timestamps
Agent responses

The Audit Trail tab makes the sequence of events visible to the reviewer.

The Export Case Record function generates a JSON representation of the case.

Example event types include:

session_opened
customer_message
intent_extracted
policy_decision
agent_reply
Handling Escalations

The agent does not escalate every difficult request.

There are three distinct outcomes:

Execute

The requested action is allowed by the supplied policy and can be recorded by the desk.

Decline

The request is outside the available entitlement but does not require supervisor approval.

Escalate

The request falls into a prohibited or authority-limited category.

Examples include:

Cash or non-original-method refunds
Free upgrades or goodwill compensation
Full-night hotel requests outside the entitlement
Fare-difference waivers above ₹1,500
Formal complaints or legal action
Requests requiring an exception to policy

An escalation records the relevant context but does not promise that the supervisor will approve the request.

Honest Limitations

This prototype demonstrates the resolution workflow but is not connected to a real airline backend.

Therefore:

Rebooking is recorded rather than sent to a reservation system.
Refunds are recorded rather than processed through a payment gateway.
Vouchers and lounge access are recorded as actions.
Hotel arrangements are recorded rather than booked with a hotel provider.
There is no live flight-status API.
There is no external customer identity service.
Session state exists only during the current browser session.
Reloading the page starts a new case.
The supervisor queue records escalation context but does not receive a real human response.
Future Improvements

A production version could extend the prototype with:

Airline reservation-system integration
Live flight-status APIs
Payment/refund integration
Real customer authentication
Persistent case storage
Role-based supervisor workflows
Versioned policy configuration
Real notification channels
Analytics for resolution and escalation trends
LLM-based natural-language understanding with the policy engine retained as the final authority
Project Files
skylink-resolution-agent/
│
├── index.html
├── README.md
└── 23csu309_Tamannafinal.pptx
index.html

Contains the complete working prototype, including:

Interface
Customer scenarios
Intent detection
Policy engine
Action handling
Escalation logic
Conversation state
Audit trail
JSON export
README.md

Project documentation, architecture, policy matrix, assumptions and implementation details.

23csu309_Tamannafinal.pptx

Final 10-slide presentation submitted for the AIONOS assignment.

Submission Links

Live Prototype:
https://skylink-resolution-agent.vercel.app/

GitHub:
https://github.com/Tamanna006/skylink-resolution-agent

Demo Video:
Paste the public Google Drive video link here.

Author

Tamanna Arora
B.Tech CSE — Data Science
The NorthCap University

Developed for AIONOS Agentic AI Factory – Assignment 3

