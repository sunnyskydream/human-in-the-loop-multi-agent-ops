# Preparation Is Not Coverage Until the Questions Arrive

A preparation system can accumulate the right evidence, predict dozens of plausible questions, and still fail at the moment of retrieval.

I found this while comparing the documents prepared before consequential evaluation sessions with the questions later captured from those sessions. The original model treated preparation as one artifact: requirements, stakeholder intelligence, evidence, and spoken answers lived in a packet whose completeness was judged by inspection.

That collapsed three different layers:

1. an **evidence layer** that should grow when new facts arrive;
2. a **compression layer** that holds the role's three fixed capability claims; and
3. a **retrieval layer** containing the spoken drill the human actually memorizes.

The distinction changed the measurement problem. Coverage is not the number of questions predicted or the number of facts stored. It is whether an observed question fits the fixed capability structure and whether the required evidence reached the retrieval layer in a usable form.

---

## Stage labels no longer bound question depth reliably

The workflow originally prepared early-stage screens mainly for background, motivation, logistics, constraints, and broad fit. Later functional rounds received the deeper implementation, diagnostic, and measurement answers.

Observed sessions did not preserve that boundary. Across three early-stage screen records, evaluators asked role-specific functional questions about experimentation, lifecycle diagnosis, measurement-framework design, technical implementation, and how a new program would be built. This was an observed pattern, not a hypothetical future shift: in this corpus, the stage label was already a weak predictor of question depth.

Low-cost AI transcription may contribute to this shift. A screener can ask a structured question and relay the captured answer without personally reconstructing its technical detail. Standardized scorecards and shared question banks could produce the same pattern without AI, so the current evidence does not establish the cause.

The possible mechanism remains testable: as structured capture becomes cheaper, this pattern may become easier to operate at scale. The preparation rule does not depend on proving that mechanism. Pillar-level functional depth now begins with the first screen and carries through every later stage without attempting to enumerate every possible question.

---

## Evidence, compression, and retrieval are different artifacts

The background preparation file is an evidence layer. It contains the requirements, submitted evidence, stakeholder intelligence, company context, canonical stories, and operating constraints for the round. It should change when genuinely new information appears.

The Rule of Three is the compression layer. For a role, it fixes three capability claims and binds each one to verified evidence and the relevant requirement hook:

```text
verified evidence -> capability claim -> requirement hook
```

The claims remain fixed across stages. Their order, evidence depth, and stakeholder emphasis can flex. This creates a stable through-line across a thirty- or forty-five-minute conversation without requiring the system to predict every question one by one.

The spoken drill is the retrieval layer. It is the artifact the human rehearses and carries into the session. A fact can be correct in the background file and still be operationally unavailable if it never reaches the drill or if repeated rewording makes the memorized version unclear.

The useful architecture is therefore:

```text
new source evidence
        |
        v
background preparation
        |
        v
three fixed capability pillars
        |
        v
stable spoken drill
        |
        v
observed questions and post-session audit
```

The version that matters for retrieval is the final spoken drill, not every intermediate evidence document.

---

## One measured case located the churn in the wrong layer

I first treated document count as evidence of prediction cost. A version comparison contradicted that interpretation.

In the selected case, background preparation grew from **43 to 58 to 62 to 62 blocks** across four versions. Consecutive block-sequence similarity was **81%, 90%, and 89%**. Each revision absorbed new stakeholder information, deeper functional answers, or a corrected ownership boundary. The document grew because the evidence changed.

The spoken drill behaved differently. Across seven versions, its length stayed approximately constant at **34 to 35 blocks**, while consecutive similarity measured **35%, 70%, 91%, 80%, 89%, and 86%**. The first revision was close to a rewrite; later versions continued rephrasing an artifact of roughly the same size.

This measurement does not show that every changed sentence was unnecessary. Block similarity is not semantic quality, and a rewrite can improve delivery. It shows where the stability question belongs: the evidence layer expanded by accretion, while the retrieval layer changed repeatedly without gaining capacity.

The same case exposed a propagation delay. A stakeholder-resourcing fact was recorded in an earlier session debrief but did not appear in the background preparation until **33 days** later. Under a packet-level model that looks like generic missing coverage. Under the layered model it is more precise: source evidence existed, but the propagation path into the preparation layer failed.

