# The Document Passed. It Was Still Wrong

I operate a governed, human-in-the-loop multi-agent workflow for high-volume, evidence-sensitive personalized-document production. Its predefined stages coordinate through shared work artifacts, and a human approves anything consequential.

The workflow can produce a document whose sentences are grammatical, whose facts all appear somewhere in the evidence, whose file opens cleanly, and whose page count is correct — and still produce the wrong document.

This is the downstream companion to [Operating Note 01](./01-parallel-fetch-identity.md), which covers identity failures during retrieval and staging. This note begins after a record has been selected and follows correctness through generation, rendering, and regeneration.

I learned this through three families of failures that initially looked unrelated:

- Evidence or positioning from one record leaked into another.
- A real metric migrated from its original action to a more impressive adjacent claim.
- Correct text became incorrect as a rendered or regenerated artifact.

The useful abstraction is that they violate three different contracts: **record isolation, claim binding, and artifact integrity**.

As of 2026-08-23, 24 stable entries in the private incident registries map to these contracts:

| Contract | Registry entries |
|---|---:|
| Record isolation | 3 |
| Claim binding | 10 |
| Artifact and state integrity | 11 |

This count is not comparable to Operating Note 01's 13 timestamped incidents over 54 days. These registries were consolidated retrospectively. They support a count of observed failure entries, not a clean incident rate or elapsed-time claim; several entries describe recurring failure shapes rather than one uniquely timestamped event.

A quality gate that checks only one contract cannot certify the document.

---

## Contract 1: evidence must stay inside its record boundary

Personalized generation begins with reuse. A shared template, a canonical evidence base, and a common style system are what make volume possible.

They also create leakage paths.

In one batch, two sibling variants inherited mixed positioning and skills. Each phrase was valid somewhere in the system. It simply belonged to the other variant. In another case, independent work appeared under an organization heading, changing the apparent ownership of the work without changing a single factual word. Internal review language also surfaced in an external document because both artifacts were assembled from nearby text.

These were not hallucinations. They were boundary failures.

The early checks asked whether each statement was supported by the global evidence base. That is necessary but insufficient. In a personalized workflow, the stronger question is whether the statement is supported **for this record, in this section, under this owner, for this audience**.

> **Globally true does not mean locally admissible.**

The prevention is a record contract written before generation:

| Boundary | What must be locked |
|---|---|
| Record | Identity, source body, and target purpose |
| Positioning | Center of gravity and explicit exclusions |
| Evidence | Approved facts and ownership scope |
| Section | What kind of content may appear here |
| Audience | Internal analysis versus released wording |

The reviewer then compares sibling outputs pairwise. A single-document review can confirm that every phrase sounds plausible; a pairwise review can reveal that two variants are sharing the same center of gravity or carrying each other's specialized language.

Shared evidence should reduce duplication of research. It should not erase the boundary between outputs.

---

## Contract 2: a metric belongs to a claim tuple

The second failure family was more subtle because every component was true.

One source bullet described analytics work across several business measures. A neighboring bullet described data-quality improvements across a large operating scope. During compression, the scope number migrated into the analytics sentence. The resulting statement was fluent and numerically sourced, but it claimed that the analytics work covered the entire larger scope.

Elsewhere, a lead count drifted upward in the funnel and became customers, conversions, pipeline, or revenue. In another case, work performed in partnership with a technical team became sole implementation ownership because the stronger verb produced a cleaner sentence.

The model had not invented a number, an activity, or an outcome. It had invented the **relationship among them**.

That is why entity-plus-number matching does not protect claim integrity. A metric has to remain bound to its complete source tuple:

```text
actor + action + object + metric or scope + outcome + ownership level
```

If two adjacent source tuples are:

```text
analyzed A across measures B, C, and D
improved data quality across scope E
```

then this sentence is not supported:

```text
analyzed A across scope E
```

All of its nouns may be present in the source. Their relationship is not.

Here, verb altitude means the ownership level encoded by the action verb. *Partnered*, *led requirements*, *directed*, *implemented*, and *owned* are not stylistic synonyms.

Three constraints now stay immutable during compression:

**Funnel stage.** Leads do not become customers or revenue unless a newer source proves the transition.

**Verb altitude.** The action verb remains matched to the ownership relationship in the source.

**Metric object.** Every number remains attached to the exact action and object it originally measured.

> **Claim integrity is relational. The dangerous error is often not a false fact but a false join.**

This is also why dense summaries are risky. Combining two bullets can save a line while silently changing which action produced which result. Concision is not neutral when the source contains adjacent quantified evidence.

---

## Contract 3: the rendered artifact must preserve the approved state

The third family began after the language was already correct.

A hyperlink existed in the document structure but rendered like ordinary text. A heading inherited list indentation and shifted even though its words were unchanged. A single trailing word sat alone on a line. One page became visibly sparse while an automated check still reported the expected page count.

