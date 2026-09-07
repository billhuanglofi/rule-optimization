I need the concrete impacted Rule ID result now.

From the completed EU PRODLIKE analysis, export the 3,277
matcher-confirmed behavior-change Rule IDs.

Do NOT include the 6 NO_SELECTION_CHANGE-only diagnostic IDs.

Produce:

1. EU_POTENTIAL_IMPACTED_RULE_IDS.csv

One row per unique Rule ID with columns:

rule_id
impact_category
strict_selection_change
tie_determinization
match_set_change
legacy_winner_in_any_case
v26_winner_in_any_case
number_of_change_groups
number_of_witnesses
sample_legacy_winner
sample_v26_winner
confidence

2. Also produce EU_POTENTIAL_IMPACTED_RULE_PAIRS.csv

For every actual winner transition / competition relationship include:

legacy_rule
v26_rule
candidate_rules
change_type
witness_count
runtime_count
confidence

3. Give me the exact counts:

Total exported IDs
NO_SELECTION_CHANGE-only IDs
Potential impacted IDs
Strict-change IDs
Tie-determinization IDs
Match-set-change IDs
IDs appearing in multiple categories
Runtime-confirmed IDs
UNKNOWN/unresolved candidate count

4. Print the first 100 potential impacted Rule IDs in the terminal
so I can immediately inspect them.

5. Explicitly answer:

"How many EU PRODLIKE Rule IDs have been positively demonstrated
by the matcher to participate in a legacy/V26 behavior change?"

Do not perform another discovery run.
Use the completed results.
