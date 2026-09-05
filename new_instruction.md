============================================================
ULTIMATE MISSION / ACCEPTANCE CRITERIA
============================================================

The final objective of this investigation is NOT simply to find duplicate,
similar, or overlapping rules in the database.

The objective is to develop a reusable method that identifies rules/groups
whose ROUTING SELECTION MAY CHANGE between non-V26 and V26.

The target behavior is:

    SAME effective payment/message/request

    non-V26:
        matching candidates = [...]
        selected rule       = A

    V26:
        matching candidates = [...]
        selected rule       = B

    where A != B

Example:

    non-V26 winner:
        CBPR_GB_IB_151632_02

    V26 winner:
        CBPR_GB_IB_151632_01

    competing candidates:
        CBPR_GB_IB_151632_02
        CBPR_GB_IB_151632_01

This is the primary kind of impact we need to discover.


IMPORTANT PRINCIPLES
====================

1. DATABASE ANALYSIS IS ONLY ONE INPUT

RULE_ID + CONDITIONS is useful for discovering potential competition, but DB
analysis alone is not sufficient proof.

Routing may depend on:

- payment/message body
- extracted values
- generated headers
- normalized fields
- derived fields
- request/routing context
- condition evaluation semantics

The real question is:

    "Can the same effective matcher input cause these rules to compete,
     and do legacy and V26 select different winners?"


2. THE SAME INPUT MUST BE COMPARED

Whenever possible, legacy and V26 must be evaluated using the SAME effective
matcher input.

Do not compare unrelated legacy and V26 requests merely because their rule IDs
look related.

If historical runs cannot be directly correlated, state this explicitly.


3. DO NOT ASSUME COMPETITION IS ALWAYS BETWEEN TWO RULES

A request may match:

    Rule A
    Rule B
    Rule C
    ...

Retain the complete candidate set.

For reporting, derive:

    legacy winner -> V26 winner

but do not throw away the other matching candidates.


4. DISTINGUISH DISCOVERY FROM PROOF

Use these confidence categories:

OBSERVED_SELECTION_CHANGE

    A real historical/test request shows:
        legacy winner != V26 winner

REPLAY_CONFIRMED_SELECTION_CHANGE

    The same effective input was evaluated using both implementations and
    produced different winners.

CONFIRMED_LATENT_SELECTION_CHANGE

    No historical occurrence was found, but a valid synthetic matcher input
    was constructed and actual evaluators choose different winners.

POSSIBLE_SELECTION_CHANGE

    Static analysis indicates that rules may compete and selection may differ,
    but no concrete common input has yet been proven.

COMPETING_SAME_RESULT

    Rules can compete but legacy and V26 select the same winner.

NO_OVERLAP

    Rules cannot match the same effective input.

UNKNOWN

    The current analyser cannot safely decide because of expressions,
    unsupported operators, missing runtime context, etc.


5. DO NOT CLAIM "ALL IMPACTED RULES" WITHOUT COVERAGE EVIDENCE

The eventual method should report its coverage.

If some condition operators, expressions, derived fields, or request paths
cannot be modelled, explicitly list them.

The correct result may therefore be:

    confirmed impacts
    +
    confirmed latent impacts
    +
    unresolved possible impacts

rather than falsely claiming mathematical completeness.


6. USE EXISTING KNOWN MISMATCHES AS A VALIDATION CORPUS

We already have an existing mismatch result containing columns conceptually
similar to:

    Winner_v3.33.2.13
    Winner_v3.319.7
    Candidate_Rules
    Result
    Count

Examples include high-frequency mismatches such as:

    CBPR_GB_IB_151632_02
        ->
    CBPR_GB_IB_151632_01

Treat these as known expected results / validation seeds.

First establish exactly how this dataset was generated and its provenance.

Then ensure the new investigation method can reproduce known cases.

If it cannot reproduce an obvious known mismatch, investigate why before
scaling the approach.


