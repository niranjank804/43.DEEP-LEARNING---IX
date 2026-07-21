Hi Lisa,

I have added the Source and Target date parameters to the SCN WFP Assumptions - Copy SCN process, following the same pattern used in the other SCN Copy Scenario processes (e.g., SCN Earned Premium - Copy Scenario).

Changes made

Added four new parameters: pSourceStart, pSourceEnd, pTargetStart, pTargetEnd (format YYYYMM), matching the prompts and structure used across the existing SCN Copy Scenario processes.
Added the year-shift logic so that when Target and Source date ranges differ, Period values are correctly mapped to the target year, consistent with the existing SCN Copy Scenario processes.
Updated the Zero Out view and Source view to scope the Period dimension using the Source/Target date ranges instead of the full Period dimension.
Updated the SCN Workforce Planning - Copy Scenarios - Master process to pass all six parameters through to this process.
Testing completed

Ran the process with pSourceScenario: Base, pTargetScenario: 1, and matching Source/Target date ranges (202301–202312) — process completed successfully with no errors.
Confirmed the process runs cleanly with the new parameters and that the year-shift logic does not introduce any errors.
