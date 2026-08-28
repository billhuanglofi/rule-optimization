# Phase E2 — Targeted Estate Validation

Estate-wide discovery is complete.

Current estate findings include approximately:

- 12,311 RULES_V2 records
- 12,191 reconstructable rules
- ~704,922 rule-node rows
- 418 unique node IDs
- 112 node IDs already covered by MY/IN/SG
- 306 new node IDs
- 184 new condition dimensions
- 468 new flow signatures
- 284 new error signatures
- 37 structural anomaly candidates

Decision from estate discovery:

DOMAIN_MAPPING_REQUIRED

Do NOT start estate-wide optimization.

The objective of this phase is to expand trusted domain knowledge efficiently
without manually reviewing every market or every rule.

Use the existing estate snapshot.
Do not query Oracle unless local evidence is insufficient for a specific issue.

---

## 1. Use population novelty ranking

Read:

analysis/estate/population-novelty-ranking.md

Group rule populations into:

LOW_NOVELTY
MEDIUM_NOVELTY
HIGH_NOVELTY

Use dimensions such as:

- percentage of already-known node IDs
- number of new condition dimensions
- number of new flow signatures
- number of new error signatures
- unknown message semantics
- missing market/BRT mappings
- structural anomalies

Do not use rule-ID pattern as market evidence.

---

## 2. Select the next validation wave

Automatically select:

- 3 LOW_NOVELTY populations
- 2 MEDIUM_NOVELTY populations
- 1 HIGH_NOVELTY population

Prefer populations with meaningful rule counts.

Do not choose tiny populations unless they introduce a highly reused node or
condition semantic.

For each selected population report:

Population:
Rule count:
Node rows:
Unique node IDs:
Known node IDs:
New node IDs:
New condition dimensions:
New flow signatures:
New error signatures:
Structural anomalies:
Why selected:

Do not start analysis until this selection is recorded.

---

## 3. Reuse generic knowledge aggressively

Reuse existing GLOBAL semantics when evidence supports them, including:

- condition/action separation
- rank ordering
- block/error/retry parsing
- transaction-state parsing
- payload dependency logic
- destination/routing_hub is technical downstream information
- prs-* is channel/upstream ingress, not market
- MX receiver-side geography behavior
- MESSAGE_AGNOSTIC catch-all concept
- known generic node semantics

Do NOT reuse market-specific mappings from MY/IN/SG unless their BRT scope
explicitly permits it.

---

## 4. Learn new node semantics once

For all selected populations, collect every new NODE_ID.

Do not reason about the same new node repeatedly.

For each unique new node produce:

Node ID:
Occurrences:
Populations:
Representative rules:
Canonical NODES description:
Representative VERB examples:
Likely type:
- CONDITION
- ACTION
- CONTROL
- FORMATTER
- ROUTING
- REPORTING
- OTHER

Semantic classification:
- GENERIC_REUSABLE
- BRT_SPECIFIC
- MARKET_SPECIFIC
- UNKNOWN

Confidence:
Evidence:

Prioritize new nodes by:

frequency × number of populations using them

so that understanding one widely reused node can resolve many rules.

Create:

analysis/estate/node-semantic-expansion.md
analysis/estate/node-semantic-expansion.json

---

## 5. Learn new condition dimensions

For every new condition dimension in the selected populations, determine:

- what business question it represents;
- allowed operators;
- typical values;
- whether it contributes to:
  - market
  - message family
  - channel/source
  - routing
  - participant
  - product/service
  - currency
  - account selection
  - other business dimensions.

Do not infer meaning from the node name alone.

Use canonical metadata + representative values + surrounding rule context.

Classify:

GENERIC_BUSINESS_CONDITION
BRT_SPECIFIC_CONDITION
TECHNICAL_CONDITION
UNKNOWN_CONDITION

---

## 6. Market/domain mapping discovery

For each selected population:

First classify market using approved condition evidence only.

Look for:

- cond-country
- sendercountry
- receivercountry
- structured sender/receiver address or BIC
- explicit BRT conditions

Do not infer market from:

- population name
- rule ID
- destination
- routing hub
- processor
- endpoint
- environment

If market cannot be safely established:

cluster the rules by common condition pattern.

Create ONE SME question per shared pattern.

Do not ask one question per rule.

---

## 7. Message-family discovery

Determine:

MX
MT
MESSAGE_AGNOSTIC
CUSTOM
UNKNOWN

Use explicit conditions or strongly validated semantic evidence.

Do not force unknown families into existing categories.

If a group behaves as a catch-all independent of message family, consider
MESSAGE_AGNOSTIC only when evidence supports that behavior.

---

## 8. Structural anomaly review

Review the 37 estate structural anomaly candidates separately.

