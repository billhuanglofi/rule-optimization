# Phase 1 — Rule Understanding Agent Prompt

Use this document as the execution prompt for the AI agent.

## Mission

Build a reliable business/technical understanding of the existing rule set.

The main questions are:

1. **Which traffic does each rule apply to?**
2. **Which market or geography does it represent?**
3. **Which business/payment/message flow does it participate in?**
4. **What are the important conditions?**
5. **What actions occur on the happy path?**
6. **What happens on retry and error paths?**
7. **What external variables, services, processors, hubs, or endpoints are referenced?**
8. **What transaction-state/reporting side effects occur?**

Do not optimize yet.

---

## Step 1 — Inventory all rules

Create a complete list from `RULES_V2` or the equivalent source.

For each rule collect at least:

```text
rule_id
description
environment
```

Then count the nodes in `RULES_NODES`.

Output:

```text
analysis/rule-inventory.md
```

---

## Step 2 — Reconstruct each rule

For each `rule_id`:

1. fetch all `RULES_NODES`;
2. sort by `RANK ASC`;
3. resolve each `NODE_ID` against `NODES`;
4. retain:
   - rank,
   - node ID,
   - node type,
   - verb,
   - operator,
   - environment;
5. identify branch boundaries.

Never drop rank information.

---

## Step 3 — Parse conditions

For nodes that behave like conditions, extract condition dimensions.

Look for signals such as:

```text
country
market
region
currency
instruction currency
message type
receiver
sender
channel
source
destination
BIC
product
service
transaction state
payload flags
environment predicates
```

Store the literal evidence and a normalized interpretation.

Example:

```yaml
condition:
  dimension: country
  raw: "country:CBPR"
  normalized: "CBPR"
```

If a condition is custom or unclear, keep it as:

```yaml
condition:
  dimension: custom
  raw: "<exact meaningful fragment>"
  normalized: UNKNOWN
```

---

## Step 4 — Determine market classification

Use the following evidence order:

1. explicit country/market/region condition;
2. explicit currency + routing/business context;
3. explicit receiver/sender/product context;
4. rule description;
5. rule ID/name.

Produce these fields:

```yaml
market:
  primary: "..."
  secondary: []
  confidence: HIGH|MEDIUM|LOW
  evidence: []
```

Possible values may include known project-specific markets or `UNKNOWN`.

Do not use deployment environment as market.

---

## Step 5 — Determine flow classification

A rule may participate in more than one flow.

Classify using node behavior, not only the rule name.

Suggested flow tags:

```text
validation
routing
payment-initiation
payment-completion
message-formatting
translation
recombination
duplicate-check
sanctions
screening
pdp-reporting
callback
notification
sms
replay
retry
error-handling
processor-delivery
channel-delivery
custom
```

For every flow tag, provide evidence.

Example:

```yaml
flows:
  - value: sanctions
    confidence: HIGH
    evidence:
      - "act-pol-lib-sanctions-boxing"
      - "SANCTION_NACKED transaction state"
```

---

## Step 6 — Describe the rule as a flow

Create a short ordered narrative.

Preferred format:

```text
IF <conditions>
THEN <main actions in order>
ON ERROR <error behavior>
ON RETRY <retry behavior>
ENDS WITH <terminal action/state>
```

Example structure only:

```text
IF country=CBPR and currency=USD
THEN validate message -> route -> perform reporting
ON ERROR send to replay -> complete payment message
ENDS WITH payment completion
```

Do not invent details not supported by the rule.

---

## Step 7 — Resolve dependencies

Record:

- global variables;
- external endpoint names;
- service names;
- processors/hubs;
- payload flags;
- producer/consumer dependencies;
- transaction states;
- replay topics;
- callbacks.

Do not print secret values. Prefer variable IDs and descriptions.

---

## Step 8 — Detect structural semantics

Record whether the rule contains:

```yaml
branches:
  retry: true|false
  error: true|false
  nested: true|false

terminal_behavior:
  node: "..."
  transaction_state: "..."
```

Also record important block ranges by rank.

---

## Step 9 — Add confidence and questions

For every uncertain interpretation:

```yaml
confidence: LOW
open_question: "..."
```

Examples:

- Is `HUB_MAS` a destination market or a processing hub?
- Does a currency condition represent market routing or settlement currency only?
- Is a transaction state final or intermediate?

---

## Step 10 — Produce team-readable outputs

Create:

```text
analysis/
├── rule-inventory.md
├── rule-index-by-market.md
├── rule-index-by-flow.md
├── rule-index-by-transaction-state.md
├── rule-index-by-node.md
└── rules/
    └── <RULE_ID>.md
```

Use `docs/04-output-contract.md` and `templates/rule-card.md`.

---

## Phase-1 completion criteria

Phase 1 is complete only when:

- every active rule is inventoried;
- every rule has an ordered rule card;
- every rule has market classification or `UNKNOWN`;
- every rule has one or more flow tags or `UNKNOWN`;
- all classifications include evidence;
- main/error/retry behavior is separated;
- unresolved questions are listed;
- cross-rule indexes are generated;
- no production rule has been changed.

At the end, produce a short `analysis/phase-1-gaps.md` containing only unresolved issues that block confident understanding.