The case was selected because it had the most document versions. It describes one company and one round, not a representative sample. It supports a directional finding—accretion in the evidence layer and rewording in the retrieval layer—not a general churn rate.

---

## Count pillar coverage before counting predicted questions

Question-by-question coverage rewards enumeration. The Rule of Three is designed to avoid that cost by giving unexpected questions a stable capability home.

The first classification is therefore structural:

- **Pillar-covered:** the question tests at least one of the three role-level capability claims.
- **Outside-pillar:** the question exposes a material capability that the role card failed to represent.

Only then does the audit test retrieval within the covered pillar:

| Class | Condition | Repair |
|---|---|---|
| **P-R — Ready** | The question maps to a pillar and the needed evidence was usable in the final drill | Preserve the stable module |
| **P-B — Background only** | The pillar held and the evidence existed in background prep, but it did not reach the drill | Repair propagation into the retrieval layer |
| **P-L — Live assembly** | The pillar held and the response was assembled adaptively from truthful evidence during the session | Validate afterward; promote only if the example is reusable |
| **P-M — Misprepared** | The pillar held, but the drill supplied stale, irrelevant, or unsafe evidence | Correct the evidence and its recency or ownership boundary |
| **X — Outside pillar** | The question does not map to any of the three capability claims | Reassess the role card rather than merely adding a question |

This produces two primary measurements:

```text
pillar coverage = questions mapped to at least one pillar / substantive question intents
drill retrieval = P-R / pillar-covered question intents
```

Background-only, live-assembly, misprepared, and outside-pillar counts remain visible as a failure distribution. They should not be compressed into one top-line percentage because each requires a different repair.

Live assembly is not automatically a failure. It can show that the Rule of Three worked as intended: an unanticipated question reached a stable pillar, and the human combined relevant evidence without needing a prewritten answer. Post-session review separates productive adaptation from unsupported improvisation. A newly surfaced detail can complement the story bank after its source, ownership, and metric boundary are confirmed. An example without a recoverable metric can remain qualitative rather than being discarded or converted into a number.

That distinction supports a third measure:

```text
supported response coverage = (P-R + validated P-L) / pillar-covered question intents
```

Drill retrieval still measures what was rehearsed. Supported response coverage measures whether the prepared structure plus valid human adaptation could answer the question.

The unit is a substantive question intent, not every sentence ending in a question mark. A main question and several follow-ups can test the same capability. Administrative, scheduling, and rapport questions remain outside the denominator. The strongest available source record determines whether the audit can recover exact wording, question intent, or only a broad topic.

---

## Source fidelity bounds the coverage claim

Preparation coverage and transcript accuracy are different questions. The former asks whether the prepared capability structure could answer what was asked. The latter asks how faithfully the preserved record represents the session. Coverage cannot be stronger than the source used to reconstruct the question.

The audit therefore classifies every session record before it extracts question intents:

| Source class | What it preserves | What the coverage audit may claim |
|---|---|---|
| **Verbatim record** | Speaker-attributed wording and sequence, subject to capture quality | Exact wording, sequence, and question intent |
| **Structured summary** | Compressed topics, decisions, and attributed facts | Question intent or topic after corroboration; not exact wording |
| **Contemporaneous notes** | What the operator recorded as salient | Topic-level evidence; not complete session coverage |
| **Later reconstruction** | A retrospective account of the session | A review lead only; never denominator evidence without corroboration |

A derived summary can initiate review, but it cannot silently change canonical evidence or prove that a phrase was spoken. Corrections live in the derived review layer while the original source remains unchanged. This preserves both the source and the evidence that its transformation introduced uncertainty.

This note is therefore not a transcript-accuracy benchmark. Source fidelity is an admission rule for the coverage denominator: exact-wording claims require a verbatim record, while lower-fidelity sources support only the level of inference their structure preserves.

---

## Version control supports the measurement; it is not the thesis

The evidence layer may legitimately change when a stakeholder is identified, the date or format changes, new operating facts arrive, or an ownership correction is confirmed. Counting those revisions as churn would punish the system for learning.

The retrieval layer needs a stricter contract:

- build a provisional drill from the fixed Rule of Three when stakeholder information is incomplete;
- mark the few sections allowed to flex—order, evidence depth, role-specific ask-backs, and logistics;
- confirm whether new information has arrived before rebuilding;
- propagate verified changes without rewriting stable modules; and
- freeze the final drill used for the session.

