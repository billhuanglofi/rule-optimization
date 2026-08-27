# Phase 2B — Business Family Review and Naming

The Phase 2A business-model discovery is complete.

Do NOT optimize, merge, delete, or rewrite rules yet.

The goal of this phase is to make the discovered business model understandable
to engineers and SMEs, and to validate that the 124 business families represent
real business patterns rather than accidental technical clustering.

Use existing local data only.
Do not query Oracle unless a specific missing fact cannot be resolved locally.

## 1. Preserve existing family IDs

Keep stable IDs such as:

BF001
BF002
BF003

Do not renumber them.

Add a human-readable business name and description.

Example:

BF001
Human name:
SG MX GPI inbound payment-receipt flow

Do not invent names from rule IDs alone.

Use evidence from:
- market conditions
- message family/type
- channel/source conditions
- flow signature
- downstream behavior
- transaction states
- representative rules

If a business purpose is uncertain, say UNKNOWN or use a neutral technical name.

## 2. Review the largest families first

Review at least:

- top 20 largest business families
- every family with >= 20 rules
- every cross-market family
- every family containing known source defects
- every family containing partial business context

For each family produce:

Family ID:
Human-readable name:
Rule count:
Markets:
Direction:
Direction confidence:
Channel/source:
Message family:
Important entry conditions:
Typical business flow:
Typical error/retry flow:
Downstream system category:
Transaction-state pattern:
Terminal outcome:
Representative rules:
Known variants:
Known defects:
Confidence:
SME question, if any:

## 3. Validate family boundaries

For each reviewed family, sample at least 3 representative rules.

Check whether they genuinely share:

- business entry conditions
- message family
- business flow
- major state transitions
- terminal outcome

Do not require exact implementation to match.

Classify the family:

VALIDATED_FAMILY
TOO_BROAD
TOO_NARROW
MIXED_BUSINESS_PURPOSE
NEEDS_SME_INPUT

If TOO_BROAD or MIXED_BUSINESS_PURPOSE:
propose a better family split.

Do not change production rules.

## 4. Review direction carefully

Current direction is weak metadata inferred from rule IDs such as IB / OB.

Keep:

direction_source = rule_id_metadata
direction_confidence = LOW or MEDIUM

Do not promote this to strong business evidence unless supported by conditions
or BRT knowledge.

Try to find condition-based or flow-based direction evidence such as:

- sender vs receiver role
- channel ingress
- message direction semantics
- return/reply message type
- BRT-specific conditions

If no stronger evidence exists, retain the weak metadata explicitly.

## 5. Separate business dimensions

Keep these independent:

market
direction
channel/source
message family
business conditions
business flow
downstream system
transaction-state model
error/retry model

Do not infer market from downstream routing.

Do not infer direction from market.

Do not infer channel from destination.

## 6. Cross-market family review

For the families shared across:

- MY + IN + SG
- MY + IN
- MY + SG
- IN + SG

produce a comparison showing:

Shared business behavior:
Different market conditions:
Different channel/source conditions:
Different downstream routing:
Different transaction states:
Different error handling:
Different implementation nodes:

The purpose is to determine whether they are truly the same business family
with market-specific configuration, or merely structurally similar.

Use classifications:

SAME_BUSINESS_FAMILY
RELATED_VARIANT
STRUCTURALLY_SIMILAR_ONLY
UNCERTAIN

Do not call anything redundant yet.

## 7. Review the 16 partial-context rules

Group the 16 partial rules by shared missing information.

Do not review one-by-one unless necessary.

Report:

Partial rules:
Unique missing-context patterns:
Rules per pattern:
Question needed to resolve each pattern:

## 8. Known defects

Keep the known source defects separate from business-family validity.

Examples include:
- CBPR_IN_OB_209023
- SG unmatched control-marker rules
- SG receiver-side evidence conflict

A family may still be business-valid even if one member is a source defect.

## 9. Produce outputs

Create:

analysis/business-family-review.md
analysis/business-family-names.md
analysis/cross-market-family-review.md
analysis/partial-context-review.md

Update:

analysis/business-families.json

with fields such as:

human_name
family_validation_status
direction_source
direction_confidence
business_confidence

## 10. Final decision

Report:

Families reviewed:
VALIDATED_FAMILY:
TOO_BROAD:
TOO_NARROW:
MIXED_BUSINESS_PURPOSE:
NEEDS_SME_INPUT:

Cross-market:
SAME_BUSINESS_FAMILY:
RELATED_VARIANT:
STRUCTURALLY_SIMILAR_ONLY:
UNCERTAIN:

Partial rules remaining:
Business-model gaps remaining:

Then recommend one of:

READY_FOR_OPTIMIZATION_DISCOVERY
BUSINESS_FAMILY_REFINEMENT_REQUIRED
MORE_SME_INPUT_REQUIRED

Do NOT start optimization automatically.
