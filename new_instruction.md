# Phase 1.7 — Hierarchical Signatures and Scale Readiness

The CBPR_MY Phase 1 pilot is now validated.

Do not start optimization.

Do not modify the validated interpretation rules unless a new business
conflict is discovered.

## 1. Freeze the validated pilot

Record the current baseline as:

PHASE_1_CBPR_MY_VALIDATED

Preserve:

- current rules.jsonl
- Phase 1 gaps/resolutions
- Phase 1.5 market validation
- Phase 1.6 flow validation
- business-rules.md
- node catalog
- parser/generator version

Create a snapshot/version marker so later changes can be compared against it.

## 2. Add hierarchical rule signatures

The existing exact signature is too specific for family-level analysis.

For every rule generate these independent signatures:

### condition_signature

Include normalized business entry conditions such as:

- market_group
- country/sendercountry
- currency
- message type
- product/service
- important BRT condition dimensions

Exclude:

- rule ID
- environment
- destination unless destination is itself an explicit condition

### flow_signature

Represent major business actions in execution order.

Example:

VALIDATION
> DUPLICATE_CHECK
> SANCTIONS
> ROUTING
> PDP_REPORTING
> PAYMENT_COMPLETION

Normalize equivalent node IDs into common semantic categories.

Do not include implementation-only values that prevent useful clustering.

### error_signature

Represent error/retry structure independently.

Include:

- error block presence
- retry block presence
- replay/wiretap behavior
- error terminal action
- important error transaction states

### exact_signature

Retain the current detailed structural signature for exact comparison.

## 3. Produce clustering statistics

Create:

analysis/signature-analysis.md

Report:

Rules:
Unique condition signatures:
Unique flow signatures:
Unique error signatures:
Unique exact signatures:

Top 20 condition clusters:
Top 20 flow clusters:
Top 20 error clusters:

For each cluster include:

- number of rules
- representative rule IDs
- important common characteristics

Do not label anything as redundant yet.

## 4. Detect differences inside families

For rules sharing the same flow_signature but different exact_signature,
summarize the dimensions causing the differences.

Examples:

same flow, different:
- currency
- market condition
- destination
- transaction state
- callback configuration
- error behavior
- payload flag
- implementation node

Create:

analysis/signature-differences.md

The purpose is understanding, not optimization.

## 5. Prepare cross-market validation

Do not run the entire estate immediately.

Identify 2–3 additional rule populations that are structurally or
business-wise different from CBPR_MY.

Prefer populations that exercise different:

- market/BRT mappings
- currencies
- destinations/processors
- message types
- flow families

For each candidate report:

- estimated rule count
- node count
- unique node IDs
- new node IDs not seen in CBPR_MY
- expected business-rule gaps

Do not classify a new market using MY-specific fallback rules unless
the BRT explicitly says they are reusable.

## 6. Business-rule scoping

Review all permanent business rules.

Classify each as:

GLOBAL
MARKET_SPECIFIC
BRT_SPECIFIC
PILOT_ONLY

For example:

Destination/routing_hub is not market evidence
→ likely GLOBAL

cond-sendercountry = MY -> market_group = MY
→ MY/BRT-specific

Do not apply market-specific mappings globally.

Create:

docs/business-rule-scope.md

## 7. Decision gate

Recommend one of:

READY_FOR_CROSS_MARKET_PILOT
NEEDS_TAXONOMY_WORK
NEEDS_PARSER_WORK

Do not start Phase 2 optimization automatically.
