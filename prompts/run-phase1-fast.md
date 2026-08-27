# Run Phase 1 in Fast Mode

Execute Phase 1 for the requested rule scope in **high-throughput mode**.

## Mandatory constraints

- Do not query Oracle per rule.
- Do not query `NODES` per node.
- Do not query `GLOBAL_VARS` per variable.
- Bulk extract first.
- Persist local snapshots.
- Reuse cached node semantics.
- Parse and classify locally.
- Use deep AI reasoning only for ambiguous rules.
- Do not generate hundreds of detailed rule cards unless requested.
- Checkpoint progress and resume after interruption.
- Do not make production changes.

## Before execution

Show me:

```text
1. scope/filter
2. estimated rule count
3. estimated rule-node row count
4. planned Oracle queries
5. expected local cache files
6. checkpoint size
7. deterministic classification rules
8. criteria for deep AI review
```

Target:

```text
Per-rule Oracle calls: 0
```

## Execution order

1. inventory scoped rules
2. bulk extract rule/node data
3. bulk resolve global variables
4. persist cache
5. build unique node catalog
6. parse all rules locally
7. classify explicit market/flow cases deterministically
8. generate `analysis/rules.jsonl`
9. build rule signatures/clusters
10. create ambiguity queue
11. deep-review only ambiguous cases
12. generate indexes and gap report

## Required outputs

```text
analysis/rules.jsonl
analysis/rule-inventory.md
analysis/rule-index-by-market.md
analysis/rule-index-by-flow.md
analysis/rule-index-by-transaction-state.md
analysis/phase-1-gaps.md
analysis/cache/node-catalog.json
analysis/cache/progress.json
analysis/cache/snapshot-metadata.json
```

## Final summary

Report:

```text
Rules processed:
UNDERSTOOD:
PARTIAL:
UNKNOWN:
NEEDS_REVIEW:
Oracle calls made:
Cache reused:
Unique node IDs:
Unique rule signatures:
Largest clusters:
Open questions:
```

Do not start Phase 2 automatically.
