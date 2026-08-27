Begin Phase 2A — Business Model Discovery.

Do NOT optimize, merge, delete, or rewrite rules yet.

The purpose of this phase is to infer a structured business model from the
validated rule data for MY, IN, and SG.

Use the existing local snapshots and validated classifications only.
Do not query Oracle unless a specific missing fact cannot be obtained locally.

1. Preserve the validated rule-level evidence.

Do not replace detailed rule records.

Business-model conclusions must remain traceable back to:
- rule IDs
- conditions
- node ranks
- flow signatures
- transaction states
- approved SME/BRT mappings

2. Build a normalized business-context model.

For every rule derive, where evidence exists:

market
direction
channel_or_instruction_source
message_family
message_type
currency
sender_geography
receiver_geography
participant/institution conditions
business/product conditions
flow_family
error_family
downstream_systems
transaction_states
terminal_outcome

Use UNKNOWN where evidence is insufficient.

Do not infer fields from rule names unless explicitly marked as weak metadata.

3. Keep these concepts separate.

MARKET:
business/geographic classification derived primarily from approved conditions.

DIRECTION:
inbound / outbound / return / internal / unknown.

CHANNEL / SOURCE:
where the instruction entered from, for example GPI, XSSL, TEC, or prs-* flows.

MESSAGE FAMILY:
MX / MT / custom / unknown.

DOWNSTREAM SYSTEM:
routing hub, processor, service, endpoint.

Do not treat channel, destination, routing hub, processor, or rule ID as market.

4. Apply the validated MX rule.

For MX / ISO 20022 traffic:
- receiver-side geography normally determines market after an explicit approved
  cond-country rule;
- sender/receiver geography differences are not automatically conflicts;
- retain both sender and receiver geography.

5. Discover business families.

Cluster rules using business-level dimensions rather than exact implementation.

Suggested family key:

market
+ direction
+ channel/source
+ message family
+ major condition family
+ normalized flow signature

Do not include:
- rule ID
- exact endpoint
- exact global variable name
- implementation-only node differences

unless they materially change business behavior.

6. For each discovered business family produce:

Business family name:
Markets:
Directions:
Channels:
Message families:
Typical entry conditions:
Typical main flow:
Typical error/retry flow:
Typical transaction-state progression:
Typical terminal outcome:
Downstream system categories:
Number of rules:
Representative rules:
Known variants:
Known exceptions:
Confidence:
Open business questions:

Do not call differences redundant yet.

7. Build business-condition matrices.

Create a matrix showing which condition dimensions define each business family.

For example:

Family | Market | Direction | Channel | Message | Currency | Sender | Receiver | Flow

The goal is to see which conditions actually distinguish business behavior.

8. Build cross-market comparison.

Compare MY, IN, and SG at the business-family level.

Identify:

- business families shared across all markets;
- families shared by two markets;
- market-specific families;
- same business flow with different market conditions;
- same conditions with different downstream routing;
- same flow with different error behavior.

Do NOT classify these as optimization candidates yet.

9. Build a business flow model.

Create a high-level model such as:

Channel / Source
    ↓
Entry Conditions
    ↓
Validation
    ↓
Business Processing
    ↓
Routing / Processor
    ↓
Reporting
    ↓
Completion

with optional branches:

Error
Retry
Replay
Callback

Derive the actual stages from evidence rather than forcing every rule into this
example.

10. Required outputs

Create:

docs/business-model.md
analysis/business-families.md
analysis/business-condition-matrix.md
analysis/business-flow-model.md
analysis/cross-market-business-model.md
analysis/business-model-gaps.md

Also create a machine-readable form:

analysis/business-families.json

11. Business-model gaps

Only create a gap when business meaning cannot be established safely.

Cluster gaps by shared question.

Examples:

- meaning of a channel condition
- direction cannot be determined
- transaction-state meaning unknown
- two flows look structurally identical but business purpose is unclear

Do not ask one question per rule.

12. Final report

Report:

Rules modeled:
Business families:
Cross-market families:
Market-specific families:
Direction families:
Channel/source families:
Message families:
Flow families:

Rules with complete business context:
Rules with partial business context:

Unique business-model gaps:
Rules affected by gaps:

Largest business families:
Most common flow families:
Most common condition dimensions:

Then recommend whether the project is ready for:
BUSINESS_MODEL_REVIEW
or
MORE_DOMAIN_INPUT_REQUIRED

Do not start optimization automatically.
