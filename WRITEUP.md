# Proposal review design

## Setup and scoring

The project is a 150 kW solar installation on a warehouse roof in Windsor. My approach is simple: AI reads the proposals, rules check and score them, and people review unclear information and make the award decision.

The admin sets the category, criteria, evidence requirements, weights, scoring scales, limits, dates, rounding and tie rules. Nothing category-specific is hard-coded. The settings below are an example called v1. Weights must total 100% before a version can be used.

Before scoring, check that the bidder states its own Ontario electrical contractor licence, WSIB clearance and at least CAD 5M liability insurance. The price must be no more than CAD 260,000, with completion within 16 weeks of award.

A clear failure means **ineligible**. B offers a future subcontractor arrangement instead of meeting the bidder-held licence requirement, so its submitted proposal is ineligible. This does not mean B definitely has no licence. Other missing or unclear information means **flagged** for clarification. Both statuses have null score and rank.

Eligible proposals receive a 0-100 score for each criterion:

| Criterion | Weight | Example v1 scoring scale |
|---|---:|---|
| Experience | 30% | 100 * min(qualifying project count / 15, 1) |
| Price | 25% | 100 * (260,000 - total CAD) / 260,000 |
| Timeline | 15% | 100 * (16 - weeks) / 16 |
| Warranty | 15% | 50 * min(panel years / 25, 1) + 50 * min(workmanship years / 12, 1) |
| Team and approach | 15% | 25 each for a stated named lead, described crew, safety plan and performance-monitoring plan |

Experience means commercial installations of at least 100 kW completed in the five years before evaluation. The 15-project and warranty caps are assumptions for this example, not requirements from the brief. Reject negative values and review zero prices or durations. Keep all criterion scores within 0-100.

The price scale measures savings against the budget ceiling. A proposal close to the ceiling gets few price points, even though it meets the budget requirement. A price or timeline exactly at its limit passes but earns zero points for that preference.

A structural review does not replace a safety plan, and progress reports do not replace performance monitoring. Missing optional features earn zero only after a reviewer confirms the omission; unresolved information stays flagged.

Total = sum(weight * criterion score) / 100. Use decimal arithmetic without rounding intermediate results. Round the final total half-up to two decimals. Sort by unrounded total, highest first. Exact ties share rank; skip the next rank and use proposal letters only for display order.

## Sample results and assumptions

For this example only, assume evidence confirms A's 14, C's 9 and E's 8 projects fall inside the five-year window. "Since 2020/2021" does not establish this by itself. Without dated evidence, those proposals remain flagged.

Also assume a reviewer has checked C's facts, cleared its instruction-attack alert and confirmed missing optional features. These are hypothetical conditions for the example, not reviews that actually happened. Accept the summaries' stated named leads here; real uploads need identifiable evidence.

| Proposal | Experience | Price | Timeline | Warranty | Team | Total/status |
|---|---:|---:|---:|---:|---:|---|
| A | 93.3333 | 8.4615 | 25 | 91.6667 | 100 | 62.62 / ranked 1 |
| E | 53.3333 | 6.7308 | 18.75 | 100 | 100 | 50.50 / ranked 2 |
| C | 60 | 6.1538 | 12.5 | 91.6667 | 50 | 42.66 / ranked 3 |
| B | - | - | - | - | - | null / ineligible |
| D | - | - | - | - | - | null / flagged |

D needs a confirmed schedule before scoring. Ignore and log C's embedded instruction; it earns neither bonus nor penalty. C stays flagged until human review is complete.

## 1. Where I use AI and ordinary rules

Code handles uploads and reads PDF text, using OCR for scans. AI extracts facts, source excerpts and warnings. Code checks the output, applies mandatory requirements, calculates scores and ranks proposals. AI helps read text; rules keep the calculations predictable. AI never awards the contract.

## 2. Making results repeatable and clear

Save the reviewed facts, criteria version and calculation version for each run. The same facts and versions give the same calculation. Fresh AI extraction may differ, even with fixed settings. Show each fact, source, check, criterion score, weight and points added to the total. Save document hashes, prompts, model versions and extraction results.

## 3. Where people review and correct

A reviewer checks evidence, resolves flags and fixes extraction mistakes. Corrections return to validation and mandatory checks. Fixing a fact does not allow someone to waive a requirement. Any permitted exception needs separate authorization under an explicit exception policy. Record who acted, when, why, the evidence, approval, original values/results and revised values/results. Keep exceptions visible. A human makes the award and explains any departure from the ranking.

## 4. When the admin changes criteria

Create a new version and keep earlier results. Check the weights and scales, preview the effects, then rerun every affected proposal using that version. Reuse facts only if their definitions still match; collect and review new evidence when needed. Do not compare scores from different versions. Release the reviewed replacement results together. A changed requirement applies consistently to everyone.

## 5. Risks and safeguards

Wrong text extraction or invented facts: require source evidence, nulls for unknowns, output checks and human review. Unclear dates: request dated evidence. Manipulation: ignore embedded instructions, log alerts and require review. Unfair treatment: use relevant criteria consistently and record corrections. Cost: limit file size, pages, tokens and retries; flag failures rather than guess. Security: restrict access and retention, scan files, isolate document parsing and check licence/coverage evidence before award. Use only fictional samples and store no secrets.
