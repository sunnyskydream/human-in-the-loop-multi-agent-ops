# Measure Segment Density Before You Scale the Motion

Qualification systems are usually judged one record at a time: does this record pass the profile? That question matters, but it hides an earlier one about the source itself: does the segment contain enough target-shaped records to justify a scaled motion?

I operate a governed, human-in-the-loop multi-agent workflow that evaluates inbound records against a fixed profile and reserves costly, individualized work for the survivors. The records could be leads, referrals, applications, or suppliers. The operating problem is the same: more possible inputs than the system can examine deeply.

This note examines the intake and allocation layer that precedes the reasoning, review, and human-approval loops described in the repository.

Four measurements changed how I allocate that attention: **segment density, disqualification-reason distribution, outcome calibration, and filter coverage**.

---

## 1. Measure the segment before optimizing the filter

I tested one expansion hypothesis against seven bounded source lists. Together they contained **408 records**. Only **two** belonged to the target class, and one of those sat beyond the intended tier.

That is a density measurement, not a qualification or conversion result:

| Measure | Definition | Question it answers |
|---|---|---|
| **Segment density** | Target-class records / all records in the bounded segment | Does this segment contain enough of what the system seeks? |
| **Qualification rate** | Qualified records / evaluated target-class records | How often do relevant records clear the full profile? |
| **Conversion rate** | Downstream outcomes / qualified or submitted records | What happens after the system advances a record? |

The observed segment density was **2 / 408, or about 0.5%**. Most records were not failing the profile. They belonged to a different class before qualification began.

A later source-level sweep answered a different question. It covered fifteen source collections, and **eleven returned no target-class record**. Total record counts were not captured for the additional sources, so no combined density exists. The first measurement was record-level density; the second was source incidence. Joining them would create a number the evidence does not support.

Separately, the two strongest survivors across the broader pipeline shared nearly the same downstream operating context. That observation did not change the density calculation. It refined the monitoring decision: the broader segment was sparse, but one narrower slice produced unusually relevant records.

The resulting policy was:

> **Monitor the narrow slice; do not scale the broad segment.** Recheck it on a fixed cadence and expect rare matches rather than steady volume.

The evidence describes a build-heavy, target-class-light source mix at the time of measurement. It does not establish why the mix existed or how mature any individual source was.

---

## 2. Make rejection reasons diagnostic

Every disqualified record in the workflow receives one primary reason from a closed list of eight. Seven reasons are based on explicit evidence such as a numeric threshold, required qualification, location or access constraint, operating mode, or level. One is interpretive: whether the record is close enough to the target class to merit further effort.

Deterministic conditions can be applied mechanically and reported. The interpretive decision goes to a human with the conflicting evidence attached. An earlier version applied that judgment silently and narrowed the pipeline without an approved rule.

The closed vocabulary made source behavior queryable. In one measured batch, one source's primary disqualifications concentrated below the required value or level floor, while another source's concentrated on required history or credentials above the supported ceiling.

That is not a qualification-rate comparison; the log does not contain the denominators needed for that claim. It is a failure-distribution observation, and it is stated here without ratios: the specific batch is no longer identifiable in the closed-reason log, so the counts cannot be re-derived from the record. The first source suggests a tighter floor. The second requires an explicit stretch policy: retain near-boundary records when the upside justifies review, or apply a ceiling when it does not.

The first implementation forced every record into exactly one reason while leaving other failed conditions in free text. That made the primary cause countable but hid multi-causal patterns. The stronger contract is:

> **Require one primary reason, permit structured secondary reasons, and keep the vocabulary closed enough to remain interpretable.**

Without that structure, a rejection field records activity but cannot explain what should change.

---

## 3. Test preferences against outcomes and filters against coverage

For months, I weighted one category heavily because it appeared to be the obvious fit. It advanced **one of 47 records, or 2.1%**, to the next external review stage, the lowest rate among the measured groups. A secondary signal performed differently: records carrying it advanced **6 of 66 times, or 9.1%**, versus **9 of 242, or 3.7%**, for everything else. The difference was approximately **2.5 times**.

