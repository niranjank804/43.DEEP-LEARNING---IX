As an EPA System Admin, I need to investigate and fix a security issue identified when creating a new version where users in the Everyone group are expected to have Read access, but that access is not being recognized.

The intended behavior is that assigning Read access to the Everyone group grants visibility to all applicable users. In the newly created version, this behavior is not occurring, indicating a potential issue with how security is applied during version creation or processed through existing security logic (including TI). This PBI includes investigating the root cause and implementing a fix if one is identified, ensuring the version security behaves consistently with expectations.
Acceptance criteria
Team agreement is reached that Everyone group should have Read access in the new version
Security behavior for the new version is reviewed and understood
Reason the Everyone group is not being recognized is identified
Impacted security logic (including TI, version setup, or DIM security) is identified
Fix is implemented if a solution is identified
Fix does not negatively impact existing security behavior
Users in the Everyone group have Read access as expected after the fix
Security results are validated against expected behavior
No unintended changes to unrelated security settings
Changes are documented and meet the team’s Definition of Ready and Definition of Done




Hi Sherry,

Thank you for reviewing this and for the detailed testing.

Your assumption was correct. The original implementation still contained hardcoded Program Code exclusions in the Data tab, which limited maintainability because any future exclusions would have required a TI code change.

I updated the logic to make the exclusion handling fully data-driven using the `SSAP Excl Flag` attribute instead of hardcoded code values.

The Data section now uses:


IF( ATTRS( 'Program Code', dProgramCode, 'SSAP Excl Flag' ) @= 'Y' );
    nRecordSkip = nRecordSkip + 1;
    ITEMSKIP;
ENDIF;


I also commented  the hardcoded `ATTRPUTS` statements from the Epilog so exclusions are now controlled directly through the dimension attribute maintenance rather than embedded in TI code.

I validated the change by:

* setting an additional Program Code flag to `Y`
* rerunning the process
* confirming the skipped record count increased automatically
* confirming no code changes were required for the new exclusion

The hierarchy rebuild process (`DIM - Program Code - SSAP`) continues to work correctly using the same attribute-driven logic.

Thank you again for identifying the hardcoded dependency.

Regards,
Niranjan
