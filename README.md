Hi Lisa,

I wanted to follow up on my previous email regarding the IBM Support case for our PA Cloud memory utilization issue (TS022372520).

IBM has closed the case due to inactivity while we were still gathering internal inputs. I need to reopen it and respond to their diagnostic questions — your perspective would be helpful before I do.

Specifically, I'd appreciate your input on:

Whether you are aware of any recent changes — data growth, hierarchy expansion, new dimensions, or version upgrades — that coincide with the memory pressure increase
Whether you recall large view executions, dashboard activity, or rule recalculations during peak memory periods
Any context on how the workload today compares to when the environment was performing better
Even a quick reply or a 15-minute call would be enough to move forward. I want to make sure your team's perspective is captured before I respond to IBM.

Please let me know your availability.

Thanks,
Niranjan Patra

2. Reply to IBM Support to reopen case TS022372520
Subject: Request to Reopen Case TS022372520 – PA Cloud Memory Utilization

Hello Madeleine,

Thank you for the detailed recommendations in your June 10 response. I apologize for the delayed reply — we were coordinating internally before responding.

We would like to request that this case be reopened so we can continue the investigation. We are still within the 30-day reopen window.

Responses to your diagnostic questions:

TI processes/batch loads at time of spike — Yes, the identified spike (~50 GB) was traced to a TI process containing a nested WHILE loop generating excessive iterations. This has since been corrected. Baseline memory pressure (90%+) persists outside of that event.
Scheduled jobs/data loads — Yes, overnight batch loads run on weekdays. Memory does not fully release after these complete.
Higher source data volume — Data volume has grown incrementally over the past year, including additional historical years retained in cubes (approximately 8 years of history).
Recent changes — No major version upgrades recently. Some dimension hierarchy additions have been made over the past 6–12 months.
Dimension or hierarchy changes — Yes, some hierarchies have been expanded. No new multiple-hierarchy structures introduced recently.
Has the workload performed better in the past — Yes, memory utilization was more stable 12–18 months ago prior to data growth and hierarchy expansion.
Large view/dashboard/rule activity during spike — Likely yes during business hours; we observe peak memory during periods of active dashboard usage and YTD/QTD view expansions as you noted.
Database and process name — I can provide the specific process name upon request. Please advise how you'd like this shared securely.
PA Agent Restart

We are prepared to proceed with the PA Agent restart for diagnostic purposes. We propose Sunday, June 22 at 12:00 PM EST as an off-peak window. Please confirm if this works on your end and what actions are needed from our side.

Open Question

You mentioned that a memory increment for our PA Cloud instance may be possible — could you confirm available upgrade tiers (e.g., 768 GB, 1 TB) and the process to request this? We understand a formal sizing consultation would go through IBM Sales/Expert Labs, but any guidance you can provide on options would be appreciated.

Thank you for your continued support on this case.

Best regards,
Niranjan Patra
[Your company/title if needed]
Case: TS022372520 | Related: TS022197433
