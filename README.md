Hi Lisa,

Here is a quick update on **PBI 4066762 – Group Insurance Allocation**.

I have completed the design documentation for the Group Insurance Allocation solution. The work completed included:

* Reviewing the October 2025 manual Cash and Accrual workbooks and documenting the allocation methodology using the same three-step calculation (FTE by Segment followed by Salary by Centre).
* Confirming all required source data:

  * Group Insurance source from Actual Allocation (E01300 / C200593 / A700018-00)
  * FTE from Financial Summary (A900001-01, Actual, YTD)
  * Salary from Actual Allocation (A700001-01, B000)
* Confirming the target cube is Actual Allocation (confirmed by Sherry).
* Designing the complete TI solution, including:

  * Existing Load Actuals process (no changes required)
  * Zero Out Group Insurance process
  * Run Group Insurance Allocation process
  * Export Group Insurance to GFS process
* Documenting the allocation methodology, TI processing flow, cube usage, dimension requirements, process architecture, dependencies, design decisions, and acceptance criteria mapping.

All acceptance criteria for the design phase have been addressed, and the design document is ready for review.

From a design perspective, **PBI 4066762 can be considered complete for this sprint**. Development will proceed in a future sprint once the following dependencies are available:

* PBI 3961015 – Centre Dimension enhancements
* PBI 3957726 – Route Control implementation
* OFSAA Salary file (A700001-01, B000)

Could you please let me know the expected delivery date for the OFSAA Salary file? This will help us plan the development and UAT activities.

Thanks,

Niranjan
