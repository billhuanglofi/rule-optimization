You are now responsible for completing this investigation end-to-end.

Do not stop after designing the next step.
Do not ask me what to do next unless you are blocked by something that genuinely
requires credentials, network access, or information that cannot be derived from
the repository/environment.

Investigate, implement isolated analysis tooling where needed, execute the
analysis, validate the results, and produce the FINAL investigation report.

======================================================================
MISSION
======================================================================

We are investigating rule-selection behavior differences between:

    legacy / non-V26: 3.33.2.13
    V26:              3.319.7

The ultimate question is:

    Across an environment's complete rule population, which rules/payments can
    experience a different selected rule between legacy/non-V26 and V26 because
    multiple rules compete for the same effective matcher input?

Typical result:

    legacy/non-V26 winner:
        rule-XXX

    V26 winner:
        CBPR-XXXX

while both rules are valid competitors for the same effective routing input.

The final solution must be reusable across:

    EU PRODLIKE
    AP PRODLIKE
    AM PRODLIKE

and should not depend on manually knowing the affected rule IDs in advance.


======================================================================
IMPORTANT CONCEPTUAL MODEL
======================================================================

The problem is NOT a simple DB duplicate-rule problem.

Actual routing depends on:

    raw payment/message
        ->
    parsing
        ->
    extracted message fields
        ->
    generated headers
        ->
    normalized / derived fields
        ->
    effective matcher input
        ->
    rule conditions
        ->
    multiple matching rules
        ->
    legacy selection
        vs
    V26 selection

Therefore:

DB analysis identifies possible competition.

A concrete effective matcher input proves that competition is reachable at the
matcher level.

The REAL legacy and V26 implementations determine the actual winner behavior.

Runtime/Splunk evidence proves whether the behavior has occurred historically.


======================================================================
CURRENT CONTEXT
======================================================================

Previous investigation work appears to have established or strongly suggested
the following. DO NOT blindly trust these statements: verify them while working.

1. There is already an isolated differential-analyzer implementation.

2. Legacy and V26 adapters execute their respective real Java implementations
   in separate JVMs because of incompatible classes/artifacts.

3. Python orchestration does not duplicate the matching/winner logic.

4. An effective matcher-input format already exists.

5. A neutral rule snapshot format already exists or is partially implemented.

6. A known test pair has been reproduced:

       CBPR_GB_IB_151632_02
       CBPR_GB_IB_151632_01

   using one common realistic effective matcher input.

   Approximate observed behavior:

       legacy:
           top candidates include _01 and _02
           historical/constructed result may resolve to _02

       V26:
           top candidates include _01 and _02
           deterministic result = _01

7. Existing investigation suggests normal V26 precedence resembles:

       weight DESC
       rank DESC
       rule ID ASC

8. Legacy behavior is materially different and may contain complete
   encounter-order-dependent ties.

9. Some V26 precedence fields may be derived before condition filtering.

10. nextRules may bypass normal StoreCache deterministic sorting and therefore
    requires separate treatment.

11. There is an existing workbook/CSV containing likely winner transitions,
    including examples similar to:

       CBPR_GB_IB_151632_02 -> CBPR_GB_IB_151632_01

    with occurrence counts.

12. The workbook is useful as a golden/validation dataset, but its provenance may
    not be authoritative.

13. The rule database exposes RULE_ID and CONDITIONS, and CONDITIONS contains
    routing predicates such as message type, country, sender/receiver,
    routing and related extracted/derived fields.

Again: VERIFY these from source/current artifacts before using them as facts.


======================================================================
NON-NEGOTIABLE RULES
======================================================================

1. Do NOT modify production source behavior.

2. Do NOT change database data.

3. Do NOT make production configuration changes.

4. Analysis tooling may be added or modified only in an isolated investigation
   area such as differential-analyzer or equivalent.

5. Prefer reuse of actual legacy/V26 production parser, matcher, store and
   comparator code over reimplementing semantics.

6. Never claim "no impact" simply because static analysis cannot understand a
   predicate.

7. Unsupported/uncertain semantics must be classified UNKNOWN.

8. Do not limit analysis to rules having equal V26 weight/rank.

   Legacy and V26 precedence models differ, so strict ordering reversals may also
   exist.