The preparation receipt therefore needs both layer identities:

```text
background preparation version
final spoken-drill version and hash
role-level Rule-of-Three card
source and evidence-bank versions
freeze timestamp
```

This receipt does not make the answers correct. It establishes which evidence layer and retrieval layer the observed questions are testing.

---

## The prospective coverage loop

The retrospective case located a propagation and stability problem. It did not produce a valid coverage rate because the preparation artifacts were not frozen prospectively for that purpose and the available session records vary in fidelity.

For each future session:

1. Fix the role-level Rule of Three before the round sequence begins.
2. Allow the background preparation to absorb verified new evidence.
3. Freeze the final spoken drill before the session.
4. Preserve the strongest available source record without editing it to match the debrief.
5. Classify the source record and extract substantive question intents only at the granularity that class supports.
6. Map each intent first to Pillar-covered or Outside-pillar, then to P-R, P-B, P-L, or P-M.
7. Attach the pillar, drill section, background evidence, and source excerpt used for the classification.
8. Send ambiguous mappings, every live-assembly example, and every misprepared case to human review.

The first prospective result should report per-session and pooled counts rather than present one small corpus as a benchmark. A higher pillar-coverage rate would support the compression design. A lower drill-retrieval rate paired with strong validated live assembly would indicate adaptive coverage rather than failure; low results on both would show that pillar evidence did not reach the human in usable form.

---

## What would prove this wrong

This method assumes the Rule of Three covers enough of the role's real question space to reduce one-by-one prediction. If material questions repeatedly fall outside all three pillars, the compression is hiding capability gaps rather than reducing preparation cost.

It also assumes retrieval stability matters. If repeated drill rewrites produce better relevance and delivery without confusion or claim drift, similarity loss is not an operational defect.

Finally, the method assumes observed source records recover the questions that mattered. If summaries and notes systematically omit difficult exchanges, the measured denominator will reward preparation against an incomplete record.

The claim is not that every question can be predicted or that a spoken drill should never change. It is that preparation coverage should be tested at two levels: whether the fixed capability structure absorbed the question and whether the correct evidence reached the retrieval layer.

---

## Numbers and denominators

| Value | What was counted | Denominator | Source |
|---|---|---|---|
| **43 / 58 / 62 / 62 blocks** | Non-empty paragraphs and table cells in four consecutive background-preparation versions | Each document independently | `python-docx` extraction, 2026-08-27 |
| **81% / 90% / 89%** | Block-sequence similarity across the three consecutive preparation-version pairs | Each consecutive pair | `difflib.SequenceMatcher`, 2026-08-27 |
| **34 to 35 blocks across seven versions** | Non-empty paragraphs and table cells in the spoken drills | Each drill independently | `python-docx` extraction, 2026-08-27 |
| **35% / 70% / 91% / 80% / 89% / 86%** | Block-sequence similarity across six consecutive drill-version pairs | Each consecutive pair | `difflib.SequenceMatcher`, 2026-08-27 |
| **33 days** | Elapsed time from the source debrief recording the stakeholder-resourcing fact to its first appearance in the background preparation | Calendar days between the two dated artifacts | Debrief and document timestamps, verified 2026-08-27 |
| **Three early-stage screen records** | Corroborating records containing role-specific functional questions | Three selected observed screens; illustrative evidence, not the full screen corpus and not a rate | Source records, structured debriefs, and operator confirmation, reviewed 2026-08-27 |

**Selection limitation:** the version-comparison figures describe one deliberately selected case—the company and round with the most versions. The three-screen observation is corroborating evidence rather than an exhaustive screen denominator. Neither is a sampled estimate, a cross-company rate, or evidence that the same direction will appear everywhere.

No aggregate question-coverage rate is reported. The corpus has not yet been coded prospectively at the pillar and retrieval levels against frozen spoken drills.

---

*This method note reports one retrospective version comparison and defines a prospective coverage contract for a governed, human-in-the-loop preparation workflow. It is not a benchmark or a claim that the observed similarity patterns transfer to another system. Automated components can organize evidence, compare versions, propose mappings, and surface gaps; source interpretation, ambiguous coverage decisions, canonical-memory changes, and consequential responses remain under human approval.*
