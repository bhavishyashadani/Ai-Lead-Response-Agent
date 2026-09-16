# I Turned Inbound Website Leads Into Gmail Drafts With Gemini 3.8 Flash

## 1. The capability I spotted

Google released **Gemini 3.8 Flash on September 2, 2026**. Google positions it as its most intelligent Flash model, built for long-horizon software engineering, autonomous agents and complex enterprise workflows.

For this build I use Gemini 3.8 Flash through Google's current **Interactions API** and its structured-output capability. The model returns a typed JSON object rather than unconstrained prose.

Official documentation:
- Gemini API pricing: https://ai.google.dev/gemini-api/docs/pricing
- Getting started: https://ai.google.dev/gemini-api/docs/get-started
- Structured output: https://ai.google.dev/gemini-api/docs/structured-output

## 2. The caveat I designed around

The model can make unsupported assumptions when generating customer-facing communication.

I designed around that by:
- instructing Gemini to use only facts in the lead,
- requiring Unknown when information is absent,
- requiring structured output,
- creating a Gmail draft rather than automatically sending it.

The human remains responsible for the final customer-facing message.

## 3. The pain point

A 50–300 person service/product company can receive website leads while the sales team is occupied with existing work.

The operational gap is:

**lead arrives → someone reads it → understands it → qualifies it → writes a response.**

That delay creates repetitive work and can leave warm leads waiting.

## 4. Why this pairing

A simple mail merge can insert a person's name. Gemini 3.8 Flash can interpret the free-form message and produce multiple useful fields:

1. Lead summary
2. Pain point
3. Urgency
4. Buying signal
5. Recommended action
6. Personalized reply

That makes it suitable for a small operational workflow rather than a generic chatbot.

## 5. What I built

```text
Gmail
  |
  | unread + [WEBSITE LEAD]
  v
Python script
  |
  v
Gemini 3.8 Flash
  |
  | structured LeadAnalysis
  v
Gmail API
  |
  v
Draft reply
  |
  v
Human review + send
```

There is no custom frontend, database, hosted server, Make.com, n8n, or visual workflow builder.

## 6. Step-by-step

### Step 1 — Lead arrives

I use a synthetic inbound lead in my own Gmail account.

**Screenshot: 01-lead-email.png**

### Step 2 — Python finds the lead

The script searches only for unread messages whose subject contains `[WEBSITE LEAD]`.

**Screenshot: 02-code-editor.png**

### Step 3 — Gemini analyzes it

The lead is passed to Gemini 3.8 Flash through the Interactions API.

The response is forced to match the LeadAnalysis JSON schema.

**Screenshot: 03-terminal-output.png**

### Step 4 — Gmail draft

The script uses the Gmail API `drafts.create` endpoint.

It deliberately does not send the message.

**Screenshot: 04-gmail-draft.png**

### Step 5 — Human review

The salesperson reviews and edits the generated draft before sending.

**Screenshot: 05-final-result.png**

## 7. Testing

I tested:
- a high-intent lead with a concrete problem,
- a vague lead,
- a lead with an explicit timeline,
- missing information that should not be invented.

The desired failure behavior is to return Unknown rather than hallucinate.

## 8. What I would build next

For production:
- CRM creation after human approval,
- lead ownership/routing,
- response-time metrics,
- persistent processed-message storage,
- API retry/error handling,
- historical-lead evaluation,
- approval controls for higher-risk replies.

I deliberately kept these out of the prototype because the assignment asks for the smallest working version.

## 9. Other capability → pain pairings

| Capability | Pain | Smallest workflow |
|---|---|---|
| Gemini 3.8 Flash | Slow lead qualification | Gmail → Gemini → Gmail draft |
| Gemini multimodal understanding | Teams manually inspect long recordings | Video → relevant moments → summary |
| Agent/tool capabilities | CRM updates get forgotten | Meeting outcome → agent → CRM action |
| Structured output | Manual document extraction | Document → AI → structured record |
| AI agents | Morning operational context is fragmented | Gmail + Calendar → daily briefing |
