PA Cloud Memory Utilization Consistently at 90% + sizing Consultation Request

Solution description
Hello Niranjan,



Thank you for your patience. I would like to provide an update on this case.



Requests related to environment sizing, memory management, and performance optimization fall outside the scope of IBM Support and are handled by consulting services.



Next Steps

We recommend the following:

Contact your IBM sales represen...

Show more

Case number and description
Case number

TS022372520

We are reaching out to IBM Support for a consultation and recommendation regarding persistent high memory utilization on our IBM Planning Analytics Cloud instance at Assurant.
Our PA Cloud instance (512 GB RAM) has been consistently running at 90%+ memory utilization during normal business operations. We have conducted internal investigation and research into this issue and would like IBM's expert guidance.
Research conducted to date:

 1.Monitored server memory via }StatsForServer and }StatsByCube system cubes
 2. Identified high garbage collection ratios indicating memory pressure
 3.Traced a significant memory spike (~50 GB) to a TI process with a nested WHILE loop generating excessive  	iterations .this has since been resolved
 4.Reviewed PA Cloud memory threshold alert behavior and documented recurring breach patterns

Reference: Please link this case to any previous support cases opened by Assurant related to memory management on this instance.  TS022197433
https://urldefense.proofpoint.com/v2/url?u=https-3A__www.ibm.com_mysupport_500gJ00000C30scQAB&d=DwMCaQ&c=rRy1VLRfG3iq78wJmVOlVGvgLG8qPGddnoapq3qqxOA&r=_4XAOjUhOlitCtk_WN4YOymdPdflqYxttYRuGR0FKB4&m=Lxbu1fyGugzgLolobjiqv7odCHoCjRsZPYsiQ94viS_1N0Zhf7pEu_yuym00pm1q&s=UzvO-m_LTB41D4vQruoyTLKlI0Za71NzlwTCG38Lv_Q&e=

Questions for IBM:

 1.Given our instance size and workload profile, what memory configuration does IBM recommend for a PA Cloud SaaS   	environment of our scale?
 2. If we decide to increase memory, what are the available upgrade tiers (e.g., 768 GB, 1 TB) and what would IBM consider 	optimal for sustained sub-80% utilization?
 3.Are there any IBM-recommended best practices or configuration changes for reducing baseline memory consumption on PA 	Cloud SaaS that we may not currently have in place?
 4.Can IBM review our current memory utilization patterns and provide a formal sizing assessment?

Requested Action:
A consultation call or written recommendation from IBM on optimal memory sizing for our environment.
Case history
Characters remaining: 10000
Search case history
Expand messages
Ascending order
Filters

Start date
mm/dd/yyyy
End date
mm/dd/yyyy
MG
mgarmendiam (IBM) changed status from Awaiting your feedback to Closed by IBM
Jun 12, 2026, 12:52
MG
mgarmendiam (IBM)
Jun 12, 2026, 12:52
Thank you for working with us. This case has now been closed due to inactivity. I hope that the suggestions made were helpful, and that the issue reported is no longer occurring. If you determine this issue needs additional attention, you can reopen this case within the next 30 days.



If you receive a survey from IBM after this case closes, please take a few minutes to respond. Your candid feedback is extremely valuable as we strive to deliver the best technical support possible and exceed your expectations.



Try our virtual assistant! Get quick responses to questions and receive technical guidance by clicking the blue icon in the lower right corner of the IBM Support Site. Log in for the best experience! https://www.ibm.com/mysupport



Thank you for using IBM


Show less
IBM Support (IBM)
IBM Support (IBM)
Jun 12, 2026, 12:51
Hello, this is an automated IBM Support message to remind you that this case is awaiting your action. Your response is necessary for analysis to proceed or to confirm resolution of this case. Please update the case at your earliest convenience with the previously requested information, give an update on when that information will likely be provided, or clarify the action plan with IBM Support.

If no further action on the case is needed, please close the case. If we do not hear back from you, the case will automatically close in 6 business days.

We value your business, thank you for allowing IBM to assist you.


Show less
Jun 10, 2026
MG
mgarmendiam (IBM)
Jun 10, 2026, 16:08
Hello Niranjan,



Thank you for your patience. I would like to provide an update on this case.



Requests related to environment sizing, memory management, and performance optimization fall outside the scope of IBM Support and are handled by consulting services.



Next Steps

We recommend the following:

Contact your IBM sales representative or partner
Engage consulting services (e.g., Expert Labs) for a detailed assessment
Note: IBM Cloud Support is not able to assign or coordinate consulting resources.



On the other hand, for the issue you are experiencing with Memory Utilization we have these Recommendations & Observations:



Data Volume

Evaluate whether it is necessary to retain extensive historical data (e.g., 8 years) in the cube
Moving older data out of the cube may help reduce memory usage
Memory Analysis

Enable and review }StatsByCube (statistics cube)
This will help identify which cubes or activities are consuming the most memory
Reference: }StatsStatsByCube - IBM Documentation

}StatsStatsByCube - IBM Documentation



Suggested Troubleshooting Approach

If you would like to further analyze memory usage:

1. Enable Performance Monitoring

In PA Workspace: right-click the server → Start Performance Monitor
2. Review Control Cubes

}StatsForServer (overall memory usage)
}StatsByCubes (memory by cube/activity)
3. Establish a Baseline

Restart the server
Capture memory usage immediately after startup
Monitor metrics over time (e.g., 30 min, 1 hour, peak periods)
4. Identify High Consumption Areas

Review workloads such as:
Views
TI processes
Rule calculations
This analysis requires collaboration from your side to monitor usage and identify workload patterns