9. Do not evaluate only an isolated pair when proving impact.

   A generated witness must be evaluated against the COMPLETE rule snapshot.

   A third or fourth rule may match and alter the actual winner.

10. Do not assume only two rules can compete.

11. Preserve complete matching/top-candidate groups.

12. Historical Splunk traffic is evidence enrichment, NOT the only discovery
    mechanism.

13. A current DB snapshot must not silently be treated as the historical rule
    state for old traffic.

14. Keep evidence provenance throughout the investigation.


======================================================================
CORE DEFINITIONS
======================================================================

Use these concepts consistently.


A. Effective Matcher Input

The final field/value map actually presented to RuleMatcher after parsing,
extraction, normalization and derived-field generation.


B. Matching Rules

Every rule that evaluates true for a given effective matcher input.


C. Top Candidates

The subset remaining at the highest precedence level before an
encounter-order-dependent or final deterministic winner is chosen.


D. Legacy Possible Winners

When legacy has no deterministic comparator between top candidates, represent
ALL possible top winners.

Do NOT arbitrarily choose one.


E. V26 Winner

The winner produced by the actual V26 production selection implementation.


F. Effective-Input Witness

A concrete effective matcher-input map that passes through the actual matcher
and proves that a competition can occur.


G. Request-Reachable Witness

A real or valid payment/message that passes through the actual
payment -> extraction -> effective-input path and generates the effective input.


======================================================================
FINAL IMPACT CLASSIFICATIONS
======================================================================

Use these categories.


1. OBSERVED_STRICT_SELECTION_CHANGE

A real historical/test execution is available and:

    legacy has a deterministic winner A
    V26 has deterministic winner B
    A != B

and the evidence is version/snapshot/provenance reliable.


2. OBSERVED_TIE_RESOLUTION_CHANGE

Legacy top candidates contain multiple possible winners.

Historical legacy runtime selected A.

V26 deterministically selects B.

A != B.

This is a real observed behavioral change even though legacy did not
mathematically guarantee A.


3. REPLAY_CONFIRMED_STRICT_SELECTION_CHANGE

A captured real effective matcher input has been replayed through both exact
implementations and produces deterministic A -> B.


4. REPLAY_CONFIRMED_TIE_DETERMINIZATION

A captured real effective input gives:

    legacy possible winners = {A, B, ...}
    V26 deterministic winner = B

even if authoritative legacy runtime winner is unavailable.


5. CONFIRMED_LATENT_STRICT_SELECTION_CHANGE

No historical execution is required, but a valid synthetic effective-input
witness has been constructed, validated by the real matcher against the complete
rule snapshot, and:

    legacy deterministic winner != V26 winner


6. CONFIRMED_LATENT_TIE_DETERMINIZATION

A valid synthetic witness proves:

    legacy possible winners contain multiple rules
    V26 deterministically reduces them to one result


7. MATCH_SET_CHANGE

The same effective matcher input causes the actual legacy and V26 implementations
to disagree on which rules match.

Keep this separate from pure precedence changes because it may indicate parser,
filtering, evaluator or rule-construction changes.


8. POSSIBLE_SELECTION_CHANGE

Static analysis indicates overlapping predicates and/or different precedence,
but no concrete witness has been validated.


9. COMPETING_BUT_SAME_EFFECTIVE_RESULT

Rules can compete, but legacy and V26 produce materially equivalent winner
behavior.


10. NO_OVERLAP_PROVEN

The predicates are provably mutually exclusive.


11. UNKNOWN

Not enough evidence or unsupported semantics.


12. UNSUPPORTED_NEXT_RULES

The input uses a nextRules path whose exact ordering semantics have not yet been
modeled correctly.


======================================================================
PHASE 0 — INVENTORY EXISTING WORK
======================================================================

Before implementing anything new:

1. Inspect the repository.
2. Inspect differential-analyzer.
3. Inspect existing legacy-adapter and v26-adapter.
4. Inspect README/design notes.
5. Inspect generated examples, snapshots and reports.
6. Inspect prior investigation outputs.
7. Inspect any CSV/workbook-related scripts.
8. Inspect git history relevant to rule selection.
9. Determine what is already implemented and verified.

