# Phase 2C — Resolve Remaining Business-Model Gaps

Phase 2B is complete.

Current state:

- 124 business families
- 121 VALIDATED_FAMILY
- 3 NEEDS_SME_INPUT
- 16 partial-context rules
- 2 shared missing-context patterns

Do NOT optimize rules yet.

Use existing local data only.
Do not query Oracle.

## 1. Investigate the 10 rules with missing message-family context

Do not ask me a generic question yet.

For each of the 10 rules, inspect the full ordered conditions and actions and
determine whether message family can be established from existing evidence.

Look specifically for evidence such as:

- cond-originmessagetype
- explicit message type conditions
- pacs.*
- camt.*
- MT message identifiers
- ISO 20022-specific conditions
- formatter/action nodes whose semantics are already validated
- translation/recombination nodes that are message-family specific
- input payload fields with explicit message type
- other BRT condition fields related to message type

Do NOT infer message family from rule ID alone.

Group the 10 rules by shared evidence pattern.

For each pattern report:

Pattern ID:
Affected rules:
Conditions shared:
Actions shared:
Possible message family:
Evidence:
Confidence:
Why the current classifier could not classify it:
Exact SME question, if still needed:

If existing explicit business evidence is sufficient, resolve it without
requiring SME input.

If evidence is implementation-only rather than a business condition, keep the
message family unresolved and explain why.

## 2. Keep message family and exact message type separate

Model:

message_family:
- MX
- MT
- CUSTOM
- UNKNOWN

message_type:
- pacs.008
- pacs.009
- camt.xxx
- MT103
- etc.

A rule may have:

message_family = MX
message_type = UNKNOWN

if there is enough evidence to establish ISO 20022/MX but not the exact message.

Do not require exact message type just to establish message family.

## 3. Review the six retained exceptions separately

These are NOT one business-model ambiguity.

Separate them into:

### Confirmed source defects

- CBPR_IN_OB_209023
- CBPR_SG_OB_339182
- CBPR_SG_OB_339183
- CBPR_SG_OB_339184
- CBPR_SG_OB_339185

For these rules:

- business understanding may remain UNDERSTOOD
- structural_status = SOURCE_DEFECT
- optimization_readiness = BLOCKED
- do not count them as unresolved business-model gaps
- do not change the generic parser

### Market evidence conflict

- CBPR_SG_IB_33273

Keep this as a real business/market exception.

Show the exact evidence:

- explicit cond-country
- MX receiveraddress-derived country
- message family/type
- sender geography
- receiver geography
- direction evidence
- relevant BRT conditions

Do not resolve this conflict automatically.

Produce one concise SME question specifically for this rule.

## 4. Review the 3 NEEDS_SME_INPUT families

Identify exactly why each family is marked NEEDS_SME_INPUT.

For each:

Family ID:
Human name:
Rule count:
Affected rules:
Missing business dimension:
Existing evidence:
What can already be concluded:
Exact question requiring SME input:

Do not ask me to review entire families if one shared answer can resolve them.

## 5. Do not let known defects block business-model readiness

Change the decision logic:

Known source/configuration defects should be tracked separately from
business-model completeness.

A model can be ready for optimization discovery when:

- business purpose is understood;
- family boundaries are validated;
- known malformed source rules are explicitly excluded/blocklisted;
- unresolved business ambiguity is small and isolated.

Do not require malformed rules to become structurally valid before the
business model itself can be considered complete.

## 6. Produce a concise SME decision sheet

Create:

analysis/sme-decision-sheet.md

It should contain ONLY questions that genuinely require business knowledge.

Target fewer than 5 questions.

For every question include:

- affected rule/family count
- representative rules
- current evidence
- choices if appropriate
- what changes after I answer

Do not include questions that can be answered from existing local evidence.

## 7. Re-evaluate readiness

After this analysis report:

Validated business families:
Families still needing SME input:

Complete-context rules:
Partial-context rules:

Confirmed source defects:
Actual unresolved business questions:

Then recommend:

READY_FOR_OPTIMIZATION_DISCOVERY

or

MORE_SME_INPUT_REQUIRED

Do not start optimization automatically.
