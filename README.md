# SkyLink Resolution Desk

A customer-facing resolution agent for airline disruption, built for AIONOS Assignment 3.

**Live prototype:** https://claude.ai/artifact/RT18MmsrxxbhbwHrZ5Fxh6
**Demo video:** _(paste your Drive link here)_

---

## Run it

No build step, no keys, no dependencies.

```bash
git clone <this-repo>
cd <this-repo>
python3 -m http.server 8000
# open http://localhost:8000
```

Or just open `index.html` in a browser. The whole agent is one self-contained file.

When it runs on the hosted link it uses Claude for two things per turn (reading intent, writing the reply). When it runs locally or the model is unavailable, it falls back to a keyword intent parser and composes replies from the policy engine's own wording. **The decisions are identical in both modes** — that is the point of the design.

---

## The one-line claim

> The model writes sentences. It never writes entitlements.

Every amount, eligibility check, rule citation and escalation is computed in JavaScript from the data pack. The language model classifies intent into a fixed enum, and then writes wording it is forbidden to add to.

---

## Architecture

```
┌───────────────────────────────────────────────┐
│ Interface — chat, decision rail, audit, export│
└───────────────────────┬───────────────────────┘
                        ▼
┌───────────────────────────────────────────────┐
│ Intent layer  ·  Claude (quick tier)          │
│ message + last 6 turns → {requests[], mood}   │
│ requests filtered against a 16-value enum     │
│ fallback: keyword parser over the same enum   │
└───────────────────────┬───────────────────────┘
                        ▼
┌───────────────────────────────────────────────┐
│ Policy engine  ·  deterministic code          │
│ f(requests, booking, session flags) →         │
│ [{verdict, title, detail, rule}]              │
│ verdict ∈ execute | decline | escalate | ask  │
└───────────────────────┬───────────────────────┘
                        ▼
┌───────────────────────────────────────────────┐
│ Action + escalation                           │
│ applies each entitlement once, opens ESC      │
│ tickets, tracks the choice the customer owes  │
└───────────────────────┬───────────────────────┘
                        ▼
┌───────────────────────────────────────────────┐
│ Reply layer  ·  Claude                        │
│ receives the verdicts as the ONLY permitted   │
│ content; may not add, soften or invent one    │
└───────────────────────────────────────────────┘

Grounding store (read by all, written by none):
  3 customer profiles · 4 booking rows · R1–R5 · P1–P5 · ₹1,500 ceiling
Case record: turns, verdicts, rule ids, tickets, timestamps → JSON export
```

The decision rail on the right of the screen fills in **before** any reply text is generated, so you can see that the sentence follows the verdict rather than the other way round.

---

## Process flow, one turn

1. Customer message arrives.
2. Intent layer returns request types + mood.
3. Engine reads the booking and session flags.
4. Verdicts emitted, each carrying its rule id.
5. Actions applied, escalation tickets opened.
6. Reply written from the verdicts.
7. Audit entries appended.

---

## Policy matrix

| Customer asks for | Condition | Verdict | Rule |
|---|---|---|---|
| Rebooking | Flight cancelled by the airline | Execute, free | R1 |
| Refund | Cancelled, original payment method | Execute | R1 + R3 |
| Refund to cash / another method | Any | Escalate | P5 |
| Meal voucher | Any delay | Execute ₹500 | R2a |
| Lounge access | Delay over 3 hours | Execute | R2b |
| Hotel | Delay over 5 hours | Execute, delayed hours only | R2c |
| Hotel | Delay of 5 hours or less | Decline, offer what applies | R2c |
| Full night's hotel | Any | Escalate | P1 |
| Free upgrade or goodwill | Any tier, Platinum included | Escalate | P1 + R5 |
| Move to a higher-fare flight | Voluntary change | Execute, customer pays | R4 |
| Waive fare difference | Above ₹1,500 | Escalate | P2 |
| Legal action or formal complaint | Any | Escalate immediately | P4 |
| Customer's own missed flight | Any | Escalate | P3 |

`R1`–`R5` are the service rules from section 3 of the data pack. `P1`–`P5` are the prohibited actions from section 4.

---

## Scenarios