Produce an internal checklist:

    reusable and correct
    reusable but incomplete
    incorrect
    missing

Do not rebuild functionality that already exists and is correct.


======================================================================
PHASE 1 — RECONFIRM EXACT PRODUCTION SEMANTICS
======================================================================

Reconfirm from source the exact legacy and V26 flows.

Trace:

    DB row
      ->
    parser
      ->
    Rule object
      ->
    store/cache
      ->
    filtering
      ->
    matching
      ->
    top-candidate extraction
      ->
    final selection


For LEGACY determine exactly:

- rule construction
- condition/evaluator filtering
- primary ordering/scoring
- secondary ordering
- complete-tie behavior
- collection types
- encounter-order dependencies
- matchMost/matchFirst/findFirst semantics
- nextRules behavior
- numeric/operator ordering behavior


For V26 determine exactly:

- weight calculation
- rank calculation
- when those values are derived
- what happens before/after cache filtering
- final comparator
- rule ID comparison semantics
- matching flow
- nextRules behavior


Create a concise formal description:

    LegacyPrecedence(rule, context)
    V26Precedence(rule, context)

Do not simplify away behavior that can affect ordering.


======================================================================
PHASE 2 — DOCUMENT MATCHING SEMANTICS
======================================================================

Inspect all supported condition/evaluator types.

Document exact semantics for at least:

    =
    ==
    IN
    LIKE
    IN_LIKE

plus any other operator currently present in EU/AP/AM rule populations.

For each operator establish:

- case sensitivity
- wildcard semantics
- regex conversion
- anchoring
- NULL handling
- empty strings
- trimming
- multiple values
- escaping
- numeric handling
- expression handling

Produce an operator support matrix:

Operator
Present in environment?
Exact semantics understood?
Static intersection supported?
Witness generation supported?
Actual matcher validation supported?


Do not implement approximate semantics if actual production evaluator code can
be invoked.


======================================================================
PHASE 3 — MAP CONDITION FIELDS TO EFFECTIVE INPUT
======================================================================

Enumerate all distinct condition field names in the available environment rule
datasets.

For every field determine its runtime origin:

    DIRECT_HEADER
    EXTRACTED_FROM_MESSAGE_BODY
    NORMALIZED_FIELD
    DERIVED_FIELD
    CONFIGURATION_VALUE
    REQUEST_FLAG
    UNKNOWN

Trace source code where possible.

This is needed both for witness generation and later request-reachability
analysis.


======================================================================
PHASE 4 — IMPLEMENT COMPLETE VERSIONED RULE SNAPSHOT SOURCE
======================================================================

Finish a reusable RuleSource interface.

It must be able to export the complete rule population required by BOTH adapters.

Do not export only simplified RULE_ID + CONDITIONS if that loses information
required by either production parser.

Preserve raw information whenever possible.

Snapshot metadata must include:

    environment
    source DB/schema
    extraction timestamp
    extraction query/version
    source rule count
    condition count
    checksum/hash
    application/rule-format version if relevant
    provenance

The format must be environment-neutral.

Do not hard-code EU rule IDs/countries in evaluation code.


Expected conceptual interface:

    RuleSource(environment) -> VersionedRuleSnapshot


Implement EU PRODLIKE first if that is the currently accessible environment.

Then parameterize for:

    AP PRODLIKE
    AM PRODLIKE

If an environment cannot be accessed now, produce exact configuration/query
instructions so the same tool can run there without code changes.


======================================================================
PHASE 5 — BUILD A SOUND COMPETITION CANDIDATE GENERATOR
======================================================================

Goal:

    identify rule combinations that MAY match the same effective input

without requiring a blind all-rules Cartesian explosion when safe pruning is
possible.


Important:

Pruning must be SOUND.

Only remove a candidate pair/group when incompatibility is proven.

Examples:

    country = GB
    country = US

can safely prove NO overlap.

But:

    LIKE
    IN_LIKE
    regex
    missing field constraints
    expressions

must remain candidates unless incompatibility is genuinely established.


Use cheap indexes/signatures where safe:

