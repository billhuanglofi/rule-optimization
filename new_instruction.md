# Phase 1.8 — CBPR_IN Cross-Market Pilot

Proceed with CBPR_IN% as the next cross-market Phase 1 pilot.

The objective is to validate that the Phase 1 framework generalizes beyond CBPR_MY.

Do not start Phase 2 optimization.

## Important business-rule restriction

The following MY-specific rule MUST NOT be reused:

`cond-sendercountry = MY -> market_group = MY`

Do not automatically generalize this into:

`cond-sendercountry = IN -> market_group = IN`

unless the IN BRT or SME explicitly confirms that mapping.

The following rule remains GLOBAL:

- destination / routing_hub describes technical downstream routing information;
- destination / routing_hub is NOT independent market evidence.

## 1. Preserve the MY validated baseline

Do not modify:

- CBPR_MY validated snapshot;
- MY Phase 1.5 validation;
- MY Phase 1.6 validation;
- MY business mappings.

Treat CBPR_MY as a frozen reference baseline.

## 2. Create a separate IN snapshot

Scope:

`CBPR_IN%`

Use the same high-throughput architecture:

- bulk Oracle extraction;
- zero per-rule Oracle queries;
- local snapshot;
- local deterministic parsing;
- cached node semantics;
- ambiguity clustering.

Create market-separated cache/output files so MY and IN results cannot overwrite each other.

Suggested structure:

analysis/markets/IN/
analysis/cache/IN/

## 3. Reuse only approved semantics

Reuse:

- GLOBAL business rules;
- previously validated generic node semantics;
- generic branch parsing;
- generic transaction-state parsing;
- hierarchical signature logic.

For the 5 node IDs that are new compared with MY:

- inspect them once;
- classify their semantics;
- add them to the reusable node catalog only when meaning is clear.

Report these new nodes separately.

## 4. Discover IN market evidence

Do NOT pre-classify the 235 rules as IN merely because:

- their IDs contain `CBPR_IN`;
- the extraction scope is `CBPR_IN%`.

The scope selects the population but is not market evidence.

Run the deterministic classifier and identify the actual BRT-aligned condition patterns used by these rules.

Possible evidence may include:

- explicit country condition;
- sender-country condition;
- receiver-country condition;
- product/BRT-specific condition;
- approved mapping.

Do not invent mappings.

## 5. Generate gaps before asking for manual rule review

If some rules cannot be classified confidently, cluster them by shared cause.

Create:

`analysis/markets/IN/phase-1-gaps.md`

Prefer:

"78 rules affected by 2 ambiguity patterns"

instead of:

"78 rules need individual review."

For each ambiguity provide:

- affected rule count;
- shared condition pattern;
- representative examples;
- exact question for SME;
- evidence already available.

I will answer BRT questions after this first pass.

## 6. Generate hierarchical signatures

For every IN rule generate:

- condition_signature
- flow_signature
- error_signature
- exact_signature

Then compare IN against MY at the FAMILY level.

Create:

`analysis/cross-market/MY-vs-IN.md`

Report:

### Shared semantics
- node IDs shared
- flow families shared
- error families shared

### New IN semantics
- new node IDs
- new flow families
- new error families
- new condition dimensions

### Different business configuration
Identify cases where the same flow structure exists in MY and IN but differs only by:
- market conditions;
- currency;
- destination;
- processor;
- transaction state;
- endpoint/global variable.

Do NOT call these duplicates or optimization candidates yet.

## 7. Required first-pass IN metrics

Report:

Rules processed:
Node rows:
Unique node IDs:
New node IDs vs MY:

UNDERSTOOD:
PARTIAL:
UNKNOWN:
NEEDS_REVIEW:

Unique ambiguity patterns:
Rules affected by ambiguity:

Condition signatures:
Flow signatures:
Error signatures:
Exact signatures:

Shared flow signatures with MY:
New flow signatures vs MY:

Oracle calls:
Processing duration:

## 8. Decision gate

At the end return one of:

IN_BRT_INPUT_REQUIRED
IN_PILOT_READY_FOR_VALIDATION
PARSER_EXTENSION_REQUIRED

Do not automatically resolve BRT-specific ambiguity.

Do not automatically start Phase 1.5/1.6 validation.

Do not start SG, HK, the full estate, or Phase 2.
