Update the Phase 1 market-classification strategy.

From now on, focus primarily on CONDITION evidence when determining market.

The key principle is:

Conditions determine where/who the rule applies to.
Routing/actions determine what happens to the instruction.

Add a reusable condition-driven market classifier.

For any normalized country code XXX:

1. If an explicit condition contains:

   cond-country = XXX

   classify:
   market_group = XXX
   confidence = HIGH
   source = explicit_country

2. If:

   cond-sendercountry = XXX

   classify:
   market_group = XXX
   confidence = HIGH
   source = sender_country

3. If:

   cond-receivercountry = XXX

   classify:
   market_group = XXX
   confidence = HIGH
   source = receiver_country

4. If an approved sender-address/BIC condition contains a country code
   in the defined country-code position, for example:

   ABCDXX...

   where XX is a recognized market/country code,

   classify:
   market_group = XX
   confidence = HIGH
   source = sender_address_country

   Preserve:
   - original condition
   - original address/BIC
   - extracted country code
   - node ID
   - rank

Do not use arbitrary substring matching for addresses.
Parse the country code only from the approved structured position/format.

Market evidence precedence:

1. explicit country
2. sender country
3. receiver country
4. approved address/BIC-derived country
5. other BRT-approved condition mapping

If multiple condition sources agree, increase evidence strength but keep
the same market.

Example:

cond-sendercountry = SG
cond-receivercountry = SG

=> market_group = SG
=> confidence = HIGH
=> evidence_sources = [sender_country, receiver_country]

If approved condition sources conflict, do NOT choose one silently.

Example:

cond-sendercountry = IN
cond-receivercountry = SG

=> market_conflict = true
=> status = NEEDS_REVIEW

unless the BRT explicitly defines how direction determines which condition
owns the market classification.

The following are NOT market evidence by themselves:

- destination
- routing_hub
- prs-* routing conditions
- processor
- endpoint
- deployment environment
- rule ID
- extraction scope

`prs-*` remains GLOBAL channel/source-flow information only.

Re-run the SG classification from the existing local snapshot only.

First report the market evidence distribution:

explicit_country:
sender_country:
receiver_country:
sender_address_country:
multiple_agreeing_conditions:
conflicting_conditions:
no_condition_market_evidence:

Then regenerate SG gaps based on the remaining rules only.

Do not query Oracle.
Do not automatically start SG validation.
