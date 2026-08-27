Apply the SME resolution for A02.

Use the existing local snapshot only. Do not re-query Oracle.

Update the classifier so that:

1. destination/routing_hub is treated only as downstream-system / technical-routing information;
2. destination/routing_hub must not be used as evidence for market_group;
3. market classification must come from BRT-defined business conditions or approved mappings;
4. rules previously marked ambiguous only because of routing_hub_not_market_proven should be re-evaluated;
5. if no BRT-aligned market evidence exists, keep market unresolved rather than inferring it from destination;
6. update phase-1-gaps.md with the number of rules resolved by this SME decision and the number still unresolved.

Also move this rule into permanent project knowledge, for example:
docs/business-rules.md or config/domain-mappings.yaml

--------
### Destination vs market

`destination` / `routing_hub` represents downstream-system or technical-routing information.

It is not market evidence by itself.

Market classification must follow BRT-defined business logic or approved mappings.
