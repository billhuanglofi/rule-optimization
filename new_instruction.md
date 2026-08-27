# Phase 1.9 — Validate and Freeze CBPR_IN

The IN SME gaps have been resolved.

Current expected state:

- 235 rules
- 195 market classifications from explicit country evidence
- 40 market classifications from approved IN outbound BRT evidence
- 1 known source defect: CBPR_IN_OB_209023

Do not query Oracle.
Use the existing IN snapshot only.

## 1. Validate market evidence for all 235 rules

Every `market_group = IN` classification must have an approved evidence path.

Approved sources:

### explicit_country

Example:

`cond-country = IN`

### outbound BRT fallback

For OUTBOUND rules only:

- `cond-sendercountry = IN`
- approved sender institution condition = HSBC
- approved sender institution condition = HASE

The exact normalized matched condition must be recorded.

Not allowed as market evidence:

- rule ID containing IN
- extraction scope
- destination
- routing_hub
- processor
- endpoint
- environment

For each rule ensure:

```json
"market_classification_source": {
  "type": "...",
  "evidence": ["..."]
}
