Milestone 1 is successful.

We have demonstrated one concrete differential case:

    CBPR_GB_IB_151632_02
    CBPR_GB_IB_151632_01

Using one common effective matcher input:

    Legacy 3.33.2.13:
        both compete
        winner = CBPR_GB_IB_151632_02

    V26 3.319.7:
        both compete
        winner = CBPR_GB_IB_151632_01

The result is currently classified as:

    CONFIRMED_LATENT_SELECTION_CHANGE

because the common matcher input was constructed realistically but was not
captured directly from one historical payment.

DO NOT spend the next milestone manually validating more individual DB pairs.

The objective now is to GENERALIZE the differential method so that we can
discover impacted rules across an entire environment and later reuse the same
method for EU PRODLIKE, AP PRODLIKE, AM PRODLIKE, etc.

Do not modify production code or DB data.


============================================================
MILESTONE 2 OBJECTIVE
============================================================

Build/design a reusable differential impact analyser whose primary unit of
analysis is an EFFECTIVE MATCHER INPUT.

Conceptually:

    analyse(effectiveMatcherInput, environmentRules)

must produce:

    legacyMatchingRules
    legacyTopCandidates
    legacyWinner

    v26MatchingRules
    v26TopCandidates
    v26Winner

    selectionChanged = legacyWinner != v26Winner

The same matcher input must be supplied to both implementations.


============================================================
1. GENERALIZE THE EXISTING ONE-PAIR HARNESS
============================================================

First inspect what was created for Milestone 1.

Separate any hard-coded assumptions for:

    CBPR_GB_IB_151632_01
    CBPR_GB_IB_151632_02

from the actual reusable logic.

Design the evaluator so that it accepts:

    effective matcher input
    rule snapshot / rule collection

and executes the REAL legacy and V26 selection implementations.

Do not hard-code the expected candidate pair.

The evaluator must discover whatever rules match.


============================================================
2. RETAIN COMPLETE COMPETITION INFORMATION
============================================================

Do NOT assume exactly two rules compete.

For each input retain:

    allLegacyMatchingRules
    legacyTopCandidates
    legacyWinner

    allV26MatchingRules
    v26TopCandidates
    v26Winner

There may be 3, 4 or more matching rules.

For reporting purposes derive:

    winnerTransition =
        legacyWinner + " -> " + v26Winner

But retain the complete candidate group internally.


============================================================
3. DEFINE WHAT COUNTS AS AN IMPACT
============================================================

Primary behavior-change condition:

    legacyWinner != v26Winner

Classify results as:

OBSERVED_SELECTION_CHANGE
    Effective input was captured from a real historical/test execution and the
    selected rules are supported by authoritative logs.

REPLAY_CONFIRMED_SELECTION_CHANGE
    A captured effective input was replayed through both exact implementations
    and the winners differ.

CONFIRMED_LATENT_SELECTION_CHANGE
    A valid/realistic synthetic effective matcher input makes both versions
    select different winners, but historical execution has not yet been proven.

POSSIBLE_SELECTION_CHANGE
    Static evidence suggests a competition/change but no common effective input
    has been demonstrated.

NO_SELECTION_CHANGE
    Both versions produce the same winner.

UNKNOWN
    Evidence is insufficient.


============================================================
4. FIRST INPUT SOURCE: HISTORICAL / TEST EFFECTIVE INPUTS
============================================================

Investigate how we can obtain actual effective matcher inputs from existing
executions.

Possible sources:

    Splunk
    initiator logs
    content-validation test runs
    regression test artefacts
    stored request/header dumps
    existing CSV/workbooks
    local captured logs

The important data is NOT merely the raw payment.

Ideally recover the final values presented to RuleMatcher after:

    body parsing
    field extraction
    header creation
    normalization
    derived fields

For every source, determine:

    Can we reconstruct the complete matcher input?
    Which fields are available?
    Is provenance/version known?
    Which request/test ID identifies the source?


============================================================
5. INVESTIGATE THE KNOWN _01/_02 HISTORICAL CASE
============================================================

Before scanning many inputs, finish the evidence chain for the known pair.

We have identifiers for:

    non-V26 execution
    V26 execution
    alternative V26 rerun

