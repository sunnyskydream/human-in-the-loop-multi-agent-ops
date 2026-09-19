# The Human Was the Message Bus: Moving Agent Coordination onto the Blackboard

I operate a governed, human-in-the-loop multi-agent workflow for high-volume, evidence-sensitive personalized-document production. Its predefined stages coordinate through shared work artifacts, and a human approves anything consequential. <!-- claim-ok: series boilerplate identical to note 02; no measurement -->

The [README architecture](../../README.md#architecture) names three roles: reasoning, polish, and reviewer. Two persistent sessions carry the reasoning and polish work without a direct messaging channel; the README's [coordination control plane](../../README.md#coordination-control-plane) shows how they share state. The reviewer sub-agent audits within the workflow, while a second model reviewed this note to reduce correlated blind spots. Four cross-model passes found nine required corrections after the author's own checks. The division of work emerged from observed performance.

The repository's README says: *"Agents do not message each other — each reads and writes the blackboard."* For work artifacts, that was true. Coordination still depended on me: **I carried handoffs across**, pasting each request into the other session and the reply back. [Operating Note 01](./01-parallel-fetch-identity.md) and [Operating Note 02](./02-generated-document-integrity.md) follow records through the pipeline. This note covers the inbox that replaced the relay, the three ways it failed on day one, and the mechanism behind all three: **stop relying on rules agents have to remember.** The pattern generalizes past my system.

---

## What the relay cost

The work artifacts lived on the blackboard. The **list of open requests** lived nowhere. Each handoff was a separate file written for one task, so the only way to know what was still pending was to remember.

When I finally moved them, **48 handoff files had accumulated across three folders**, dated from early August to the day of the cleanup. Nothing in them said which were closed.

The cost never showed up as an incident. No record was corrupted and no document went to the wrong place. It showed up as friction: every request crossed a human before it reached its recipient, a stale handoff looked exactly like a live one, and "the earlier note" could mean any of several files.

That is also why it survived so long. The incident registry described in [note 01](./01-parallel-fetch-identity.md) catches things that go wrong. A relay that *works* — slowly, by hand — never generates an incident. It just grows.

---

## The inbox

One file holds every open coordination request—agent-to-agent or human-gated—one row per request:

| Field | Purpose |
|---|---|
| ID | Stable reference for replies and the archive |
| From → To | Which agent (or the human) is asked |
| Needs | `—` if the recipient can act alone; otherwise a human-gate marker for a decision or fact only I hold |
| Ask | One-sentence request plus a checkable `Done when:` condition; detail goes in the linked file |
| Link | Where the evidence or detail lives |
| Status | `open` · `open (trigger: YYYY-MM-DD)` · `in progress (agent, YYYY-MM-DD)` · `blocked: reason` |

A second, append-only file receives each row when it closes, with a date and a one-line outcome. Each agent's standing instructions tell it to read the open file at the start of every session. I no longer relay. I start the sessions and answer the rows marked for me.

Four policy rules sit on top:

1. **Ownership does not move through the inbox.** A request to write into the other agent's store is a request, not a transfer.
2. **A row never authorizes an outward action.** Publishing, submitting, sending, and deleting still require my explicit approval in the session. The inbox coordinates work; it does not grant permission.
3. **The receiver closes the row**; a row addressed to me is closed by the agent that asked, once I answer.
4. **Disagreements are escalated, not overwritten.** A disputed row is blocked for me. Neither agent edits the other's conclusion to win.

---

## Version 0 broke three ways on day one

The first version was a Markdown table that both agents edited by hand, governed by written rules.

### Failure 1: the instructions did not load

At the time, the polish session read its standing-instructions file only when a session started in that file's folder. Its first session started elsewhere. I could not observe its startup from the other side, so the first inbox row was not a task: it asked the agent to acknowledge and to state where it had loaded its instructions from. The acknowledgment arrived only after I pasted the instruction by hand, and it said so. That is an operational receipt, not a startup trace — it shows the routing worked in that session, not that it loads automatically. The fix was a short pointer in the agent's global instructions, which it reads from any folder.

> **If an agent's compliance with a coordination rule cannot be observed, make the first item a receipt, not a task.**

### Failure 2: two ID collisions

Each session chose "the next number" by reading the file. The polish session tried to create row 012, which was already taken, and moved its request to 014. Row 014 then collided again under concurrent editing, and the surviving request became 015. The patch was a written rule — one agent takes odd numbers, the other even.

### Failure 3: closed rows came back

Two rows that had been moved to the archive reappeared in the open file. One agent read the file, the other changed it, and the first wrote back its stale copy, restoring rows that were already closed. They sat in both files until an inspection caught and repaired it. Nothing about the table's appearance showed the problem.

The third failure is the important one. It is the classic read-modify-write race, and a written rule cannot prevent it, because both writers followed the rules.

> **Collisions announce themselves. A lost update does not.**

---

## Version 1: from remembered rules to a mechanism

All three failures share the shape [note 01](./01-parallel-fetch-identity.md) documents for records: a prevention that lives in a rule someone has to remember. So version 1 removed the hand edits.

Both agents now change the inbox only through one small command-line tool, built around five principles:

| Principle | How version 1 enforces it |
|---|---|
| **One allocator for IDs** | `add` allocates the ID itself — the next unused number across the open file *and* the archive. The odd/even rule is retired, because nobody picks an ID any more. It refuses a request with a missing or blank finish line. |
| **One writer at a time** | Every write takes an exclusive lock file, re-reads under the lock, and replaces the file atomically. A concurrent writer waits instead of overwriting. |
| **An explicit lifecycle** | `claim`, `defer` and `block` set status to one of the allowed forms. Claim records the agent and date, and only the receiver can claim; defer parks a row until a trigger date; block records a reason. |
| **An atomic hand-back** | `close` moves the row to the archive with date and outcome in one operation, and refuses a closer who is not entitled to close. It writes the archive first, so an interruption leaves the row in *both* files — detectable — never in neither. It checks any recognized backticked file path in the row's link and warns if one does not resolve; a link that is not machine-checkable is reported as such rather than passed silently. |
| **Invariants checked, not assumed** | `check` fails if any ID appears twice, or in both files; if a Needs value is outside the allowed set; if a status does not match the allowed forms *in full* — a valid prefix followed by other text fails; or if a row is malformed. Open rows older than three working days are reported; a deferred row starts ageing on its trigger date. The workflow's schema check runs it. |

The tool has regression tests that run against a throwaway copy of the live inbox. The one that matters most launches twelve writes from two simulated agents at the same time and asserts twelve distinct IDs and twelve surviving rows. Other tests cover, among other cases, the close rules, a half-finished close, hand-edited and malformed statuses, trigger dates, link checking, the required finish line, a stray delimiter inside a request, and the event trace — including a trace write that fails.

Review found two enforcement claims broader than the tool's behavior: status validation matched only a prefix, and the link check was described as universal. The tool was tightened for the first and the description narrowed for the second, each with tests, before publication. Tightening the grammar immediately failed one live row whose status had been typed by hand.

The human-readable view did not change: it is still the same Markdown table, read in an editor's preview. Only the way it is written changed.

> **Coordination state is data. Give it one writer path, or it will have as many as it has writers.**

---

## What the inbox did not replace

The records that justify a scoring or production decision stay where the ledgers point to them. During the cleanup, audit and results files were deliberately left in place, because the scored-record ledger references them by filename and the schema check fails if one goes missing. A handoff is a request between agents. An audit record is evidence. Moving the first kind is housekeeping; moving the second would break provenance. <!-- claim-ok: the schema check fails when a referenced record is missing; stated fact, not forecast -->

---

## Day one, as of the snapshot

At the snapshot time (see the table below), the inbox held 21 IDs: 19 closed and 2 open, with requests in both directions. After the acknowledgment row closed, I still relayed at least one message by hand, out of habit — a completion notice the receiving agent could already read directly. A habit outlives the mechanism that made it necessary.

This is a record of one day and one design change, not evidence that the inbox holds up over weeks. The claims above are about what happened and what the tool enforces, not about how it will perform.

## The rule

> **A coordination rule that depends on remembering is not a rule.** The agents followed every written rule on day one, and all three failures happened anyway.

This is note 01's rule, applied one layer up. There, the thing being remembered was which source produced which record. Here, it was which number was free, and whether the file had changed since it was last read. Neither survived being written down.

---

## What would prove this wrong

Version 1 makes claims about what the tool enforces, not about how the inbox will perform. Those claims are testable:

| Test | Target |
|---|---|
| A row lost, duplicated, or resurrected while every writer used the tool | 0 |
| A row present in both files, or an out-of-grammar status, that `check` does not report | 0 |
| A new standalone handoff file created instead of an inbox row | 0 |

The first two would mean the mechanism is wrong. The third would mean the agents route around it, which is the failure the inbox exists to prevent. I will report the result after the next fifty inbox rows, either way, as a checkpoint appended to this note.
---

*The workflow is human-in-the-loop by design: its stages are predefined, components are agentic, and consequential decisions remain with the human orchestrator. It is not an autonomous agent, a productized framework, or production distributed infrastructure. The inbox is a coordination convention between two agent sessions with a small enforcing tool; it is not a messaging system, and the agents do not wake each other — I start every session.*

---

## Numbers and denominators

Every quantitative claim above, with what was counted and against what.

**This records that a denominator was written, not that it is correct.** Whether a stated denominator matches its claim stays a human judgement.

Inbox counts are frozen at a snapshot rather than recomputed from the live file, which keeps changing.

| Value | What was counted | Denominator |
|---|---|---|
| **48 files** | Standalone handoff documents moved to the archive during the cleanup, dated from early August to cleanup day | All handoff-named files in the three working folders at cleanup time; audit and results records excluded by design |
| **21 IDs** (19 closed, 2 open) | Inbox rows at the snapshot, 2026-09-18 16:55 local time: 19 numbered requests, one retroactive row for work finished just before the inbox existed, and one follow-up row | All rows in the open and archive files at the snapshot |
| **2 · 2 · 1** | ID collisions · archived rows resurrected by a stale write · missed standing-instruction loads | Version 0, day one |
| **12 concurrent writes** | Parallel additions in the race regression test, each required to produce a distinct ID and a surviving row | One test run against a copy of the live inbox |
| **9 corrections** | Required changes identified across four cross-model review passes | The four review passes: 5, then 2, then 1, then 1 |
| **50 rows** | Size of the next checkpoint window | The next fifty inbox rows created after publication |