- exact equality constraints
- disjoint IN sets
- compatible scope
- condition field sets
- message family where semantics prove exclusion
- country where semantics prove exclusion

Do not use weight/rank as a competition prerequisite.

Weight/rank affect precedence, not whether rules can both match.


Output candidate groups/pairs with:

    rule IDs
    static overlap state
    why they remain candidates
    conflicting/non-conflicting predicates
    unsupported predicates


======================================================================
PHASE 6 — BUILD PREDICATE INTERSECTION + WITNESS GENERATION
======================================================================

Create a reusable WitnessGenerator.

Given candidate rules, attempt to construct an EFFECTIVE MATCHER INPUT satisfying
their predicates.

Progressively support intersection combinations such as:

    EQ vs EQ
    EQ vs IN
    EQ vs LIKE
    EQ vs IN_LIKE
    IN vs IN
    IN vs LIKE
    LIKE vs LIKE
    LIKE vs IN_LIKE
    IN_LIKE vs IN_LIKE

plus operators found in the real environment.

Static reasoning only proposes values.

EVERY witness must then be validated using the actual production matcher.

For each witness:

    validate rule A matches
    validate rule B matches
    validate any intended group members match

If validation fails, either improve generation or mark UNKNOWN.

Never report static string reasoning alone as CONFIRMED.


======================================================================
PHASE 7 — CRITICAL: RUN EVERY WITNESS AGAINST ALL RULES
======================================================================

This is mandatory.

Once a witness is generated from a candidate pair/group:

DO NOT evaluate only those rules.

Run:

    LegacyAdapter.evaluate(
        witness,
        COMPLETE environment rule snapshot
    )

and:

    V26Adapter.evaluate(
        witness,
        COMPLETE environment rule snapshot
    )


Capture:

LEGACY
    allMatchingRules
    topCandidateRules
    possibleWinners
    deterministicWinner if one exists
    orderDependent flag
    selectionPath

V26
    allMatchingRules
    topCandidateRules
    winner
    selectionPath


This prevents false impacts where a third rule outranks both rules that generated
the witness.


======================================================================
PHASE 8 — DETECT DIFFERENTIAL BEHAVIOR
======================================================================

For every validated witness compare the complete results.

Detect at least:

    strict winner reversal
    legacy ambiguity -> V26 determinization
    match-set difference
    same-result competition
    nextRules special path
    unknown

Store a complete evidence object containing:

    environment
    ruleSnapshotId
    witnessId
    witnessSource
    effective matcher input
    legacy matching rules
    legacy top candidates
    legacy possible winners
    legacy deterministic winner
    V26 matching rules
    V26 top candidates
    V26 winner
    selection path
    behavior-change classification
    evidence confidence


======================================================================
PHASE 9 — DEDUPLICATE AND AGGREGATE IMPACTED TRANSITIONS
======================================================================

Different witnesses may prove the same behavior-change transition.

Aggregate results by both:

A. Winner transition

    legacy winner/possible winner -> V26 winner

B. Competition group

    canonical sorted set of top/matching candidate rule IDs


Do not lose full candidate sets when reporting a simple A -> B transition.


Desired summary columns:

    environment
    legacy_rule
    v26_rule
    behavior_change_type
    candidate_rules
    number_of_witnesses
    witness_sources
    sample_witness_id
    sample_effective_input
    legacy_possible_winners
    v26_winner
    rule_snapshot_id
    confidence


======================================================================
PHASE 10 — USE KNOWN WORKBOOK AS A GOLDEN VALIDATION DATASET
======================================================================

Locate/read the existing mismatch CSV/workbook if available.

Do NOT use its winner pairs as seeds for the discovery algorithm.

Run the generic discovery process independently.

Then compare discovered transitions against the workbook.

For every workbook transition classify:

    REDISCOVERED
    PARTIALLY_SUPPORTED
    NOT_FOUND
    CONTRADICTED
    UNTESTABLE_DUE_TO_MISSING_RULE_SNAPSHOT

Calculate:

    total known transitions
    rediscovered transitions
    rediscovery percentage

Also weight by occurrence count if available:

    percentage of known mismatch occurrences covered