**Priya Nair — Gold, SK4821X, SK-204 cancelled.** The agent offers the choice R1 gives her (free rebooking within 24 hours or a full refund) instead of choosing for her. On "furious, cash refund plus a free business upgrade": the refund is executed to the original method, the cash request escalates under P5, the upgrade escalates under P1, and Gold tier is explained as seat priority rather than goodwill under R5. Entitlements already granted are never withdrawn. If she then threatens legal action, P4 escalates immediately.

**Arvind Kulkarni — Silver, TR1190B, delayed 4h.** Voucher and lounge access applied under R2b without him having to ask. The hotel request is **declined**, not escalated — R2c starts above 5 hours and this delay is 4. Declining inside policy is desk work; escalating it would be hiding behind a supervisor.

**Meher Kaur — Platinum, WL7742, delayed 6h.** Voucher, lounge and hotel for the delayed hours applied under R2c. The full night's stay escalates under P1. The move to the higher-fare flight is allowed under R4 with the ₹2,000 difference payable by her; her refusal to pay escalates under P2 because ₹2,000 is above the ₹1,500 desk ceiling.

---

## Inputs, sources and assumptions

**Sources** — all transcribed verbatim from the assignment data pack into one object in `index.html`:
customer profiles, booking rows, the five service rules, and the allowed/prohibited list. The three sample conversations were read for tone only and are never treated as fact or policy. Nothing else is available to the agent — no web access, no invented flight numbers, no invented amounts.

**Assumptions**

- **Identity is pre-verified.** The chat opens from a booking reference, so the agent does not re-ask for the PNR. It states the reference it is working from.
- **Delay bands are exclusive.** "More than 5 hours" excludes exactly 5, so a 4-hour delay gets voucher + lounge and no hotel.
- **A cancellation is not a delay.** The delay compensation table does not apply to Priya's cancelled flight; R1 does.
- **Declining is not escalating.** Escalation is reserved for the five prohibited categories. A no that cites its clause and offers the alternative is a resolution.
- **Actions are recorded, not transacted.** There is no inventory or payment system in the data pack, so "rebooked" means a recorded action against the case, not a seat held.

---

## AI tools used

| Tool | When | What it did |
|---|---|---|
| **Claude (in the running agent)** | Run time | Two constrained calls per turn. One classifies the message into the request enum and reads mood; one writes the reply from verdicts it may not alter. Quick tier, no memory between calls, prompt assembled from the grounding store. |
| **Claude (building it)** | Build time | Turned the data pack into the grounding object, drafted the rule matrix, wrote the page, and stress-tested the engine against edge phrasings of the three scenarios. |
| **Claude artifact hosting** | Delivery | Published the prototype as a shareable link so a reviewer can try it with no install. |

**Prompt discipline.** The intent prompt can only return values from a fixed list, and anything outside it is dropped before the engine sees it. The reply prompt receives the verdicts as the only content it may communicate and is told not to add an amount, a flight, a date or a gesture. Neither prompt contains a policy the engine doesn't already enforce.

---

## The conversation and action record

Every turn appends to three structures: the transcript, the decision list (verdict, rule id, ticket id, timestamp) and the audit trail (`session_opened`, `customer_message`, `intent_extracted`, `policy_decision`, `agent_reply`). **Export case record** writes all three to `CASE-XXXX-NNNN.json`.

---

## Honest limits

- Rebooking, vouchers, lounge, hotel and refunds are recorded as actions, not sent to a reservation or payments system.
- No free-text identity verification, no multi-PNR households, no partial refunds.
- The supervisor queue is one-way: escalations are raised with full context, but a human decision doesn't yet write back into the same case record.
- Session state is in memory. Reloading starts a fresh case.

**Next week's version:** move the grounding object behind a policy service, put the rule matrix in versioned config so ops can change a ceiling without a deploy, and log every verdict for audit sampling.

---

## Files

```
index.html          the entire agent — grounding data, intent layer, policy engine, UI
README.md           this file
DEMO_SCRIPT.md      shot-by-shot plan for the demo video
RollNo_Tamanna.pptx the 10-slide deck
```

Built by Tamanna · B.Tech CSE (Data Science), The NorthCap University.
