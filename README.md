Hi Vaibhav — yes, I've identified the root cause. Forecast_Allocation_2 chore (Thread 11532) is holding a write lock on the SCN cubes and has been running since ~08:39 AM. P1EP.SSIS.LOAD.COMPLETION is stuck in a wait loop because it's polling for the actuals chore to complete, which is blocked by this lock.

I tried cancelling the blocked threads from PAW Monitor but it's not working — this requires infra-level intervention. Robert logs in at 5 PM and I'll loop him in immediately to force-kill the thread. Will update you as soon as it's resolved.
