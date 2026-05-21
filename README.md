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
Thank you — the documentation links are helpful and we will use them as reference.
We are satisfied with the outcome of this case. To summarise our understanding for the record:

The memory spike (~475 GB) during the security TI process is expected behaviour at our current scale and is not a misconfiguration
No server-level parameter will significantly reduce peak memory for ELISANC-heavy workloads
Any further reduction in memory footprint would require a redesign of the processing approach, which is outside standard support scope

We will proceed with scheduling the security chore during off-peak hours as our mitigation. If we observe any unexpected behaviour after monitoring the scheduled runs, we will raise a new case.
You can go ahead and close this case. Thanks again for your help throughout.
Regards,
Niranjan Patra
EPA System Administration
