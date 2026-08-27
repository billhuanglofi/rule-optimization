# Phase 1.5 — Validate the CBPR_MY Classifier

Do not optimize rules yet.

The CBPR_MY pilot now reports:

- 507 rules processed
- 507 UNDERSTOOD
- 0 PARTIAL
- 0 UNKNOWN
- 0 NEEDS_REVIEW
- 0 remaining ambiguity patterns

Before scaling to the full rule estate, validate that these classifications are correct and that SME rules were not over-applied.

Use the existing local snapshot only unless a specific validation question requires new DB evidence.

## 1. Validate market evidence

For every rule, verify that `market_group = MY` has an explicit evidence path.

Allowed evidence paths currently include:

### Path A — explicit BRT-aligned market/country condition

Example:

`cond-country = MY`

### Path B — approved SME fallback

When no explicit country/market condition exists:

`cond-sendercountry = MY`
→ `market_group = MY`

### Not allowed as market evidence

The following MUST NOT independently classify market as MY:

- rule ID/name containing `MY`
- destination
- routing_hub
- processor
- endpoint
- deployment environment
- arbitrary occurrence of the text `MY`

For every one of the 507 records, add a normalized field such as:

```json
"market_classification_source": {
  "type": "explicit_country|sendercountry_fallback|approved_mapping",
  "evidence": ["..."]
}
