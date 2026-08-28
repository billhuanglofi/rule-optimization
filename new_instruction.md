Yes, confirm both decisions as follows.

## 1. Canonical country conditions are approved GLOBAL market evidence

These condition types are business/geographic evidence globally and do not
require separate approval for every market:

cond-country = XXX
-> market_group = XXX
-> confidence = HIGH
-> source = explicit_country

cond-sendercountry = XXX
-> market_group = XXX
-> confidence = HIGH
-> source = sender_country

cond-receivercountry = XXX
-> market_group = XXX
-> confidence = HIGH
-> source = receiver_country

This applies to CN and other markets as well.

Do not require a separate CN-specific mapping merely because the population
was not part of the original MY/IN/SG pilots.

However, keep existing precedence/conflict rules:

- explicit cond-country is direct strong evidence;
- for MX / ISO 20022, receiver-side geography normally has business precedence
  when sender and receiver geography differ;
- sender/receiver differences alone are not defects for cross-border traffic;
- if two approved business conditions genuinely contradict each other, do not
  silently choose one — retain the conflict for review/defect classification.

Continue to NOT use these as market evidence:

- rule ID
- population name
- extraction scope
- destination
- routing hub
- processor
- endpoint
- environment

So for the 13 Wave-1 cases, apply the canonical country-condition rule directly
where the evidence exists and report any genuine conflicts separately.

## 2. The six unmatched/unclosed control-marker cases are source defects

The rule model requires correctly paired control markers.

Examples:

block-on-error
...
block-on-error-end

block-on-retry
...
block-on-retry-end

Therefore, for the six Wave-1 cases where:

- an end marker has no matching start;
- a block starts as one type and ends as another type; or
- a block reaches the end of the rule without its required matching end;

classify:

structural_status = SOURCE_DEFECT
optimization_readiness = BLOCKED

Do not change the generic parser.
Do not insert a missing marker.
Do not reinterpret the stored sequence based on nearby action names.

The business context of those rules may still be understood independently.

## 3. Continue

Re-evaluate the 13 market cases and six structural anomalies from the existing
local snapshot.

Then regenerate the SME decision sheet.

Do not ask these two questions again unless a genuinely different condition
semantic or control construct is discovered.

After that, recommend Wave 2 populations based on:

maximum additional estate coverage per new SME question

Do not start Wave 2 automatically.
