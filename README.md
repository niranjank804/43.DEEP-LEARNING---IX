Here is information from the previous case with IBM – I believe Robert asked Gangadhar to try and research what was running at the times of day we were getting the critical incidents, but not sure what came of that: 
Newest message on top, oldest on the bottom so might be helpful to start at the bottom and read upward. 

Case number
TS020838735
In the past two weeks our memory usage has increased. I've tried restarting or production databases but memory usage increases to around 80% with a day. This is the first time I've seen memory usage increase like this. Any ideas on how to keep the memory usage down and why the sudden increase?

•	
o	Laura.Rakhimberdina (IBM)
Jan 22, 2026, 13:51
Thank you for working with us. I hope you were satisfied with the handling of this case. If you have any further questions, you can reopen this case any time in the next 30 days. If you need assistance on another issue, please open a new case with IBM.

If you receive a survey from IBM after this case closes, please take a few minutes to respond. Your candid feedback is extremely valuable as we strive to deliver the best technical support possible and exceed your expectations.

Try our virtual assistant! Get quick responses to questions and receive technical guidance by clicking the blue icon in the lower right corner of the IBM Support Site. Log in for the best experience! https://www.ibm.com/mysupport

Thank you for using IBM
Show less
o	GA
gangadhar.akambram (Customer) changed status from Waiting for IBM to Closed by Client
Jan 22, 2026, 09:51
o	GA
gangadhar.akambram (Customer)
Jan 22, 2026, 09:51
Hi Laura,

We have reviewed the situation, and we are continuing the investigation on our side. At this time, no further assistance is required from IBM. We will open a new support case if additional help is needed.

Thank you for your support.

Kind regards,
Gangadhar.

o	GA
gangadhar.akambram (Customer) changed status from Awaiting your feedback to Waiting for IBM
Jan 22, 2026, 09:50
•	Jan 19, 2026
o	LA
Laura.Rakhimberdina (IBM)
Jan 19, 2026, 02:30
Hello Gangadhar,

Thank you for the update. 
The memory increase after the chore run is noted. Please keep us informed of any new findings.

Kind regards,
Laura
o	LA
Laura.Rakhimberdina (IBM) changed status from Waiting for IBM to Awaiting your feedback
Jan 19, 2026, 02:30
o	GA
gangadhar.akambram (Customer)
Jan 19, 2026, 02:18
Hello Laura,

We are continuing to investigate the issue. Please find the latest observations below:
•	The database has been restarted multiple times. After each restart, memory usage initially decreases but gradually increases again.
•	TM1 server logs have been reviewed, and no errors were identified.
•	We have observed that memory usage rises significantly after a specific chore runs.
•	The root cause of the memory spike has not yet been determined.
•	We are actively analyzing running processes and monitoring memory behavior to isolate the issue.
•	Garbage memory usage has exceeded 100 GB across three environments.
The investigation remains ongoing, and we will share further updates.
Thank you for your understanding.

Kind regards,
Gangadhar
Show less
o	GA
gangadhar.akambram (Customer) changed status from Awaiting your feedback to Waiting for IBM
Jan 19, 2026, 02:18
•	Jan 13, 2026
o	LA
Laura.Rakhimberdina (IBM)
Jan 13, 2026, 02:08
Hello Gangadhar,

Could you please provide an update on the progress of the investigation on your side?
Thank you!

Kind regards,
Laura
•	Dec 23, 2025
o	LA
Laura.Rakhimberdina (IBM)
Dec 23, 2025, 14:52
Hello Gangadhar,

Thank you for reply! We will keep this case open and waiting for updates.

Kind regards,
Laura
o	LA
Laura.Rakhimberdina (IBM) changed status from Waiting for IBM to Awaiting your feedback
Dec 23, 2025, 14:52
o	GA
gangadhar.akambram (Customer)
Dec 23, 2025, 11:49
Hello Laura,

I have reviewed the previous updates and discussed this internally with Robert. I am Currently analyzing few processes and reviewing StatsByCube and StatsByProcess where high memory usage was observed.

The investigation is still in progress. I will share further updates.
Kindly keep the case open.

Thanks for understanding,
Gangadhar.
Show less
o	GA
gangadhar.akambram (Customer) changed status from Awaiting your feedback to Waiting for IBM
Dec 23, 2025, 11:49
o	 
Enhanced Customer Data Repository (ECuRep)
Dec 23, 2025, 06:07
Automated response from: Robert Cobas <robert.cobas@assurant.com>
________________________________________

