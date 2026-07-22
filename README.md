Hi Lisa,

Following up on the data movement issue you reported — I found the root cause and have now validated the fix.

Root cause

The Period dimension uses annual assumption buckets named "Input 2026", "Input 2027", etc., rather than plain YYYYMM values. My original code was building the Period subset using raw year numbers (e.g., "2026") and filtering elements against the YYYYMM parameter range as a string comparison. Neither matched the actual "Input YYYY" element names, so the Period subset came back empty regardless of the date range entered — that's why no data moved when you ran it.

Fix applied

Updated the Zero Out view and Source view Period subset logic to build element names using the "Input " prefix (e.g., "Input 2026") to match the actual dimension structure.
Updated the target period construction in the Data tab to apply the same "Input " prefix when writing to the target scenario.
Testing completed

Entered a test value (Merit Increase = 12) under Scenario: Base, Version: Forecast, Period: Input 2026 for a specific leaf Center/Location/Employee.
Ran the process with Source Scenario: Base, Target Scenario: 1, Source/Target dates 202601–202612 (no year shift) — Record Count: 1, Processed: 1. Confirmed the value copied correctly to Scenario 1, Input 2026.
Re-ran with Target dates 202701–202712 (year shift) — Record Count: 1, Processed: 1. Confirmed the value correctly landed under Input 2027 in the target scenario, validating the year-shift logic.
Re-confirmed blank parameter, invalid Scenario, and Source = Target validations still pass after the fix.
The process is now fully validated end-to-end and has been added to the SCN Workforce Planning - Copy Scenarios - Master process. I'll move this to Peer Review next unless you'd like to review anything further first.

Thanks again for catching this.

Niranjan Patra

