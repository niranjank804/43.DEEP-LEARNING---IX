Subject: Memory Optimisation Achieved — 45 GB Reduction | Security - Set Center Location Access | Assurant GFS Dev
Hi Robert,
I wanted to share a significant performance win on the Assurant GFS Dev environment that I completed today.
Issue Identified
While reviewing the Dimensions Chore, I identified that the Security Master process — specifically the child process 'Security - Set Center Location Access' — was causing a critical memory spike of approximately 50 GB on every execution. This was a major risk to server stability and overall environment performance.
Root Cause
After a thorough review of the TurboIntegrator script, I identified three compounding problems:
Subset Build Without Level Filter — The WHILE+DIMNM loops building the Center, Location, and }Clients dimension subsets had no level filter, causing both leaf and consolidated elements to be loaded into the source view. TM1 then expanded all consolidated cell intersections in memory, causing the spike.
Redundant Nested WHILE Loops in Data Tab — The Data tab contained a nested WHILE loop construct that re-iterated the entire Center × Location dimension for every single record processed, resulting in millions of unnecessary CELLGETS and CELLPUTS calls held in memory simultaneously.
No ZeroOut View Optimisation — The VIEWZEROOUT call had no leaf-level filter on Center and Location dimensions, causing TM1 to expand all consolidated cell intersections in memory before zeroing them out.
Optimisations Applied
Added ELLEV()=0 leaf-level filter to all dimension subset builds (Center, Location, }Clients) — pure TI function, no MDX required
Added leaf-only Center and Location subsets to the ZeroOut view to prevent VIEWZEROOUT from expanding consolidated cells in memory
Removed the entire nested WHILE Idx1/Idx2 loop from the Data tab and replaced with a single direct CELLGETS + CELLPUTS write per record
Added explicit VIEWDESTROY and SUBSETDESTROY calls in the Epilog for all temporary views and subsets to prevent orphaned server objects on error exits
Result
Memory consumption reduced from ~50 GB → ~1–3 GB — a saving of approximately 45 GB per execution. The purpose and output of the process remain completely unchanged.
Please let me know if you would like me to walk you through the changes or if this needs to go through a CHG ticket before being applied to higher environments.
Best regards,
Niranjan

