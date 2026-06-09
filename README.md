Hi Lisa,

Just a quick update to let you know PBI-4008402 is done.

After going through all the chores and security processes, the issue came down to the security group clean-up Sherry did on the cube to cube settings between February and May. The Everyone group was modified during that clean-up, which meant any new Version element added after that point would not automatically get a security entry in }ElementSecurity_Version. Since 2026RF1 was the first version inserted after the clean-up, users could not see it. All versions created before the clean-up were not affected as their entries were already in place.

To fix this I built a new TI process called Security - Set Version Access. It loops through all elements in the Version dimension and writes READ access to }ElementSecurity_Version for the 12 groups Robert used when he manually fixed 2026RF1. I also updated Security - Master to call this new process before System - Refresh Security runs, so the Dimensions chore will handle Version security automatically every night going forward without any manual steps needed.

Both changes have been tested in DEV  and the overnight Dimnensions chore will take care of everything from there.

To verify the fix is working on your end, you can add a new test version element to the Version dimension then run Security - Set Version Access with that version name as the parameter. Log in as a regular business user straight after and check whether the new version is visible in the Version drop down. It should appear immediately without needing to wait for the overnight chore. Once confirmed you can delete the test element and we are good to go. 


For any future version inserts the step is simple — insert the version and run Security - Set Version Access with that version name and users will see it straight away without waiting for the overnight run.



Thanks,
Niranjan
