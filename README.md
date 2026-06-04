Hi  Sherry, and Lisa,

I wanted to share the findings from my investigation into why users could not see the 2026RF1 version after it was manually inserted, and outline the fix I have prepared before running anything in DEV.

Root Cause

After reviewing every chore and security process in Production, I confirmed the following:

The Dimensions chore runs a process called Security - Master nightly, which handles Center and Location security and calls System - Refresh Security at the end. However, there is no process anywhere in the system that writes security entries to }ElementSecurity_Version for newly added Version elements.

When 2026RF1 was manually inserted into the Version dimension, no security access entry was created for any group. The nightly Security - Master process ran on schedule and correctly refreshed the security cache — but since no access entry existed for 2026RF1, users could not see it. The cache was accurate, it was the missing security entry that was the problem.

Robert's manual intervention fixed it immediately because he wrote the security entry and then ran System - Refresh Security, pushing the update to all users.

Gap Identified

This is not a one-time issue. Every time a new version is manually inserted, this same problem will occur because no automated process exists to grant security on new Version elements. This was never built.

Fix Prepared

I have built a new TI process called Security - Set Version Access in DEV. It does the following:

Loops through the Version dimension and the Groups dimension
Writes READ (or WRITE) access to }ElementSecurity_Version for each group
Calls System - Refresh Security at the end
Supports parameters so it can be run for a specific version or all versions, a specific group or all groups
Once confirmed, the plan is to:

Run the process in DEV and verify it completes successfully
Add it to Security - Master in the Dimensions chore so Version security is handled automatically every night — the same way Center and Location security is handled today
Migrate to Production
Update the version creation runbook: after any manual version insert, run Security - Set Version Access immediately — do not wait for the overnight chore
Question for Robert — Before I Run

Before I run this in DEV, I need your confirmation on one thing. When you fixed the 2026RF1 visibility issue, which groups did you grant access to, and what access level (READ or WRITE)? I want to make sure the new process matches exactly what the correct security setup should look like, so I do not overwrite anything incorrectly.

Once I have your confirmation I will run in DEV, verify the results, and share the output from Process Stats before touching Production.

Please let me know if you have any questions or concerns about this approach.

Thanks,
Niranjan
