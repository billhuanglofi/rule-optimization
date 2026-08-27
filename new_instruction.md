Proceed with the CBPR_MY% Phase 1 pilot now.

Do not spend more time estimating unless you encounter a concrete technical blocker.

## Goal

Produce a fast, reproducible first-pass understanding of all scoped CBPR_MY% rules while minimizing Oracle calls and minimizing unnecessary LLM reasoning.

## Execution requirements

### 1. Bulk extraction only

- Target zero per-rule Oracle queries.
- Target zero per-node Oracle queries.
- Target zero per-global-variable Oracle queries.
- Fetch the scoped RULES_V2 / RULES_NODES / NODES data in bulk.
- Fetch relevant GLOBAL_VARS in bulk.
- Use stable pagination only if required.
- Prefer the largest safe batch size supported by the tool.

Do not query Oracle repeatedly for data already available locally.

### 2. Avoid sending the whole raw dataset through LLM reasoning

If possible, write bulk Oracle results directly into local cache files and process them with local code.

Preferred flow:

Oracle
→ local raw snapshot
→ local parser
→ normalized structured records
→ AI only for unresolved semantics

Do not individually reason over all ~30,000 node occurrences.

### 3. Build the node catalog once

There are approximately 71 unique node IDs in the current scope.

Interpret each unique NODE_ID once and store its semantics in:

analysis/cache/node-catalog.json

Reuse that interpretation across every rule occurrence.

Do not reinterpret the same node for different rules unless its VERB makes the behavior genuinely rule-specific.

### 4. Parse all rules deterministically first

For every rule, reconstruct nodes by ascending RANK and extract locally:

- conditions
- market candidates
- currency
- message type
- routing hub / processor
- main actions
- error branches
- retry branches
- transaction states
- payload flags
- global-variable references
- terminal behavior

Generate the structured result first.

Primary output:

analysis/rules.jsonl

Do not generate a long Markdown rule card for every rule yet.

### 5. Classify obvious cases automatically

Use direct evidence to classify rules without deep AI review.

Examples:

- explicit country condition → market
- instruction currency condition → currency
- sanctions node → sanctions flow
- recombination node → recombination flow
- translation node → translation flow
- duplicate-check node → duplicate-check flow
- PDP reporting node → pdp-reporting
- payment-message-complete → payment-completion
- block-on-error → error branch
- block-on-retry → retry branch

Use evidence + confidence for every classification.

### 6. Cluster before deep review

This is important.

Do NOT assume every PARTIAL / UNKNOWN / NEEDS_REVIEW rule needs an independent AI review.

First cluster ambiguous rules by:

- normalized rule signature
- unresolved node semantics
- unresolved market/hub interpretation
- transaction-state pattern
- branch structure
- shared missing dependency

Then identify UNIQUE AMBIGUITY PATTERNS.

Example:

51 ambiguous rules
may represent only:
- 3 unresolved node meanings
- 2 hub-vs-market questions
- 3 unusual flow signatures

Review those shared causes first.

If one resolution applies to multiple rules with identical evidence, propagate it to those rules.

Never propagate a conclusion when the underlying evidence differs.

### 7. Human review should be question-driven

Instead of saying:

"51 rules need human review"

prefer:

"51 rules affected by 6 unique ambiguity patterns."

Generate:

analysis/phase-1-gaps.md

with entries such as:

## Ambiguity A01

Affected rules: 23

Shared cause:
`HUB_MAS` cannot yet be confidently classified as a market or routing hub.

Evidence:
...

Question for SME:
Is HUB_MAS a geographic destination, a processing hub, or both?

This allows one human answer to resolve many rules.

### 8. Checkpoint

Checkpoint every 100 rules.

Maintain:

analysis/cache/progress.json

Include:

- total rules
- processed rules
- UNDERSTOOD
- PARTIAL
- UNKNOWN
- NEEDS_REVIEW
- unique signatures
- unique ambiguity patterns

If interrupted, resume from the checkpoint.

Do not restart extraction or parsing from the beginning.

### 9. Stop conditions

Pause and ask me before continuing if:

- more than 20% of rules remain UNKNOWN;
- one unknown node affects many rules;
- branch parsing appears unreliable;
- market taxonomy itself is ambiguous;
- Oracle extraction requires repeated per-rule calls;
- raw result volume cannot be processed locally safely.

Do not solve a shared business ambiguity by guessing.

### 10. Required first-pass outputs

Produce:

analysis/rules.jsonl
analysis/rule-inventory.md
analysis/rule-index-by-market.md
analysis/rule-index-by-flow.md
analysis/rule-index-by-transaction-state.md
analysis/phase-1-gaps.md
analysis/cache/node-catalog.json
analysis/cache/progress.json
analysis/cache/snapshot-metadata.json

Do not create 507 individual Markdown rule cards during this pass.

### 11. Final metrics

At completion, report only:

Rules processed:
Node rows processed:
Unique node IDs:
Unique rule signatures:

UNDERSTOOD:
PARTIAL:
UNKNOWN:
NEEDS_REVIEW:

Unique ambiguity patterns:
Rules affected by ambiguity:

Oracle calls made:
Bulk extraction duration:
Local parsing duration:
Classification duration:
Total automated duration:

Top 10 largest rule-signature clusters:
Top shared ambiguity patterns:

Then recommend whether the architecture is ready to scale from CBPR_MY% to the full rule estate.

Start execution now.
