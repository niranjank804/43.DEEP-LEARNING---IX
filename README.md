As an EPA System Admin, I need to allocate Cash Group Insurance amounts across DIMs based on Payroll amounts, so that cash balances are proportionally distributed in alignment with payroll drivers and financial reporting consistency is maintained.

A new process will:
Source Cash Group Insurance data from the input cube.
Leverage Payroll amounts as the allocation driver at the required grain (ex: Cost Center, Entity, Account, Period DIM).
Calculate proportional allocation using Payroll share:
Allocation % = Payroll Amount / Total Payroll Amount (within allocation scope).
Apply calculated allocation % to Cash Group Insurance amounts.
Write results to the target cube for Cash Allocation results.
Handle zero or missing Payroll scenarios by assigning zero allocation or routing to a default bucket (as defined).
Ensure any overlap logic with Accrual allocation is controlled within this Cash process (Cash logic takes precedence).
Use Period DIM for time-based processing.
Support TI process for repeatable execution and testing.
Dependencies:
Payroll data must be available and validated prior to execution.
Accrual allocation feature may introduce shared components; conflicts resolved within this process.
Acceptance criteria:
Allocation Logic
Given valid Payroll data exists, when the TI runs, then Cash Group Insurance amounts are allocated proportionally based on Payroll amounts.
Allocation calculation matches:
Payroll / Total Payroll within defined scope.
Dimensional Accuracy
Allocation occurs across required DIMs (including Period DIM and Cost Center/Entity as applicable).
Output is written to the correct target cube intersections.
Zero / Missing Payroll Handling
When Payroll amount = 0 or missing, then allocation results in 0 or follows defined default handling.
No division errors occur.
Cash vs Accrual Overlap Handling
When overlap conditions exist between Cash and Accrual logic, the Cash allocation logic governs and applies correctly.
No duplicate or conflicting postings are created.
Data Integrity
Total allocated Cash amount equals the original input total (no loss or over-allocation).
TI Execution
TI runs successfully end-to-end without errors.
Logging or checkpoints exist for troubleshooting.
Testing
Unit testing confirms allocation accuracy across multiple scenarios (normal, zero payroll, partial payroll).
Documentation & Review
Process documentation is created/updated.
Product Lead review completed.

Good day!

Here are the requirements for the Group Insurance Allocation that once the Dev environment is ready for coding again, I would like you to take the lead on. There are parts that are the same and therefore the build for those can be together and parts that are using different data where maybe the .PRO is in one, but it notes whether the data is Cash or Accrual to do the different calculation. 

Please review the attached requirements documents that have been approved by the business for information about the two items. Additionally, I created Features for each with PBIs started for the Cash Allocation. https://dev.azure.com/AIZ-Global/GT.GFS-Program/_workitems/edit/3916372/ When you open this, you can see all the PBIs in the Related Work section:  

If you have questions on the process/build, please reach out. 

Thanks,
Lisa  


