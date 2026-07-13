Hi Lisa,

PBI 3961015 has been completed in the Dev environment.

As requested, I merged the GrpIns Centers build into the existing DIM - Center - Build Allocation Hierarchy process instead of maintaining it as a separate TI process. I also updated the GrpIns Excl Flag to use 'Y' for excluded centers.

I completed testing in Dev and verified the following:

GrpIns Centers is built successfully under Center Alloc Hiers.
Centers C200591 and C200593 (GrpIns Excl Flag = 'Y') are correctly excluded from the GrpIns Centers hierarchy.
The hierarchy rebuild completes successfully and can be rerun without issues.

With these changes, the acceptance criteria for PBI 3961015 have been completed.

Please let me know if you would like to review the implementation or if any additional changes are required before UAT.

Thanks,

Niranjan
