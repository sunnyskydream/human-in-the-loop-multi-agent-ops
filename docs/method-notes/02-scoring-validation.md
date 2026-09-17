# A Score Is Not Evidence Until Its Outcomes Are Comparable

A scoring system can assign every record a number, route the highest bands into expensive work, and accumulate months of outcomes without producing evidence that the score works.

I learned this while auditing the decision layer of a governed, human-in-the-loop multi-agent workflow. The system evaluates more records than a human can examine deeply, assigns each one a priority, and reserves individualized work for the survivors. Outcomes return to a tracker and are meant to sharpen later decisions.

The loop looked measurable. It had scores, bands, timestamps, dispositions, and downstream outcomes.

It was not yet measurable.

The first audit exposed four separate failures: **label normalization, denominator alignment, outcome maturity, and model-version provenance**. Together they produced a reassuring conclusion that the stored evidence did not support.

This note examines the measurement contract behind the repository's human-approved reflection loop. Before an outcome can update memory, the system has to prove that the comparison is well formed.

---

## Two queries disagreed, and the disagreement was the evidence

The audit began with two queries over the same tracker. One returned **56** records in the highest priority band. Another returned **92**.

Both results appeared during the same review. The smaller number was used without reconciling it against the larger one.

The difference was one character:

```text
A-   ASCII hyphen-minus
A−   Unicode minus sign, U+2212
```

Thirty-six valid records used the second form. One parser normalized it; the other required the first form and silently classified those records as unscored.

That single normalization failure changed the headline:

| Measure | First result | Corrected result |
|---|---:|---:|
| Highest-band records | 56 | **92** |
| Highest-band resolved outcomes | 23 | **47** |
| Highest-band advances | 0 | **2** |
| Records classified as unscored | 137 | **101** |

The defect was not that Unicode exists. It was that two computations claiming to measure the same cohort produced different totals and no invariant forced them to agree.

> **A result is not validated because the query ran. When two paths define the same cohort, disagreement is a failed gate.**

The mechanical repair is straightforward: normalize labels at write time, normalize again at read time, and assert that every stored label maps to exactly one member of a closed vocabulary. The more important repair is procedural: every analysis begins with cohort reconciliation before it computes a rate.

---

## Comparable rates require comparable denominators

The first analysis then made a second error. It compared zero advances among **23 resolved records** with a baseline advance rate calculated across **all records**, including unresolved ones.

The numerator described the same event. The denominator did not describe the same population.

After normalizing the labels and restricting every band to resolved outcomes, the result was:

| Priority band | Resolved | Advanced | Advance rate among resolved |
|---|---:|---:|---:|
| Highest | 47 | 2 | **4.3%** |
| Middle | 33 | 5 | **15.2%** |
| Practice or exploratory | 10 | 2 | **20.0%** |
| Unscored | 63 | 5 | **7.9%** |

The ordering was opposite the intended direction. The highest band advanced at roughly one quarter of the middle band's observed rate.

That result was not a verdict. A two-sided exact comparison between the highest band and the two lower scored bands produced **p = 0.081**. The groups were small, differently aged, and created under scoring rules that changed during the observation window.

But it was no longer reasonable to call the result unremarkable. It was an early warning.

This distinction matters in any selective pipeline. If unresolved records remain in the denominator, newer cohorts will look worse merely because they have had less time to advance or close. If resolved records are used for one band and all records for another, the comparison has no stable interpretation.

The outcome contract now requires every reported rate to name both parts:

```text
event / eligible comparison population
```

"Conversion rate" is not a complete metric definition. "Advanced records divided by records that either advanced or reached a terminal no-advance state" is.

---

## Resolution time is censored, and silence is a state

The tracker contained **334** dated records. At the audit date, **181**, or 54%, had no recorded terminal outcome or advance. Many belonged to recent cohorts, so that figure could not be labeled ghosting.

The older records still showed the problem. Among **168 records more than 60 days old, 65, or 39%, remained unresolved**.

Another calculation found that 90% of the **observed dated rejections** occurred within 60 days. It was tempting to turn that into a planning statement: wait two months and the cohort will be 90% resolved.

That inference is invalid. The latency distribution contains only records that eventually produced a dated rejection. It excludes the unresolved records whose missing outcomes are the reason the measurement is difficult.

> **A percentile among observed closures is not a resolution forecast for the full cohort.**

