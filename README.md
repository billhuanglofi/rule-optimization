# Rule Optimization — Phase 1 Fast Starter

This repository is for understanding and later optimizing RULE-based payment/routing logic.

Current focus: **Phase 1 — understand and classify rules at scale.**

Core execution model:

> **Bulk extract once → cache locally → parse deterministically → use AI only for ambiguity.**

Do not optimize, delete, merge, reorder, or rewrite production rules during Phase 1.

## Layout

```text
.
├── AGENTS.md
├── README.md
├── docs/
│   ├── 01-phase-1-fast-workflow.md
│   ├── 02-rule-taxonomy.md
│   └── 03-performance-and-batching.md
├── prompts/
│   └── run-phase1-fast.md
└── templates/
    └── rule-card.md
```

## Phase 1 deliverables

- machine-readable `analysis/rules.jsonl`
- rule inventory
- index by market
- index by flow
- transaction-state index
- ambiguity/gaps report
- optional detailed rule cards for ambiguous or selected rules

## Working rule

> Evidence first. Interpretation second. Optimization later.

Use `UNKNOWN` when business meaning cannot be proven.
