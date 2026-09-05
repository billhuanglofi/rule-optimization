Continue the investigation. Do NOT modify production code or database data.

The next objective is to identify RULE PAIRS whose selection behavior can change
between the legacy implementation and V26.

Important finding from the previous investigation:
The regression surface is broader than only V26 rules with equal weight/rank,
because legacy and V26 calculate precedence differently.

Therefore DO NOT limit the analysis to:
    same V26 weight AND same V26 rank.

We need to compare the relative ordering of every plausible competing rule pair
under BOTH algorithms.


OBJECTIVE
=========

Build a method to answer, for a pair of rules A and B:

1. Can A and B potentially match the same request?
2. What precedence does LEGACY assign to A vs B?
3. What precedence does V26 assign to A vs B?
4. Does the relative ordering change?
5. If legacy precedence is tied/undefined, does V26 make the result deterministic?
6. Which rule would V26 select?
7. Have we observed this pair competing at runtime?


PHASE 1 — Precisely model both precedence algorithms
=====================================================

From the source already investigated, write down the exact precedence key for
LEGACY and V26.

Do not simplify them into generic descriptions.

For example determine the exact legacy values involved in selection:
- condition count?
- evaluator count?
- number of conditions after filtering?
- number before filtering?
- any operator precedence?
- collection/iteration order?

And determine the exact V26 values:
- Rule.getWeight()
- Rule.getRank()
- Rule.getId()
- any other comparator or filtering step

Pay special attention to the previous finding that V26 weight/rank may be
calculated BEFORE some conditions are filtered from the cached rule.

Document this explicitly.


PHASE 2 — Build a "precedence fingerprint" for every rule
=========================================================

Using the actual database/schema identified previously, produce a dataset with
one row per rule containing enough information to evaluate BOTH algorithms.

Desired conceptual columns:

rule_id

legacy_condition_count
legacy_evaluator_count
legacy_precedence_value

v26_weight
v26_rank
v26_rule_id_sort_key

condition_fields
condition_operators
condition_values

plus any relevant scope fields actually present in the implementation.

Do not invent enabled/effective-date fields if the schema does not contain them.

Also expose:
- conditions discarded by V26 cache filtering
- original calculated weight/rank
- final conditions actually evaluated

because the earlier investigation found these may differ.


PHASE 3 — Find candidate competing pairs
========================================

We must avoid comparing every rule against every other rule if possible.

Determine safe coarse filters that prove two rules CANNOT compete.

Use only filters justified by actual source semantics.

Examples may include:
- incompatible message field requirements
- incompatible exact equality conditions
- incompatible routing/domain scope
- incompatible country
- incompatible message type

But do not assume these fields exist or are hard scopes unless verified.

Generate candidate pairs:

rule_a
rule_b

where there is no obvious proof that they cannot both match.


PHASE 4 — Compare legacy vs V26 precedence
==========================================

For each candidate pair, calculate:

legacy_relation:
    A_BEFORE_B
    B_BEFORE_A
    TIED_OR_UNDEFINED

v26_relation:
    A_BEFORE_B
    B_BEFORE_A
    TIED_BEFORE_ID
    [whatever states match the actual implementation]

Then calculate:

behavior_change_type

Use these categories:

ORDER_REVERSED
    Legacy prefers A but V26 prefers B,
    or legacy prefers B but V26 prefers A.

LEGACY_AMBIGUOUS_V26_DETERMINISTIC
    Legacy cannot deterministically distinguish the two,
    while V26 deterministically chooses one.

PRECEDENCE_CHANGED_BUT_WINNER_SAME
    Internal score/order changed but the same rule still wins.

NO_PRECEDENCE_CHANGE
    Both versions prefer the same rule for the same reason.

UNKNOWN
    Source semantics are insufficient to determine the comparison.


PHASE 5 — Determine semantic overlap
====================================

Precedence change alone is not an impact.

A pair is only potentially impactful if both rules can match the SAME input.

For each behavior-change pair, determine whether overlap is:

