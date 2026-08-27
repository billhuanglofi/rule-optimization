### SME Resolution

Status: PARTIALLY_RESOLVED

Business rule:

IF:
- no explicit country/market condition exists; AND
- `cond-sendercountry` exists; AND
- its normalized value is `MY`

THEN:
- classify the rule market as `MY`;
- confidence = HIGH;
- use the `cond-sendercountry` node/rank/value as evidence.

Example evidence:

`cond-sendercountry = MY`
→ market = MY

Precedence:

1. Explicit country/market condition wins.
2. Otherwise use `cond-sendercountry = MY`.
3. If neither is available, keep the market unresolved.
4. If signals conflict, do not guess; mark `NEEDS_REVIEW`.

Do not classify a rule as MY merely because the text `MY`
appears somewhere else in the rule.
