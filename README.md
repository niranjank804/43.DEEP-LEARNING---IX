Subject: PBI 4091546 – Development Completed

Hi Lisa,

Development for PBI 4091546 (Add US Loc Flag to }ElementAttributes_Location) is complete and tested in Dev.

Summary:

Added a new US Loc Flag string attribute to the Location dimension.
Updated the DIM - Location process to populate it automatically during the nightly dimension load.
US locations (under 1:USA MGR) are set to Y; non-US locations remain blank.
Verified existing attributes (Enabled, ID-Name, CAD Location, etc.) are unaffected.
Testing in Dev confirms the results match the acceptance criteria. Let me know if you'd like any additional validation before this moves to QA.

Thanks,
Niranjan
