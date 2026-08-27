# Performance and Batching Guide

## Database call budget

For a normal scoped Phase-1 run, target:

```text
Discovery/count queries:      <= 5
Bulk extraction queries:      <= 10
Per-rule DB queries:           0
Per-node DB queries:           0
Per-global-variable queries:   0
```

Additional calls require a specific unresolved question.

## Bad pattern

```text
for each rule:
    query RULES_NODES
    query NODES
    query GLOBAL_VARS
    analyze
    write markdown
```

## Preferred pattern

```text
bulk query scoped rules
bulk query scoped rule-node data
bulk resolve node metadata
bulk resolve global vars
save cache
parse locally
classify locally
deep-review ambiguity only
generate reports
```

## Persist raw data before reasoning

Always save source data before classification.

Benefits:

- restart safety
- reproducibility
- less DB load
- easier debugging
- easier comparison between runs

## Checkpoint size

Recommended:

```text
50–100 rules
```

## Stable pagination

If extraction needs pagination:

- use stable keys
- preserve ordering by `RULE_ID`, then `RANK`
- avoid tiny result limits
- avoid unnecessary offset-only pagination

## Parallelism

Preferred:

```text
remote DB reads:
    low concurrency

local parsing:
    parallel if supported

AI ambiguity review:
    bounded parallelism
```

Protect the database from a query storm.

## Node reuse

If hundreds of rules reuse the same node, interpret that node once.

This is one of the highest-impact optimizations.

## Rule clustering

Cluster by normalized signature.

Deep-review representative patterns first.

Only inspect every member individually when differences matter.

## Delay prose generation

Structured JSON/JSONL is faster and cheaper than hundreds of long Markdown rule cards.

Generate prose after the complete dataset exists.

## Progress report

After every checkpoint report only:

```text
processed / total
UNDERSTOOD
PARTIAL
UNKNOWN
NEEDS_REVIEW
remaining
```

Do not print long per-rule logs unless requested.

## Stop and request human input when

- more than 20% of rules are `UNKNOWN`
- one unknown custom node affects many rules
- a key variable family cannot be resolved
- branch parsing is unreliable
- the market taxonomy itself is unclear

Fixing one shared ambiguity may resolve hundreds of rules.
