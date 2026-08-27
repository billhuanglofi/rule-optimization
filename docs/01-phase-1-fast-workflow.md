# Phase 1 — Fast Rule Understanding Workflow

## Mission

Understand the scoped rule set with minimal remote calls.

Main questions:

1. Which traffic reaches each rule?
2. Which market/geography does it represent?
3. Which payment/message flow does it participate in?
4. What are its important conditions?
5. What happens on the main path?
6. What happens on retry/error paths?
7. Which services, processors, hubs, endpoints, and variables are referenced?
8. Which transaction states/reporting side effects occur?

Do not optimize yet.

## Step 0 — Plan before execution

Before querying, report:

```text
Scope/filter:
Estimated rule count:
Estimated rule-node rows:
Expected Oracle calls:
Expected cache files:
Checkpoint size:
Expected deep-review criteria:
```

Target zero per-rule Oracle calls.

## Step 1 — Bulk inventory

Collect rule metadata for the whole requested scope.

Do not fetch individual rules one by one.

## Step 2 — Bulk extract rule-node data

Prefer one joined extraction of scoped rules, `RULES_NODES`, and canonical `NODES` metadata.

Conceptually:

```sql
SELECT
    r.ID AS RULE_ID,
    r.DESCRIPTION AS RULE_DESCRIPTION,
    r.ENVIRONMENT,
    rn.RANK,
    rn.NODE_ID,
    rn.VERB,
    rn.OPERATOR,
    n.DESCRIPTION AS NODE_DESCRIPTION,
    n.VALUE AS NODE_VALUE,
    n.CLASS AS NODE_CLASS
FROM RULES_V2 r
JOIN RULES_NODES rn
    ON rn.RULE_ID = r.ID
LEFT JOIN NODES n
    ON n.ID = rn.NODE_ID
WHERE <scope>
ORDER BY r.ID, rn.RANK;
```

Adapt this to the real schema.

If pagination is required, use a stable key strategy and avoid tiny result limits.

Persist the result immediately.

## Step 3 — Bulk resolve global variables

Fetch relevant `GLOBAL_VARS` in bulk.

Do not fetch one variable at a time.

Do not expose sensitive values in Markdown output.

## Step 4 — Build node catalog

Interpret unique node IDs once.

Create:

```text
analysis/cache/node-catalog.json
```

For each node store:

```text
node_id
type
category
known verb semantics
branch role
confidence
```

## Step 5 — Parse rules locally

For every rule:

1. group rows by `RULE_ID`
2. sort by `RANK`
3. assign branch membership
4. extract conditions
5. extract actions
6. extract transaction states
7. extract global variable references
8. extract payload dependencies
9. determine terminal behavior

Do not generate prose yet.

## Step 6 — Fast deterministic classification

Classify direct evidence mechanically.

Examples:

```text
country condition           -> market candidate
instruction currency        -> currency
sanctions node              -> sanctions flow
recombination node          -> recombination flow
translation node            -> translation flow
duplicate node              -> duplicate-check
payment completion          -> payment-completion
PDP reporting               -> pdp-reporting
block-on-error              -> error branch
block-on-retry              -> retry branch
```

## Step 7 — Write normalized rule records

Primary output:

```text
analysis/rules.jsonl
```

Recommended shape:

```json
{
  "rule_id": "...",
  "description": "...",
  "environment": "...",
  "node_count": 0,
  "conditions": [],
  "market": {},
  "flows": [],
  "actions": [],
  "branches": {},
  "dependencies": {},
  "transaction_states": [],
  "terminal_behavior": {},
  "signature": "...",
  "status": "UNDERSTOOD",
  "confidence": "HIGH",
  "open_questions": []
}
```

## Step 8 — Build rule signatures and clusters

Use normalized semantics, not only rule names.

Suggested signature components:

```text
market
currency
message type
condition set
ordered major actions
destination/processor
error behavior
terminal behavior
```

Analyze shared structures once.

## Step 9 — Create ambiguity queue

Only rules marked:

```text
PARTIAL
UNKNOWN
NEEDS_REVIEW
```

should receive deeper reasoning.

## Step 10 — Deep review ambiguous rules only

Use local evidence first.

Call Oracle again only when a specific missing fact cannot be resolved locally.

## Step 11 — Generate team-readable outputs

Create:

```text
analysis/rule-inventory.md
analysis/rule-index-by-market.md
analysis/rule-index-by-flow.md
analysis/rule-index-by-transaction-state.md
analysis/phase-1-gaps.md
```

Detailed rule cards are optional.

## Step 12 — Checkpoint

Every 50–100 processed rules save progress and resume from there if interrupted.

## Completion criteria

Phase 1 is complete when:

- every scoped rule has a structured record
- every rule has market classification or `UNKNOWN`
- every rule has flow classification or `UNKNOWN`
- classifications include evidence
- main/error/retry behavior is separated
- unresolved questions are listed
- indexes are generated
- no production behavior was changed
