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
