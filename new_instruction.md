# Estate-Wide Rule Discovery

Pause detailed optimization design for now.

We have validated the framework using MY, IN, and SG pilots, but these markets
represent only part of the full rule estate.

The next objective is to inspect ALL rules in the target database and determine
what additional rule populations, schemas, semantics, and business families
exist.

This is discovery only.

Do NOT:
- modify production rules
- generate migration SQL
- merge/delete rules
- assume MY/IN/SG market mappings apply globally
- perform deep LLM review rule-by-rule

Use the high-throughput bulk/local-cache architecture.

---

## 1. Preserve current validated baselines

Do not modify the frozen MY, IN, or SG results.

Verify their baseline/checksum markers before and after this scan.

The current MY/IN/SG knowledge becomes reference knowledge only.

---

## 2. Inventory the complete database rule estate

Bulk-read all relevant RULES_V2 / RULES_NODES records.

Target:
- zero per-rule Oracle queries
- zero per-node Oracle queries

Capture at minimum:

- RULE_ID
- DESCRIPTION
- ENVIRONMENT
- NODE_ID
- RANK
- VERB
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