The checkpoint therefore cannot fire on a projected calendar date or on records submitted. It fires on observed, comparable outcomes within the group being tested. A volume trigger can arrive quickly while the relevant band remains almost entirely unresolved.

Unresolved is not an inconvenience to delete from the analysis. It is a tracked state with at least three possible mechanisms: the process is still active, the outside party closed it without returning a signal, or the tracker failed to capture the closure. Those mechanisms require different operational responses even when they occupy the same cell today.

---

## A historical score without a model version has no stable meaning

The scoring rules changed eight times during the period covered by the audit. Some changes altered thresholds. Others changed what the score meant.

The tracker recorded the resulting tier but not the model version, component scores, or whether a tier had been assigned prospectively or reconstructed later. The label "highest band" therefore joined decisions made under different contracts.

The system had outcomes, but it could not tell which decision rule produced them.

Every new decision record now needs this minimum provenance:

```text
scoring model version
decision timestamp
fit classification
pursuit or value score
pursuit band
final allocation tier
outcome state and outcome timestamp
prospective or retrospective assignment
```

Backfilling is especially dangerous. Once a reviewer knows which records advanced, assigning historical tiers can manufacture the expected relationship without any deliberate falsification. A retrospective score must therefore be assigned outcome-blind, labeled retrospective, and reported separately from prospectively scored records.

Versioning is not administrative detail. It is what makes an outcome attributable to a decision rule.

---

## One score was carrying three different decisions

The audit also exposed a modeling problem beneath the data-quality failures. The original tier mixed three questions:

1. Can this record succeed against the requirements?
2. Is success valuable enough to justify the cost of pursuit?
3. How much of the system's limited attention should this source or category receive?

Those questions should not share one validation target.

The rebuilt model separates them:

| Decision | What it represents | What can test it |
|---|---|---|
| **Fit** | Evidence that the record can clear the substantive requirements | Advancement and later-stage progression among mature, comparable records |
| **Pursuit value** | Whether the possible outcome justifies time, constraints, and opportunity cost | Continued interest, realized value, withdrawal, and cost-to-pursue measures |
| **Search allocation** | Where the next unit of sourcing or intake effort should go | Target-class density and calibrated outcomes by source or segment |

The final allocation tier may combine fit and pursuit value because operations require one routing decision. It should not then be treated as a pure probability estimate. A record can have strong fit and weak pursuit economics, or modest fit and unusually high strategic value.

This changes the validation plan. **Fit** should show monotonic progression if it is useful. **Pursuit value** should explain whether the progressed outcome was worth seeking. **Allocation** should improve the yield of scarce review effort. Testing all three against one early-stage event would recreate the ambiguity the split was designed to remove.

> **Before asking whether a score predicts, state what decision the score makes and which outcome could prove that decision useful.**

---

## The same measurement contract across four domains

The record and outcome names change, but the controls do not:

| Contract | Demand generation | Clinical trial screening | Grants | Procurement |
|---|---|---|---|---|
| **Closed, normalized labels** | Lead grades and lifecycle stages | Eligibility tiers and enrollment stages | Eligibility and review bands | Supplier-risk and qualification tiers |
| **Comparable denominator** | Matured leads eligible for the measured stage | Assessed referrals reaching an enrollment decision | Decided applications within one cycle | Completed evaluations within one sourcing round |
| **Model-version provenance** | Scoring-model and routing-rule version | Protocol version and eligibility-criteria revision | Program criteria and reviewer-rubric version | Qualification policy and weighting version |
| **Censoring-aware state** | Open opportunity vs lost vs missing update | Pending assessment vs excluded vs lost to follow-up | Pending decision vs declined vs withdrawn | Pending diligence vs rejected vs abandoned |
| **Outcome-blind backfill** | Historical rescoring without revenue labels exposed | Eligibility replay without enrollment outcomes exposed | Eligibility replay without award status exposed | Risk replay without award decision exposed |

The common requirement is not a particular statistical test. It is a traceable relationship among the record, the rule that scored it, the population used for comparison, and the outcome that rule was intended to improve.

---

## The rebuilt measurement gate

The next review cannot repeat the first audit with cleaner punctuation and call that validation. It must be prospective.

Before the checkpoint can fire:

