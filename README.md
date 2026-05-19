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




Hi Kamil,
Thanks for the thorough response — that is very clear and helpful.
We understand and accept IBM's assessment. The memory spike being a scaling limitation of the current approach rather than a misconfiguration is useful to have on record, and we have shared that context internally.
We will look at scheduling the security chore during off-peak hours as the primary mitigation for now. Any longer-term redesign of the processing approach will be scoped separately as a project.
A couple of quick follow-up questions before we close this out:

Is there any IBM documentation or best practice guide on handling large-scale security propagation in TM1 that you could point us to? Even if code redesign is out of support scope, it would be useful reference material.
Would you recommend keeping this case open while we monitor the scheduled runs, or is it better to close and raise a new case if we see unexpected behaviour?

Thanks again for your time on this.
Regards,
Niranjan Patra
EPA System Administration
