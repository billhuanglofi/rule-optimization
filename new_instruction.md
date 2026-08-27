Update the market-classification logic with the following SME/BRT clarification.

# MX / ISO 20022 Market Classification Rule

For ISO 20022 MX messages, including message families such as:

- pacs.*
- camt.*
- other approved MX message types

the business market normally follows the receiver side, especially the country represented by receiveraddress.

This rule applies only when the message has been positively identified as MX / ISO 20022.

## 1. Identify MX first

Determine whether the rule applies to an MX message using an explicit message-type condition.

Examples may include:

- message type = MX
- pacs.*
- camt.*

Use the actual normalized message-type field from the rule data.

Do not infer MX from arbitrary text.

Add:

message_family = MX | NON_MX | UNKNOWN

## 2. MX market evidence precedence

For MX rules, use this market-evidence preference:

1. explicit cond-country = XXX, if it is clearly an approved BRT market condition
2. approved country derived from receiveraddress
3. cond-receivercountry = XXX
4. cond-sendercountry = XXX
5. approved senderaddress/BIC-derived country
6. other approved BRT mappings

Important MX behavior:

When sender-side and receiver-side geographic evidence differ, receiver-side evidence normally represents the relevant market for MX processing.

Example:

message type = pacs.008
sendercountry = HK
receiveraddress = ABCDSG...

If receiveraddress is a valid approved structured address/BIC and its country component resolves to SG:

market_group = SG
confidence = HIGH
market_classification_source = mx_receiver_address_country

Preserve both sender-side and receiver-side geography in the rule record.

Do not discard sendercountry information.

## 3. Receiveraddress parsing

Do not search arbitrary text for a country code.

Only derive country from receiveraddress when:

- the receiveraddress format is known and approved
- the country-code position is structurally defined
- the extracted code is a valid country/market code
- the raw value, extracted code, node ID, rank, and condition are retained as evidence

If receiveraddress format is unclear, do not guess.

Keep the rule unresolved or use the next approved evidence source.

## 4. Sender/receiver differences are not automatically conflicts for MX

For MX messages, a difference such as:

sendercountry = HK
receiveraddress country = SG

may be expected.

Record it as:

market_group = SG

market_classification_source:
- type = mx_receiver_address_country
- evidence = receiveraddress condition

related_geography:
- sender_country = HK
- receiver_country = SG

Do not automatically mark NEEDS_REVIEW just because sender and receiver geography differ.

## 5. When MX should be marked NEEDS_REVIEW

Mark NEEDS_REVIEW only when:

- receiver-side evidence conflicts with other receiver-side evidence
- multiple receiver conditions disagree
- receiveraddress country extraction is unreliable
- an explicit approved BRT market condition contradicts the receiver-derived market
- another approved BRT rule establishes different precedence

## 6. Non-MX rules

Do not automatically apply the MX receiver preference to non-MX messages.

For NON_MX rules, continue using the normal condition-driven market classifier.

## 7. Existing global rules remain unchanged

Continue to enforce:

- destination / routing_hub is not market evidence by itself
- prs-* means channel/upstream ingress, not market
- rule ID is not market evidence
- extraction scope is not market evidence
- processor is not market evidence
- endpoint is not market evidence
- environment is not market evidence

## 8. Re-run current SG classification

Apply this clarification to the existing SG local snapshot only.

Do not query Oracle again.

Re-run market classification and report:

MX rules:
NON_MX rules:
UNKNOWN message-family rules:

MX market evidence sources:
- explicit_country:
- mx_receiver_address_country:
- receiver_country:
- sender_country:
- other approved mapping:

MX sender/receiver geography differences:
MX unresolved receiveraddress formats:
MX conflicting receiver-side evidence:

NON_MX market evidence sources:

Rules with no approved market evidence:
Remaining ambiguity patterns:

Also show 5-10 representative MX examples with:

- rule ID
- message type
- sender country/address
- receiver country/address
- selected market
- classification source
- confidence

Do not start SG validation automatically.
Do not start HK.
Do not start Phase 2 optimization.
