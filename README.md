Hi Jakub,
Thank you for the detailed and clear responses — this is exactly what we needed.
To summarize our understanding based on your guidance:
No automated/scheduled restart is available or configurable in PA Cloud (current versions)
REST/PAW API supports only Start/Stop operations — a full service-level restart cannot be triggered programmatically
IBM already performs weekly maintenance — additional windows cannot be requested
RAM monitoring via PA Admin/Monitoring is the recommended proactive approach to determine restart frequency
We will proceed with the following interim approach:
Monitor RAM trends via PAW Admin / Monitoring
Perform manual restarts via PAW Administration on a scheduled basis as needed
Submit a formal enhancement request via IBM Ideas (PAOC-I-471) for a native scheduled restart capability
Could you please confirm one final thing: does IBM's existing weekly maintenance window include a TM1 database restart, or is it infrastructure-level maintenance only?
We will close this case once confirmed. Thank you for your time.
Best regards,
Niranjan Patra