- every new record must carry a scoring-model version and separated decision components;
- label normalization must reject any value outside the closed vocabulary;
- outcome fields must contain state rather than advice or free-text instructions;
- withdrawals, external closures, and substantive rejections must remain distinguishable;
- cohort counts must reconcile across independent queries; and
- each comparison group must reach an adequate resolved count under the same model version.

The trigger is based on observed resolved records in the group that matters, not a date forecast and not total pipeline volume.

The result must be reported even if the ordering remains inverted. If the highest fit classification does not outperform the next fit band after comparable prospective cohorts mature, the fit model needs rebuilding. If fit predicts progression but the pursuit layer does not improve realized value or effort allocation, that layer needs a different outcome contract.

---

## What would prove this wrong

This note makes three claims that future evidence can overturn:

- **Normalization and versioning are load-bearing.** If independently implemented queries continue to agree without them, their role has been overstated.
- **The early inversion is a warning rather than a verdict.** A prospective, single-version cohort may restore the intended ordering; if it does, the historical inversion reflected cohort and model drift rather than a broken fit rule.
- **Separated decisions require separated outcomes.** If one outcome consistently validates fit, pursuit value, and allocation without hiding tradeoffs, the three-contract split is unnecessary.

The first audit did not show that the scoring system failed. It showed that the system had not preserved the evidence required to tell.

> **A score becomes operational evidence only when its label is stable, its cohort is comparable, its rule is versioned, and its outcome matches the decision it was designed to make.**

---

## Numbers and denominators

**Evidence status.** The audit was performed against the tracker state on 2026-08-27. No row-level snapshot was preserved, so the cohorts below cannot be reconstructed in full today. An independent later review recomputed the figures and exactly corroborated the ones that do not drift with time — the count of records more than 60 days old, and the count of mislabelled highest-band records — and reproduced the exact test result; the remaining cohorts could be corroborated in direction but not reproduced row for row. This note is therefore an independently reviewed retrospective measurement, not a reproducible benchmark. Future measurement notes require a frozen audit-date snapshot.

| Value | What was counted | Denominator | Source |
|---|---|---|---|
| **56 → 92** | Records classified in the highest priority band before and after label normalization | All 334 dated records | Normalized tracker query, verified 2026-08-27 |
| **23 → 47** | Highest-band records classified as resolved before and after correction | The corrected 92-record highest-band cohort | Same tracker query |
| **0 → 2** | Highest-band records that advanced before and after correction | The corrected 47 resolved highest-band records | Same tracker query |
| **137 → 101** | Records with no assignable band before and after correction | All 334 dated records | Same tracker query |
| **36** | Highest-band records written with U+2212 rather than an ASCII hyphen | The corrected 92-record highest-band cohort | Label audit |
| **4.3%** | Highest-band advance rate | 2 advances among 47 resolved highest-band records | Corrected outcome query |
| **15.2%** | Middle-band advance rate | 5 advances among 33 resolved middle-band records | Corrected outcome query |
| **20.0%** | Practice-band advance rate | 2 advances among 10 resolved practice-band records | Corrected outcome query |
| **7.9%** | Unscored advance rate | 5 advances among 63 resolved unscored records | Corrected outcome query |
| **p = 0.081** | Two-sided Fisher exact comparison of highest band with the two lower scored bands | 2 / 47 against 7 / 43 | Exact test recomputed 2026-08-27 |
| **334** | Records carrying a parseable dated submission | All tracker rows with a valid date at the audit date | Tracker audit |
| **181 / 54%** | Records with no terminal outcome at the audit date | All 334 dated records; the figure is inflated by immature recent cohorts and is not a ghosting rate | Tracker audit |
| **168** | Records more than 60 days old at the audit date | All 334 dated records | Tracker audit |
| **65 / 39%** | Aged records that remained unresolved | The 168 records more than 60 days old | Tracker audit |
| **90% within 60 days** | Latency percentile among observed dated rejections only | 145 dated closures; censored because it excludes the 39% of aged records that never closed, so it is not a resolution forecast | Closure-latency audit |
| **Eight scoring-rule changes** | Recorded scoring-rule revisions during the observation window | Change log from 2026-05-25 through 2026-08-27 | Internal change log |

---

*This method note reports aggregated operating measurements from one governed workflow. It is not an execution trace, a benchmark, or a claim that the observed rates transfer to another system. The workflow remains human-in-the-loop: automated components prepare and check evidence, while consequential routing and rule changes require human approval.*
