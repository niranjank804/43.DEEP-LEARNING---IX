Subject: Design Clarifications Needed – Group Insurance Cash Allocation (Feature 3916372)

Hi Lisa / Sherry,

I have reviewed the Cash and Accrual requirement documents, the PBI acceptance criteria, and the related work items under Feature 3916372. Before finalizing the TI design, I would like to confirm a few items to ensure the implementation aligns with the intended business process.

Issue 1 – Allocation Method

The business requirement document describes a two-step allocation process where FTE determines the Segment split and Salary (A700001-01, Pay_Code = REGU) determines the allocation within each Segment.

The PBI acceptance criteria describes a single-step allocation process where:

Allocation % = Payroll Amount / Total Payroll Amount

These appear to be different allocation methods.

Question 1: Should the Cash allocation be built using the FTE → Segment → Salary method from the BRD, or the direct Payroll proportional method described in the PBI?

Question 2: If Payroll is the intended driver, is "Payroll" equivalent to A700001-01 (Salaries – Regular / REGU), or should a different account be used?

Issue 2 – Payroll Amount Class

The Salary example in the BRD references Amount Class B000, while the PBI refers only to Payroll amounts.

Question 3: Should the Payroll driver be sourced from Amount Class B000 only, or from another / all Amount Classes?

Issue 3 – Allocation Scope

The PBI references "Total Payroll Amount within allocation scope" but does not define the scope.

Question 4: What defines the denominator for the allocation percentage calculation? For example:

All Centers across E00330 and E01300
Segment-based totals
Another grouping
Issue 4 – Period Logic

The BRD references Reporting Period YTD for FTE data, while the PBI states that the Period dimension should be used for processing. These are not necessarily the same thing — using YTD Payroll in the denominator produces a different allocation percentage than using current-month Payroll only.

Question 5: Should the Payroll driver be sourced using Current Month values or Reporting Period YTD values?

Issue 5 – Target Cube and Shared Components

Your email indicated that Cash and Accrual share common components.

Question 6: What is the target cube for Cash allocation results? Is it the same target cube used by the Accrual process or a separate cube?

Question 7: Should the Cash process reuse the existing Accrual helper cube, source views, and supporting attributes / hierarchies (GrpIns Excl Flag, GrpIns Incl Flag, Segment, etc.)?

Issue 6 – ZeroOut Logic and Process Sequencing

The PBI notes that Cash allocation logic should govern when overlap with Accrual exists.

Question 8: What is the intended run sequence between the Cash and Accrual processes in Automic?

Additionally:

Should the Cash process ZeroOut existing allocation results before writing?
If both processes write to the same target cube, how should overlap be handled to avoid duplicate postings or unintended overwrites?
Issue 7 – Zero Payroll Handling

Question 9: If Total Payroll equals zero for a period, should the process:

Write zero allocations,
Skip allocation processing, or
Route the amount to a default bucket?
If a default bucket should be used, could you please identify the appropriate target?

Minor Document Discrepancy – For Awareness

I noticed a difference between the Cash and Accrual BRDs regarding Center C200591:

Cash BRD: excluded within EPA and not present on the OFSAA file.
Accrual BRD: excluded within EPA but included on the OFSAA file.
Since the center appears to be excluded from allocation processing in both cases, this likely does not affect the design, but one of the documents may need to be updated for consistency.

The allocation design cannot be finalized until questions 1 through 5 are confirmed, as those define the core calculation algorithm. In the meantime, I will review the existing Accrual solution and document any components that can be reused for the Cash build.

Thank you,
Niranjan
