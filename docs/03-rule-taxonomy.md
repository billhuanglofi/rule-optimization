# Rule Classification Taxonomy

Use this taxonomy to keep classifications consistent across teammates and AI tools.

## 1. Market / geography dimensions

Keep these separate:

| Dimension | Meaning | Example evidence |
|---|---|---|
| `origin_market` | where traffic originates | country/source condition |
| `destination_market` | intended geographic destination | explicit destination/country condition |
| `market_group` | business grouping used by the rules | project-specific market code |
| `currency` | instruction/settlement currency | instruction-currency condition |
| `routing_hub` | technical processing destination | hub/receiver value |
| `environment` | PROD/UAT/SIT/etc. | table environment field |

Do **not** merge `routing_hub` and `market` unless business evidence says they are equivalent.

## 2. Flow tags

Use multiple tags when appropriate.

### Validation

Signals:

- validation action nodes;
- message-type checks;
- field validation;
- pre-validation transaction states.

### Routing

Signals:

- receiver/destination selection;
- processor or hub selection;
- app URI / endpoint selection;
- channel selection.

### Recombination

Signals:

- recombination IDs;
- auto-recombination;
- recombination service output;
- recombination fail/retry logic.

### Translation

Signals:

- translated payload flags;
- message translation nodes.

### Duplicate check

Signals:

- duplicate-check nodes;
- duplicate-failure transaction state.

### Sanctions / screening

Signals:

- sanctions boxing;
- sanctions callbacks;
- sanctions ACK/NACK transaction states.

### Error handling / replay

Signals:

- `block-on-error`;
- `act-on-error`;
- retry blocks;
- error replay topics;
- wiretap/replay service.

### Payment completion

Signals:

- payment-message-complete;
- initiator service terminal action;
- completion transaction states.

### PDP reporting

Signals:

- PDP reporting node;
- reporting `msgSts`;
- reporting-related transaction state.

### Notification / SMS

Signals:

- SMS gateway;
- alert boxer;
- notification service.

## 3. Condition normalization

Normalize common condition families.

Example structure:

```yaml
conditions:
  - dimension: country
    value: CBPR
    operator: "="
    rank: 0
    node_id: cond-country
    raw_verb: "country:CBPR"

  - dimension: instruction_currency
    value: USD
    operator: "="
    rank: 0
    node_id: cond-instructioncurrency
    raw_verb: "..."
```

Keep the raw fragment so a reviewer can verify the interpretation.

## 4. Action normalization

Normalize actions into:

```yaml
actions:
  - rank: 10
    node_id: act-...
    category: routing|validation|reporting|...
    summary: "..."
    key_values:
      transaction_state: "..."
      destination: "..."
```

## 5. Branch classification

Use:

```text
MAIN
ERROR
RETRY
ERROR_NESTED
RETRY_NESTED
UNKNOWN_BRANCH
```

Every ordered node should belong to a branch.

## 6. Confidence rubric

### HIGH

Direct condition/action evidence.

Example:

```text
cond-country explicitly states CBPR
```

### MEDIUM

Multiple supporting clues, but no single explicit field.

Example:

```text
USD + known CBPR receiver + rule description
```

### LOW

Mostly naming convention or incomplete evidence.

Example:

```text
rule ID contains HK but no geographic condition
```

## 7. Unknown is valid

Use `UNKNOWN` instead of forcing a category.

An `UNKNOWN` with a precise question is more useful than an incorrect confident classification.