CONFIRMED_OVERLAP
POSSIBLE_OVERLAP
NO_OVERLAP
UNKNOWN

Use exact application matching semantics.

Start with operators whose intersection is safely provable, such as exact
equality / IN where possible.

Do not claim general LIKE/regex overlap can be solved with simplistic SQL.

For difficult predicates such as:
- Java regex
- LIKE if internally regex based
- IN_LIKE
- expressions

mark them for application-level evaluation if necessary.

Investigate whether the actual matcher/evaluator can be reused to validate
overlap rather than reimplementing matching semantics.


PHASE 6 — Produce the impact candidate report
=============================================

The final output I want is conceptually:

rule_a
rule_b
legacy_precedence_a
legacy_precedence_b
legacy_relation
v26_weight_a
v26_rank_a
v26_weight_b
v26_rank_b
v26_relation
v26_expected_winner
behavior_change_type
overlap_status
reason
confidence

Prioritize rows where:

behavior_change_type IN (
    ORDER_REVERSED,
    LEGACY_AMBIGUOUS_V26_DETERMINISTIC
)

AND

overlap_status IN (
    CONFIRMED_OVERLAP,
    POSSIBLE_OVERLAP
)


PHASE 7 — Runtime confirmation using Splunk
===========================================

Separately, use the exact multiple-rule-match log format previously identified.

Find actual runtime events where candidate pairs competed.

For each observed event extract:

timestamp
message/request identifier
all_matching_rules
selected_rule
rank/weight if present
service version
relevant routing result if available

Aggregate by canonical rule pair.

Desired output:

rule_a
rule_b
runtime_occurrences
first_seen
last_seen
selected_rules_seen
sample_request_id


PHASE 8 — Join static and runtime evidence
==========================================

Classify each pair:

CONFIRMED_BEHAVIOR_CHANGE
    Static analysis says legacy/V26 precedence differs
    AND Splunk shows the pair competing.

LATENT_BEHAVIOR_CHANGE
    Static analysis says precedence can differ
    AND overlap is possible/confirmed
    BUT no runtime occurrence has yet been found.

OBSERVED_COLLISION_NO_PROVEN_VERSION_CHANGE
    Splunk shows the rules competing
    but static analysis does not yet prove a legacy/V26 ordering change.

SAFE_SAME_ORDER
    Rules may overlap but both algorithms produce the same winner.

UNKNOWN
    More evidence is required.


IMPORTANT EDGE CASES
====================

Explicitly investigate these findings from the previous report:

1. nextRules path
   Determine whether selection through nextRules bypasses normal StoreCache
   deterministic ordering. These pairs may need a separate category.

2. Condition filtering
   Weight/rank may be calculated before conditions are removed.
   Make sure pair comparison reproduces this exact behavior.

3. Expressions
   If expression rules exist, do not silently ignore them.

4. Request-level feature toggles / extendedHeaders
   Determine whether the matching rule set can differ depending on request
   flags. Identify whether this needs to be part of the candidate model.

5. CL example inconsistency
   The reported rule-382015 / CBPR_CL_IB_382001 result apparently does not fit
   normal V26 ID-ascending behavior and rule-382015 is absent from the current
   database.
   Treat this as an unresolved case.
   Do not force it to fit the general hypothesis.
   Explain possible paths that could produce it.

6. Do not rely on the supplied CSV as authoritative evidence if its provenance
   cannot be established.


DELIVERABLES
============

Do not implement a fix.

Return:

1. Exact legacy precedence formula
2. Exact V26 precedence formula
3. SQL/query for generating per-rule precedence fingerprints
4. SQL/query for generating candidate rule pairs where safely possible
5. Method/code design for semantic-overlap checking
6. Behavior-change classification algorithm
7. Splunk query for confirming candidate pairs at runtime
8. Final proposed impact-report schema
9. Any remaining blockers

Most importantly:

A pair having the same V26 weight/rank is NOT automatically an impact.

A pair is an impact candidate when:

    the rules can compete for the same request
    AND
    legacy versus V26 can select/order them differently.

Prove these two properties separately.
