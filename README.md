Hi Lisa,

I completed the research on the WFP security issue. Summary of findings, what I've done so far, and the proposed next steps below.

Findings — two separate causes in the cell security rules on }CellSecurity_Workforce Planning Input and }CellSecurity_SCN Workforce Planning Input:

Version gap. The location-security gates (Rules 0000 and 0100) were scoped only to the Forecast version. In any other version — including Plan — they never executed, so cells fell through to the final [] = 'READ' default. This let Plan intersections bypass Center/Location security, which is what the Argentina user observed.
Hierarchy dependency. The Limited-user compensation restriction (Rule 0110) relies on Employee hierarchy ancestry (Existing / Orphans). For employees that belong to multiple rollups — e.g. ee 100325 under both Existing and Employee Control Total, per Sherry — that test may not behave consistently.
Approach — phased, to minimize production risk:

Phase 1 (low risk): Remove the Forecast-only scope from Rules 0000 and 0100 so Center/Location security is enforced in every version. The Center Location Security WFP lookup is version-independent, so this introduces no new security data and no per-version setup — it simply applies the existing, already-populated access model to Plan as well. No change to compensation masking, write permissions, or the New/Replacement workflow; Plan remains read-only.
Phase 2 (only if still required): If Limited users can still see compensation after Phase 1 testing, redesign Rule 0110 so it no longer depends on hierarchy ancestry. That affects the Existing / New / Replacement workflow, so I will bring it back to you and Sherry for explicit business approval before implementing.
Status: I have applied the Phase 1 change in QA on both cubes and saved a copy of the original rules for rollback. Nothing has been migrated to Production — that will only happen after testing is complete and the change goes through the normal migration/approval process.

Next steps:

Sherry to set Mayank up with the same Limited access as the reported Argentina user, so he can validate the views in QA.
I will run the test cases (authorized vs. unauthorized Center/Location, Forecast vs. Plan, plus a standard-user regression check) and confirm whether existing-employee compensation is correctly hidden.
Based on the result, either proceed to the Production migration (Phase 1 only) or open Phase 2 for your and Sherry's approval.
I'll report the QA test results back to the team. Please let me know if you'd like any adjustment to the approach.

Regards,
Niranjan
