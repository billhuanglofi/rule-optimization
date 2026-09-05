Do not redesign the existing differential-analyzer.

The next objective is to turn it into a reusable IMPACT DISCOVERY ENGINE.

The long-term requirement is:

    run against EU PRODLIKE
    run against AP PRODLIKE
    run against AM PRODLIKE

and automatically identify rule competitions where legacy/non-V26 and V26
can produce different winning rules.

Historical Splunk traffic is NOT the primary discovery mechanism.
It will later be used to prove which discovered impacts have actually occurred.


CORE PRINCIPLE
==============

The unit of analysis must be:

    one effective matcher-input witness
    +
    the COMPLETE environment rule snapshot
    +
    the real legacy evaluator
    +
    the real V26 evaluator

Do not evaluate an isolated pair without evaluating all rules.

A third rule may also match the witness and change the actual winner.


PHASE 1 — Complete environment RuleSnapshot
===========================================

Build a reusable RuleSource that exports the full rule population required by
both adapters.

For every environment snapshot preserve enough raw information to reconstruct
the actual production Rule objects.

Include provenance:

    environment
    snapshot timestamp
    source DB/schema
    extraction query/version
    rule count
    source checksum/hash

The evaluator must not contain EU-specific IDs or assumptions.


PHASE 2 — Build a sound competition-candidate generator
=======================================================

Using parsed CONDITIONS, identify rules that MAY be simultaneously satisfiable.

Do NOT start with a blind Cartesian product if safe pruning is possible.

However, candidate pruning must be SOUND:

    only discard a pair/group if we can prove that the predicates cannot both
    be true.

Examples:

    field = GB
    field = US

can safely prove incompatibility.

But:

    LIKE
    IN_LIKE
    expression conditions
    missing predicates

must not be discarded unless incompatibility is actually proven.

The goal is high recall, not aggressive pruning.


PHASE 3 — Build a predicate-intersection / witness generator
============================================================

For candidate competitions, attempt to construct one concrete EFFECTIVE
MATCHER INPUT satisfying the competing predicates.

Example output:

{
    "MsgType": "...",
    "Country": "GB",
    "ReceiverAddress": "...",
    "Routing": "...",
    ...
}

Use the exact application condition semantics already discovered.

Support incrementally:

    equality
    IN
    LIKE
    IN_LIKE
    combinations of those

For unsupported/complex expressions, return UNKNOWN rather than assuming
no overlap.

Every generated witness MUST be validated by the real matcher.

Static reasoning proposes the witness.
The real matcher proves whether each rule actually matches.


PHASE 4 — Evaluate the witness against ALL environment rules
=============================================================

For every successfully generated witness:

Run:

    LegacyAdapter.evaluate(witness, completeRuleSnapshot)

and:

    V26Adapter.evaluate(witness, completeRuleSnapshot)

Capture:

Legacy:
    allMatchingRules
    topCandidateRules
    possibleWinners
    orderDependent

V26:
    allMatchingRules
    topCandidateRules
    winner

Do not restrict either engine to the candidate pair that generated the witness.


PHASE 5 — Detect behavior changes
=================================

Classify the result.

STRICT_SELECTION_CHANGE

    Legacy has a deterministic top winner A.
    V26 deterministically selects B.
    A != B.

TIE_DETERMINIZATION_CHANGE

    Legacy top/possible-winner set contains multiple rules.
    V26 deterministically chooses one member.
    Therefore another legacy top rule could previously win.

NO_SELECTION_CHANGE

    Both algorithms result in the same effective winner behavior.

MATCH_SET_CHANGE

    Legacy and V26 disagree on which rules match the same witness.
    Keep this separately because this may indicate matcher/parser/filtering
    differences rather than precedence differences.

UNKNOWN

    Analysis cannot safely establish behavior.


PHASE 6 — Produce impacted winner transitions
=============================================

Aggregate behavior-changing witnesses into transitions such as:

    rule-XXX -> CBPR-XXXX

Desired output:

environment
legacy_rule
v26_rule
behavior_change_type
competing_rules
witness_id
witness_headers
legacy_possible_winners
v26_winner
confidence

Multiple different witnesses may map to the same rule transition.

Aggregate:

    transition
    number_of_witnesses
    sample_witnesses


PHASE 7 — Use the existing mismatch workbook as a GOLDEN SET
=============================================================

Do NOT use the workbook to drive the analyser.

After generating results independently, compare them with the known workbook.

For each known transition classify:

    REDISCOVERED
    PARTIALLY_REDICOVERED
    NOT_FOUND
    CONTRADICTED

Use high-frequency examples such as:

    CBPR_GB_IB_151632_02
        ->
    CBPR_GB_IB_151632_01

as regression tests for discovery quality.

The important metric is recall:

    How many known transitions can the generic analyser rediscover without
    knowing their IDs in advance?


PHASE 8 — Only then add runtime evidence
========================================

After static+witness differential discovery works, enrich discovered transitions
using Splunk/regression traffic.

Splunk should answer:

    Has this competition actually occurred?
    Which legacy rule was actually selected?
    Which V26 rule was actually selected?
    How often?
    First/last seen?
    Example message ID?

This changes classification from:

    POTENTIAL / LATENT IMPACT

to:

    OBSERVED IMPACT

But inability to find Splunk evidence must NOT remove a valid synthetic
differential result.


PHASE 9 — Environment portability
=================================

The analyser must eventually work as:

    analyse EU_PRODLIKE
    analyse AP_PRODLIKE
    analyse AM_PRODLIKE

Only these should vary:

    RuleSource configuration
    DB connection/query
    environment metadata
    optional runtime evidence source

These must remain environment-independent:

    rule normalization
    witness generation
    LegacyAdapter
    V26Adapter
    differential comparison
    impact aggregation


MOST IMPORTANT DESIGN REQUIREMENTS
==================================

1. DB analysis identifies constraints; it does not prove production impact.

2. A witness proves that competition is possible.

3. The witness must be evaluated against ALL rules, not just the pair that
   created it.

4. The real legacy and V26 implementations must decide winners.

5. Legacy order-dependent ties must be represented as possible-winner sets,
   not arbitrarily converted into one deterministic winner.

6. Unknown operator semantics must produce UNKNOWN, not false NO-IMPACT.

7. Splunk is evidence enrichment, not the primary discovery algorithm.


NEXT DELIVERABLE
================

Do not implement Splunk collection yet.

First deliver and, where practical, implement:

    RuleSnapshot exporter
    CandidateCompetitionGenerator
    WitnessGenerator
    whole-snapshot differential evaluation
    behavior-change report

Run it first on EU PRODLIKE.

Then compare the independently discovered transitions against the existing
mismatch workbook and report the rediscovery rate.

Also list every unsupported condition/operator that prevents complete coverage.
