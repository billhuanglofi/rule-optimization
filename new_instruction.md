## Ambiguity A01: branch structure issue

Affected rules: 1

Representative rule:
- `CBPR_IN_OB_209023`

### SME Resolution

Status: RESOLVED_AS_SOURCE_DEFECT

`CBPR_IN_OB_209023` has an incorrect rule setup.

The following structure is not an intentional supported control-flow pattern:

- `block-on-error`
- `act-pol-sms-retry-service`
- `block-on-retry-end`

The parser must NOT reinterpret or automatically repair this sequence.

Classification:

- source/rule configuration defect
- keep structural validation status as `NEEDS_REVIEW`
- exclude this rule from assertions that require valid error/retry block pairing
- do not use this example to change the generic branch parser

The rule may still receive independent market/condition classification if valid market evidence exists.

Action for later phase:
Correct the production rule setup through the normal rule-change process.



## Ambiguity A02: missing approved IN market evidence

Affected rules: 40

### SME Resolution

Status: RESOLVED

This is an IN BRT-specific market classification rule.

For an OUTBOUND rule:

IF any approved IN-origin evidence is present:

- sender institution is HSBC; OR
- sender institution is HASE; OR
- `cond-sendercountry = IN`

THEN:

- classify `market_group = IN`
- confidence = HIGH
- record the exact matched condition as market evidence

This mapping applies to OUTBOUND rules only.

### Important restrictions

- `CBPR_IN%` in the rule ID is not market evidence.
- extraction scope is not market evidence.
- destination/routing_hub is not market evidence.
- processor or endpoint is not market evidence.
- do not apply this outbound rule to inbound rules.
- do not generalize this mapping to another market without its BRT/SME approval.

### Precedence

1. Explicit approved country/market condition.
2. Approved IN outbound BRT evidence:
   - sender institution = HSBC
   - sender institution = HASE
   - sendercountry = IN
3. If evidence conflicts, use `NEEDS_REVIEW`.
4. If no approved evidence exists, keep market unresolved.
