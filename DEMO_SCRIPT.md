# Demo video — shot list

Target: **4 to 5 minutes**, screen recording with voice. Keep the file under 10 MB — record at 1080p and, if it's oversized, export at 720p or trim the pauses. Upload to Drive and set sharing to "Anyone with the link".

Open the live link full-screen before you start recording. Don't show your desktop, your email, or the assignment PDF.

---

### 0:00 — 0:25 · What this is

> "This is a customer-facing resolution agent for airline disruption. Three real cases from the data pack, one agent. The thing I want to show you is the split: the language model reads the customer, but it never decides what the customer gets. That's computed in code, and you can watch it happen on the right."

Point at the three persona cards and the decision rail.

### 0:25 — 1:40 · Priya, the angry one

Click **Priya Nair**. Read the opening line the agent gives.

Click chip 1 ("My flight to Goa tonight has been cancelled…").

> "First thing it does is not choose for her. Rule R1 gives the customer the choice — rebooking or refund — so the agent asks. That's an 'Awaiting answer' verdict, not an action."

Click chip 2 ("I'll take the refund.").

> "Now it executes. Full refund, seven business days, original payment method — and it cites R1 plus R3 in the rail."

Click chip 3 (the furious cash-plus-upgrade message).

> "Three requests in one angry message. Watch the rail fill in **before** the reply is written. Refund already granted, and it stays granted. Cash refund escalates under P5. Free upgrade escalates under P1 — and notice Gold tier doesn't buy her that, because R5 gives priority rebooking and nothing else."

Click chip 4 (legal threat).

> "Legal action is P4 — immediate escalation, and the agent doesn't predict what the specialist team will say."

### 1:40 — 2:30 · Arvind, the honest no

Click **Arvind Kulkarni**, then chip 1.

> "Four-hour delay. Voucher and lounge applied under R2b before he asks for them."

Click chip 2 (the hotel request).

> "This is the one I care about. Hotel cover starts above five hours; his delay is four. So the agent declines and quotes the clause — it doesn't invent a hotel to be liked, and it doesn't escalate to a human to avoid saying no. Declining inside policy is the desk's job."

### 2:30 — 3:20 · Meher, two escalations at once

Click **Meher Kaur**, then chip 1.

> "Six hours, so R2c: voucher, lounge, and a hotel covering the delayed hours only."

Chip 2 (full night).

> "A full night is beyond the clause, so it escalates under P1 — while the delayed-hours cover she already has stays live."

Chip 3 (higher-fare flight, refusing the ₹2,000).

> "Moving to the higher-fare flight is allowed under R4, but voluntarily — so the difference is hers. She refuses, and ₹2,000 is above the ₹1,500 desk ceiling, so P2 sends it to a supervisor. The agent promises nothing on the way out."

### 3:20 — 4:05 · The record, and the architecture

Open the **Audit trail** tab, scroll it.

> "Every message, every extracted intent, every verdict with its rule id and ticket, timestamped."

Click **Export case record**.

> "That's the whole case as one JSON file — transcript, decisions, audit."

Open the **Policy** tab.

> "And these are the only rules it has. R1 to R5 from section 3, P1 to P5 from section 4. It has no other source of truth — no web access, no invented flights, no invented amounts."

### 4:05 — 4:40 · Type something unscripted

Type a message of your own that isn't a chip — something like *"can you just give me some miles for the trouble"* or *"I want to speak to a manager"*.

> "It isn't a scripted demo — the intent layer handles phrasing it hasn't seen, and the same engine decides the outcome."

### 4:40 — 5:00 · Close on the limits

> "What's real: the intent layer, the policy engine, the escalation logic and the record. What's simulated: the bookings themselves — nothing here talks to a reservation system, because the data pack has no inventory. The next step is moving the rule matrix into versioned config so ops can change a ceiling without a deploy."

Stop recording.

---

### Before you upload

- File name: `RollNo_Name.mp4` with your actual roll number.
- Drive sharing: **Anyone with the link · Viewer**. Open it in an incognito window to check.
- Under 10 MB.
