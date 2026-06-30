PBI 3961016: Group Ins Alloc - New Entity attribute and hierarchy
As an EPA System Admin, I need to enhance the Entity DIM by adding an inclusion flag and building a related hierarchy so that only the appropriate Entities are included for FTE and Salary data processing and reporting.

This includes:
New Entity attribute to flag the Entities that are included for the FTE and Salary data (GrpIns Incl Flag)
Build hierarchy based on flagged items on Entity attribute GrpIns Incl Flag (GrpIns Entities)
Acceptance criteria:
A new attribute GrpIns Incl Flag exists on the Entity DIM
The GrpIns Incl Flag identifies Entities included for FTE and Salary data
A GrpIns Entities hierarchy exists based on Entities where GrpIns Incl Flag is set
The hierarchy includes only flagged Entities
Hierarchy updates correctly when attribute values change
No impact to existing Entity hierarchies or EPA processes
