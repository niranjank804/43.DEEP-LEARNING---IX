Hi Sherry, Lisa, and Robert,

Thank you Sherry — that is the missing piece. The security group clean-up you performed on the cube to cube settings between February and May is almost certainly the root cause of this issue. Let me explain why this fits perfectly, and then I have a few follow-up questions before we proceed.

Why This Explains Everything

All versions created before the clean-up (2026Plan and earlier) were already visible to users and continued to work fine — their security entries were already in place and were not affected by the clean-up.

2026RF1 was the first version created after the clean-up. Whatever setting was previously granting access to new Version elements automatically was no longer there after the clean-up ran. So when RF1 was inserted, no security entry was created for it and users could not see it.

Robert's manual fix worked because he directly restored that access for RF1 specifically — bypassing the gap that the clean-up left behind.

Questions Before We Proceed

I have three questions that will give us the complete picture before I touch anything in DEV.

Question 1 — For Robert (Critical — Still Outstanding)

Robert, I have not yet heard back on this — can you confirm exactly what you did to fix the 2026RF1 visibility issue? Specifically:

Did you manually write an entry into }ElementSecurity_Version for RF1?
Or did you run a process to do it?
Which groups did you grant access to, and what access level — READ or WRITE?
Your answer tells us precisely what the correct security setup looks like and ensures the permanent fix matches what you applied.

Question 2 — For Sherry (Most Likely Culprit)

During the clean-up, was there an Everyone group, or any catch-all or default group, that was removed or had its access level changed?

In TM1, a group like Everyone typically holds a base READ access that applies automatically to all elements — including new ones added after the group is set up. If that group was removed or modified during the clean-up, every new Version element created afterward would have no default access, which is exactly the behavior we are seeing with RF1 while all prior versions remain visible.

Even a general description of which groups were affected and what changed would help narrow this down significantly.

Question 3 — Quick Check Anyone Can Do Right Now

Before we restore any backup, can someone open }ElementSecurity_Version in PAW and check the following:

Do prior versions such as 2026Plan have entries in that cube for all groups?
Does 2026RF1 have no entry at all, or does it have an entry with NONE as the access level?
This check takes about two minutes and will immediately confirm whether the gap is in that cube, and whether Robert's fix added a new entry or changed an existing one. This will tell us a lot before we even look at the backup.

Next Steps

I fully support Sherry's suggestion to restore a late 2025 backup in DEV or QA for a side-by-side comparison. Once we see the difference in settings between before and after the clean-up, I can rebuild the correct setting, test it in DEV, and confirm the fix before anything goes to Production.

I am holding off on running the new process I built until we confirm this is the right approach. I do not want to apply a workaround if the correct fix is to restore a setting that existed before.

Please come back to me on the three questions above and we can move quickly from there.

Thanks,
Niranjan
