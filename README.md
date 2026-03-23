# Full Stack Engineer — Take-Home Assignment

**Recommended time:** 3–5 days (work at your own pace — no timer)
**Deliverables:** Working code + architecture diagram + recorded walkthrough
**AI tools:** You may use AI for coding. You must be able to explain every decision in the live interview.

---

## The Task

Build a working **AI-powered email triage agent** using the AI SDK of your choice (Vercel AI SDK, LangChain, Anthropic SDK, OpenAI SDK — your pick).

The agent receives an inbound email and must:
1. Classify it (urgency, category, suggested action)
2. Draft a response if appropriate
3. Decide whether the draft needs human approval before sending

You are building the **backend logic + a minimal UI to interact with it** — not a production email system.

---

## What's In This Repo

```
├── README.md              ← you're here
├── sample-emails.json     ← test data (6 emails covering different categories)
├── .env.example           ← API key template
└── docs/                  ← put your architecture diagram here
```

Use the sample emails to seed your database and test your agent. They cover: court notification, new client inquiry, newsletter spam, opposing counsel, billing question, and scheduling request — all in Spanish, which is the real-world context.

---

## Part 1: The Agent (code)

Build a Next.js app (App Router) with:

### Data Model (Prisma + SQLite)

Design the schema yourself. It must support:
- **Organizations** (multi-tenant — all data scoped to an org)
- **Contacts** (name, email, relationship: `client`, `opposing_counsel`, `court`, `unknown`)
- **Inbound emails** (from, subject, body, receivedAt)
- **Triage results** (the agent's classification, linked to the email)
- **Draft responses** (the agent's suggested reply, with an approval state)

We're evaluating your schema design — make it yours, not a copy of the above bullet points.

### Agent Logic

Implement the triage agent using your chosen SDK. It must:

1. **Classify** each email:
   - `urgency`: high / medium / low
   - `category`: one of `legal_proceeding`, `client_inquiry`, `scheduling`, `billing`, `spam_newsletter`, `other`
   - `suggestedAction`: `draft_reply` | `escalate_to_lawyer` | `archive`

2. **Use at least one tool call** — for example, a `lookupContact` tool that checks whether the sender exists in the contacts table and returns their relationship type. The agent should use this to inform its classification (an email from a known `court` contact is more likely urgent than one from `unknown`).

3. **Conditionally draft a response** — if `suggestedAction` is `draft_reply`, the agent should produce a draft. The draft must be stored with status `pending_approval`.

### UI

A single page is enough. It should:
- Show a list of processed emails with their triage results
- Let you click an email to see the classification + draft response
- Have approve / reject / edit buttons on drafts
- Have a way to submit a new email (paste or form) and see the agent process it

No need for auth, real email integration, or polish. shadcn/ui defaults are fine.

### What We're Looking For

| Area | Signal |
|------|--------|
| SDK usage | Do you understand tool calling, structured output, streaming? Not just wrapping a prompt in `fetch`. |
| Schema design | Proper relations, org-scoping, approval state modeling |
| Server/client split | Data fetching in server components, interactivity in client components |
| Error handling | What happens when the LLM returns garbage? When the tool call fails? |
| Agent design | Is the tool definition well-constrained? Does the system prompt actually work? |

---

## Part 2: System Design (diagram)

On paper, whiteboard, or Excalidraw — **draw the architecture** for scaling this email triage system to production. Assume:

- 500 emails/day across 50 law firms (multi-tenant)
- Emails arrive via webhook (Sendgrid/Postmark inbound parse)
- Some firms want auto-send for low-urgency replies, others want approval on everything
- You need to track: agent accuracy over time, approval/rejection rates, avg response time

**Your diagram should show:**
1. The inbound email flow: webhook → processing → agent → approval → send
2. Where background jobs vs. synchronous processing vs. real-time updates are used
3. How per-org configuration (approval rules) fits in
4. Where you'd add observability (tracing, metrics, error tracking)

Export as PNG/PDF and place it in the `/docs` folder. Hand-drawn and photographed is fine — we care about the thinking, not the tool.

---

## Part 3: Walkthrough (recorded)

Record a **10-minute Loom** (or similar) where you:

1. Demo the working app (~3 min)
2. Walk through your schema design and explain your choices (~3 min)
3. Walk through your architecture diagram and explain what changes at scale (~3 min)
4. One thing you'd do differently with more time (~1 min)

This is the part AI can't do for you. We want to hear how you think.

---

## Submission

1. **Fork this repo** (keep it private)
2. Build your solution in the fork
3. Place your architecture diagram in the `/docs` folder
4. Update this `README.md` with your setup instructions (keep the assignment brief above, add a "Setup" section below)
5. Email jamie@juryo.ai with:
   - Subject: `Take-Home — [Your Name]`
   - Link to your fork (invite `jamiekyaellis@gmail.com` as a collaborator)
   - Link to the Loom recording

---

## Starter Resources

Don't waste time on boilerplate. Pick your SDK and scaffold fast:

### Fastest Start: LangGraph (one command, UI included)

```bash
npx create-agent-chat-app@latest
```

This scaffolds a full-stack app with a Next.js chat UI + prebuilt LangGraph agents (ReAct, memory, research, retrieval). Pick the ones relevant to the task and modify from there. Runs the web app on `:3000` and agent API on `:2024`.

- [create-agent-chat-app repo](https://github.com/langchain-ai/create-agent-chat-app)
- [LangGraph.js docs](https://langchain-ai.github.io/langgraphjs/)

### Alternative: Vercel AI SDK (what we use internally)

```bash
npx create-next-app@latest email-triage --typescript --tailwind --app --src-dir
cd email-triage
npx shadcn@latest init
npm install ai @ai-sdk/anthropic zod prisma @prisma/client
npx prisma init --datasource-provider sqlite
```

- [AI SDK docs](https://ai-sdk.dev/docs/introduction) — start here
- [Building AI Agents guide](https://vercel.com/kb/guide/how-to-build-ai-agents-with-vercel-and-the-ai-sdk) — tool calling, agent loops
- [AI SDK templates gallery](https://vercel.com/templates/ai) — more starters

### Other SDKs (also fine)

| SDK | Install | Docs |
|-----|---------|------|
| **Anthropic SDK** | `npm install @anthropic-ai/sdk` | [Agent SDK quickstart](https://platform.claude.com/docs/en/agent-sdk/quickstart) |
| **OpenAI SDK** | `npm install openai` | [Function calling guide](https://platform.openai.com/docs/guides/function-calling) |

### Architecture Diagram

Use whatever you're comfortable with. Some options:
- [Excalidraw](https://excalidraw.com) — free, browser-based, hand-drawn aesthetic
- [Excalidraw system design templates](https://github.com/aretecode/system-design-templates-excalidraw) — pre-made shapes for databases, queues, services
- [tldraw](https://tldraw.com) — similar to Excalidraw
- Paper + phone camera — totally fine

### Recording

- [Loom](https://loom.com) — free tier, easiest option
- QuickTime (Mac) or OBS (any platform) — upload to Google Drive or YouTube (unlisted)

---

## FAQ

**Which AI SDK should I use?**
Whichever you're most comfortable with. We use Vercel AI SDK + Anthropic, but we won't penalize you for using OpenAI SDK or LangChain. We want to see that you understand the SDK deeply, not that you picked the "right" one.

**Do I need to use a paid API?**
Yes — you'll need an API key for at least one LLM provider. Most have free tiers or credits. If cost is a blocker, email us and we'll provide a key.

**Can I skip the Loom?**
No. The walkthrough is a core part of the evaluation. It's how we distinguish "built it and understands it" from "generated it."

**What if I can't finish everything?**
Ship what works. Document what's missing in the README. A working agent with a rough UI beats a polished UI with no agent.