High-volume known transitions should be investigated first when something is not
rediscovered.

For example, verify whether known transitions similar to:

    CBPR_GB_IB_151632_02
       ->
    CBPR_GB_IB_151632_01

are rediscovered WITHOUT hard-coding those IDs.


For every NOT_FOUND transition identify the root cause:

    witness-generation gap
    unsupported operator
    snapshot mismatch
    nextRules
    input/context dependency
    workbook issue
    rule no longer present
    implementation bug
    other


======================================================================
PHASE 11 — HISTORICAL RUNTIME EVIDENCE / SPLUNK
======================================================================

After static+witness differential discovery works, enrich the results with
runtime evidence.

Search for exact application log statements identified from source, including:

    multiple rules matched
    candidate rule IDs
    rank/weight if logged
    selected rule
    effective matcher headers/input
    message ID
    business identifier
    correlation ID
    service version


Do not assume broad SPL searches will work.

Use targeted searches against discovered rule IDs and sample identifiers.

For each discovered impacted transition attempt to establish:

    runtime occurrences
    authoritative legacy selected rule
    authoritative V26 selected rule
    service versions
    first seen
    last seen
    unique messages
    sample message/request ID
    actual candidate rules
    actual effective matcher input if available


If direct Splunk access is unavailable or searches time out:

DO NOT stop the investigation.

Instead:

1. produce exact SPL queries,
2. produce CSV schema expected from Splunk export,
3. build an importer,
4. continue all non-runtime analysis,
5. classify runtime evidence as PENDING.


======================================================================
PHASE 12 — HISTORICAL REPLAY WHEN POSSIBLE
======================================================================

If actual effective matcher inputs can be recovered from logs/test artifacts:

replay them against the appropriate rule snapshot.

Be careful:

    historical input + current rule snapshot

is NOT historical proof.

Record explicit evidence class:

    HISTORICAL_SNAPSHOT_REPLAY
    CURRENT_SNAPSHOT_REPLAY


When possible compare:

    authoritative logged legacy winner
    legacy replay result

    authoritative logged V26 winner
    V26 replay result


If replay disagrees with logs, investigate before trusting the replay.

Possible causes include:

    wrong snapshot
    incomplete headers
    application version mismatch
    feature toggles
    nextRules
    configuration differences
    extraction differences


======================================================================
PHASE 13 — nextRules MUST BE A SEPARATE PATH
======================================================================

Trace nextRules fully.

Determine:

    source collection
    ordering
    filtering
    comparator
    whether StoreCache ordering applies
    whether HashSet/HashMap encounter order can decide output


If the existing adapters do not model nextRules correctly:

either implement an exact nextRules adapter path using production code,

or classify affected witnesses:

    UNSUPPORTED_NEXT_RULES

Do not mix those results into normal StoreCache conclusions.


======================================================================
PHASE 14 — CONDITION-FILTERING / DERIVED-PRECEDENCE ANOMALIES
======================================================================

Explicitly inspect rules where:

    weight/rank/required variables are derived
    BEFORE
    some conditions are later filtered or removed

For each anomalous rule report:

    source conditions
    removed conditions
    effective runtime conditions
    derived V26 weight
    derived V26 rank
    actual evaluated conditions

Determine whether such anomalies create differential winner behavior.

These rules should receive a dedicated flag in the final report.


======================================================================
PHASE 15 — ENVIRONMENT PORTABILITY
======================================================================

The analysis engine must remain environment-independent.

Architecture should approximately separate:

    RuleSource
    EffectiveInputSource
    CandidateCompetitionGenerator
    WitnessGenerator
    LegacyAdapter
    V26Adapter
    DifferentialEvaluator
    RuntimeEvidenceSource
    ImpactAggregator
    Reporter


Environment config should supply:

    environment name
    DB connection/schema
    rule export query/config
    Splunk index/source/sourcetype
    service identifiers
    application version expectations
    optional historical artifacts


The following must NOT be hard-coded in core logic:

    EU rule IDs
    country
    schema names
    one Splunk index
    one workbook
    one known pair


======================================================================
PHASE 16 — APPLY TO ALL ACCESSIBLE ENVIRONMENTS
======================================================================