7. DESIGN FOR MULTIPLE ENVIRONMENTS FROM THE START

The matching and comparison logic must be environment-independent.

The eventual workflow should be reusable against environments such as:

    EU PRODLIKE
    AP PRODLIKE
    AM PRODLIKE

Environment-specific inputs may include:

    DB connection / rule snapshot
    Splunk index/source
    application configuration
    historical request corpus
    legacy version
    target V26 version

Do not hard-code:

    EU-specific rule IDs
    country values
    table data
    Splunk indexes
    current candidate lists


8. PREFER PRODUCTION IMPLEMENTATION OVER REIMPLEMENTATION

Where practical, reuse actual application classes for:

    CONDITIONS parsing
    message/header extraction
    normalization
    condition evaluation
    RuleMatcher
    legacy rule selection
    V26 rule selection

Avoid implementing an independent approximation unless necessary.

If approximation is unavoidable, document exactly where it differs from
production behavior.


9. KEEP STATIC AND DYNAMIC ANALYSIS COMPLEMENTARY

Eventually the method should contain two complementary discovery paths.

PATH A — observed/runtime impact

    historical/test requests
        ->
    effective matcher input
        ->
    legacy evaluation
        +
    V26 evaluation
        ->
    different selected rule

PATH B — latent/static impact

    DB rule definitions
        ->
    possible competing rules/groups
        ->
    construct common matcher-input witness
        ->
    legacy evaluation
        +
    V26 evaluation
        ->
    different selected rule

PATH A finds exercised impact.
PATH B attempts to find impact not exercised by available traffic.


10. FINAL DESIRED REPORT

The long-term output should support fields such as:

    environment

    request_or_witness_id
    source

    legacy_matching_rules
    v26_matching_rules

    legacy_selected_rule
    v26_selected_rule

    competition_group

    effective_input_summary

    behavior_change_type
    overlap_status

    occurrence_count
    first_seen
    last_seen

    confidence
    evidence
    unresolved_reason

For aggregation, also produce:

    legacy_selected_rule
    v26_selected_rule
    candidate_set
    occurrence_count


============================================================
PROGRESSIVE DELIVERY
============================================================

Do not attempt the entire solution in one implementation.

Proceed in milestones.

MILESTONE 1

Take ONE known mismatch, preferably:

    CBPR_GB_IB_151632_02
        ->
    CBPR_GB_IB_151632_01

and prove end-to-end:

    DB CONDITIONS
        ->
    effective matcher input
        ->
    both/competing rules match
        ->
    legacy selection
        ->
    V26 selection
        ->
    different winner

Explain any missing runtime inputs if this cannot yet be reproduced.


MILESTONE 2

Generalize the above comparison so that one effective matcher input can be
evaluated automatically against both implementations.


MILESTONE 3

Run the differential evaluator over an available historical/regression request
corpus and reproduce the existing mismatch report.


MILESTONE 4

Aggregate differences into impacted rule pairs/groups.


MILESTONE 5

Use DB CONDITIONS to discover possible competing rules that historical traffic
did not exercise.


MILESTONE 6

Generate or derive valid matcher-input witnesses for those static candidates
and evaluate them using both selectors.


MILESTONE 7

Parameterize the process so the exact same methodology can run against:

    EU PRODLIKE
    AP PRODLIKE
    AM PRODLIKE


============================================================
DO NOT DO YET
============================================================

Do not:

- modify production routing behavior
- update DB rule data
- recommend rule fixes
- assume rule ID naming conventions imply competition
- run an unrestricted Cartesian comparison without first finding safe ways to
  reduce candidate scope
- declare a pair impacted solely because its CONDITIONS look similar
- declare completeness while unsupported matching semantics remain


At every milestone report:

    What was proven?
    What evidence proves it?
    What remains an assumption?
    What is currently impossible to determine?
    What is the next smallest experiment that would reduce uncertainty?
