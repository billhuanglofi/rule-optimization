I need you to investigate a potential rule-selection regression in this repository.

IMPORTANT:
- Investigation only.
- Do NOT modify any source files.
- Do NOT create commits.
- Do NOT refactor anything.
- Do NOT propose a fix until the investigation is complete.
- Base conclusions on actual source code and configuration found in this repository.
- When reporting findings, include file paths, class/method names, and relevant line numbers where possible.

Background
==========

We are investigating behavior differences between a legacy version and V26 of the initiator service.

The current hypothesis is:

1. Multiple rules can match the same incoming message.
2. Legacy behavior did not have a deterministic tie-breaker for certain equally ranked rules.
3. V26 introduced deterministic sorting.
4. V26 appears to sort using something equivalent to:

    Comparator.comparingInt(Rule::getWeight).reversed()
        .thenComparing(
            Comparator.comparingInt(Rule::getRank).reversed()
        )
        .thenComparing(Rule::getId)

5. Therefore, when two rules have:
   - the same weight
   - the same rank
   - and both match the input

   V26 selects the alphabetically/lower sorted rule ID.

Examples observed during investigation include pairs similar to:

    CBPR_GB_IB_151339_02
    CBPR_GB_IB_151344_016

and:

    CBPR_CL_IB_382001
    rule-382015

Runtime logs also appear to contain messages similar to:

    "There are more than one rules matching the headers"

and:

    "The selected Rule ID is: ..."

The objective of this investigation is to understand how we can identify ALL potentially ambiguous rule pairs from:
1. the database/static rule configuration
2. runtime logs / Splunk


Investigation tasks
===================

Please investigate the repository and answer the following.

A. Rule model
-------------

Find the Rule model/domain object.

Determine:

- Rule ID field
- Rule weight
- Rule rank
- conditions associated with a rule
- enabled/disabled status if applicable
- effective/start/end dates if applicable
- country / direction / message type / service / scope fields if applicable

Explain how Rule.getWeight() is calculated.

Specifically determine whether weight means:
- number of conditions
- number of distinct input fields
- or something else.

Explain exactly how Rule.getRank() is calculated.

Identify the ranking values for operators such as:
- ==
- =
- IN
- LIKE
- IN_LIKE
- any other supported operators

Do not infer these values. Find the implementation.


B. Rule ordering / selection
----------------------------

Find the exact code that sorts matching rules.

Find all usages of:
- Comparator
- getWeight()
- getRank()
- getId()
- sorted(...)
- sort(...)
- findFirst()
- matchMost(...)
- RuleMatcher
- StoreCache

Determine the COMPLETE rule precedence order.

For example, verify whether it is actually:

    weight DESC
    rank DESC
    rule ID ASC

and then first matching rule.

Show the exact implementation.

Also determine whether sorting happens:
- when rules are loaded into cache
- before matching
- after matching
- or in multiple places.

Identify the data structure containing rules before sorting.

Pay particular attention to:
- HashMap
- ConcurrentHashMap
- HashSet
- collections with undefined iteration order


C. Legacy behavior
------------------

Use git history if available.

Find when `.thenComparing(Rule::getId)` or equivalent rule-ID tie-breaking was introduced.

Identify:
- commit
- PR reference if present
- reason from comments/commit messages if available

Also inspect the previous implementation.

Explain what happened in the legacy implementation when two rules had identical precedence.

Determine whether the old selection could depend on:
- map iteration order
- set iteration order
- insertion order
- database return order
- cache loading order
- another factor

Clearly distinguish confirmed findings from hypotheses.


D. Matching semantics
---------------------

Find the actual rule matching implementation.

For every supported operator, document how matching works.

Especially investigate:

    ==
    IN
    LIKE
    IN_LIKE

Determine:
- wildcard syntax
- case sensitivity
- NULL behavior
- empty-string behavior
- trimming behavior
- multiple values
- escaping
- regex conversion if applicable

This is important because later we want to detect whether two rules can overlap.