I will be out of office starting on 12/22 and returning on 1/5 . For EDM or EPA issues or questions please email EDM Scrum Team edm.scrum.team@assurant.com. For escalations please contact Josh Yopp.
_____________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________

This e-mail message and all attachments transmitted with it may contain legally privileged and/or confidential information intended solely for the use of the addressee(s). If the reader of this message is not the intended recipient, you are hereby
notified that any reading, dissemination, distribution, copying, forwarding or other use of this message or its attachments is strictly prohibited. If you have received this message in error, please notify the sender immediately and delete this
message and all copies and backups thereof. Thank you.
Show less
o	LA
Laura.Rakhimberdina (IBM)
Dec 23, 2025, 06:04
Hello,

I'd be grateful for your response on this case, otherwise, I'll proceed to mark it as the closed by the end of business day Tuesday, December 23rd, should I not hear back. You may reopen the case within 30 days if you have any additional questions.

Thanks for understanding,
Laura
•	Dec 22, 2025
o	 
Enhanced Customer Data Repository (ECuRep)
Dec 22, 2025, 02:36
Automated response from: Robert Cobas <robert.cobas@assurant.com>
________________________________________

I will be out of office on 11/19 . For EDM or EPA issues or questions please email EDM Scrum Team edm.scrum.team@assurant.com. For escalations please contact Josh Yopp.
_____________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________

This e-mail message and all attachments transmitted with it may contain legally privileged and/or confidential information intended solely for the use of the addressee(s). If the reader of this message is not the intended recipient, you are hereby
notified that any reading, dissemination, distribution, copying, forwarding or other use of this message or its attachments is strictly prohibited. If you have received this message in error, please notify the sender immediately and delete this
message and all copies and backups thereof. Thank you.
Show less
o	LA
Laura.Rakhimberdina (IBM)
Dec 22, 2025, 02:34
Hello Gangadhar

Could you please review the previous post?
Thank you!

Kind regards,
Laura
•	Dec 17, 2025
o	ZU
Zuzanna.Chrzastek (IBM)
Dec 17, 2025, 02:48
Hello Gangadhar

This is Zuzanna. Laura is currently on sick leave, so I’m updating the ticket.

The technote you sent in your previous message relates to Planning Analytics Local, so it won’t be very helpful for troubleshooting Planning Analytics on Cloud.

The Agent tab in PAW shows 92% memory usage because your entire system is using that much memory. This is expected behavior - at the moment, the Assurant GFS database is using 332 GB of RAM, and Assurant is using 131 GB of RAM.

You mentioned that you reviewed the performance monitor and checked StatsByCube and StatsByServer. Did you find which cube uses that much memory?

Zuzanna
Show less
o	ZU
Zuzanna.Chrzastek (IBM) changed status from Waiting for IBM to Awaiting your feedback
Dec 17, 2025, 02:48
•	Dec 16, 2025
o	GA
gangadhar.akambram (Customer)
Dec 16, 2025, 08:16
Hello Laura, 

Could you please let us know if there is any update on this case? We are waiting for your reply.
Thank you!

Kind regards,
Gangadhar.
o	GA
gangadhar.akambram (Customer) changed status from IBM is working to Waiting for IBM
Dec 16, 2025, 08:16
•	Dec 11, 2025
o	LA
Laura.Rakhimberdina (IBM)
Dec 11, 2025, 04:01
Hello Gangadhar,

Thank you for explanation!

Please let us investigate this for you and I will get back with updates shortly.

Kind regards,
Laura
o	LA
Laura.Rakhimberdina (IBM) changed status from Waiting for IBM to IBM is working
Dec 11, 2025, 04:01


•	
o	gangadhar.akambram (Customer)
Dec 10, 2025, 10:01
Hello Laura,

We are facing issue with the Agents, where the Agents health is showing as Critical, even though tm1 databases appear healthy. The memory usage temporarily reduces after troubleshooting but gradually increases again.

I have followed all the trouble shooting steps provided in the IBM documentation including the points mentioned below. 
Steps already performed:
Restarted the databases 
Checked StatsByCube
Checked StatsByServer
Verified via performance monitor

