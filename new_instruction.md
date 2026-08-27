# Phase 1.6 — Semantic Flow Validation

The market-classification validation has passed.

Do not query Oracle again. Use the existing local snapshot.

The purpose of this step is to validate that the deterministic parser
correctly understands rule flow semantics, not only market classification.

## 1. Select a representative sample

Select approximately 25 rules from the existing 507-rule pilot.

Include:

- 5 simple rules
- 5 rules using the sendercountry fallback
- 5 rules with the largest node counts
- 4 rules containing retry/error branches
- 3 rules with unusual transaction-state sequences
- 3 rules from the largest shared/similar signature groups

A rule may satisfy more than one category.

## 2. Produce compact semantic reviews

For each sampled rule show:

### Rule ID

Market:
- value
- classification source

Entry conditions:
- ordered condition nodes
- normalized meaning

Main flow:
- ordered major actions

Error flow:
- start/end ranks
- major actions
- terminal action

Retry flow:
- start/end ranks
- major actions
- terminal action

Transaction states:
- ordered values and ranks

External dependencies:
- routing hub
- processor/service
- global variables

Terminal behavior:
- final meaningful main-path node

Parser assessment:
- PASS
- QUESTION
- FAIL

Do not create verbose prose.

## 3. Validate structural correctness

Check that:

- nodes are interpreted in rank order;
- error/retry blocks are correctly separated from the main path;
- block start/end markers are correctly paired;
- transaction states are attached to the correct branch;
- payment completion is correctly identified where applicable;
- destination/routing hub remains technical information;
- conditions are not accidentally interpreted as actions;
- actions are not accidentally interpreted as conditions.

## 4. Create a validation report

Create:

analysis/phase-1.6-flow-validation.md

Summary:

Sample size:
PASS:
QUESTION:
FAIL:

Branch parsing errors:
Condition parsing errors:
Transaction-state errors:
Terminal-action errors:
Unknown node semantics:

List every QUESTION/FAIL explicitly.

## 5. Decision gate

If there are zero material FAIL results and any QUESTION items are
understood/shared semantic issues rather than parser defects:

mark:

PHASE_1_PILOT_VALIDATED

Otherwise:

- identify the shared cause;
- fix the parser or business mapping;
- regenerate from local cache;
- repeat this validation.

Do not start Phase 2 automatically.