Do not automatically treat them as parser defects.

Classify each cluster:

KNOWN_SOURCE_DEFECT_PATTERN
LIKELY_SOURCE_DEFECT
NEW_VALID_CONTROL_PATTERN
PARSER_EXTENSION_REQUIRED
UNKNOWN

Use the validated MY/IN/SG malformed-marker findings as examples, but do not
assume all anomalies are defects.

Never modify the parser merely to make an anomaly disappear.

---

## 9. Flow-family generalization

Compare new flow signatures with existing validated flow families.

Classify:

KNOWN_FLOW
KNOWN_FLOW_WITH_VARIANT
NEW_BUSINESS_FLOW
IMPLEMENTATION_VARIANT
UNKNOWN_FLOW

Pay attention to cases where many exact rules reduce to a small number of
normalized flows.

These may later become optimization candidates, but do NOT optimize yet.

---

## 10. Update the reusable domain catalog

Promote knowledge only when justified.

Maintain:

GLOBAL
BRT_SPECIFIC
MARKET_SPECIFIC
POPULATION_SPECIFIC

For every promoted semantic record:

- scope
- evidence
- confidence
- first validated population
- populations confirmed in
- exceptions

Do not silently promote a population-specific behavior to GLOBAL.

---

## 11. Coverage measurement

After the selected validation wave, report the improvement:

Before:
Known node IDs: 112 / 418
Known condition dimensions: ...
Known flows: ...
Known error patterns: ...

After:
Known node IDs:
Known condition dimensions:
Known flows:
Known error patterns:

Also report:

Rules now covered by trusted semantics:
Rules still primarily novel:

The goal is maximum estate coverage per SME question.

---

## 12. Decide the next validation wave

After processing the six selected populations, recommend:

EXPAND_LOW_NOVELTY_VALIDATION
MOVE_TO_MEDIUM_NOVELTY
HIGH_NOVELTY_DOMAIN_WORK_REQUIRED
PARSER_EXTENSION_REQUIRED

Do not automatically process all remaining populations.

---

## 13. Required outputs

Create:

analysis/estate/targeted-validation-wave-1.md
analysis/estate/node-semantic-expansion.md
analysis/estate/node-semantic-expansion.json
analysis/estate/condition-semantic-expansion.md
analysis/estate/domain-mapping-gaps.md
analysis/estate/structural-anomaly-review.md
analysis/estate/domain-coverage.md
analysis/estate/sme-decision-sheet.md

The SME decision sheet should contain only genuine business questions.

Prefer fewer than 10 shared questions.

---

## 14. Final report

Report:

Populations validated:
Rules covered:

Known node IDs before:
Known node IDs after:

New GENERIC_REUSABLE semantics:
New BRT_SPECIFIC semantics:
New MARKET_SPECIFIC semantics:
Still UNKNOWN:

New condition dimensions resolved:
Still unknown:

Known flow coverage gained:
New business flows discovered:

Structural defects:
New valid control patterns:
Parser extensions required:

SME questions:
Rules affected by those questions:

Then recommend the next validation wave.

Do not start optimization implementation.
Do not generate production SQL.- VERB
- OPERATOR
- canonical NODES metadata

Persist an estate-wide local snapshot.

Do not send the entire raw dataset through LLM reasoning.

---

## 3. Discover rule-ID populations

Analyze rule IDs as metadata only.

Group naming patterns such as:

CBPR_MY_*
CBPR_IN_*
CBPR_SG_*
CBPR_HK_*
...

but also discover patterns we have never seen before.

Create normalized rule-ID population patterns.

Example output:

| Population pattern | Rules | Example IDs |
|---|---:|---|
| CBPR_MY_* | ... | ... |
| CBPR_IN_* | ... | ... |
| NEW_PATTERN_A | ... | ... |

IMPORTANT:

Rule-ID pattern may be used to discover populations.

It must NOT be used as business market evidence.

---

## 4. Compare the full estate against known semantics

Build the union of already known node IDs from MY + IN + SG.

For the full estate report:

- total unique NODE_IDs
- already-known NODE_IDs
- new NODE_IDs
- frequency of each new NODE_ID
- example rules containing each new node

Classify new nodes initially as:

KNOWN_GENERIC
LIKELY_GENERIC
MARKET_OR_BRT_SPECIFIC
UNKNOWN

Do not guess detailed semantics for UNKNOWN nodes.

---

## 5. Discover new condition dimensions

Identify every unique condition-node family across the estate.

Compare against known conditions such as:

- country
- sendercountry
- receivercountry
- senderaddress
- receiveraddress
- message type
- currency
- account
- routing
- participant
- product/service

Report new condition dimensions not present in MY/IN/SG.

