pbi3957726: Group Ins Alloc - Route Control (Salary vs Rate setting)
As an EPA System Admin, I need a TI process that reads a calculation route setting stored on Entity E01300 / Center C200593 (value = Salary or Rate) so the allocation logic runs the correct calculation path without code changes.

New cube? where would the setting be stored? New measure? 

Acceptance criteria:
The TI process reads the setting at Entity = E01300 and Center = C200593 for the selected Period .
Valid values are Salary and Rate.
If the value is missing/invalid, the process defaults to Salary (current manual method) and writes a log message.
The chosen route is written to the TI log for traceability.

