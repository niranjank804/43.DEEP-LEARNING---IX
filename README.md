Subject: Updated Design Document – PBI 4066762 Group Insurance Allocation

Hi Sherry,

Thank you for taking the time to review the design document and for your valuable feedback.

I've updated the design document to incorporate your suggestions.

The key changes are:

ZeroOut Process: The standalone ZeroOut process has been merged into the PROLOG of the Run Group Insurance Allocation process. Since Automic always executes the zero-out and allocation together for the same period, this simplifies the workflow while still ensuring the process remains safe to re-run.
Helper Cube: I have included a design decision stating that the use of a Helper Cube will be evaluated during the coding phase. We'll first assess whether the existing Expense Helper Cube can accommodate the Salary and FTE validation data. If not, we'll consider creating a dedicated Helper Cube. Since the allocation can currently be performed directly from the source cubes, I've treated this as a follow-on enhancement rather than part of the current design scope.

I've also updated the process architecture, logic summary, acceptance criteria, and design decisions to reflect these changes.

Please find the updated design document attached for your review. Let me know if you have any additional comments or if you'd like me to make any further changes before we proceed to the coding phase.

Thanks,
Niranjan Patra
