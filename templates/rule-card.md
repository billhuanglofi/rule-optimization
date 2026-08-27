# Rule: `<RULE_ID>`

## 1. Metadata

```yaml
rule_id: ""
description: ""
environment: ""
node_count: 0
status: UNDERSTOOD|PARTIAL|UNKNOWN|NEEDS_REVIEW
```

## 2. Business summary

Write 2–5 sentences answering:

- What traffic reaches this rule?
- What does the rule do?
- What is the final outcome?

## 3. Market classification

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

## 4. Flow classification

```yaml
flows:
  - value: UNKNOWN
    confidence: LOW
    evidence: []
```

## 5. Conditions

| Rank | Node ID | Dimension | Operator | Value | Evidence/Notes |
|---:|---|---|---|---|---|

## 6. Ordered node flow

| Rank | Branch | Node ID | Type | Short interpretation | Important verb fields |
|---:|---|---|---|---|---|

## 7. Main path

```text
IF ...
THEN ...
ENDS WITH ...
```

## 8. Error path

```yaml
present: false
start_rank:
end_rank:
behavior: ""
replay_or_wiretap: ""
completion_node: ""
```

## 9. Retry path

```yaml
present: false
start_rank:
end_rank:
behavior: ""
```

## 10. Dependencies

### Global variables

| Variable | Environment | Purpose | Used at rank |
|---|---|---|---:|

### External services / hubs / processors

| Name | Type | Evidence | Used at rank |
|---|---|---|---:|

### Payload dependencies

| Consumer rank | Flag/value | Required producer | Producer rank | Satisfied? |
|---:|---|---|---:|---|

## 11. Transaction states

| Rank | State | Meaning / context | Branch |
|---:|---|---|---|

## 12. Terminal behavior

```yaml
main_terminal_node: UNKNOWN
main_terminal_rank:
error_terminal_node: UNKNOWN
retry_terminal_node: UNKNOWN
```

## 13. Evidence quality

```yaml
overall_confidence: LOW|MEDIUM|HIGH
strongest_evidence:
  - ""
weak_or_inferred:
  - ""
```

## 14. Open questions

- None yet.

## 15. Optimization observations for later

Do not change the rule in Phase 1.

- None yet.
