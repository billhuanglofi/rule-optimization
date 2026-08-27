### SME Resolution

Status: RESOLVED

Business rule:

`destination` / `routing_hub` values describe the downstream system or technical routing destination.

They do **not** determine the business market category by themselves.

The market category is defined by the BRT (Business Rule Template) and should be derived from BRT-aligned business conditions/mappings, not from the destination value.

Therefore:

- treat `destination` / `routing_hub` as downstream-system information;
- do not infer `market_group` from destination alone;
- do not infer `destination_market` from destination alone unless an explicit BRT mapping says so;
- preserve destination as a separate technical dimension;
- determine market from BRT-defined conditions or approved business mappings;
- if BRT-derived market evidence is missing, keep market as `UNKNOWN` or `PARTIAL` rather than guessing.

Confidence: HIGH
Source: SME / BRT business definition
