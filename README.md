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
Thanks for the clarification — that makes sense.
To answer your question: yes, the process did perform better in the past. The slowdown has been gradual and ties back to a few things that have changed over time.
The Center dimension has grown quite a bit and is now sitting at around 26,000 elements, up from a much smaller count when the process was first built. Location is around 550. On top of that, both dimensions are multi-hierarchy — leaf elements can sit under 7 or more rollup parents across different hierarchy trees, which makes ELISANC heavier than it would be in a simpler structure. We are also running 252 user groups through the process now, and each one drives roughly 14.3 million ELISANC evaluations (26,000 × 550). No TM1 version upgrades have happened around the same time, so this is purely a growth and complexity issue.
To be clear on what we need from IBM — we are not asking for TI code changes. Our questions are:

Are there server-level configuration parameters that can reduce memory pressure during ELISANC-heavy TI runs?
Does IBM have any guidance on memory behaviour for ELISANC at this scale — 26,000+ elements, multi-hierarchy?
Is a ~475 GB memory spike during this process expected given the scale, or does it point to a configuration gap?

Thanks,
Niranjan Patra
EPA System Administration
