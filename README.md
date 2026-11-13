# duska-score-feed

The Scorer's public feed for the duska.si experiment: how closely the AI-built tree agrees with the trees Kevin built himself.

One file, `feed.json`. Numbers only: no names, no dates, no places, no per-person results. The detail is in a private repo until the end; each row carries the SHA-256 of its private results file, so the detail can later be shown to have been fixed at the time.

Rules: `rubric.md` in https://github.com/primerogueinc/duska-coordinator. Agreement is not accuracy: Kevin's trees are the reference, not the truth (rubric section 6).

A row is never changed. A correction is a new row that names the row it corrects and gives the reason.

## Fields of a row

| Field | Meaning |
|---|---|
| `row` | Row number, counting from 1 |
| `kind` | `snapshot`, or `correction` |
| `batch` | The Scorer's batch ID. The commit that added the row has it in its message |
| `scored_at_utc` | When the row was computed, UTC |
| `rubric_version`, `rubric_commit` | The version of the rubric used and the coordinator commit it was read at |
| `ledger_version` | The coordinator's ledger version used for who is living and for where each AI fact came from |
| `scope` | Which people were scored |
| `ai_commits` | The stamped commit of each AI repo that was read |
| `reference.tree_files`, `reference.tree_files_sha256` | How many of Kevin's tree files the reference was built from, and the SHA-256 of each. The same hashes are sealed in `sealed/README.md` of the coordinator repo |
| `reference.facts_headline` | Facts in Kevin's trees, in scope, that count in the headline |
| `reference.facts_exact_date` | Exact dates in Kevin's trees, in scope. Scored outside the headline |
| `reference.facts_headline_by_tier` | Headline facts by Kevin's own confidence: A firm, B probable, C tentative (rubric 6a) |
| `main_measure` | Which tiers are the main measure. Tier A alone, unless it holds fewer than twenty facts |
| `headline` | The numbers of rubric section 4 for the headline facts. See below |
| `exact_dates` | The same numbers for exact dates |
| `extras` | Facts the AI tree states for people in scope that none of Kevin's trees has. Counted, never scored as errors. `headline_by_proof_level` splits them by the AI repos' own proof level |
| `by_generation`, `by_generation_note` | The breakdown by generation, or why it is held back |
| `family_groups` | The second table of the rubric (brothers, sisters, other relatives): how many reference facts it has and whether it was scored |
| `results_sha256` | SHA-256 of the private per-fact results file for this row |
| `corrects_row`, `reason` | For a correction: the row it replaces and why. Otherwise null |

## Inside `headline` and `exact_dates`

| Field | Meaning |
|---|---|
| `withheld` | Facts about a person who is living or of unknown status that the agents may not publish. Outside every count below |
| `all_facts` | Counts and ratios over every scored fact |
| `without_given_or_contaminated` | The same with two kinds of fact removed: those Kevin gave the agents, and those reached by way of an exposure in the contamination log. This is the real measure of the rebuild |
| `removed_as_given`, `removed_as_contaminated` | How many facts were removed for each reason |
| `conflicts_on_tier_B_or_C` | Conflicts on facts Kevin himself holds as probable or tentative. Not counted as AI errors; they are questions for the records |

Each of `all_facts` and `without_given_or_contaminated` has four blocks: `all_tiers`, `tier_A`, `tier_B`, `tier_C`. Each block has:

| Field | Meaning |
|---|---|
| `facts` | Facts in Kevin's trees in this block |
| `match` | Both trees state it and agree |
| `near_match` | They agree within a stated tolerance |
| `conflict` | Both state it and disagree |
| `missing` | Kevin's tree states it and the AI tree does not |
| `not_comparable` | One side is too vague to compare |
| `matches_one_version` | Kevin's own files disagree and the AI tree matches one of them. Counted apart |
| `coverage` | (`match` + `near_match`) / `facts`. Null if the block is empty |
| `agreement` | (`match` + `near_match`) / (`match` + `near_match` + `conflict`). Null if there is nothing to compare |

## Proofs

`.github/workflows/stamp.yml` is an unchanged copy of https://duska.si/stamp.yml. Every commit on `main` gets a manifest and an OpenTimestamps proof on the `proofs` branch.
