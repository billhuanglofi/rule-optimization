The discovery phase is complete. Do not redesign or rerun the generic discovery
algorithm unless a correctness defect is found.

Now HARDEN the final result for release-impact assessment.

1. Review every one of the 5 EU STRICT latent transitions individually.
   For each provide:
   - complete legacy matching/top-candidate result
   - complete V26 matching/top-candidate result
   - generated witness
   - all rules matching that witness
   - exact reason legacy prefers A
   - exact reason V26 prefers B
   - whether the witness is only matcher-valid or also demonstrably
     request/message-reachable
   - source DB conditions for the decisive rules

2. Keep the 17,190 tie-determinization groups separate from strict transitions.
   Do not present them as 17,190 confirmed production incidents.

3. Keep MATCH_SET_CHANGE results separate from precedence-only changes.

4. Reconcile the 3,283 unique impacted-rule IDs into:
   - strict-change rules
   - tie-determinization rules
   - match-set-change rules
   - overlapping categories
   - runtime-confirmed rules
   - latent-only rules

5. Produce an executive release-impact table with:
   Environment
   Legacy winner / possible winner
   V26 winner
   Change type
   Candidate rules
   Witness status
   Request-reachable?
   Historical/runtime observed?
   Runtime count
   Confidence
   Recommended investigation priority

6. Highlight the independently rediscovered known mismatch dataset:
   - 15/15 transitions rediscovered
   - 2,334/2,334 occurrence-weighted rediscovery
   - clearly state that workbook runtime/service-version provenance remains
     unknown, so this is validation/supporting evidence rather than authoritative
     runtime proof.

7. Quantify unresolved coverage:
   - 96,496 EU UNKNOWN pairs
   - operators/reasons causing UNKNOWN
   - condition fields with unknown request/extraction origin
   - nextRules coverage
   - current-vs-historical snapshot limitation

8. For AP PRODLIKE, produce the same summary format as EU and clearly distinguish
   "no strict generated transitions" from "no behavior change."

9. For AM PRODLIKE, do not fabricate results. State exactly what configuration
   or access is missing and provide the ready-to-run command/config.

10. Produce a final management-friendly conclusion answering:

    A. What has definitely changed in V26?
    B. Which rule transitions are strongest candidates for actual impact?
    C. Which known regression transitions were independently reproduced?
    D. How much of the rule population was covered?
    E. What remains unknown?
    F. What needs runtime evidence before release decisions?
    G. Which rules should be reviewed first?

Do not end with another investigation plan.

Update FINAL_INVESTIGATION_REPORT.md and produce a concise
RELEASE_IMPACT_SUMMARY.md containing only the decision-relevant findings.
