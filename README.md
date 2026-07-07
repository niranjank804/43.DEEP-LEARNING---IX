Hi Lisa,

Thank you for the clarification. I have updated the existing DIM - Entity - Build Allocation Hierarchy process rather than using a separate process.

The GrpIns Entities hierarchy is now built dynamically based on the GrpIns Incl Flag attribute. I verified the following:

Entities with GrpIns Incl Flag = Y are automatically added to GrpIns Entities when the process runs
Clearing the flag removes the entity from the hierarchy on the next process run
The hierarchy is built as part of the existing allocation hierarchy process
Existing allocation hierarchies continue to function as expected
The DEV implementation is complete and ready for your review.

Thanks,
Niranjan
