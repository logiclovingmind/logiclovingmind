# zEi

**AI automation engineer.** I ship production AI systems by directing coding agents —
I own the architecture, data model, integrations and deployment; the implementation is
AI-assisted by design.

Based in Bengaluru · [logiclovingmind.com](https://logiclovingmind.com)

---

### What I build

**Agent systems on WhatsApp.** Multi-tenant platforms where onboarding a client is a
database insert rather than a deployment. Lead qualification in English, Hinglish and
Gujarati, structured data capture, Google Calendar booking, and human handoff when the
conversation needs a person.

**Internal operating systems.** Dashboards that unify sales pipeline, delivery and
finance, with LLM agents reading across the whole system to summarise activity and
surface what needs attention.

**The unglamorous half.** Postgres schema design with row-level security, forward-only
migrations, per-tenant credential encryption, Meta app review and webhook verification,
OAuth and service accounts, cron scheduling, CI and backups.

---

### Selected work

Everything in this table with a link is public and readable right now.

| Project | What it is |
|---|---|
| [**wa-agent-platform**](https://github.com/logiclovingmind/wa-agent-platform) | Multi-tenant WhatsApp AI agent SaaS — Cloudflare Workers, Durable Objects for per-conversation state, 17-table Postgres schema with RLS on every table, AES-GCM credential encryption with version rotation, safety escalation layer. 283 tests against the real Workers runtime. |
| [**real-estate-whatsapp-agent**](https://github.com/logiclovingmind/real-estate-whatsapp-agent) | Lead qualifier in English, Hinglish and Gujarati — structured capture, Google Calendar booking, human handoff. [Live demo](https://whatsapp-demo-agent.vercel.app). |
| [**crm-standard**](https://github.com/logiclovingmind/crm-standard) | Brokerage operations software built to the constraint that it runs on a 1GB droplet — SQLite in WAL mode, one process, role scoping enforced at the query layer rather than the UI. |
| [**pocket-split**](https://github.com/logiclovingmind/pocket-split) | Offline-first expense-splitting PWA — Dexie/IndexedDB, Supabase realtime, offline write queue, conflict resolution. [Live](https://pocket-split-six.vercel.app). |
| [**music-xp**](https://github.com/logiclovingmind/music-xp) | Python daily automation with a self-tuning taste model across 23 languages. |
| **dominius** *(private)* | Internal AI business OS in daily use — 13 modules across pipeline, prospecting, delivery and finance, with a WhatsApp lead-qualifier agent and a finance agent tracking capital and runway. Private because it holds live commercial data; happy to walk through it on a call. |
| **crm-premium** *(private)* | Postgres CRM with TOTP two-factor auth, QR enrolment and audit logging. |

Adapted the same agent architecture across **10 verticals** — real estate, driving school,
pathology lab, salon, spa, gym, education, automobile, clinic and tourism — including a
medical-advice refusal guardrail for the clinical variant.

---

### Working with

`TypeScript` `Node.js` `Python` `React` `Next.js`
`PostgreSQL` `Supabase` `SQLite` `Redis`
`Cloudflare Workers` `Durable Objects` `Vercel` `Render`
`Meta WhatsApp Cloud API` `Google Workspace APIs` `OAuth` `Webhooks`

---

Open to AI automation, forward-deployed and solutions engineering roles.
Reach me at **zeixdream@gmail.com**.
