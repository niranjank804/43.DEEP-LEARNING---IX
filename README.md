Environment: IBM Planning Analytics Cloud
Affected Instances: Assurant Dev, Assurant QA, Assurant GFS Dev

Issue:
After a production environment copy was performed on 26-Jun-2026, all three lower environment TM1 servers (Assurant Dev, Assurant QA, Assurant GFS Dev) start but automatically stop within seconds. This affects all lower environments refreshed from production.

Steps to Reproduce:

Click Start on Assurant Dev, Assurant QA, or Assurant GFS Dev instance
Server shows "Processing" briefly
Server automatically changes to Stopped within seconds
Logs Reviewed:

tm1server.log shows a graceful shutdown sequence only - no FATAL, ERROR, or Exception messages found
AgentActions.log confirms multiple start attempts by multiple users
No application-level error found in startup logs across all affected instances
Request:
Please investigate the environment refresh and restore all three lower environment instances (Assurant Dev, Assurant QA, Assurant GFS Dev) to a startable state.
