# Phase 3B — Optimization Candidate Deep Dive

The business model is validated and optimization discovery is complete.

Current state:

- 1,451 rules modeled
- 124 validated business families
- 0 unresolved SME/business questions
- known defects are blocked
- no P1 optimization candidate was identified
- current candidates are P2 discovery targets

Do NOT modify production rules.
Do NOT generate implementation SQL.
Do NOT merge or delete rules.

The objective is to understand WHY the largest P2 families contain so many
rule variants and determine whether the variation is:

1. business-required;
2. configuration-required;
3. implementation duplication;
4. parameterizable;
5. potentially obsolete/unreachable.

Start with BF001 and then the next 2-4 highest-impact P2 families.

---

## 1. Deep-dive BF001

Current known shape:

- approximately 251 rules
- approximately 244 condition patterns
- approximately 250 exact implementations
- 1 normalized business flow
- 2 error/retry patterns

Do not conclude that BF001 should be merged.

Instead determine what dimensions create the 244 condition patterns.

Build a normalized variation matrix.

Possible dimensions include:

- market
- direction
- channel/source
- message family/type
- sender country
- receiver country
- sender address
- receiver address
- currency
- participant/institution
- product/service
- routing condition
- downstream processor
- transaction-state variation
- payload flags
- endpoint/global variable
- error/retry behavior
- terminal behavior

For every differing dimension report:

Dimension:
Unique values/patterns:
Rules affected:
Business-required? YES / NO / UNKNOWN
Evidence:
Potentially parameterizable? YES / NO / UNKNOWN

---

## 2. Separate condition structure from condition values

This is important.

Two rules may contain different values but the same logical condition structure.

Example:

Rule A:
country = SG
currency = SGD
receiver = BANK_A

Rule B:
country = SG
currency = USD
receiver = BANK_B

These have different exact conditions but may share the same condition schema:

country
+ currency
+ receiver

Create:

condition_schema_signature

This signature should describe WHICH condition dimensions exist, not their
specific values.

Then report for BF001:

Rules:
Unique exact condition patterns:
Unique condition-schema signatures:

Top condition schemas:
- schema
- rule count
- varying values
- representative rules

This will tell us whether 244 condition patterns are actually only a small
number of reusable business templates.

---

## 3. Separate flow structure from configuration

Since BF001 has one normalized business flow, determine whether its exact
implementation differences come mainly from configuration.

For the common flow identify:

Common ordered business stages:
Common actions:
Common terminal behavior:

Then identify varying implementation parameters:

- routing destination
- global variable
- endpoint
- transaction state
- payload field
- formatter configuration
- channel configuration
- callback configuration
- other verb values

Report:

structural differences
versus
configuration-value differences

Do not treat configuration differences as separate business flows.

---

## 4. Look for parameterization opportunities

Identify patterns where many rules have:

same business purpose
+ same condition schema
+ same flow
+ different values

These may be candidates for a data-driven/template-driven rule model.

Classify potential opportunities as:

VALUE_PARAMETERIZATION

CONDITION_TABLE

ROUTING_CONFIGURATION

COMMON_FLOW_TEMPLATE

COMMON_ERROR_TEMPLATE

COMMON_COMPLETION_TEMPLATE

Do not propose implementation yet.

For each opportunity show:

Affected rules:
Business family:
Common structure:
Values that vary:
Current number of rules:
Potential conceptual representation:
Business risks:
Confidence:

Example conceptual result:

Current:

40 rules

same:
country + currency + receiver conditions
same processing flow

different:
currency
receiver
destination

Potential model:

1 rule/template
+ 40 configuration rows

This is only a conceptual opportunity, not a proposed production change.

---

## 5. Find meaningful subfamilies inside BF001

Do not use exact rule signature.

Cluster BF001 using:

condition_schema_signature
+ normalized business flow
+ error_signature
+ terminal behavior

Then report:

BF001 total rules:
Business subfamilies:
Largest subfamily:
Smallest subfamily:

For each subfamily:

Subfamily ID:
Rule count:
Condition schema:
Common flow:
Error pattern:
Values that vary:
Representative rules:
Business interpretation:

The goal is to reduce:

251 individual rules

into a much smaller number of understandable implementation patterns.

---

## 6. Identify outliers

Within each large family identify rules that are unusual compared with peers.

Examples:

- unique condition schema
- unique action
- unique transaction state
- unique routing path
- extra retry/error behavior
- hardcoded value
- unique downstream system
- unusually high/low node count

Classify:

EXPECTED_BUSINESS_VARIANT
POSSIBLE_LEGACY_VARIANT
POSSIBLE_DEFECT
NEEDS_REVIEW

Do not call something obsolete without evidence.

---

## 7. Analyze repeated tails and blocks

Find identical or near-identical ordered subsequences such as:

validation -> reporting -> completion

or

block-on-error
-> replay/wiretap
-> payment-message-complete
-> block-on-error-end

Report repeated sequences with:

Sequence ID:
Normalized nodes/actions:
Occurrences:
Business families:
Markets:
Configuration differences:

This can reveal reusable common components even when whole rules cannot be
consolidated.

---

## 8. Rank optimization forms separately

Do not use one generic optimization score.

For each family score:

### Rule-count reduction potential

Could many rules become fewer rule definitions plus configuration?

### Maintenance reduction potential

Would one shared template reduce repeated changes?

### Runtime simplification potential

Would the execution path become simpler?

### Risk

Could consolidation accidentally change business behavior?

### Evidence confidence

How strongly does current rule evidence support the opportunity?

Use:

HIGH
MEDIUM
LOW

---

## 9. Produce a candidate review sheet

For the top 10 deep-dive opportunities create:

Candidate ID:
Business family:
Opportunity type:
Current rule count:
Condition schemas:
Normalized flows:
Exact implementations:
Main varying dimensions:
Potential target model:
Estimated conceptual reduction:
Business behavior preserved:
Known exceptions:
Risks:
Confidence:
Required human validation:

Do not calculate fake precision.

Example:

Current rules: 60
Potential template families: 3-5

is better than claiming:

Reduction = 93.7%

without an implementation design.

---

## 10. Keep known defects excluded

The existing known defects remain BLOCKED.

Do not use malformed or conflicting rules as consolidation examples.

If a newly discovered outlier appears defective, add it to the defect report
instead of forcing it into an optimization family.

---

## 11. Required outputs

Create:

analysis/optimization-deep-dive.md
analysis/optimization-subfamilies.md
analysis/condition-schema-analysis.md
analysis/parameterization-opportunities.md
analysis/repeated-flow-blocks.md
analysis/optimization-review-sheet.md

Also update machine-readable candidate data where useful.

---

## 12. Final report

Report:

Families deep-reviewed:
Rules covered:

Unique exact condition patterns:
Unique condition schemas:
Unique normalized flows:
Unique error patterns:

Parameterization opportunities:
Common-flow-template opportunities:
Common-error-template opportunities:
Common-completion-template opportunities:

Outliers:
Possible new defects:

Top 10 opportunities:

For BF001 specifically report:

Rules:
Exact condition patterns:
Condition schemas:
Exact implementations:
Normalized flows:
Error patterns:
Largest implementation subfamily:
Main dimensions causing fragmentation:

Then recommend:

READY_FOR_OPTIMIZATION_DESIGN

or

MORE_CANDIDATE_ANALYSIS_REQUIRED

Do NOT implement any changes.
