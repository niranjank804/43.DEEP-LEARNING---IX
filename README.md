Subject: PBI 4034136 – Argentina SCN WFP Load – Confirmation needed before I make any changes

Hi [Name],

Before I touch the SCN Workforce Planning Summary – Copy Version process for PBI 4034136, I want to confirm a few points so I build it right the first time and don't rework anything later. Could you please confirm the following:

1. Definition of "Current Year" (most important)
The PBI asks the load to start from the Current Year instead of Last Actual Period. I can derive "Current Year" two ways:

Server calendar year – simplest, no dependencies.
Planning year maintained by Finance – read from a control object so Finance owns the rollover.
This matters at year-end: if Workforce Planning starts budgeting FY2027 while the server clock still reads December 2026, the server-year option would not load 2027 until the calendar turns. Which definition should I use?

2. Calendar vs. fiscal year
Period leaf members are YYYYMM (calendar). Is the planning "year" the calendar year, or do we run a non-January fiscal year?

3. Scope
Is this change for the Argentina SCN process only, or should the same Current-Year logic apply to other locations / WFP load processes as well?

4. Last Actual Period
After this change the process will no longer read "Last Actual Period" from Global Variables. Is that value still used by other processes (leave it as-is), or is it being fully retired?

5. Forward boundary
Confirm we load Current Year and everything forward, including all forecast/future periods, with no upper cut-off.
