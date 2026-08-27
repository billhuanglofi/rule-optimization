# Rule: `<RULE_ID>`

## Metadata

```yaml
rule_id: ""
description: ""
environment: ""
node_count: 0
status: UNDERSTOOD|PARTIAL|UNKNOWN|NEEDS_REVIEW
```

## Business summary

Answer:

- What traffic reaches this rule?
- What does it do?
- What is the final outcome?

## Market classification

```yaml
origin_market:
  value: UNKNOWN
  confidence: LOW
  evidence: []

destination_market:
  value: UNKNOWN
  confidence: LOW
  evidence: []

market_group:
  value: UNKNOWN
  confidence: LOW
  evidence: []

currency:
  values: []
  evidence: []

routing_hub:
  values: []
  evidence: []
```

## Flow classification

```yaml
flows:
  - value: UNKNOWN
    confidence: LOW
    evidence: []
```

## Conditions

| Rank | Node ID | Dimension | Operator | Value | Evidence/Notes |
|---:|---|---|---|---|---|

## Ordered node flow

| Rank | Branch | Node ID | Type | Short interpretation | Important verb fields |
|---:|---|---|---|---|---|

## Main path

```text
IF ...
THEN ...
ENDS WITH ...
```

## Error path

```yaml
present: false
start_rank:
end_rank:
behavior: ""
replay_or_wiretap: ""
completion_node: ""
```

## Retry path

```yaml
present: false
start_rank:
end_rank:
behavior: ""
```

## Dependencies

### Global variables

| Variable | Environment | Purpose | Used at rank |
|---|---|---|---:|

### External services / hubs / processors

| Name | Type | Evidence | Used at rank |
|---|---|---|---:|

## Transaction states

| Rank | State | Meaning / context | Branch |
|---:|---|---|---|

## Terminal behavior

```yaml
main_terminal_node: UNKNOWN
main_terminal_rank:
error_terminal_node: UNKNOWN
retry_terminal_node: UNKNOWN
```

## Rule signature

```text
<normalized signature>
```

## Evidence quality

```yaml
overall_confidence: LOW|MEDIUM|HIGH
strongest_evidence:
  - ""
weak_or_inferred:
  - ""
```

## Open questions

- None yet.

## Optimization observations for later

Do not change the rule in Phase 1.

- None yet.