Once EU PRODLIKE is working:

run the SAME engine for every environment currently accessible:

    EU PRODLIKE
    AP PRODLIKE
    AM PRODLIKE

Do not require code changes between regions.

Only environment configuration/data should change.

For each environment produce independent:

    rule snapshot
    coverage metrics
    discovered impacts
    runtime evidence
    unresolved unknowns


If AP or AM access is unavailable, produce a ready-to-run command/config file
and exact steps so the analysis can be executed immediately once access is
available.


======================================================================
COVERAGE REQUIREMENTS
======================================================================

Do NOT claim "all possible impacted rules" without quantifying coverage.

Final report must include:

1. total rules
2. rules parsed successfully
3. rules containing supported operators
4. rules containing unsupported operators
5. candidate competitions generated
6. competitions proven non-overlapping
7. witnesses generated
8. witnesses matcher-validated
9. whole-snapshot differential evaluations executed
10. impacted transitions discovered
11. UNKNOWN candidates
12. nextRules cases
13. filter-anomaly cases
14. golden-set rediscovery percentage
15. runtime-confirmed transitions
16. latent-only transitions


Also report condition-operator coverage:

    operator
    rules using it
    intersection supported?
    witness generation supported?
    matcher validation supported?
    unresolved count


======================================================================
FALSE-NEGATIVE SAFETY RULE
======================================================================

The investigation prioritizes recall over aggressive pruning.

Whenever uncertain whether two predicates can overlap:

KEEP THE CANDIDATE.

Never discard it merely because overlap is inconvenient to analyze.

Use:

    UNKNOWN / NEEDS_WITNESS

rather than:

    NO_OVERLAP


======================================================================
SCALABILITY
======================================================================

Do not perform an unnecessarily expensive naive Cartesian product if safe
indexing/pruning can reduce it.

However correctness is more important than speed.

Implement:

- predicate indexing
- condition signatures
- caching
- witness deduplication
- canonical competition-group IDs
- parallel execution where safe
- resumable/intermediate result files if analysis is large

Record runtime metrics.


======================================================================
AUTONOMY / BLOCKER HANDLING
======================================================================

Do not stop simply because one source is unavailable.

If DB access is unavailable:
    use existing exports/snapshots and produce exact DB extraction commands.

If Splunk access is unavailable:
    produce exact SPL + importer and continue static/witness analysis.

If historical rule snapshots are unavailable:
    explicitly classify current-snapshot replay.

If an operator cannot be solved statically:
    attempt actual matcher-based witness validation.

If witness construction for an operator is unsolved:
    retain UNKNOWN and quantify coverage.

If one environment is inaccessible:
    complete all accessible environments and provide ready-to-run configuration
    for the blocked one.

Only ask me a question if there is literally no reasonable path forward without
information only I can provide.


======================================================================
FINAL OUTPUT ARTIFACTS
======================================================================

Produce durable artifacts in the investigation workspace.

At minimum create:

1. FINAL_INVESTIGATION_REPORT.md

2. impact-summary.csv

3. impact-detail.json or jsonl

4. unknown-candidates.csv

5. coverage-report.csv

6. rule-snapshot metadata/files per environment

7. witness dataset

8. golden-set comparison report

9. runtime/Splunk evidence report or pending-search package

10. environment config/templates for:
       EU_PRODLIKE
       AP_PRODLIKE
       AM_PRODLIKE

11. README with exact commands required to rerun the full analysis.


======================================================================
FINAL IMPACT REPORT SCHEMA
======================================================================

The detailed final impact table should contain, where available:

environment

behavior_change_type

legacy_rule
legacy_deterministic_winner
legacy_possible_winners

v26_rule
v26_winner

candidate_rules
all_legacy_matching_rules
all_v26_matching_rules

selection_path

witness_id
witness_source
effective_matcher_input

request_reachable
historically_observed

runtime_occurrences
unique_messages
first_seen
last_seen

sample_message_id
sample_correlation_id

legacy_service_version
v26_service_version

rule_snapshot_id

filter_anomaly
nextRules

golden_set_status

confidence

evidence
notes


======================================================================
FINAL REPORT STRUCTURE
======================================================================