An early repair solved one wrapping problem by compressing typography across the entire document. The file fit, but the intervention changed far more of the artifact than the defect required.

These failures exposed a category error: checking document text is not the same as checking a document.

The final artifact includes layout, hierarchy, pagination, hyperlink affordance, typography, and the relationship between editable and exported versions. Some of those properties exist only after rendering. Others live below the paragraph-text layer in styles, runs, numbering, or hyperlink metadata.

The correct review loop is therefore multimodal:

1. Inspect structure and style at the document-object level.
2. Render the artifact.
3. Inspect pages visually, not only by count.
4. Make the smallest local repair.
5. Render again and verify that the repair did not create a new defect elsewhere.

> **If the defect exists only after rendering, a text-only reviewer cannot close it.**

### The fix must survive regeneration

Artifact integrity also has a time dimension.

In one incident, the editable document was corrected while its export or regeneration source remained stale. The current file looked right, but the next generation could recreate the old error. In another, a corrected tracker coexisted with a stale full-source store; the downstream generator read the stale copy and produced a coherent document against the wrong requirements.

The repair existed. The system had not converged.

This is why every correction now has a write set:

| Surface | Closure question |
|---|---|
| Authoritative evidence | Is the approved fact correct at its origin? |
| Tracker or index | Does the operational record point to the same state? |
| Generator input | Will the next run preserve the correction? |
| Editable output | Does the reviewed document express it? |
| Export | Does the released artifact match the editable version? |
| Review record | Does the audit note describe what actually exists? |

A four-part change later made the same point from another direction. It was reported complete, but a direct filesystem check showed that one whole part had never run and one supporting artifact still carried the old interpretation.

The completion summary described intent. The filesystem described state.

> **A completion report is not a read-back test.**

---

## Why one "quality score" cannot cover all three

The three contracts fail differently and require different evidence:

| Failure | Weak check that passed | Gate that can catch it |
|---|---|---|
| Cross-record leakage | Statement exists in the global evidence base | Record contract, exclusions, and pairwise comparison |
| Metric-to-claim corruption | Entity and number both appear in source material | Full tuple trace for every quantified phrase |
| Ownership inflation | Strong verb is topically plausible | Verb altitude matched to the source relationship |
| Formatting drift | Text and page count are correct | Structural inspection plus rendered-page review |
| Stale regeneration | Current output contains the fix | Write-set audit across source, generator, output, and export |

Collapsing these into one score hides the failure mode. A document can score well on factuality while leaking another record's positioning. It can pass semantic review while rendering badly. It can look perfect today while its stale generator guarantees failure tomorrow.

The reviewer does not need to be large or clever. The gates need to be explicit about which contract they test.

---

## What the incident registry contributes

The registry is not a second set of operating instructions. It is the case history behind the gates.

Each entry records:

- the exact failure that was observed;
- the authoritative correction;
- the boundary or relationship the failure violated;
- every surface affected by the repair; and
- the mechanical or review gate that should prevent recurrence.

Concise rules belong in the creator and reviewer instructions. The registry holds the incidents and reasoning that would make those rules too long to execute reliably at runtime.

The fix method is now consistent across all three contracts:

1. Classify the failure as isolation, claim binding, artifact integrity, or more than one.
2. Correct the authoritative evidence rather than only the visible output.
3. Declare and update the full write set.
4. Regenerate every derived artifact.
5. Verify using the modality where the defect exists: semantic, structural, or visual.
6. Record the incident only after the read-back test passes.

This prevents the registry from becoming a museum of correct observations attached to a system that can repeat them.

---

## The rule

> **A generated document is not correct merely because its facts are true. The evidence must belong to this record, each metric must remain bound to its source claim, and the rendered artifact must preserve the approved state across regeneration.**

Or more compactly:

> **Correctness is not a property of the sentence. It is a relationship among evidence, record, claim, and artifact.**

---

## What would prove this wrong

The contracts come from observed failures, but the gates are new. Over the next fifty generated documents, I am tracking live escapes and controlled canaries:

| Test | Target |
|---|---:|
| Cross-record evidence reaching an approved document | 0 |
| Approved quantified or ownership claims that fail full source-tuple trace | 0 |
| Released artifacts diverging from approved editable or generator state | 0 |
| Known canaries for all three contracts detected before approval | 100% of verification runs |

The canaries deliberately introduce a wrong-record phrase, a metric moved between adjacent claim tuples, and a formatting defect or stale export. A clean batch does not prove that the gates ran. The controlled fixture makes silence a failure signal.

If any live defect escapes or any canary passes undetected, the three-contract review failed. I will report the result after fifty documents either way.

---

*The workflow is human-in-the-loop by design: its stages are predefined, components are agentic, and consequential decisions remain with the human orchestrator. It is not an autonomous agent, a productized framework, or production distributed infrastructure. The repository documents the architecture rather than shipping a runnable implementation; this is an incident analysis, not an execution trace.*

