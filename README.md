# Rule Optimization AI Starter

This repository is a shared, tool-agnostic workspace for understanding and later optimizing our RULE-based payment/routing logic.

The project is intentionally **phase-based**. Phase 1 is discovery only: understand what each rule does, which market/business context it belongs to, and which flow(s) it participates in. Do **not** optimize or rewrite rules during Phase 1.

## Project goals

1. Build a trustworthy inventory of existing rules.
2. Classify each rule by market, flow, purpose, conditions, actions, dependencies, and error/retry behavior.
3. Make the inventory understandable to both engineers and AI assistants.
4. Use evidence from SQL / DB metadata rather than guessing from names.
5. Create a stable baseline before later optimization work.

## Suggested repository layout

```text
.
├── AGENTS.md
├── README.md
├── docs/
│   ├── 01-domain-context.md
│   ├── 02-phase-1-rule-understanding.md
│   ├── 03-rule-taxonomy.md
│   └── 04-output-contract.md
└── templates/
    └── rule-card.md
```

## Phases

### Phase 1 — Understand and classify

For every rule:

- identify its purpose;
- identify market / geography evidence;
- identify payment or message flow(s);
- identify entry conditions;
- identify major actions;
- identify main path, retry path, and error path;
- identify external/global-variable dependencies;
- identify transaction-state changes;
- record uncertainty explicitly.

**Deliverable:** rule inventory + per-rule cards + cross-rule indexes.

### Phase 2 — Detect overlap and duplication

Compare rules with similar conditions, markets, actions, and destinations.

### Phase 3 — Optimization candidates

Find redundant checks, duplicated nodes, unnecessary branches, inconsistent routing, stale rules, and opportunities for shared components.

### Phase 4 — Change design and validation

Propose changes, simulate DB impact, validate rule invariants, and prepare safe PRs.

## Working rule

> Evidence first. Interpretation second. Optimization later.

Do not silently infer missing business meaning. Mark it as `UNKNOWN` and list what evidence would resolve it.
