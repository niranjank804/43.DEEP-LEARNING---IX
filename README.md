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



o: Lisa Nordling
Cc: Robert Cobas
Subject: RE: Code Review
Hi Lisa,
Thank you for flagging this.
For PBI 4007614, I created two brand new TI processes in GFS Dev:

Exchange Rates – Load OFSAA Data — loads exchange rates from the OFSAA source table into the Exchange Rates cube
DIM – Exchange Rates – Create View — creates the default view for the Exchange Rates cube

The implementation introduced new TI processes only and did not modify existing rules, dimensions, or existing TI process logic.
One potential area worth validating is whether the Argentina deployment included any Exchange Rates cube logic, processes, or reporting dependencies related to ARS currency handling, as ARS rates are included in the OFSAA source and were loaded during Dev testing.
Hi Robert,
Looping you in as Lisa suggested. Could you please help with the following:

Can you share the list of objects (TI processes, rules, dimensions, or cube changes) that were moved to Production as part of the Argentina build?
Specifically, if any of those objects include RUX, PRO, or anything related to the Exchange Rates cube or ARS currency handling, I want to cross-check against what I built for PBI 4007614 to confirm there is no conflict before QA deployment.

Happy to jump on a quick call if that is easier.
Lisa, I will revert to you once Robert confirms the object list.
Thanks,
Niranjan Patra
Senior Engineer





