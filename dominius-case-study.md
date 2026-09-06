# dominius — internal AI business OS

*Engineering case study · Zainulabedin Qureshi · [logiclovingmind.com](https://logiclovingmind.com) ·
zeixdream@gmail.com*

---

**Status:** private repository, in daily use. This write-up exists because the code
holds live commercial data and cannot be made public. Everything described here is
verifiable on a screen share.

85 commits between 11 June and 8 August 2026. 11,792 lines of TypeScript across 76
files. Next.js 16 App Router, React 19, Tailwind, TypeScript strict.

---

## What it is

A single operator-facing application that covers the whole business: where leads come
from, what stage each deal is at, what is being delivered, and what the money is doing.
Thirteen modules — `today`, `pipeline`, `leads`, `qualify`, `sales`, `delivery`,
`finance`, `adspy`, `chat`, `brain`, `maya`, `privacy`, `login` — over nine API route
groups (`auth`, `biz`, `brain`, `cron`, `finance`, `health`, `intake`, `maya`,
`webhook`).

The reason it is one system rather than five tools is that the LLM agents read across
it. A question like "what needs attention today" is only answerable if pipeline,
delivery and finance sit behind the same interface.

---

## Two agents with deliberately different safety profiles

**Maya** is the WhatsApp lead qualifier. She is customer-facing, so she is given
tools rather than prose freedom — four of them: `save_lead`, `get_available_slots`,
`book_appointment`, `escalate_to_human`. Anything with a consequence goes through a
tool call, which means the consequence is a typed function I wrote and not a sentence
the model produced.

The interesting one is `escalate_to_human`. Calling it does four things: sets
`isHandedOff` on the session, writes the lead as `handed_off` with the escalation
reason attached, notifies the team over Telegram, and — most importantly — makes the
agent go permanently silent on that thread. There is an explicit early return on
`session.isHandedOff` before any model call. A handoff that the agent can talk over is
not a handoff.

The customer still gets a closing line, and it is language-matched: a `looksHinglish`
check over the conversation history decides between the English and Hinglish
acknowledgement. Falling back to English on a thread that has been conducted in
Hinglish reads as the system breaking.

**The finance agent** is internal-only and has the opposite shape. It reads the full
ledger and answers questions about capital position and runway. It gets far more
context and far more latitude, because the only person it can mislead is me. Same
codebase, same model, different trust boundary — and the boundary is drawn by what
each agent can *do*, not by how each prompt is worded.

---

## Conversation state: Upstash Redis over REST

Sessions live in Upstash Redis, addressed over the REST API rather than a TCP client,
with a 30-day TTL. The session is a single typed object carrying the message history,
extracted lead data, booking state, handoff flag and activity timestamps.

REST rather than a pooled connection is a serverless decision. Next.js route handlers
on Vercel do not keep a socket alive between invocations, so a connection-pooling
Redis client spends its life reconnecting. A stateless HTTP call fits the execution
model. It costs a little latency per operation and removes an entire class of
connection-exhaustion failure.

Session shape drives behaviour beyond the conversation. `lastActivityAt` plus a
`COLD_LEAD_DAYS = 3` threshold defines a cold lead — engaged, not booked, not handed
to a human, quiet for three days — which is the exact population worth a re-engagement
nudge. That is a query over session state, not a separate CRM table that would
immediately drift out of sync.

---

## Google Sheets as the finance ledger

Every finance transaction is written to a Google Sheet with a fixed eleven-column
header, and mirrored into Redis for read paths.

This is the decision most engineers would argue with, so here is the reasoning. The
people who need to audit, correct and annotate financial rows are not going to open a
database client, and building them a CRUD interface good enough to trust would have
been weeks of work that earns nothing. A spreadsheet is already the best financial
review UI ever shipped, and it was already where the numbers lived.

The cost is real: Sheets has no transactions, no constraints, and an API quota. So it
is not the read path. Writes go to Sheets, reads come from the Redis mirror
(`redisMirrorTransaction`, `redisAppendTransaction`, `redisReplaceTransactions`), and
the sheet stays the human-editable source of truth. Charts and agent queries never
touch the API. Google Apps Script covers the automation that has to run inside the
sheet itself.

I would not choose this for a customer-facing product. For an internal ledger with one
writer and a strong requirement for human editability, it was the correct trade, and
it shipped in days rather than weeks.

---

## What this project is evidence of

- Designing two agents against different threat models in one codebase, and enforcing
  the difference in the tool layer rather than in prompt text.
- Treating handoff as a state machine with a hard stop, because the failure mode that
  actually costs customers is an agent that keeps talking after a human took over.
- Picking storage per access pattern — Redis for hot session state, Sheets for
  human-audited records, mirrored so neither is asked to do the other's job.
- Multilingual output that degrades in the right language.
- Shipping the unglamorous half: cron, health checks, a privacy module, session TTLs,
  backups.

The public counterpart is
[wa-agent-platform](https://github.com/logiclovingmind/wa-agent-platform) — the same
agent architecture rebuilt multi-tenant on Cloudflare Workers with Durable Objects, a
17-table Postgres schema with row-level security on every table, AES-GCM credential
encryption, and 283 tests against the real Workers runtime. That one can be read line
by line.
