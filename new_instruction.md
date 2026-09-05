Continue from the completed investigation.

Do NOT modify production code or database data.

The current findings indicate:

- No strict legacy-vs-V26 reversal was found among the clean current prod-like
  ordinary rule fingerprints.
- Legacy complete ties are unordered.
- Normal V26 precedence is deterministic: weight DESC, rank DESC, rule ID ASC.
- The GB pair is confirmed to overlap and changes from legacy ambiguity to a
  deterministic V26 winner.
- 19 exact-signature pairs have been identified as high-priority static
  candidates.
- 10 current prod-like rules have filter anomalies.
- Runtime/Splunk evidence is currently incomplete.
- nextRules must be treated separately because it bypasses normal deterministic
  StoreCache ordering.

The next objective is NOT to discover more broad candidates.

The next objective is to VALIDATE the existing candidate pairs and produce an
impact list.


PHASE 1 — Validate the 19 exact-signature pairs
===============================================

For each of the 19 exact-signature candidate pairs:

1. Load both rules using the same rule construction path used by the service.
2. Identify the final conditions/evaluators actually used at runtime.
3. Construct a concrete request/header/input witness that satisfies BOTH rules.

Prefer reusing the real matching implementation.

Do NOT prove overlap merely by comparing strings in SQL if the actual matcher
can be invoked.

For each pair report:

rule_a
rule_b
witness_available
witness_input
rule_a_matches
rule_b_matches
legacy_score_a
legacy_score_b
legacy_relation
v26_weight_a
v26_rank_a
v26_weight_b
v26_rank_b
v26_expected_winner
classification
reason

Classification should be one of:

CONFIRMED_AMBIGUOUS_TO_DETERMINISTIC
    Both rules match.
    Legacy ends in a complete unresolved tie / encounter-order dependency.
    V26 deterministically selects one rule.

CONFIRMED_STRICT_REVERSAL
    Both rules match.
    Legacy deterministically prefers one rule.
    V26 deterministically prefers the other.

SAME_WINNER
    Both rules match but behavior does not materially change.

NO_OVERLAP
    No common request can be demonstrated.

POSSIBLE_OVERLAP
    Static evidence suggests overlap but a concrete witness cannot yet be
    constructed.

UNKNOWN
    Insufficient information.


PHASE 2 — Pay special attention to filter-anomalous rules
=========================================================

Investigate the 10 rules previously identified as having cache/filter anomalies.

For each such rule show:

rule_id
original/source conditions
conditions removed during StoreCache.createRule
conditions retained at runtime
weight calculated before filtering
rank calculated before filtering
required_variables before filtering
effective runtime predicates

Then identify every other rule with which it could compete.

Important:

V26 precedence must use the derived fields exactly as production does, even if
some contributing conditions are later removed.

Determine whether any of these anomalies create a case where:

- two rules have unexpected equal weight/rank
- a rule receives precedence from a condition it no longer evaluates
- the relative ordering differs from what the visible runtime conditions imply

Add those pairs to the validation report.


PHASE 3 — Re-check strict reversals only within confirmed overlaps
=================================================================

Do NOT perform another unrestricted all-rules Cartesian comparison.

Instead, among pairs where a concrete overlap witness has been demonstrated,
compare the exact legacy and V26 algorithms.

Confirm whether there are any:

    LEGACY A > B
    V26    B > A

or vice versa.

If none exist, explicitly conclude:

    "No strict reversal has been demonstrated in the current validated
     prod-like rule population."

Distinguish this from:

    "Strict reversal is impossible."

We do not have enough evidence to claim the latter.


PHASE 4 — Separate nextRules
============================

Do not mix nextRules behavior with normal StoreCache ordering.

Identify rules reachable through nextRules and determine:

- what collection supplies them
- whether encounter order is deterministic
- whether weight/rank/ID sorting is applied
- whether matchFirst/findFirst operates directly on unordered collection order

Create a separate classification:

NEXT_RULES_ORDER_DEPENDENT

for any case whose behavior depends on this path.


PHASE 5 — Produce identifiers for Splunk validation
===================================================

For every CONFIRMED overlap pair, generate a canonical pair key:

    min(rule_id) + " <-> " + max(rule_id)

Also provide:
- expected V26 winner
- classification
- any known request/header witness
- relevant rule IDs exactly as logged

Produce a plain list suitable for use in Splunk searches.

Do not claim runtime impact yet.


PHASE 6 — Runtime validation plan
=================================

Using the exact logging formats already found in source, produce SPL that can
search for each confirmed pair.

The objective is to establish:

    Did Rule A and Rule B actually appear together as matching candidates?

and, if available:

    Which rule was selected?

For each pair aim to return:

rule_a
rule_b
occurrences
first_seen
last_seen
selected_rule
sample_message_id
sample_correlation_id
service_version

If broad searches are unreliable, generate targeted SPL for each confirmed
pair instead of one expensive global search.


FINAL DELIVERABLE
=================

Produce one consolidated impact table:

rule_a
rule_b
overlap_status
legacy_relation
v26_relation
v26_expected_winner
behavior_change_type
filter_anomaly
nextRules_path
runtime_status
confidence
evidence

Sort in this priority:

1. CONFIRMED_STRICT_REVERSAL + runtime seen
2. CONFIRMED_AMBIGUOUS_TO_DETERMINISTIC + runtime seen
3. CONFIRMED_STRICT_REVERSAL, runtime unknown
4. CONFIRMED_AMBIGUOUS_TO_DETERMINISTIC, runtime unknown
5. POSSIBLE_OVERLAP
6. NO_OVERLAP / SAME_WINNER

Do not propose a production fix yet.

The goal of this step is to establish the smallest defensible list of rules
whose behavior could actually change in V26.
