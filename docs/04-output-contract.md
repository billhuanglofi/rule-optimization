# Phase-1 Output Contract

All AI tools should generate the same basic output shape so results can be reviewed and merged.

## 1. Rule inventory

File:

```text
analysis/rule-inventory.md
```

Use a table:

| Rule ID | Description | Env | Node Count | Market | Flow(s) | Confidence | Status |
|---|---|---:|---:|---|---|---|---|

`Status` values:

```text
UNDERSTOOD
PARTIAL
UNKNOWN
NEEDS_REVIEW
```

---

## 2. Rule index by market

File:

```text
analysis/rule-index-by-market.md
```

Example:

```markdown
# Rules by Market

## CBPR
- `RULE_A` — routing + validation
- `RULE_B` — sanctions + completion

## UNKNOWN
- `RULE_X` — insufficient market evidence
```

---

## 3. Rule index by flow

File:

```text
analysis/rule-index-by-flow.md
```

Group rules under normalized tags from `docs/03-rule-taxonomy.md`.

---

## 4. Rule card per rule

File:

```text
analysis/rules/<RULE_ID>.md
```

Use `templates/rule-card.md`.

Every card must contain:

- metadata;
- ordered nodes;
- conditions;
- market classification;
- flow classification;
- main path;
- error path;
- retry path;
- dependencies;
- transaction states;
- terminal behavior;
- confidence;
- open questions;
- later optimization observations.

---

## 5. Evidence requirements

Every important statement must be traceable to at least one of:

```text
RULES_V2
RULES_NODES
NODES
GLOBAL_VARS
validator logic
SQL source
PR diff
```

When possible, cite:

```text
rule_id
rank
node_id
field/value
file + line
```

Example:

```markdown
Evidence:
- `RULES_NODES`: rule `CBPR_X`, rank `0`, node `cond-country`, verb contains `country:CBPR`.
```

---

## 6. Do not expose secrets

For global variables:

Good:

```text
AP_PAYMENT_ENDPOINT — payment endpoint variable
```

Avoid:

```text
https://actual-sensitive-host/...
```

unless the repository already treats the value as public and the user explicitly needs it.

---

## 7. Optimization observations

Phase 1 may notice potential issues, but must not act on them.

Use:

```markdown
## Optimization observations for later

- POSSIBLE_DUPLICATION: ...
- POSSIBLE_UNUSED_BRANCH: ...
- POSSIBLE_HARDCODED_VALUE: ...
```

These are observations only, not recommendations to change production behavior.
