Environment: IBM Planning Analytics Cloud
Affected instance: Assurant Dev (and any other lower envs affected)

Issue: After a production environment copy was performed on [today's date], 
the Assurant Dev TM1 server starts but automatically stops within seconds. 
This affects all lower environments refreshed from production.

Steps to reproduce:
1. Click Start on Assurant Dev instance
2. Server shows "Processing" briefly
3. Server changes to Stopped automatically

Logs reviewed:
- tm1server.log shows a graceful shutdown sequence only (no FATAL/ERROR/Exception)
- AgentActions.log confirms multiple start attempts
- No application-level error found in startup logs

Request: Please investigate the environment refresh and restore the 
lower environment(s) to a startable state.