For every new condition dimension include:

- node ID
- occurrence count
- number of rules
- representative verb values
- representative rule populations

Do not classify business meaning solely from the node name.

---

## 6. Discover new message families

Using explicit conditions only, identify message families/types such as:

- MX / pacs.*
- MX / camt.*
- MT
- custom/internal
- MESSAGE_AGNOSTIC
- UNKNOWN

Report any new message-type patterns not seen in the pilot markets.

Do not force unknown message patterns into existing categories.

---

## 7. Run the generic structural parser estate-wide

Use already validated GENERIC semantics for:

- ordered rank reconstruction
- condition/action separation
- error/retry block detection
- transaction states
- payload dependencies
- terminal behavior
- GLOBAL destination-vs-market rule
- GLOBAL prs-* channel-ingress semantics

Do not apply MY-, IN-, or SG-specific BRT mappings outside their scopes.

Record structural anomalies separately.

---

## 8. Detect structural novelty

Report new control-flow structures that were not observed in MY/IN/SG.

Examples:

- new block markers
- new nesting structures
- new retry structures
- unusual terminal behavior
- new producer/consumer dependencies

Classify:

KNOWN_STRUCTURE
NEW_VALID_PATTERN_CANDIDATE
SOURCE_DEFECT_CANDIDATE
UNKNOWN_STRUCTURE

Do not change the parser merely to accept new structures.

---

## 9. Generate estate-wide hierarchical signatures

For every rule generate:

- condition_signature
- flow_signature
- error_signature
- exact_signature

Then report:

Total rules:
Unique condition signatures:
Unique flow signatures:
Unique error signatures:
Unique exact signatures:

Compare them with the MY/IN/SG signatures.

Report:

- already-known families
- variants of known families
- completely new families

---

## 10. Discover business populations, but do not over-classify market

Where approved condition evidence clearly identifies a market, classify it.

Otherwise leave market as UNKNOWN/PARTIAL.

The current generic market evidence strategy may use approved condition evidence
such as:

- cond-country
- sendercountry
- receivercountry
- approved structured BIC/address country
- applicable message-family-specific precedence

But do not automatically generalize a market-specific BRT fallback.

For example:

MY sendercountry mapping remains MY-specific.
IN outbound mapping remains IN-specific.
SG-specific mappings remain SG-specific.

New markets should produce clustered SME/BRT questions.

---

## 11. Build novelty scores for rule populations

For each discovered rule population calculate a novelty profile using:

- new node IDs
- new condition dimensions
- new message families
- new flow signatures
- new error signatures
- unknown market mappings
- structural anomalies

Classify populations:

LOW_NOVELTY
MEDIUM_NOVELTY
HIGH_NOVELTY

This should help decide which population to validate next.

---

## 12. Find potential business families estate-wide

Use the existing business-family model, but treat it as provisional outside
validated MY/IN/SG scope.

Cluster using:

market/market evidence
message family
channel/source
condition schema
normalized business flow
error family
terminal outcome

Do not optimize yet.

Report whether the existing 124-family model expands significantly.

---

## 13. Specifically compare against current optimization findings

Determine whether BF001-BF005 patterns are:

- local to MY/IN/SG
- common across the estate
- instances of a much larger generic pattern

For example, search for other populations that have:

many exact rules
+ few condition schemas
+ one/few normalized flows

These may be stronger optimization opportunities than BF001.

Do not rank them for implementation yet.

---

## 14. Do not generate thousands of Markdown files

Primary output should remain machine-readable.

Create:

analysis/estate/
  estate-summary.md
  rule-populations.md
  node-novelty.md
  condition-novelty.md
  message-family-novelty.md
  flow-novelty.md
  structural-anomalies.md
  population-novelty-ranking.md
  business-family-expansion.md
  estate-rules.jsonl

Do not generate one Markdown card per rule.

---

## 15. Final report

Report:

Total estate rules:
Total rule-node rows:

Rule-ID population patterns:
Previously known populations:
New population patterns:

Unique node IDs:
Known node IDs:
New node IDs:

Known condition dimensions:
New condition dimensions:

Known flow signatures:
New flow signatures:

Known error signatures:
New error signatures:

Structural anomalies:

Rules with clear market evidence:
Rules requiring new BRT/SME market mappings:

Existing business families reused:
New provisional business families:

Top 10 LOW_NOVELTY populations:
Top 10 HIGH_NOVELTY populations:

BF001-like high-fragmentation families discovered:

Then recommend:

READY_FOR_TARGETED_ESTATE_VALIDATION
PARSER_EXTENSION_REQUIRED
DOMAIN_MAPPING_REQUIRED

Do not start optimization implementation.
Do not automatically deep-review every new population.