Screenshot uploaded.
IBM Documentation link: https://www.ibm.com/support/pages/how-troubleshoot-planning-analytics-administration-agent

However, we are unable to locate the /file path referenced in the IBM portal so we could not complete that part of the procedure.

Request your guidance on:
1. Specific steps required to fully resolve the Agents critical status
2. Clarifying where we can find the missing /file path in our environment 
3. Any additional logs or configurations we should review 

Kind regards,
Gangadhar.
Show less
o	 
Enhanced Customer Data Repository (ECuRep) (IBM) uploaded a file.
Screenshot_2025-12-10_210104.png
Dec 10, 2025, 10:01
o	 
Enhanced Customer Data Repository (ECuRep) (IBM) changed status from Awaiting your feedback to Waiting for IBM
Dec 10, 2025, 10:01
o	LA
Laura.Rakhimberdina (IBM)
Dec 10, 2025, 02:17
Hello,

I'd be grateful for your response on this case, otherwise, I'll proceed to mark it as the closed by the end of business day Wednesday, December 10th, should I not hear back. You may reopen the case within 30 days if you have any additional questions.

Thanks for understanding,
Laura
•	Dec 08, 2025
o	 
Enhanced Customer Data Repository (ECuRep)
Dec 08, 2025, 01:24
Automated response from: Joshua Yopp <joshua.yopp@assurant.com>
________________________________________



I will be out of the office returning Monday December 15th. Please contact my cell phone with immediate needs.
Thanks
Joshua
o	LA
Laura.Rakhimberdina (IBM)
Dec 08, 2025, 01:23
Hello Roberto,

May I please ask for update on this case?
Thank you!

Kind regards,
Laura
•	Dec 04, 2025
o	LA
Laura.Rakhimberdina (IBM) changed status from IBM is working to Awaiting your feedback
Dec 04, 2025, 05:13
o	LA
Laura.Rakhimberdina (IBM)
Dec 04, 2025, 01:52
Hello Roberto,

Thank you for reply!

We will wait for reply.

Kind regards,
Laura
o	LA
Laura.Rakhimberdina (IBM) changed status from Waiting for IBM to IBM is working
Dec 04, 2025, 01:52
•	Dec 03, 2025
o	RO
robert.cobas (Customer)
Dec 03, 2025, 08:49
•	Have there been any recent changes to rules, feeders, TI processes, chores, or data loads? There were a few minor changes to TI processes about 3 weeks ago.
•	Do you notice the memory rise after a specific chore or user activity? Not after anything specific, I've restarted the server which brings it down temporarily and then it goes back up.
•	Are there any long-running threads or unusual messages when memory is high? None
•	Is this behaviour observed only in Production or also in Test/Dev? Yes, in both.
I'll restart the server again tonight to get a baseline. I've also turned on performance monitoring.

Show less
o	RO
robert.cobas (Customer) changed status from Awaiting your feedback to Waiting for IBM
Dec 03, 2025, 08:49
o	 
Enhanced Customer Data Repository (ECuRep)
Dec 03, 2025, 01:36
Automated response from: Robert Cobas <robert.cobas@assurant.com>
________________________________________

I will be out of office starting on 11/25 and returning on 12/3 . For EDM or EPA issues or questions please email EDM Scrum Team edm.scrum.team@assurant.com. For escalations please contact Josh Yopp.
_____________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________

This e-mail message and all attachments transmitted with it may contain legally privileged and/or confidential information intended solely for the use of the addressee(s). If the reader of this message is not the intended recipient, you are hereby
notified that any reading, dissemination, distribution, copying, forwarding or other use of this message or its attachments is strictly prohibited. If you have received this message in error, please notify the sender immediately and delete this
message and all copies and backups thereof. Thank you.
Show less
o	LA
Laura.Rakhimberdina (IBM)
Dec 03, 2025, 01:34
Hello,

Please be advised the TM1 memory usage were naturally increased as users run views and TI processes. PA on Cloud do not have access to the underlying server and you cannot automate service restarts with scripts and as you aware, the only option available is the manual restart through the Administration tile in Workspace.

However, there is a scheduled time where the instances are stopped/started which will release the memory back to the operating system.
Every 3rd Saturday of the month is Maintenance weekend for PAonCloud, where all PAonCloud environments are restarted.
Thus, all running tm1 instances on each instance are stopped and started . This is scheduled.If you have not subscribed to our PA dashboard, here is the link and please subscribe so you are informed via email of any outages, maintenance work, upgrades - https://status.ai-apps-comms.ibm.com/planninganalytics