That is downstream progression, not segment density or qualification. Its purpose is calibration: a source weight should have a record-count trigger for remeasurement, not an instruction to revisit it "periodically."


The same distinction between apparent activity and measured coverage appeared in deduplication. The filter caught duplicates regularly, which made it look healthy. A coverage audit found that **93 of 371 stored records, or 25%, were structurally invisible** because the matching logic recognized only one source's identifier format.

The filter was producing catches while leaving a quarter of its comparison universe unexamined.

A later source audit exposed the same issue in the density method. A record was live and reachable through its direct address but absent from the structured feed used for the census. The interface returned many rows; it did not expose the complete universe.

> **"Complete" is a claim about a method-source combination against a defined universe. A large result set is not evidence that the source exposed every record.**

Agreement between two independent retrieval routes strengthens the coverage evidence. It still does not prove completeness.

---

## The same controls in four operating domains

The measurements above are domain-neutral. What changes across functions is the record type and the cost of advancing it:

| Control | Demand generation | Clinical trial screening | Grants | Procurement |
|---|---|---|---|---|
| **Segment density** | Target accounts within a market segment | Protocol-eligible referrals within a screening pool | Eligible applications within a program | Qualified suppliers within a market |
| **Primary and secondary rejection reasons** | MQL rejection and closed-lost reasons | Screen-failure and exclusion reasons | Eligibility and review-failure reasons | Supplier-disqualification reasons |
| **Deterministic rules vs human review** | Eligibility or suppression rules vs ambiguous account fit | Hard inclusion criteria vs investigator judgment | Statutory eligibility vs merit judgment | Compliance thresholds vs strategic fit |
| **Coverage rather than catches** | CRM deduplication and identity resolution | Patient-record matching across sites | Duplicate application or entity detection | Vendor-master matching |
| **Outcome-tested weighting** | Source scoring and channel-budget allocation | Site and referral-source allocation | Program outreach allocation | Supplier-discovery allocation |

The common design rule is simple: every consequential filter should report what it examined, every rejection should produce a usable reason, and every weighting should carry a remeasurement trigger.

---

## What would change the decisions

The measurements are operating evidence from one system, not benchmarks. The conclusions should change if later evidence changes:

- If repeated sweeps show materially higher target-class density, the monitored slice should be reconsidered as a scalable segment.
- If reason distributions do not lead to different source actions, the vocabulary is descriptive rather than diagnostic.
- If independent retrieval routes disagree, any completeness claim remains open until the missing coverage is explained.

This method note reports aggregated operating measurements; it is not an execution trace.

The method is not a claim that one set of thresholds transfers everywhere. It is a claim that **density, reason structure, outcome calibration, and coverage must be measured before a high-volume qualification system can be trusted**.

---

## Numbers and denominators

| Value | What was counted | Denominator | Source |
|---|---|---|---|
| **2 / 408** and **0.5%** | Target-class records in one bounded seven-source segment | All 408 records across those seven sources | Bounded source sweep, dataset 1, verified 2026-08-27 |
| **408** | Records examined in that bounded segment | The full seven-source segment; this is the denominator above | Same source sweep |
| **2.1%** | Advances from the heavily weighted category | One advance among 47 records in that category | Outcome tracker at the audit date, 2026-08-23 |
| **6 of 66** and **9.1%** | Advances among records carrying the secondary signal | All 66 records carrying that signal | Same outcome tracker |
| **9 of 242** and **3.7%** | Advances among all other records in that comparison | The remaining 242 records | Same outcome tracker |
| **2.5 times** | Ratio of the observed 9.1% and 3.7% advance rates | The two outcome cohorts above; descriptive, not causal | Same outcome tracker |
| **93 of 371** and **25%** | Stored records structurally invisible to the original deduplication matcher | All 371 stored records in the coverage audit | Deduplication coverage audit, 2026-08-23 |
| **Fifteen source collections; eleven with no target-class record** | Source collections returning no target-class result in a later sweep | Fifteen source collections; record totals were not captured | Separate source-level sweep |

**Dataset boundary:** the **2 / 408** record-level result and the later fifteen-source incidence sweep are different datasets. The later sweep did not preserve total record counts, so no combined density exists and none is implied here.