Retrieve, if accessible:

    deployed service version
    effective matcher input
    matching candidate rules
    selected rule

Compare the matcher inputs field by field.

Report:

    field
    non-V26 value
    V26 value
    same/different

If the effective matcher input is identical:

    replay that captured input through both exact implementations.

If it reproduces:

    legacy -> _02
    V26    -> _01

upgrade the evidence accordingly.

If the inputs differ, do NOT call that request pair a clean version-induced
selection change. Explain which input differences affected matching.


============================================================
6. BUILD A CORPUS-LEVEL ANALYSER
============================================================

Once one captured input can be processed, generalize to N inputs:

    for each effectiveMatcherInput:
        evaluate legacy
        evaluate V26
        compare winners

Produce one detailed row per input:

    environment
    input_id
    source
    message/business ID if available
    legacy_matching_rules
    legacy_top_candidates
    legacy_winner
    v26_matching_rules
    v26_top_candidates
    v26_winner
    selection_changed
    evidence_classification


============================================================
7. AGGREGATE INTO IMPACTED RULE TRANSITIONS
============================================================

Then aggregate changed inputs by:

    legacyWinner -> v26Winner

Desired output similar to the existing workbook:

    Legacy_Winner
    V26_Winner
    Candidate_Rules
    Count

but add:

    Environment
    First_Seen
    Last_Seen
    Evidence_Type
    Sample_Input_ID
    Service_Versions
    Confidence

Example:

    CBPR_GB_IB_151632_02
        ->
    CBPR_GB_IB_151632_01

    count = N


============================================================
8. USE THE EXISTING MISMATCH WORKBOOK AS A VALIDATION SET
============================================================

The existing workbook/CSV contains likely transitions such as:

    CBPR_GB_IB_151632_02 -> CBPR_GB_IB_151632_01

and other winner mismatches.

Do NOT treat that workbook as authoritative if provenance is incomplete.

Instead use it as a validation target:

    "Can the new analyser independently rediscover these transitions?"

For every existing workbook transition classify:

REDISCOVERED
    independently found by new differential analysis

SUPPORTED_ONLY
    workbook claims it but new evidence is incomplete

NOT_REPRODUCED
    new analysis does not reproduce it

CONTRADICTED
    available evidence shows the workbook transition is not valid


============================================================
9. ENVIRONMENT-INDEPENDENT DESIGN
============================================================

Do not hard-code EU PRODLIKE.

Separate:

A. Rule source
B. Effective-input source
C. Differential evaluation logic
D. Reporting

Conceptual interfaces:

    RuleSource(environment)
        -> rule snapshot

    EffectiveInputSource(environment)
        -> effective matcher inputs

    DifferentialEvaluator
        -> legacy result + V26 result

    ImpactAggregator
        -> winner transitions / candidate groups

The same evaluator must later support:

    EU_PRODLIKE
    AP_PRODLIKE
    AM_PRODLIKE

Only data/configuration sources should change.


============================================================
10. DO NOT USE DB STATIC PAIR DISCOVERY AS THE PRIMARY METHOD YET
============================================================

DB static analysis remains important, but it is the SECOND discovery layer.

First build:

    real/test effective inputs
        ->
    differential evaluation
        ->
    observed impacted transitions

After this works, we will add:

    DB conditions
        ->
    possible competition
        ->
    synthetic witness generation
        ->
    same differential evaluator
        ->
    latent impacted transitions


============================================================
DELIVERABLE FOR THIS MILESTONE
============================================================

Do not attempt to solve every environment yet.

Return:

1. How the existing Milestone 1 harness can be generalized.
2. Exact reusable components/classes/scripts proposed.
3. How one effective matcher input will be represented.
4. How legacy/V26 evaluation will be invoked.
5. How historical inputs can be collected.
6. How results will be aggregated.
7. How the existing mismatch workbook will be used for validation.
8. What needs to be parameterized for EU/AP/AM PRODLIKE.
9. Remaining blockers.

If implementation is safe and isolated from production code, implement the
smallest reusable version that can:

    take multiple effective matcher inputs
    run both evaluators
    output every case where winner differs.

Do not implement DB witness generation yet.
Do not attempt a production fix.
