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



Good Evening!

Can you please look over all the code that was done in the GFS Dev environment as I noticed it wasn’t using the most current version of Production for the starting code. All of the code that was deployed for the Argentina build isn’t in that environment and I wanted to make sure there was no overlap between what you changed and the code related to Argentina. I honestly do not know the best approach to this, perhaps if you need suggestions maybe Robert knows of the best way or can point you to what was moved for the last code change and see if anything you coded was one of those RUX, PRO, etc. 

Thanks,
Lisa 





