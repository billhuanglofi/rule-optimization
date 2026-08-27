# Domain Context for Rule Analysis

This file captures the basic rule model visible in the current project material. Treat the live repository and database as the final source of truth.

## 1. Logical data model

### `RULES_V2`

Rule-level information.

Typical meaning:

```text
ID           -> rule identifier
DESCRIPTION  -> business / routing description
ENVIRONMENT  -> target environment
```

### `RULES_NODES`

The ordered implementation of a rule.

Typical meaning:

```text
RULE_ID      -> parent rule
NODE_ID      -> condition/action node
RANK         -> execution/order position
VERB         -> node-specific configuration/payload
OPERATOR     -> condition operator when applicable
ENVIRONMENT  -> environment
```

A rule should be read in ascending `RANK`.

### `NODES`

Canonical node definitions used by `RULES_NODES.NODE_ID`.

Common naming styles:

```text
cond-*    condition
env-*     environment condition
act-*     action
block-*   flow control / branch boundary
```

Do not rely on prefix alone; resolve the actual node definition.

### `GLOBAL_VARS`

Environment-specific global values.

Typical meaning:

```text
ID           -> variable name
ENV          -> environment
DESCRIPTION  -> purpose
VALUE         -> concrete value
```

Rules may reference global variables inside node verbs.

## 2. Common rule concepts

The current rule material includes examples of concepts such as:

- country / market checks;
- instruction currency;
- message type;
- receiver or destination;
- routing;
- message validation;
- transaction state;
- duplicate checking;
- error handling;
- retry handling;
- replay / wiretap behavior;
- sanctions handling;
- SMS or service callbacks;
- payment-message completion;
- PDP reporting;
- recombination;
- translation;
- input-payload flags;
- global endpoint variables.

These are useful classification dimensions, not a complete list.

## 3. Flow-control concepts

The following patterns should be recognized as structural behavior:

```text
block-on-retry ... block-on-retry-end
block-on-error ... block-on-error-end
act-on-error   ... act-on-error-end
```

When analyzing a rule, summarize each branch separately.

## 4. Ordering matters

Rank is part of the business behavior.

Examples of order-sensitive logic:

- a condition must be evaluated before actions;
- producer nodes may need to occur before a later input-payload flag is consumed;
- payment completion may need to be the final meaningful node of a path;
- an error-path completion action may need to appear immediately before the end marker.

Therefore, never summarize a rule as an unordered list of node IDs only.

## 5. Global variables

Upper-snake-case tokens in a `VERB` may represent `GLOBAL_VARS` references.

Example pattern:

```text
$CBPR_SOME_ENDPOINT
```

When a token looks like a global variable:

1. capture the token;
2. resolve it in `GLOBAL_VARS` for the target environment;
3. record the variable description;
4. avoid exposing secrets if the stored value is sensitive.

## 6. Market vs routing destination

These are different dimensions.

A rule may have:

- origin market,
- destination market,
- currency,
- payment product,
- routing hub,
- processor,
- channel.

For example, a hub/destination value should not automatically be labeled as the market unless a business condition proves that interpretation.

## 7. Phase-1 objective

The objective is not to judge whether a rule is good.

The objective is to answer:

> What traffic reaches this rule, what conditions define that traffic, and what does the rule do with it?
