# When Your Agent Crosses Two Records: Identity in Parallel Tool Calls

I operate a governed, human-in-the-loop multi-agent workflow for high-volume, evidence-sensitive personalized-document production. Its predefined stages retrieve source records, score them against explicit criteria, stage the survivors, and generate tailored documents through shared work artifacts. A human approves anything consequential.

Over 54 days it produced thirteen data-integrity failures in two families. Before the structural rebuild, I had made twelve incident-level interventions. Some checks held within the narrow failure shape they covered, but the two root causes survived all twelve.

This is what the thirteenth taught me, and it generalizes past my system.

---

## Family 1: records wearing each other's identity

The symptom: a staged record carrying a different record's source URL. Right content, right score, wrong link.

The mechanism took seven incidents to see, and it is embarrassingly simple.

**In this tool interface, parallel fetch results did not preserve source identity in the returned content.**

I fan out seven fetches at once. Seven results come back in one block. Each one describes an entity — its name, its attributes, the fields I asked for. **None of them says which URL produced it.** The only thing connecting a result to its source is **position in the result list**.

That would be survivable if nothing ever reordered. But the next thing the agent does is *think* — sort by value, group by category, rank by priority, write the highest-scoring one first. Every reorder is a chance to break a pairing that was only ever held together by sequence.

### What the crossing did not touch

Worth stating precisely, because it bounds the damage and I got this wrong at first too.

The entity name and the record body **always travel together** — both are read out of the same result. So a crossing produces a record whose name, content, and score are all correct and mutually consistent. Only the pointer is wrong.

**No mis-scoring was found in the recorded incidents:** the content used for scoring remained correctly bound, while source pointers crossed downstream. That is an absence of found errors across the incident record and the checkpoint window, not a proof that none occurred anywhere. The harm is downstream: a pointer that sends a human to the wrong place, and an exclusion log whose pointers block the wrong future records.

If you hit this, check whether your failure has the same shape before you go re-validating your scoring logic. You may be looking for a bug that isn't there.

---

## Family 2: duplicate detection that couldn't read most of the store

The symptom: the same source entering the pipeline twice, sometimes days after the first copy had already been processed and closed.

Root cause: my deduplication extracted identifiers with a regex written for **one** source platform. Everything from every other platform was structurally invisible to it.

I finally measured it. **93 of 371 records — 25% of the store — could not be compared to anything.**

The pattern to notice: dedupe had been "working" for months. It caught duplicates constantly. Nobody looks at a gate that fires regularly and asks what fraction of the input it can even *read*.

> **If your deduplication has never reported coverage, you don't know if it works. You know it isn't crashing.**

There was a second, subtler version. Two records for the same underlying item, on two different platforms, have two different identifiers — no identifier comparison can ever match them. The only available signal is name-plus-title. Mine compared those as exact strings, and one copy carried a trailing reference code the other lacked:

```
"North America Launch Brief (DOC-84719)"   !=   "North America Launch Brief"
```

Two identical items read as different. The gate stayed silent. It was never a truncated-output problem, which is what I'd assumed for weeks.

---

## The pattern across all thirteen

| Response type | Count | Recurred? |
|---|---|---|
| A rule written for a human to remember | 6 | **All six** |
| A mechanical check | 3 | **None within the failure shape covered** |
| Partial / advisory | 4 | Mixed |

Among the first two categories, there were no exceptions in either direction.

The most instructive entry is the one where I got the diagnosis **exactly right on the first attempt**. Incident five, in my own notes:

> *Build the staging payload from a fetch-time source→entity map written immediately after each fetch wave. Never reconstruct the pairing by reading back through the conversation.*

That is the correct fix. I wrote it down. It then recurred three more times, and the last recurrence reached the outside world — a document generated for one recipient was delivered to a different one.

Being right was not the problem. **A prevention with no artifact that can fail is not a prevention.** It is a note.

### Escalation

The last three incidents landed within four days of each other, while the fixes were accumulating. That is the signal worth watching: not the incident count, but whether the interval is shrinking. Point-fixes that address symptoms let the underlying rate rise while the paperwork suggests progress.

---

## The trap inside my own fix

The repair for family 1 uses two distinct artifacts. At ingest, each fetch is bound immediately to its source. Then, after the batch payload exists and before staging, a separate verification read confirms that each source resolves to the entity the payload claims.

My first version was **worthless**, and I nearly shipped it.

The first gate compared the batch against its ingest map. But if that map is the *same* artifact the batch was built from, a crossed pairing produces a matching crossed map, and the gate passes every time. It has the shape of a check and the content of a mirror.

The fix was cheap once seen: **require the independent verification receipt to postdate the batch.** That cannot prove a second read happened, but it proves the receipt was not the pre-scoring ingest artifact.

> **When you add a verification step, ask what it compares against. If both sides descend from the same act, it verifies nothing.**

This is the failure mode I'd now look for first in anyone's eval harness — including LLM-as-judge setups where the judge and the generator share a prompt, a context, or a retrieval pass.

---

## What the pipeline looks like now

