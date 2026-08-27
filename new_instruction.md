Read my SME comments in analysis/markets/IN/phase-1-gaps.md and apply them.

Do not query Oracle. Use the existing IN local snapshot.

1. Convert resolved SME comments into permanent business knowledge.

For the IN outbound market rule:
- scope it as BRT_SPECIFIC
- market = IN
- direction = OUTBOUND
- approved evidence includes:
  - sendercountry = IN
  - approved BRT condition meaning "from HSBC"
  - approved BRT condition meaning "from HASE"

Before applying HSBC/HASE logic, identify the exact normalized node/condition
representing those meanings. Do not use arbitrary text matching.

Do not infer market from:
- rule ID
- CBPR_IN scope
- destination
- routing_hub
- processor
- endpoint

2. For CBPR_IN_OB_209023:

My SME decision is that the rule setup is wrong.

Treat it as:
SOURCE_DEFECT / NEEDS_REVIEW

Do not modify the generic branch parser to accept this structure.
Do not automatically repair or reinterpret its block markers.

Its market classification may still be determined independently from valid
market evidence.

3. Regenerate all 235 IN rule records from the existing cache.

Report:

Rules processed:
UNDERSTOOD:
PARTIAL:
UNKNOWN:
NEEDS_REVIEW:

Market evidence sources:
- explicit_country
- outbound_sendercountry
- outbound_HSBC
- outbound_HASE
- other approved mapping

Remaining rules without approved market evidence:

Known source defects:
- rule ID
- offending ranks/nodes
- reason

Remaining SME ambiguity patterns:

4. Update phase-1-gaps.md.

Resolved SME questions should be marked RESOLVED and retained as history.

Do not delete the explanation of how they were resolved.

A known source defect such as CBPR_IN_OB_209023 should move out of the
"unanswered SME ambiguity" count and into a separate structural/source-defect
section.

5. If no unresolved market ambiguity remains, run IN market-evidence validation.

Verify that every IN classification has an approved BRT evidence path.

Do not count CBPR_IN_OB_209023's structural defect as a market-classification
failure if its market evidence is independently valid.

6. Then select approximately 20-25 representative IN rules and run the same
semantic-flow validation used for the validated MY pilot.

If:
- market evidence validation passes;
- semantic-flow sample has no material parser errors;
- the only remaining issue is the confirmed source defect;

then mark:

PHASE_1_CBPR_IN_VALIDATED_WITH_KNOWN_SOURCE_DEFECTS

Freeze the IN baseline.

Do not start SG or Phase 2 automatically.
