Environment(dev): IBM Planning Analytics Cloud
Affected instances: Assurant Dev, Assurant QA, Assurant GFS Dev

Issue: After a production environment copy was performed on [26-06-2026], 
tAssurant Dev, Assurant QA, Assurant GFS Dev TM1 servers starts but automatically stops within seconds. 
This affects all lower environments refreshed from production.

Steps to reproduce:
1. Click Start on Assurant Dev, Assurant QA, Assurant GFS Dev instances
2. Servers shows "Processing" briefly
3. Servers changes to Stopped automatically

Logs reviewed:
- tm1server.log shows a graceful shutdown sequence only (no FATAL/ERROR/Exception)
- AgentActions.log confirms multiple start attempts
- No application-level error found in startup logs

Request: Please investigate the environment refresh and restore the 
lower environment(s) to a startable state.
