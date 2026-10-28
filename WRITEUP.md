# Proposal review design

## Setup and scoring

For this 150 kW warehouse solar project in Windsor, AI reads proposals, rules check and score them, and people review unclear information and decide the award.

The admin sets the category, evidence requirements, criteria, weights, scales, limits, dates, rounding and tie rules. Nothing category-specific is hard-coded. These example v1 weights must total 100%.

Before scoring, check that the bidder states its own Ontario electrical contractor licence, WSIB clearance and at least CAD 5M liability insurance. The price must be no more than CAD 260,000, with completion within 16 weeks of award.

A clear failure means **ineligible**. B's future subcontractor arrangement fails the bidder-held licence requirement on submitted evidence, not proof B has no licence. The extracted `licence_holder` distinguishes bidder, future subcontractor and unstated ownership: future subcontractor fails, unstated needs clarification, and bidder with a supported licence number passes the licence check. Other missing or unclear information is **flagged**. Unranked proposals have null ranks; normally scores are null, but C retains a clearly labelled provisional score.

Eligible proposals receive a 0-100 score for each criterion:

| Criterion | Weight | Example v1 scoring scale |
|---|---:|---|
| Experience | 30% | 100 * min(qualifying project count / 15, 1) |
| Price | 25% | 100 * (260,000 - total CAD) / 260,000 |
| Timeline | 15% | 100 * (16 - weeks) / 16 |
| Warranty | 15% | 50 * min(panel years / 25, 1) + 50 * min(workmanship years / 12, 1) |
| Team and approach | 15% | 25 each for a stated named lead, described crew, safety plan and performance-monitoring plan |

Experience means commercial installations of at least 100 kW completed in the five years before evaluation. The 15-project and warranty caps are assumptions for this example, not requirements from the brief. Reject negative values and review zero prices or durations. Keep all criterion scores within 0-100.

Price measures savings against the budget ceiling, so bids near it earn few price points. Price or timeline at its limit passes but earns zero preference points.

A structural review does not replace a safety plan, and progress reports do not replace performance monitoring. Missing optional features earn zero only after a reviewer confirms the omission; unresolved information stays flagged.

Total = sum(weight * criterion score) / 100. Use decimal arithmetic without rounding intermediate results. Round the final total half-up to two decimals. Sort by unrounded total, highest first. Exact ties share rank; skip the next rank and use proposal letters only for display order.

## Sample results and assumptions

For this example only, assume dated evidence confirms A's 14 and E's 8 projects fall inside five years. Without it they remain flagged. C's provisional calculation assumes 9 qualifying projects; "since 2020/2021" alone proves none of these counts.

C's 42.66 is provisional pending human clearance of the embedded instruction. Its calculation also assumes fact validation and confirmed optional-feature omissions; no such review is claimed. For this sample calculation, accept the summaries' stated named leads; actual extraction requires an identified person or role-holder.

| Proposal | Experience | Price | Timeline | Warranty | Team | Total/status |
|---|---:|---:|---:|---:|---:|---|
| A | 93.3333 | 8.4615 | 25 | 91.6667 | 100 | 62.62 / ranked 1 |
| E | 53.3333 | 6.7308 | 18.75 | 100 | 100 | 50.50 / ranked 2 of ranked proposals |
| C | 60 | 6.1538 | 12.5 | 91.6667 | 50 | 42.66 provisional / flagged; rank null |
| B | - | - | - | - | - | null / ineligible |
| D | - | - | - | - | - | null / flagged |

D needs a confirmed schedule before scoring. Ignore and log C's embedded instruction; it earns neither bonus nor penalty. C stays flagged until human review is complete.

## 1. Where I use AI and ordinary rules

Code handles uploads and reads PDF text, using OCR for scans. AI extracts facts, source excerpts and warnings. Code checks the output, applies mandatory requirements, calculates scores and ranks proposals. AI helps read text; rules keep the calculations predictable. AI never awards the contract.

## 2. Making results repeatable and clear

Extract twice at temperature 0 using identical source, model, prompt and configuration. Flag any field that differs, including evidence, for human review; two matching runs can still be wrong. Scoring uses deterministic code: the same confirmed facts and criteria version give the same ranking with the same calculation version. Fresh extraction is not guaranteed identical. Save versions, document hashes and both outputs. Show each fact, source, check, criterion score, weight and contribution.

## 3. Where people review and correct

A reviewer checks evidence, resolves flags and fixes extraction mistakes. Corrections return to validation and mandatory checks. Fixing a fact does not allow someone to waive a requirement. Any permitted exception needs separate authorization under an explicit exception policy. Record who acted, when, why, the evidence, approval, original values/results and revised values/results. Keep exceptions visible. A human makes the award and explains any departure from the ranking.

## 4. When the admin changes criteria

Create a new version and keep earlier results. Check weights and scales, preview effects and rerun every affected proposal. Reuse facts only where definitions still match; review new evidence when needed. Never compare different versions. Release reviewed replacement results together and apply changed requirements consistently.

## 5. Risks and safeguards

Wrong text extraction or invented facts: require source evidence, nulls for unknowns, output checks and human review. Unclear dates: request dated evidence. Manipulation: ignore embedded instructions, log alerts and require review. Unfair treatment: use relevant criteria consistently and record corrections. Cost: limit file size, pages, tokens and retries; flag failures rather than guess. Security: restrict access and retention, scan files, isolate document parsing and check licence/coverage evidence before award. Use only fictional samples and store no secrets.
