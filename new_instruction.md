# Start Estate Validation Wave 2

Wave 1 has been resolved sufficiently to continue.

Current confirmed state:

- 3,343 Wave-1 rules reviewed
- 3,330 rules resolved through approved GLOBAL canonical country conditions
- 0 remaining canonical country-condition conflicts
- 13 rules remain without canonical country evidence
- those 13 rules represent one shared BRT/domain-mapping question
- 6 structural marker anomalies are confirmed SOURCE_DEFECT
- the generic parser remains unchanged

Do not reopen already resolved Wave-1 questions.

Use the existing estate snapshot only.
Do not query Oracle unless a genuinely missing source fact cannot be obtained
from the local snapshot.

Do not modify production rules.

---

## 1. Carry the 13 unresolved Wave-1 rules as one domain gap

Do not block Wave 2 because of these 13 rules.

Keep them grouped under one unresolved domain-mapping pattern.

For these rules:

- do not infer market from rule ID or population name;
- do not invent a CN/HASE/SANC mapping;
- preserve all current condition evidence;
- keep market unresolved until SME/BRT evidence exists.

Do not ask one question per rule.

If later populations expose the same condition pattern and provide stronger
semantic evidence, update the shared gap rather than creating another question.

---

## 2. Execute Wave 2 on the recommended medium-novelty populations

Process:

- CBPR_TW_*
- CBPR_NZ_*
- CBPR_MU_*

Do NOT process CBPR_HK_* yet.

The objective is maximum semantic coverage with minimal new domain work.

Expected approximate scope:

CBPR_TW_*:
- 219 rules
- ~5 new node IDs

CBPR_NZ_*:
- 189 rules
- ~1 new node ID

CBPR_MU_*:
- 170 rules
- 0 new node IDs

---

## 3. Reuse approved GLOBAL knowledge

Apply approved global semantics including:

- cond-country = XXX -> market XXX
- cond-sendercountry = XXX -> market XXX
- cond-receivercountry = XXX -> market XXX
- MX receiver-side precedence
- structured BIC/address country extraction where approved
- prs-* = channel/upstream ingress, not market
- destination/routing_hub = downstream technical information, not market
- generic condition/action parsing
- generic transaction-state parsing
- generic error/retry marker validation
- MESSAGE_AGNOSTIC catch-all semantics where evidence matches

Do not copy market/BRT-specific fallback mappings from MY, IN, or SG into
TW/NZ/MU unless explicitly scoped GLOBAL.

---

## 4. Focus first on semantic reuse

For every node used by TW/NZ/MU:

classify it as:

KNOWN_GLOBAL
KNOWN_BRT_SPECIFIC
KNOWN_MARKET_SPECIFIC
NEW_SEMANTIC
UNKNOWN

For the small number of new nodes:

interpret each unique node once using:

- canonical NODES metadata
- representative VERB values
- surrounding ordered flow context
- usage across the selected populations

Do not analyze the same node repeatedly per rule.

---

## 5. Market classification

Use canonical country-condition evidence first.

Report separately:

explicit_country
sender_country
receiver_country
structured_address_country
multiple_agreeing_conditions
conflicting_conditions
no_approved_market_evidence

Do not create market-specific SME questions unless canonical evidence is
actually insufficient.

Cluster unresolved rules by shared condition pattern.

---

## 6. Detect new condition semantics

Identify any condition dimensions not previously understood.

For each new dimension report:

Node ID:
Rules affected:
Representative values:
Likely business meaning:
Evidence:
Scope:
Confidence:

Scope must be one of:

GLOBAL
BRT_SPECIFIC
MARKET_SPECIFIC
POPULATION_SPECIFIC
UNKNOWN

Do not promote to GLOBAL without evidence.

---

## 7. Structural validation

Apply the established marker rules.

Unmatched, mismatched, or unclosed known error/retry markers are SOURCE_DEFECT.

Do not change the parser to accept malformed rules.

If a genuinely new control construct appears, classify it separately as:

NEW_VALID_PATTERN_CANDIDATE

and show the exact ordered structure.

---

## 8. Measure semantic coverage gained

Report before/after estate coverage.

Before Wave 2:

Known node IDs: 152 / 418
Known condition dimensions: 103
Rules covered by Wave-1 selected-population semantics: 3,343
Rules still primarily novel: 8,848

After Wave 2 report:

Known node IDs:
Known condition dimensions:
Known flow signatures:
Known error patterns:

Rules covered by trusted semantics:
Rules still primarily novel:

New reusable semantics learned:
New BRT-specific semantics:
New market-specific semantics:
Still unknown:

---

## 9. SME questions

Generate a new SME decision sheet.

Only include questions that genuinely require business knowledge.

Do not include:

- already-approved canonical country conditions;
- known malformed marker patterns;
- questions answerable from local metadata/context.

Keep the 13 Wave-1 rules as one carried question unless new evidence resolves
them.

Target fewer than 5 total SME questions after Wave 2.

---

## 10. Decide whether HK should be next

After TW/NZ/MU are processed, reassess CBPR_HK_*.

Report:

CBPR_HK_* rules:
unknown node IDs remaining:
unknown condition dimensions remaining:
new flow families remaining:
expected SME questions:
estimated coverage gain:

Then recommend one of:

READY_FOR_HK_VALIDATION
ANOTHER_MEDIUM_WAVE_FIRST
HIGH_NOVELTY_DOMAIN_WORK_REQUIRED
PARSER_EXTENSION_REQUIRED

Do not automatically start HK.
Do not restart optimization design.
