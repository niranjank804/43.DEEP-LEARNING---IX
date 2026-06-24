Hi Lisa / Sherry/Robert,

I have reviewed the Cash and Accrual requirement documents, the PBI acceptance criteria, and the related work items under Feature 3916372. Before finalizing the TI design, I would like to confirm a few items to ensure the implementation aligns with the intended business process.

Issue 1 - Allocation Method

The business requirement document describes a two-step allocation process where FTE determines the Segment split and Salary (A700001-01, Pay_Code = REGU) determines the allocation within each Segment.

The PBI acceptance criteria describes a single-step allocation process where: 

Allocation % = Payroll Amount / Total Payroll Amount

These appear to be different allocation methods.

Question 1: Should the Cash allocation be built using the FTE → Segment → Salary method from the BRD, or the direct Payroll proportional method described in the PBI? There is a separate PBI that is for building out the initial process of determining if the Allocation is based on Salary or Rate: https://dev.azure.com/AIZ-Global/GT.GFS-Program/_backlogs/backlog/EDM/Backlog%20items?showParents=true&workitem=3957726

Question 2: If Payroll is the intended driver, is "Payroll" equivalent to A700001-01 (Salaries - Regular / REGU), or should a different account be used? Correct, when the driver from the PBI mentioned above is set to Salary, then the Account A700001-01 is the data that would drive that allocation. 

Issue 2 - Payroll Amount Class

The Salary example in the BRD references Amount Class B000, while the PBI refers only to Payroll amounts. 

Question 3: Should the Payroll driver be sourced from Amount Class B000 only, or from another / all Amount Classes? The data that will be sent from the OFSAA datamart is filtered to only include B000 so there wouldn’t be an option for any other Amount Class to pull (likely why I worded the PBI that way)

Issue 3 - Allocation Scope

The PBI references "Total Payroll Amount within allocation scope" but does not define the scope.

Question 4: What defines the denominator for the allocation percentage calculation? There are multiple steps to the Salary calculation. In the first step the denominator would be Total FTE based on Segment. The second step the denominator would be the Total Salary amount from the OFSAA file we will receive. The third step it is a multiplication of the value from step 1 times the value in step 2 based on Segment .  I’ve attached a file from what the user is manually doing so you can see the calculation. The tab ‘GFS Cube’ is the data that will be allocated. The tab ‘Cash Calc’ or ‘Accrual Calc’ show the calculation to come up with the final allocated amount. Its taking the data based on segments (different segments in different pivot tables) for the Entity, Center, and Salary. Also at the top of that tab it has the FTE by Segment for the first step of the calculation. Column labeled ‘% of Salary’ is Step 2 of the calculation. Column labeled ‘H&W per CC’ is Step 3 of the calculation.  
For example:
All Centers across E00330 and E01300 
Segment-based totals
Another grouping

Issue 4 - Period Logic

The BRD references Reporting Period YTD for FTE data, while the PBI states that the Period dimension should be used for processing. These are not necessarily the same thing -using YTD Payroll in the denominator produces a different allocation percentage than using current-month Payroll only.

Question 5: Should the Payroll driver be sourced using Current Month values or Reporting Period YTD values? The Payroll data coming from OFSAA will only be the reporting month we are allocating. The reason the FTE would be YTD is based on existing coding that EPA uses for what are deemed EOP accounts. FTE and Head Count are not $ values in OFSAA, however are a Qty value. The data is booked at the end of each month with each Center booking their FTE/HC data into that month with a reversal at the end of the month. Because of this the data is net for the month and if we don’t do YTD we are not getting the correct value. 

Example – this is data from Josh’s Cost Center. There were 6 people that reported to him as of January 2026, then because that remained the same through the year when you see the Monthly Balance it has 0 as its removing the 6 people each month and booking the 6 people back in so it nets to 0 people change. However if you look at the data in a YTD view it shows 6 people throughout the year so far. June 2026 has 0 as it has the -6 booked from the May reversal and the June data gets booked around the end of the month so would show 6 after that Journal is posted to OFSAA and the data hits EPA. 
 

Issue 5 - Target Cube and Shared Components

Your email indicated that Cash and Accrual share common components.

Question 6: What is the target cube for Cash allocation results? Is it the same target cube used by the Accrual process or a separate cube? They can both allocate to the same cube and I think we need a team discussion on if we think an existing allocation cube would work or a new one should be built. I will mark my calendar to bring this up if there is time on tomorrows morning call. The cube would need to have an Amount Class dimension and the Cash would go to B000 and Accrual would go to B100. 

Question 7: Should the Cash process reuse the existing Accrual helper cube, source views, and supporting attributes / hierarchies (GrpIns Excl Flag, GrpIns Incl Flag, Segment, etc.)? Yes

Issue 6 - ZeroOut Logic and Process Sequencing

The PBI notes that Cash allocation logic should govern when overlap with Accrual exists.

Question 8: What is the intended run sequence between the Cash and Accrual processes in Automic? This is another item that can be determined as a team. I believe we could run them together and it would create 2 separate JE/Control files for posting to OFSAA. However I think it would need the option to be able to be run separately in the event there was an issue with one or the other any only one needed to be run. 

Should the Cash process ZeroOut existing allocation results before writing?
If both processes write to the same target cube, how should overlap be handled to avoid duplicate postings or unintended overwrites? @Robert Cobas can you please provide direction on this? 
Issue 7 - Zero Payroll Handling

Question 9: If Total Payroll equals zero for a period, should the process: Payroll will never equal zero for a period, if it does the process should error out with a message to investigate. @Robert Cobas not sure how we handle items like this – I assume that as the Payroll data would be a separate file we run a one-shot to get from OFSAA that upon load of that data if it was empty it should error there before even starting the calculation piece of the allocation. 

Write zero allocations,
Skip allocation processing, or
Route the amount to a default bucket?
If a default bucket should be used, could you please identify the appropriate target?

Minor Document Discrepancy - For Awareness
I noticed a difference between the Cash and Accrual BRDs regarding Center C200591: I believe we are not filtering any Centers out of the OFSAA file we get and a flag would be used to exclude them from the allocation process as needed. This way if they wanted to include that Center later it wouldn’t be a code change for the OFSAA team to adjust the file. Additionally if they wanted to exclude additional Centers in the future we can just adjust the flag on that to make it work. This is the PBI that is creating the attribute for that “flag” https://dev.azure.com/AIZ-Global/GT.GFS-Program/_workitems/edit/3961015 Notes are in section 6 of the requirements. 

Cash BRD: excluded within EPA and not present on the OFSAA file.
Accrual BRD: excluded within EPA but included on the OFSAA file.
Since the center appears to be excluded from allocation processing in both cases, this likely does not affect the design, but one of the documents may need to be updated for consistency.

The allocation design cannot be finalized until questions 1 through 5 are confirmed, as those define the core calculation algorithm. In the meantime, I will review the existing Accrual solution and document any components that can be reused for the Cash build.

Thank you,
Niranjan Patra.