Identify the exact class/method that answers:

    "Does this rule match this input message?"


E. Multiple-match detection
---------------------------

Search for the log message:

    "There are more than one rules matching the headers"

or similar wording.

Find the code producing it.

Report:
- class
- method
- log level
- exact log format
- fields included in the log

Determine whether that log contains:
- all matching rule IDs
- rank
- weight
- message ID
- correlation ID
- request ID
- country
- message type
- destination
- OwningGrp
- Tag 70
- application/service version

Also find the log:

    "The selected Rule ID is"

Document its exact format.


F. Correlation fields for Splunk
--------------------------------

Identify which identifiers can be used to correlate:

1. the "multiple rules matched" log
2. the "selected rule" log
3. downstream routing/destination logs

Look for fields such as:

    messageId
    msgId
    correlationId
    x-correlation-id
    x-request-id
    x-mesh-request-id
    transactionId

Determine which identifier remains stable across the entire processing flow.

If possible, identify which identifier would also remain the same when comparing legacy and V26 test executions.


G. Database / rule persistence
------------------------------

Find where rules come from.

Determine whether they are loaded from:
- relational database
- configuration files
- API
- cache
- another service

Find:
- repository/DAO classes
- SQL queries
- ORM entities
- table names
- column names
- joins used to assemble rules and conditions

Document the relevant schema as far as it can be determined from the source.

I specifically need enough information to later write SQL that can find candidate rule collisions.

For each rule, identify where we can obtain:

    rule_id
    enabled/status
    weight or inputs needed to calculate weight
    rank or operators needed to calculate rank
    condition field name
    condition operator
    condition value
    country/scope
    effective dates

If SQL files, migrations, Liquibase, Flyway, Hibernate/JPA mappings, etc. exist, inspect those too.


H. Static collision detection feasibility
-----------------------------------------

Based on the actual implementation, assess how we could statically find risky rule pairs.

A "high-risk pair" currently means:

    both rules can match the same message
    AND same weight
    AND same rank

so V26 must use rule ID as the tie-breaker.

Do NOT implement this scanner yet.

Instead determine:

- what data would be required
- which comparisons are easy in SQL
- which comparisons require application matching semantics
- whether the existing matcher can potentially be reused
- which fields can be used to reduce the candidate set before doing expensive overlap analysis

Consider grouping/filtering candidates by things such as:

    country
    direction
    message type
    field set
    weight
    rank
    enabled state
    effective date

Explain which of these are actually valid based on the code.


I. Splunk investigation feasibility
-----------------------------------

Based on the logging code, propose the raw searches we should eventually use in Splunk.

Do NOT assume field extractions already exist.

First provide searches based on raw text such as:

    index=<index>
    "There are more than one rules matching the headers"

and:

    index=<index>
    "The selected Rule ID is"

Then identify what rex/extractions would be needed to obtain:

    candidate rule IDs
    rank
    weight
    selected rule
    correlation/message ID

Do not invent the regex until you have confirmed the exact log format from source.


Expected output
===============

Produce an investigation report with these sections:

1. Executive summary

2. Exact V26 rule-selection algorithm

3. Legacy algorithm and behavioral difference

4. Rule weight calculation

5. Rule rank calculation

6. Supported matching operators and semantics

7. Database schema / rule-loading path

8. Relevant logging statements and exact formats

9. Best correlation identifier for Splunk

10. Proposed method for finding candidate rule pairs in DB

11. Proposed method for finding actual collisions in Splunk

12. Risks / unknowns requiring further investigation

13. Important source references
    - file
    - class
    - method
    - line/reference

14. Recommended NEXT investigation steps

Do not make code changes.

At the end, give me a compact table containing:

Finding | Evidence | Confidence | Source location

Use:
- CONFIRMED = directly demonstrated by source
- LIKELY = strongly supported but not fully proven
- UNKNOWN = insufficient evidence

Most importantly:
do not treat the initial hypothesis as fact. Attempt to prove or disprove it from the repository.
