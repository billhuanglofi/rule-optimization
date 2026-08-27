## 2. Resolve CBPR_SG_IB_33273 as a confirmed business-rule defect

CBPR_SG_IB_33273 has conflicting business conditions.

This is not a valid market-precedence case and must NOT be used to change
the generic MX market-classification logic.

Classify it as:

business_context = COMPLETE
business_rule_status = DEFECT
defect_type = CONFLICTING_BUSINESS_CONDITIONS
optimization_readiness = BLOCKED
reachability_status = LIKELY_UNREACHABLE

Preserve the conflicting evidence, including:

- explicit SG country/market condition
- MX receiver-side HK geography
- relevant message type
- sender/receiver conditions

Do not automatically repair the rule.
Do not invent precedence to make the conflict disappear.

---

## 3. Search all current business families for similar defects

I have observed similar cases in other BF families.

Scan all modeled MY, IN, and SG rules for contradictory condition sets.

Look for:

- conflicting approved market conditions
- explicit market versus MX receiver-side market conflicts
- mutually incompatible country conditions
- mutually incompatible message-type conditions
- contradictory currency/product/service conditions
- contradictory participant/address conditions

Important:

Normal cross-border sender/receiver differences are NOT defects.

For MX messages, sender country differing from receiver country is normal unless
approved business conditions themselves contradict each other.

Cluster similar findings instead of reporting one issue per rule.

Create:

analysis/business-rule-defects.md
analysis/business-rule-defects.json

Use:

CONFIRMED_DEFECT
LIKELY_DEFECT
NEEDS_SME_CONFIRMATION

Do not label something CONFIRMED_DEFECT unless existing SME/BRT knowledge
supports that conclusion.

---

## 4. Separate defects from business-model gaps

Keep these categories separate:

STRUCTURAL_SOURCE_DEFECT
- malformed/unbalanced control markers

BUSINESS_CONDITION_DEFECT
- contradictory business conditions

BUSINESS_MODEL_GAP
- business meaning genuinely unknown

A defective rule can still be:

understanding_status = UNDERSTOOD
business_context = COMPLETE
optimization_readiness = BLOCKED

Known defects should not prevent the overall business model from being complete.

---

## 5. Rebuild business-model status

After applying the SME answers, regenerate the business-model summary.

Report:

Rules modeled:
Complete-context rules:
Partial-context rules:

Validated business families:
Families still needing SME input:

MESSAGE_AGNOSTIC catch-all rules:
Structural source defects:
Business-condition defects:
Likely defects needing confirmation:

Actual unresolved SME/business questions:

If there are no genuine unresolved business questions, mark:

BUSINESS_MODEL_VALIDATED

---

## 6. Continue to optimization discovery only after validation

If BUSINESS_MODEL_VALIDATED is reached, continue to optimization discovery.

Do NOT modify production rules.

Compare rules primarily inside validated business families.

Look for:

- duplicated implementations
- condition fragmentation
- repeated common flows
- repeated error/retry flows
- repeated completion/reporting tails
- routing variants
- possible shadowed rules
- possible unreachable rules
- repeated hardcoded/configuration patterns

Similarity alone is NOT enough to call something redundant.

For every optimization candidate include:

Candidate ID:
Business family:
Candidate type:
Affected rules:
Common business behavior:
Common conditions:
Actual differences:
Shared flow:
Why simplification may be possible:
Why simplification may be unsafe:
Confidence:
Required validation:

Use priority:

P1 = high-value / high-confidence
P2 = useful but needs SME validation
P3 = exploratory
BLOCKED = known defective rules

Known structural or business-condition defects must remain BLOCKED.

---

## 7. Focus on large families first

Start with the largest validated business families, especially BF001.

For each large family answer:

- Why are there this many rules?
- How many real business condition patterns exist?
- How many normalized flows exist?
- How many exact implementations exist?
- Which differences are business-required?
- Which differences are only configuration/implementation differences?

Do not assume a large family should be merged.

---

## 8. Required optimization outputs

Create:

analysis/optimization-discovery.md
analysis/optimization-candidates.json
analysis/optimization-by-family.md
analysis/common-flow-patterns.md
analysis/condition-fragmentation.md
analysis/defect-summary.md

Produce a Top 20 optimization-candidate list.

Do NOT:
- change SQL
- modify rules
- merge rules
- delete rules
- generate production changes

Stop after optimization discovery and report the findings for human review.
