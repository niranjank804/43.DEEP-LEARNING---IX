Hi Sherry,
The Dev security issues have been resolved:
General Expense — Leaf-level WRITE access is now working for Planning Analytics in Dev. Root cause was missing Access Processed = WRITE data in the Center Location Security cube in Dev.
Workforce Planning Input — The 10 WRITE measures are now working correctly. Root cause was employee 1492775 classified under ALL EMPLOYEES Orphans in the Dev Employee dimension instead of Existing. Resolved by running DIM - Employee_WD to sync the hierarchy with Production.
Could you please re-run your Security Enhancement testing to confirm Dev now matches Production at leaf level for both cubes?
Thank you,
Niranjan
