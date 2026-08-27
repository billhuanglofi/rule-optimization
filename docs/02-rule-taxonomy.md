# Rule Classification Taxonomy

Use this taxonomy consistently across teammates and agents.

## Market/geography dimensions

Keep these separate:

| Dimension | Meaning |
|---|---|
| `origin_market` | where traffic originates |
| `destination_market` | geographic destination |
| `market_group` | business grouping/code |
| `currency` | instruction/settlement currency |
| `routing_hub` | technical routing destination |
| `processor` | downstream processor/service |
| `environment` | PROD/UAT/SIT/etc. |

Do not merge these without evidence.

## Flow tags

Use multiple tags when appropriate:

```text
validation
routing
payment-initiation
payment-completion
message-formatting
translation
recombination
duplicate-check
sanctions
screening
pdp-reporting
callback
notification
sms
replay
retry
error-handling
processor-delivery
channel-delivery
custom
unknown
```

## Condition normalization

Example:

```yaml
conditions:
  - dimension: country
    value: CBPR
    operator: "="
    rank: 0
    node_id: cond-country
    raw_verb: "country:CBPR"
```

Keep raw evidence.

## Action normalization

Example:

```yaml
actions:
  - rank: 10
    node_id: act-...
    category: routing
    summary: "..."
    key_values:
      destination: "..."
```

## Branch classification

Use:

```text
MAIN
ERROR
RETRY
ERROR_NESTED
RETRY_NESTED
UNKNOWN_BRANCH
```

Every ordered node should belong to one branch.

## Confidence

### HIGH
Direct condition/action evidence.

### MEDIUM
Several supporting clues but no single explicit source.

### LOW
Mostly naming convention or incomplete evidence.

## Status

```text
UNDERSTOOD
PARTIAL
UNKNOWN
NEEDS_REVIEW
```

`UNKNOWN` is valid and preferred over guessing.