Finally, I want to clarify and confirm that Restarting the PA Agent will not resolve memory-related issues and can be disregarded for this scenario. It is better to follow the recommendations mentioned above.



Please let us know if you have any questions or once you have engaged the appropriate team. 



Thanks for your attention. 



Kind regards,



Madeleine


Show less
MG
mgarmendiam (IBM)
Jun 10, 2026, 14:22
Hello Niranjan,



Thank you for your patience while I continue to work on this case for you. 



To better understand if this is expected in-memory behavior or sustained memory pressure, I would like to gather more information:



Questions:



At the time of the memory spike, were any TI processes or batch loads running?
Are there scheduled jobs or data loads aligned with this timeframe?
Is the workload currently processing a higher volume of source data compared to before?
Have there been any recent changes such as data growth, hierarchy expansion, or version upgrades?
Have there been changes to dimensions or the introduction of multiple hierarchies?
Has this workload or process performed better in the past?
If so, do you recall what has changed since then?
Do you recall any large view execution, dashboard usage, or rule recalculation during this period?
What is the database name involved and what is the name of the process? If you can share the process that would be helpful.
Based on initial analysis of the logs and previous case information, the memory spike pattern appears to be workload-driven rather than related to a product defect. I would like to ask internally to my team about the memory increment, and I will follow up with more information on this.



Specifically, we are seeing indicators in logging that suggest large MDX view constructions involving high-cardinality dimensions (for example, time-based YTD/QTD expansions) and security cubes these are very large, consume lots amount of memory. These types of queries can significantly increase memory consumption during execution. So maybe it is good idea to request the memory increment, this is something I need to research more, please allow me more time to reply on this.



As a next step, we can perform a PA Agent restart to reset memory utilization and observe how quickly it builds up again. This helps determine whether the behavior is driven by workload patterns or sustained memory pressure. This will not correct the issue but helps temporarily to normalize the spike, if you'd like I can proceed to restart it. 



If you agree, after the restart, please monitor memory usage and share observations at:



~30 minutes
~1 hour
during peak activity
Please let us know a suitable time for the restart to avoid user disruption.



Kind regards,

Madeleine


Show less
MG
mgarmendiam (IBM) changed status from IBM is working to Awaiting your feedback
Jun 10, 2026, 14:22
Jun 10, 2026
MG
mgarmendiam (IBM)
Jun 10, 2026, 12:47
Hello Niranjan,



This is Madeleine from Planning Analytics on Cloud. I am glad to help you today.



I am checking your case, please allow me few minutes to read the information and get back to you. 



Madeleine



MG
mgarmendiam (IBM) changed status from New Case to IBM is working
Jun 10, 2026, 12:47
NP
Niranjan Patra (Customer)
Jun 10, 2026, 12:11
Created case: TS022372520

Hi Robert,

Following up with answers to the questions on the PA Agent restart:

1. Timing: Sunday daytime works well as an off-peak window. I can propose Sunday 12:00 PM EST to IBM unless you'd prefer a different slot.

2. Type of restart: This is different from a database restart from the PAW Admin screen (which we can do ourselves). The PA Agent is a separate backend component (IBM Planning Analytics Administration Agent) that, per IBM's documentation, runs as a host-level Windows service and is restarted via a script requiring OS administrator rights on the server. Since IBM manages the host OS for our PA Cloud environment and we don't have OS-level access, this isn't something we can trigger ourselves — it would need to be done by IBM.

Reference (agent restart mechanism):
- https://www.ibm.com/docs/en/planning-analytics/2.0.0?topic=components-planning-analytics-administration-agent-local-only
- https://www.ibm.com/docs/en/planning-analytics/2.0.0?topic=icpaaalo-configure-agent-windows

For comparison, database-level start/stop/restart IS self-service on Cloud via PAW Administration > Databases:
https://www.ibm.com/docs/en/planning-analytics/2.0.0?topic=databases-start-stop

3. One-time or recurring: This is a one-time diagnostic action. IBM wants to reset memory usage and observe how it rebuilds (checking at ~30 mins, ~1 hour, and during peak activity) to help determine whether the spike is workload driven. It is not a recurring fix.

If this looks good, I'll confirm Sunday 12:00 PM EST with IBM as the proposed window for the agent restart.

Thanks,
Niranjan Patra.

Hi Lisa,

I wanted to give you a quick update on the IBM Support case we raised regarding the PA Cloud memory utilization issue (consistently running at 90%+).

IBM has responded with two messages:

1. For the memory sizing/consulting question- IBM Support has indicated that environment sizing recommendations fall outside their support scope and would need to go through IBM Sales or Expert Labs. I will flag this to Robert so he can engage the IBM account team accordingly.

2. On the memory spike investigation- IBM's engineer (Madeleine) has done an initial analysis and identified that large MDX view constructions involving high-cardinality dimensions (YTD/QTD expressions and security cubes) appear to be the primary driver of memory consumption. This aligns with our internal investigation findings.

IBM has asked 8 diagnostic questions and has also proposed a PA Agent restart to observe how quickly memory rebuilds, which will help determine whether the behavior is workload-driven or a sustained pressure issue.

Importantly, IBM has also hinted that a memory increment for our PA Cloud instance may be possible- I have asked them to confirm available options, which directly addresses our sizing question.

Next Steps:
- I will respond to IBM's diagnostic questions once I have your and Robert's inputs
- The PA Agent restart window is currently being coordinated with Robert- I will confirm the scheduled date/time and update you once agreed
- Robert to engage IBM Sales/Account team for the formal sizing consultation
- I will keep you updated as IBM responds

Please let me know if you have any questions or if there is anything specific you would like me to raise with IBM.

Best regards,
Niranjan Patra
