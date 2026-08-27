# AI Instructions — Rule Optimization

These instructions apply to OpenCode, VS Code agents, Codex, or other AI coding assistants.

## Current phase

**Phase 1 — Rule Understanding and Classification**

Your job is to understand existing rules efficiently and reproducibly.

Do not change production behavior.

## Mandatory performance rules

1. NEVER query Oracle once per rule.
2. NEVER query `NODES` once per `NODE_ID`.
3. NEVER query `GLOBAL_VARS` once per variable.
4. Fetch scoped data in bulk.
5. Save a local snapshot immediately.
6. Reuse cached data.
7. Parse mechanically before using LLM reasoning.
8. Use deep reasoning only for ambiguous rules.
9. Generate structured output before prose.
10. Resume from checkpoints instead of restarting.

Target:

```text
Per-rule DB queries:          0
Per-node DB queries:          0
Per-global-var DB queries:    0
```

## Source-of-truth priority

1. current SQL / DB rows / PR diff
2. `RULES_NODES` ordered by `RANK`
3. canonical definitions from `NODES`
4. rule metadata from `RULES_V2`
5. `GLOBAL_VARS`
6. validator/rule-checker logic
7. documentation/comments
8. naming conventions only as weak hints

## Core data model

### `RULES_V2`
Rule metadata such as ID, description, and environment.

### `RULES_NODES`
Ordered rule implementation. Preserve:

```text
RULE_ID
NODE_ID
RANK
VERB
OPERATOR
ENVIRONMENT
```

Always reconstruct a rule in ascending `RANK`.

### `NODES`
Canonical node definitions. Prefixes like `cond-*`, `env-*`, `act-*`, and `block-*` are hints only.

### `GLOBAL_VARS`
Environment-specific reusable values. Do not expose secret values in reports.

## Required cache

Create/reuse:

```text
analysis/cache/
├── rule-source.jsonl
├── global-vars.jsonl
├── node-catalog.json
├── snapshot-metadata.json
└── progress.json
```

If the snapshot is valid, do not reconnect to Oracle unless required evidence is missing.

## Required processing pipeline

```text
remote DB/source
    ↓
bulk snapshot
    ↓
local cache
    ↓
deterministic parser
    ↓
normalized rule records
    ↓
fast classification
    ↓
ambiguity queue
    ↓
deep AI review only where needed
```

Do not run extraction → reasoning → Markdown in a per-rule loop.

## Required behavior for each rule

1. Load metadata and all nodes from local cache.
2. Sort by rank.
3. Resolve node semantics from `node-catalog.json`.
4. Parse meaningful `VERB` fields.
5. Separate:
   - conditions
   - main actions
   - retry branch
   - error branch
   - terminal actions
6. Identify market evidence.
7. Identify flow evidence.
8. Capture global/external dependencies.
9. Capture transaction states.
10. Calculate confidence.
11. Send only ambiguous cases to deeper reasoning.

## Evidence discipline

Every important classification must include:

```yaml
value: "..."
confidence: HIGH|MEDIUM|LOW
evidence:
  - "..."
```

If there is no direct evidence:

```yaml
value: UNKNOWN
confidence: LOW
evidence:
  - "No explicit evidence found"
```

## Important distinctions

- deployment environment is not market
- routing hub is not automatically market
- currency is not automatically market
- main flow is not error flow
- node presence is not the same as node order
- rule name is not proof

## Deterministic classification first

Examples of direct signals:

```text
cond-country                   -> market candidate
cond-instructioncurrency      -> currency
*sanction*                    -> sanctions
*recombination*               -> recombination
*translation*                 -> translation
*duplicate*                   -> duplicate-check
payment-message-complete      -> payment-completion
*pdp-reporting*               -> pdp-reporting
block-on-error                -> error branch
block-on-retry                -> retry branch
transaction-state:*           -> transaction state
input-payload:*               -> payload dependency
```

## Reuse node semantics

Interpret each unique `NODE_ID` once and store the result in:

```text
analysis/cache/node-catalog.json
```

Do not repeatedly reason about the same node across hundreds of rules.

## Rule signatures

Build a normalized signature from fields such as:

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

Use signatures to cluster similar rules.

## Ambiguity queue

Deep-review only:

```text
PARTIAL
UNKNOWN
NEEDS_REVIEW
```

Typical causes:

- conflicting market signals
- unclear custom node
- hub-vs-market ambiguity
- unusual branch structure
- unresolved variable
- inconsistent transaction-state sequence

## Phase-1 outputs

Generate first:

```text
analysis/rules.jsonl
analysis/rule-inventory.md
analysis/rule-index-by-market.md
analysis/rule-index-by-flow.md
analysis/rule-index-by-transaction-state.md
analysis/phase-1-gaps.md
```

Detailed rule cards are lazy/optional.

## Checkpointing

Every 50–100 rules update:

```text
analysis/cache/progress.json
```

Resume from the latest checkpoint.

## Do not do in Phase 1

- delete rules
- reorder nodes
- merge rules
- rewrite production SQL
- normalize states automatically
- replace global variables
- make production changes

You may record observations for later:

```text
POSSIBLE_DUPLICATION
POSSIBLE_UNUSED_BRANCH
POSSIBLE_HARDCODED_VALUE
POSSIBLE_INCONSISTENCY
```

## Before every external tool call

Ask:

> Can this be answered from the local snapshot/cache?

If yes, do not call the external tool.
