Hi Team,

Please find below the final summary of my R&D for SPIKE PBI #3981779 — "EPA: Research SPIKE – Job for System Auto Restart."

──────────────────────────────────────
OBJECTIVE
──────────────────────────────────────
Research the best design for automatically restarting the EPA (IBM Planning Analytics Cloud) instance on weekends to reduce memory accumulation and improve platform stability.

──────────────────────────────────────
RESEARCH APPROACH
──────────────────────────────────────
• Evaluated 12 automation options across IBM-native tools, TI/script-based approaches, and enterprise schedulers
• Raised a formal IBM Support case (Severity 4) to obtain IBM's official position
• Engaged IBM Support Engineer Jakub Makowka — received 3 responses across the day

──────────────────────────────────────
IBM'S OFFICIAL CONFIRMED POSITION
(April 23, 2026 — IBM Support Case)
──────────────────────────────────────
✗  Scheduled/automatic restart by customer — NOT SUPPORTED in current PA Cloud versions. Windows Task Scheduler approach previously available is no longer supported.

✗  Additional IBM-managed maintenance windows — NOT POSSIBLE beyond standard cadence.

✗  Programmatic restart via REST/PAW API — NOT POSSIBLE. REST endpoints lack permissions to restart the underlying Windows service.

✓  IBM WEEKLY MAINTENANCE already includes a Data Tier restart — confirmed by IBM. This can be verified by reviewing Server Logs in PAW after each maintenance window.

──────────────────────────────────────
FINAL CONCLUSION
──────────────────────────────────────
All customer-triggered automated restart paths are blocked in IBM PA Cloud SaaS. However, IBM's existing weekly maintenance cadence already includes a Data Tier restart, which provides the core memory reclaim benefit we were looking for.

──────────────────────────────────────
RECOMMENDED PATH FORWARD
──────────────────────────────────────
1. Review PAW Server Logs after IBM's next maintenance window to confirm restart behaviour and frequency for our environment
2. Monitor RAM trends via PAW Admin / Monitoring to assess whether the IBM maintenance cadence alone is sufficient
3. If additional restarts are needed — perform manual restarts via PAW Administration on an as-needed basis and register as a Standard Change in ServiceNow
4. Implement a pre-restart TI hygiene chore (SaveDataAll + session cleanup) to ensure data integrity before any manual restart
5. Upvote IBM Ideas PAOC-I-471 to formally push IBM Development for a native scheduled restart capability

I will update ADO PBI #3981779 with these findings and mark the SPIKE as complete.

Happy to discuss further if needed.

Best regards,
Niranjan Patra
Senior TM1 / IBM Planning Analytics Consultant
Tech Mahindra — Assurant EPA Engagement
