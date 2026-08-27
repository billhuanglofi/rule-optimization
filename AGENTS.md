# AI Instructions for Rule Optimization Project

You are working in a shared rule-analysis repository. These instructions apply whether you are running in VS Code, OpenCode, or another coding agent.

## Current project phase

The current phase is **Phase 1: Rule Understanding**.

Your job is to **understand and classify existing rules**. Do not optimize, rewrite, delete, merge, or reorder production rules unless the user explicitly starts a later phase.

## Source-of-truth priority

Use evidence in this order:

1. Current rule SQL / PR diff / database rows.
2. `RULES_NODES` rows ordered by `RANK`.
3. Node definitions from `NODES`.
4. Rule metadata from `RULES_V2`.
5. Referenced values from `GLOBAL_VARS`.
6. Existing validator or rule-checker logic.
7. Documentation and comments.
8. Naming conventions only as a weak hint.

Never treat a rule name as sufficient proof of market or flow.

## Core data model

Assume the project may contain these logical tables:

- `RULES_V2`
  - rule-level metadata such as rule ID, description, and environment.
- `RULES_NODES`
  - ordered nodes belonging to a rule.
  - important fields include `RULE_ID`, `NODE_ID`, `RANK`, `VERB`, `OPERATOR`, `ENVIRONMENT`.
- `NODES`
  - canonical definitions for condition/action nodes.
  - node IDs commonly use prefixes such as `cond-`, `env-`, and `act-`.
- `GLOBAL_VARS`
  - environment-specific variables referenced by node verbs.

When reading a rule, reconstruct it in ascending `RANK`.

## Required behavior for every rule

For each rule:

1. Read rule metadata.
2. Load all nodes for the rule.
3. Sort by rank.
4. Resolve each `NODE_ID`.
5. Parse the node `VERB` into meaningful key/value information where possible.
6. Separate:
   - conditions,
   - main-path actions,
   - retry blocks,
   - error blocks,
   - terminal actions.
7. Determine market evidence.
8. Determine flow evidence.
9. Determine external/global-variable dependencies.
10. Determine transaction-state and reporting behavior.
11. Record ambiguity and confidence.
12. Produce the output defined in `docs/04-output-contract.md`.

## Evidence discipline

Every classification must have:

- `value`
- `confidence`: `HIGH`, `MEDIUM`, or `LOW`
- `evidence`

Example:

```yaml
market:
  value: "CBPR"
  confidence: HIGH
  evidence:
    - "cond-country verb contains country:CBPR"
```

If no direct evidence exists:

```yaml
market:
  value: "UNKNOWN"
  confidence: LOW
  evidence:
    - "No explicit country/market condition found"
```

## Important distinctions

### Environment is not market

`PROD`, `UAT`, `SIT`, etc. are deployment environments. Do not classify them as market.

### Rule name is not proof

Names like `CBPR_*`, `HK_*`, or `*_IN_*` may be useful hints, but confirm them from rule conditions or payload/routing values.

### Main flow is not error flow

Treat `block-on-error`, `act-on-error`, retry blocks, and their matching end markers as separate branches.

### Presence is not order

Some logic depends on a producer node appearing **earlier** than a consumer node. Preserve rank order.

## Known validator-style invariants

Use these as consistency checks while understanding a rule:

- start/end block pairs should remain balanced;
- non-zero ranks should not be duplicated within one rule;
- rank `0` is generally for condition/environment nodes;
- node IDs should resolve to canonical nodes;
- global-variable-like tokens should resolve in `GLOBAL_VARS`;
- hardcoded URLs should be called out;
- some input-payload flags require an earlier producer node;
- `INSERT ALL ... SELECT` blocks are whitespace-sensitive;
- error flows may require replay/wiretap handling;
- operator values are constrained;
- payment flow completion nodes and PDP-reporting behavior are important terminal/reporting semantics.

These checks are **supporting evidence**, not the main goal of Phase 1.

## Do not do these in Phase 1

- Do not propose deletions just because a node looks redundant.
- Do not reorder nodes.
- Do not merge rules.
- Do not normalize transaction states.
- Do not replace global variables.
- Do not assume two rules are duplicates from names alone.
- Do not make production changes.

Instead, add an `optimization_observation` note for later phases.

## When information is missing

Create an explicit question in the rule card:

```markdown
### Open questions
- Does `HUB_MAS` represent a market destination, a processing hub, or both?
- Is this rule expected to handle only CBPR traffic or multiple products?
```

Do not invent the answer.