Additional information for the reference:
Detail for troubleshooting memory issues to review and implemenr when you encounter sudden memory spikes.

1)As per design, TM1 does not release memory back to the operating system.
When a TM1 Server process grows in size (for whatever reason), the process holds on to that memory and does not release it back to the operating system. The TM1 Server will then release/garbage the memory within itself. The memory is released back only upon a restart of the tm1 instance.

2)Known operations that can result in changes to the memory profile include:
Changes to the Database/Application
Multiple threads with the same user ID. Consider process initiated threads or concurrent TM1runti.exe usage
Creating subsets/views
Loading data
Adding dimension elements
View Zero Out
Sandboxes - Large delta file on disk
Idle sessions when IdleConnectionTimeOutSeconds is not set
Dimension reordering
Pre-calculating cubes / slices
Feeder statements
The majority of cases we have received related to memory consumption/differences, have turned out to be caused by changes in the model (cube data, rules, config file changes).

Short of stopping and starting the instance to release the memory back to OS, if you would like to do a deep dive to determine what is consuming the most memory you can do the following:

1.Turn on Performance Monitoring (right-click on the server in PAWorkspace and choose Start Performance Monitor)
https://www.ibm.com/docs/en/planning-analytics/2.0.0?topic=cubes-enabling-performance-monitoring
2.Open the following 2 control cubes: 
}StatsForServer
and
}StatsByCubes cubes.
NOTE: It is the }StatsForServer that provides the total memory consumption for the TM1 server by the OS
(total of the actual current memory usage and the garbage memory.)

Good document to reference when reviewing the 2 control cubes and confirm what is consuming the memory
https://www.ibm.com/support/knowledgecenter/en/SSD29G_2.0.0/com.ibm.swg.ba.cognos.tm1_op.2.0.0.doc/c_statsstatsbycube_n706cd.html

3)As well, if unsure if model changes or not and you would like to confirm and further investigate, we recommend clients start with a baseline and stop / start the server and take a snapshot of the memory upon restart to confirm memory used before users access / and changes made. As well, we recommend users reference the 2 control cubes for stats and make note.

Then monitor for memory increases and reference the 2 cubes again and compare to see where the increase has occurred.

Let me know if you require further information on this request.

Kind regards,
Laura
Show less
•	Nov 27, 2025
o	LA
Laura.Rakhimberdina (IBM)
Nov 27, 2025, 01:27
Hello,

To help us investigate the sudden memory increase, could you please confirm the following:
•	Have there been any recent changes to rules, feeders, TI processes, chores, or data loads?
•	Do you notice the memory rise after a specific chore or user activity?
•	Are there any long-running threads or unusual messages when memory is high?
•	Is this behaviour observed only in Production or also in Test/Dev?
This information will help us proceed with the analysis.

Thank you!

Kind regards,
Laura
Show less
•	Nov 25, 2025
o	LA
Laura.Rakhimberdina (IBM) changed status from IBM is working to Awaiting your feedback
Nov 25, 2025, 02:32
o	 
Enhanced Customer Data Repository (ECuRep)
Nov 25, 2025, 02:25
Automated response from: Lisa Nordling <lisa.nordling@assurant.com>
________________________________________

I am currently out of the office and will return on Monday, December 1st.

If you require immediate assistance contact the my scrum team at edm.scrum.team@assurant.com.

Thank you,
Lisa Nordling
o	LA
Laura.Rakhimberdina (IBM)
Nov 25, 2025, 02:25
Hello,

The investigation is still in progress.

To help us investigate the sudden memory increase, could you please confirm the following:
•	Have there been any recent changes to rules, feeders, TI processes, chores, or data loads?
•	Do you notice the memory rise after a specific chore or user activity?
•	Are there any long-running threads or unusual messages when memory is high?
•	Is this behaviour observed only in Production or also in Test/Dev?
This information will help us proceed with the analysis.

Thank you!

Kind regards,
Laura
Show less
•	Nov 24, 2025
o	LA
Laura.Rakhimberdina (IBM)
Nov 24, 2025, 07:13
Hello

Thank you for contacting IBM support. This is Laura and I will be assisting you on this case.
Let me investigate and I will get back to you with updates.
