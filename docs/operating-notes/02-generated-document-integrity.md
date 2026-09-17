# The Document Passed. It Was Still Wrong

I operate a governed, human-in-the-loop multi-agent workflow for high-volume, evidence-sensitive personalized-document production. Its predefined stages coordinate through shared work artifacts, and a human approves anything consequential.

The workflow can produce a document whose sentences are grammatical, whose facts all appear somewhere in the evidence, whose file opens cleanly, and whose page count is correct — and still produce the wrong document.

This is the downstream companion to [Operating Note 01](./01-parallel-fetch-identity.md), which covers identity failures during retrieval and staging. This note begins after a record has been selected and follows correctness through generation, rendering, and regeneration.

I learned this through three families of failures that initially looked unrelated:

- Evidence or positioning from one record leaked into another.
- A real metric migrated from its original action to a more impressive adjacent claim.
- Correct text became incorrect as a rendered or regenerated artifact.

The useful abstraction is that they violate three different contracts: **record isolation, claim binding, and artifact integrity**.

As of 2026-08-24, 25 stable entries in the private incident registries map to these contracts:

| Contract | Registry entries |
|---|---:|
| Record isolation | 3 |
| Claim binding | 11 |
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

Question context creates another locality boundary. In one review, a correctly sourced scale metric from a data-quality workstream appeared inside an answer about a particular platform. The metric still described the right action, but its placement invited the listener to attach that scale to the platform. It was true, yet not admissible in that answer.

The reviewer now classifies every sentence as a direct answer, necessary evidence, ownership boundary, role bridge, or tangent. A claim can pass tuple trace and still fail if its answer context changes what a listener reasonably infers.

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

### Fifty-document checkpoint — 2026-09-16

The document window is complete: fifty consecutive current document packets were preserved after the baseline. Each packet contains an editable document, a rendered PDF, a regeneration source, and a review note. Forty-one of the fifty were released; seven were archived without release and two remained held.

The checkpoint exposed an instrumentation gap rather than supporting a clean pass:

| Test | Evidence available | Status |
|---|---|---|
| Cross-record evidence reaching an approved document | No confirmed live escape appears in the incident record | Not established: the full window did not receive a prospective record-isolation receipt |
| Approved quantified or ownership claims failing full source-tuple trace | Review caught and corrected claim-binding errors, but did not preserve one trace receipt for every approved claim | Not established |
| Released artifact diverging from approved editable or generator state | All forty-one released DOCX/PDF pairs passed a normalized alphanumeric-token comparison requiring at least 99.5% coverage in each direction under the stated extraction method | Partial: the screen does not prove rendered-layout parity or generator-state parity |
| Known canaries for all three contracts detected before approval | No complete three-canary execution receipt exists for this window | Not established |

**Parity-screen method.** The screen selected the 41 released `*_SUBMITTED.docx` files in the fifty-packet window and paired each with the PDF in the same packet directory whose filename stem matched exactly; nonmatching portfolio PDFs were excluded, which mattered in the nine directories that held one. DOCX body paragraphs were extracted in document order with python-docx 1.2.0 using `Document(...).paragraphs`. PDF pages were extracted in page order with pypdf 6.10.2 using the default `PdfReader(...).pages[i].extract_text()`. Both strings were lowercased and tokenized with the regular expression `[A-Za-z0-9]+`. Tokens were treated as multisets using `collections.Counter`; the multiset intersection was divided separately by the total DOCX-token and PDF-token counts. The observed minimum coverage was 99.76% from the DOCX side and 99.52% from the PDF side, both in the same packet. Token order, punctuation, layout, hyperlinks, and visual rendering were outside this screen. These figures were recomputed on 2026-09-16 against the versions named here, and that rerun is the authoritative measurement: the original one-off script was not retained, so its installed versions cannot be proven. A set-based rather than multiset-based intersection produces different minima on the same documents, which is why the tokenization rule is stated.

The result is therefore not “zero defects across fifty documents.” It is that the workflow produced and preserved the promised window without preserving enough prospective evidence to evaluate three of the four targets as written. A clean incident log cannot substitute for a canary, and an editable/rendered text comparison cannot substitute for layout and regeneration checks.

The next window needs a per-document receipt binding record identity, approved claim tuples, editable hash, rendered hash, generator hash, and canary outcome before approval. Until those receipts exist, **the contracts remain useful review rules, but the fifty-document validation claim is unproven.**

---

*The workflow is human-in-the-loop by design: its stages are predefined, components are agentic, and consequential decisions remain with the human orchestrator. It is not an autonomous agent, a productized framework, or production distributed infrastructure. The repository documents the architecture rather than shipping a runnable implementation; this is an incident analysis, not an execution trace.*

---

## Numbers and denominators

Every quantitative claim above, with what was counted and against what. Enforced mechanically before publication.

**This records that a denominator was written, not that it is correct.** Whether a stated denominator matches its claim stays a human judgement.

| Value | What was counted | Denominator |
|---|---|---|
| **100%** | Target rate for known canaries across all three contracts being detected before approval | All verification runs, not all documents. A document set containing no planted defect legitimately detects nothing, so the target is stated against controlled runs |
| **50 documents** | Consecutive current document packets in the post-baseline checkpoint window | One current packet per record; failed intermediate builds and backup copies were excluded |
| **41 released / 7 archived / 2 held** | Disposition of the fifty packet units at the checkpoint | All 50 finalized packets |
| **41 released pairs** | Released DOCX/PDF pairs that passed a normalized alphanumeric-token comparison | All 41 released packets in the checkpoint window; every pair retained at least **99.5%** of the editable and rendered token inventories in each direction, but this did not test layout or regeneration equivalence |
| **99.5%** | Minimum bidirectional token-coverage threshold used by the text-content parity screen | Each released DOCX/PDF pair independently; punctuation, layout, and token order were outside this screen |
| **99.76% / 99.52%** | Observed minimum token coverage across the window, from the DOCX side and from the PDF side | For each pair, the directional denominators were its total DOCX-token count and total PDF-token count, respectively; the reported values are the minimum of each directional ratio across all 41 released pairs. Both minima fell in the same packet. Extraction method and library versions are stated above this block; recomputed 2026-09-16, and that rerun is the authoritative measurement |

Counts of registry entries quoted in the body are absolute totals from the private incident registries at the stated date, not ratios, and carry no denominator.