FINAL_INVESTIGATION_REPORT.md must contain:


1. Executive Summary

Answer clearly:

    Is there a V26 rule-selection behavior change?
    What types of change exist?
    How many impacted transitions were found?
    Which environments were analyzed?
    Which impacts are runtime-observed vs latent?
    What coverage/confidence was achieved?


2. Root Cause

Explain the exact relevant legacy and V26 behavior from source.


3. Exact Legacy Selection Algorithm


4. Exact V26 Selection Algorithm


5. Matching / Condition Semantics


6. End-to-End Routing Input Flow

    raw message
      ->
    extracted/derived headers
      ->
    effective matcher input
      ->
    rule matching
      ->
    winner


7. Differential Analysis Architecture


8. Rule Snapshot Method


9. Competition Discovery Method


10. Witness Generation Method


11. Whole-Snapshot Validation Method


12. Impact Classification Model


13. EU PRODLIKE Results


14. AP PRODLIKE Results


15. AM PRODLIKE Results


16. Top Impacted Winner Transitions

Include counts/evidence where available.


17. Runtime/Splunk Evidence


18. Existing Workbook Comparison

Include rediscovery rate.


19. Coverage and Limitations


20. Unknown / Unsupported Cases


21. nextRules Findings


22. Condition-Filtering / Weight-Rank Anomalies


23. Confidence Assessment


24. Reproduction Instructions


25. Recommended Operational Next Steps


======================================================================
EXECUTIVE SUMMARY REQUIREMENT
======================================================================

The executive summary must be understandable by someone who does not know the
implementation.

Use language similar to:

    "We identified X rule-selection transitions where the same effective routing
    input can lead to different selection behavior between legacy and V26.

    Y were observed in historical/runtime evidence.
    Z were proven using synthetic matcher-valid witnesses.
    N remain possible but unresolved.

    The primary behavior change is ...
    Additional behavior changes include ...

    Analysis covered X/Y rules and Z% of active condition/operator usage."


Do not overstate completeness.


======================================================================
TOP TRANSITIONS REQUIREMENT
======================================================================

Include a concise table like:

Legacy / possible legacy winner
V26 winner
Environment
Change type
Candidate group
Runtime count
Witness count
Evidence
Confidence


Example shape only:

rule-XXX
CBPR-XXXX
EU_PRODLIKE
OBSERVED_TIE_RESOLUTION_CHANGE
[rule-XXX, CBPR-XXXX]
2151
3
Splunk + replay
HIGH


======================================================================
SOURCE TRACEABILITY
======================================================================

For every important technical conclusion provide:

    repository path
    class
    method
    relevant line/reference
    commit/PR when applicable

Separate:

    CONFIRMED FROM SOURCE
    CONFIRMED FROM EXECUTION
    SUPPORTED BY DATA
    HYPOTHESIS
    UNKNOWN


======================================================================
SUCCESS CRITERIA
======================================================================

The investigation is complete when:

1. The differential engine evaluates whole environment snapshots.

2. Impact discovery does not depend on pre-known rule IDs.

3. At least the known EU example can be independently rediscovered.

4. Candidate witnesses are validated by the real matcher.

5. Winners are decided by the real legacy and V26 code.

6. Whole-snapshot evaluation prevents pairwise false positives.

7. Legacy order-dependent ties are modeled correctly.

8. nextRules is either modeled exactly or explicitly separated.

9. Coverage is quantified.

10. The method is environment-parameterized.

11. All accessible PRODLIKE environments have been run.

12. A final impact report and machine-readable result set are produced.


======================================================================
VERY IMPORTANT FINAL INSTRUCTION
======================================================================

Do not finish with:

    "The next step is..."

Carry out the next steps yourself wherever access permits.

Do not give me another design proposal as the final result.

The final response should say:

    what was implemented
    what was executed
    what was discovered
    the impacted rule transitions
    their evidence/confidence
    coverage
    unresolved cases
    exact output artifact locations

If some external access prevented part of the investigation, complete everything
else and provide the exact ready-to-run command/query/configuration needed for
that blocked part.

The objective is a FINAL, reproducible, environment-portable rule-impact
investigation — not another intermediate investigation plan.