| Step | Gate | Mechanical? |
|---|---|---|
| Sources arrive | Identifier check across the tracker and exclusion log for 14 source platforms | Yes |
| Fetch in parallel | Write the source→entity ingest map **immediately**, before any scoring | No — but the staging gate catches its absence |
| Score | Normalized name-plus-title check — catches cross-platform twins | Yes |
| Stage | Re-read entity must match the payload; independent verification receipt must postdate the batch | Yes |
| Post-hoc edits | Pointer corrections require a confirmed entity, and write **both** stores | Yes |

Three design decisions worth preserving:

**Verify only what you commit.** A crossed pointer on a discarded record is harmless. A typical batch scores ~22 and stages ~5, so verification costs five re-reads, not twenty-two. Scoping it to committed records is what makes it cheap enough to run every single time — and a gate you skip because it's expensive is a gate you don't have.

**Trigger on the strictest available fact.** I first gated duplicate-blocking on whether a prior copy had reached its *final outcome*. Wrong choice: an outcome may never arrive. Gating on whether it was *committed* is stricter, needs no tuning, and catches the duplicate while the first copy is still in flight.

**Leave the exceptions open, but make them loud.** Every gate has an override flag. Legitimate repeat processing exists. The point isn't to make it impossible — it's to make it a decision someone typed rather than a default nobody noticed.

---

## The rule

> **A prevention that depends on remembering is not a prevention.** Either it fails loudly on its own, or it fails silently — and the more times you write it down, the more confidently it fails.

And the corollary, from the incident where I "fixed" a wrong pointer by writing a different wrong pointer:

> **A correction made without verification is worse than the original error.** It carries false confidence and closes the investigation.

---

## Why I keep an incident registry at all

Every incident above lives in a versioned registry: what was caught, what the authoritative correction was, and what gate now prevents it. Rules live in the agent's operating instructions; the registry holds incidents and reasoning.

The split matters. Operating instructions get long, and long instructions get skimmed — by humans and models alike. The registry can be as detailed as it needs to be because nothing has to read it in full at runtime. It's the difference between a policy and a case history, and you need both.

If you're building multi-agent systems and you don't have one, the thing you're missing isn't documentation. It's the ability to notice that you've fixed the same thing six times.

---

## What would prove this wrong

The three earlier mechanical checks held within the failure shapes they covered. The rebuilt identity and deduplication gates are new, so their durability is still a prediction—not yet an outcome.

Over the next fifty records, I am tracking three tests:

| Test | Target |
|---|---|
| Crossed pointers reaching a committed record | 0 |
| Records committed twice | 0 |
| A known duplicate fixture blocked before fetching | 100% of verification runs |

The third test needs a controlled fixture. A normal batch containing no duplicates can legitimately produce zero blocks; that says nothing about whether the gate ran. Replaying a known duplicate makes silence a failure signal.

If a crossed pointer reaches a committed record, a record is committed twice, or the fixture passes the pre-fetch gate, the rebuild has failed its own claim. I will report the result after fifty records either way.

### Fifty-record checkpoint — 2026-09-16

The observation window is complete: fifty consecutive records were committed after the frozen baseline.

| Test | Observed result | Status |
|---|---:|---|
| Crossed pointers reaching the committed set | 0 recorded | No live escape was found in the committed window |
| Records committed twice | 0 | The fifty-record window contained no duplicate source address or duplicate record identity |
| Known duplicate fixture blocked before fetching | Not completed at this layer | A duplicate regression was blocked later at staging, but the full pre-fetch fixture run was interrupted by the store's lock gate and was not rerun |

This is not a full validation of the rebuilt gate. The live window produced no recorded identity escape or duplicate commit, but observation alone cannot show that a silent gate ran. The controlled test promised above was not completed at the pre-fetch boundary, so the checkpoint remains open on that claim.

The next verification run must preserve a receipt showing that a known duplicate was presented to the pre-fetch gate, blocked before retrieval, and left every downstream store unchanged. Until that receipt exists, the strongest supported conclusion is narrower: **the live observation window was clean; the controlled pre-fetch claim remains unproven.**

---

*The workflow is human-in-the-loop by design: its stages are predefined, components are agentic, and consequential decisions remain with the human orchestrator. It is not an autonomous agent, a productized framework, or production distributed infrastructure. The repository still documents the architecture rather than shipping a runnable implementation; this is an incident analysis, not an execution trace.*

---

## Numbers and denominators

Every quantitative claim above, with what was counted and against what. Enforced mechanically before publication.

**This records that a denominator was written, not that it is correct.** Whether a stated denominator matches its claim stays a human judgement — which is precisely what the errors in this note were.

| Value | What was counted | Denominator |
|---|---|---|
| **93 of 371** | Records whose source identifier no matching pattern could read | All records in the store carrying a source pointer, at time of measurement |
| **25%** | 93 ÷ 371 | as above |
| **100%** | Target rate for a planted duplicate fixture being blocked before any retrieval | All verification runs, not all batches. A batch containing no duplicate legitimately blocks nothing, which is why the test uses a fixture rather than observation |
| **50 records** | Consecutive records in the post-baseline checkpoint window | The predefined checkpoint window; each record was counted once |
| **0 crossed pointers** | Recorded crossed-pointer escapes reaching the committed set | The 50-record checkpoint window; absence was checked against the committed tracker and incident record, not against discarded candidates |
| **0 duplicate commits** | Duplicate source addresses or duplicate record identities in the committed set | The same 50 records |
