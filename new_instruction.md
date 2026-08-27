# Phase 2.0 — CBPR_SG Cross-Market Pilot

Proceed with `CBPR_SG%` as the next Phase 1 cross-market pilot.

Do NOT start rule optimization.

The objectives are:

1. validate that the generic parser works with significantly more new node semantics;
2. discover the SG BRT-specific market evidence rules;
3. preserve the validated MY and IN baselines;
4. identify shared vs SG-specific flow families.

## 1. Frozen baselines

Do not modify:

- CBPR_MY validated baseline
- CBPR_IN validated baseline
- MY business mappings
- IN business mappings
- known IN source-defect classification

Verify their freeze-marker checksums before and after the SG run.

Any checksum drift is a failure.

## 2. SG-specific snapshot

Scope:

`CBPR_SG%`

Create isolated outputs:

```text
analysis/cache/SG/
analysis/markets/SG/
