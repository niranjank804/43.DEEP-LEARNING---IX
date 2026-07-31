Subject: PBI-4107968 – Extract - Entity Sweep: pRunID_PriorPrior added, unit testing complete

Hi Lisa, Sherry,

Development and unit testing for PBI-4107968 are complete in Dev. Summary below, along with two items that need a decision before I promote.

What changed

Added pRunID_PriorPrior (String, optional) to Extract - Entity Sweep. When a value is supplied, the Prolog writes it to Table_PA_Parameter_Table under the key Alloc_EntitySweep_RunID_Prior_Prior, following the same pattern already used by pRunID_Prior. When left blank, nothing is written and the extract behaves exactly as it did before.

No changes were required to the Metadata, Data, or Epilog sections — the netting logic consumes the value downstream in SQL, so the TI only needs to publish the parameter. This mirrors the approach taken on Extract - Allocation ESS - Master.

Unit testing

All four parameter combinations were run against pSourceDate 202603:

pRunID_Prior	pRunID_PriorPrior	Result
blank	blank	Runs clean, neither key written
blank	M1R1	_RunID_Prior_Prior = M1R1
M2R2	blank	_RunID_Prior = M2R2
M2R3	M1R2	Both keys written correctly
Record count was identical at 7,415 across all four runs, confirming the new parameter has no effect on extract volume or behaviour. The staging table showed 5,883 rows for each of Steps 1–4 under RUN_ID M3R1. (The difference between 7,415 read and 5,883 written is the existing ItemSkip and Sweep Post Entity filters in the Data section — unchanged by this work.)

Two items needing a decision

1. A seed row is required in each environment.

Process Parameter Update SQL Table only updates rows that already exist — it does not insert new keys. Until I manually inserted Alloc_EntitySweep_RunID_Prior_Prior into Table_PA_Parameter_Table, the TI completed successfully but wrote nothing. The same insert will be needed in QA and Production before deployment:

INSERT INTO [FinDM_PA].[dbo].[Table_PA_Parameter_Table] (Parameter, Value, Comments)
VALUES ('Alloc_EntitySweep_RunID_Prior_Prior', '', NULL)
Flagging this specifically because there is no error if it's missed — the process runs green and netting simply never receives the value.

2. A blank parameter retains the previous run's value.

Because the write is conditional, running with pRunID_PriorPrior blank leaves whatever was written by the previous run. In testing I passed it blank and the table retained M1R1 from the run before. If netting should treat blank as "no prior-prior reference," I can add an ELSE branch that clears the value. This matches how Extract - Allocation ESS - Master behaves today, so changing it would affect both processes — happy to follow whichever you prefer.

Open dependency

I haven't been able to identify what invokes Extract - Entity Sweep. It is not called by Allocation - Run Entity Sweep, and I found no calling TI process. If it's triggered by Automic, the job definition will need pRunID_PriorPrior added in order to pass a value through. Could someone confirm how it's scheduled?

Once I have direction on the two items above I'll be ready to promote to QA.

Regards,
Niranjan

