# AI-assisted proposal triage

## Assumptions and configurable rubric

This is a design, not an application. The category is Commercial Solar Installation, for a 150 kW warehouse rooftop in Windsor. All settings below are an illustrative admin configuration v1, not constants embedded in code. The admin supplies criterion definitions, evidence requirements, units, weights, scales, thresholds, rounding and tie rules. Weights must total 100%; invalid configurations cannot be published.

Mandatory gates: the bidder itself must state it holds an Ontario electrical contractor licence; WSIB clearance and at least CAD 5M liability insurance must both be confirmed. Total price must be at most CAD 260,000 and completion at most 16 weeks from award. A demonstrated failure is ineligible. B explicitly offers a future subcontractor arrangement that does not satisfy the bidder-held licence requirement, so its submitted proposal is ineligible; this does not prove B has no licence. Other missing or ambiguous evidence is flagged for clarification. Both statuses have null total and rank.

For eligible proposals, each criterion has a 0–100 subscore:

| Criterion | Weight | Admin-configured v1 scale |
|---|---:|---|
| Experience | 30% | 100 × min(qualifying project count / 15, 1) |
| Price | 25% | 100 × (260,000 − total CAD) / 260,000 |
| Timeline | 15% | 100 × (16 − weeks) / 16 |
| Warranty | 15% | 50 × min(panel years / 25, 1) + 50 × min(workmanship years / 12, 1) |
| Team and approach | 15% | 25 each for stated named lead, described crew, safety plan, performance-monitoring plan |

Experience counts only commercial installations of at least 100 kW completed within five years before the evaluation date. Reject negative values; zero price or duration requires review. Bound subscores to 0–100. Budget/deadline boundary values pass but earn zero preference points. Price measures savings relative to the budget ceiling, so compliant bids near it receive low price subscores. A structural review or progress report does not substitute for safety or monitoring. Optional-feature omissions earn zero only after reviewer confirmation; unresolved facts remain flagged.

Total = sum(weight × subscore) / 100. Use decimal arithmetic without intermediate rounding; round the total half-up to two decimals. Sort by unrounded total descending; exact ties share rank, with the next rank skipped and letters used only for display order.

**Sample-only assumptions:** A, C and E's stated experience totals are assumed confirmed inside the five-year window. “Since 2020/2021” alone does not establish this, especially at a 2026 evaluation date; actual processing flags them until dated project evidence is validated. Also assume a human has cleared C's injection alert, checked its extracted facts, and confirmed optional-feature omissions. This illustrates a post-review ranking, not a claim that verification occurred. The summaries' stated named leads count as present for this exercise; real uploads require identifiable supporting evidence.

| Proposal | Experience | Price | Timeline | Warranty | Team | Total/status |
|---|---:|---:|---:|---:|---:|---|
| A | 93.3333 | 8.4615 | 25 | 91.6667 | 100 | 62.62 / ranked 1 |
| E | 53.3333 | 6.7308 | 18.75 | 100 | 100 | 50.50 / ranked 2 |
| C | 60 | 6.1538 | 12.5 | 91.6667 | 50 | 42.66 / ranked 3 |
| B | — | — | — | — | — | null / ineligible |
| D | — | — | — | — | — | null / flagged |

D needs a confirmed schedule, not an assumed duration. C's embedded ranking instruction is ignored and logged, earning neither bonus nor penalty. If sample assumptions remain unresolved, affected proposals stay flagged and the released ranking is regenerated.

## 1. AI versus rules

Ordinary code accepts uploads, extracts PDF text (OCR if needed), and retains page locations. AI converts untrusted text into evidence-backed candidate facts and warnings. Schema validation, eligibility, arithmetic, ranking and explanations use ordinary code and versioned rules. AI cannot award contracts or set scores.

## 2. Consistency and explainability

Freeze a validated fact snapshot, criteria version and evaluator version per run. Identical snapshots and versions give repeatable calculations; fresh AI extraction can differ even with fixed settings. Show each fact beside its source evidence, gate result, subscore, weight and contribution. Ranking reasons come from these calculations. Retain document hashes, model/prompt versions and extraction output.

## 3. Human review and audit

A reviewer confirms evidence, resolves flags and corrects extracted facts; corrections return to validation and mandatory checks. Fact correction is not permission to waive a requirement. A policy exception requires separate authorized approval under an explicit exception policy; the reviewer cannot silently bypass gates. Record authority, reasons, evidence, actor, timestamp, original and revised facts/results. A changed requirement creates a new criteria version applied to everyone. Preserve the original outcome and display any authorized exception separately. A human makes the final award and records reasons, including departures from ranking.

## 4. Changing criteria

Save edits as a new immutable version; preserve previous results. Validate weights and scales, show an impact preview, then rerun every proposal in the category against the new version. Reuse validated facts only where definitions still match; new evidence requirements trigger extraction and review. Never compare scores across versions. Publish the complete reviewed replacement run atomically.

## 5. Risks and safeguards

Wrong OCR, fabricated facts and ambiguous dates require evidence, nulls, schema checks and human validation. Injection is untrusted content: isolate it, log alerts and require review without awarding bonus or penalty points. Bias is reduced by fixed relevant criteria and consistent review, with audited corrections. Limit file size, pages, tokens, retries and cost; failures stay flagged rather than receiving guessed values. Restrict access and retention, scan uploads, sandbox parsing, and verify licence/coverage evidence before award. Use only fictional samples here; store no secrets.
