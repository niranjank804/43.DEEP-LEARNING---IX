Hi Sherry,

That is the missing piece. Thank you — I believe that security group clean-up on the cube to cube settings is the root cause.

Here is why this fits perfectly:

All versions created before the clean-up were already visible to users — their security was already in place and was not affected
2026RF1 was the first version created after the clean-up — whatever setting was previously granting access to new version elements was no longer there, so RF1 had no security entry and users could not see it
Robert's manual fix worked because he directly restored that access for RF1 specifically
Your suggestion to restore a late 2025 backup in a lower environment is exactly the right approach. Comparing the before and after settings will show us precisely what was changed and what needs to be restored or rebuilt.

To move forward I need two things from you Sherry:

1 — Can you describe what the clean-up involved?
Specifically — which groups were affected, and what were the cube to cube settings you modified? Even a general description helps narrow down where to look when we do the comparison.

2 — Can you arrange the backup restore in DEV or QA?
A late 2025 backup (before the clean-up) restored in a lower environment will let us do a side-by-side comparison of the security settings then vs now and identify the exact gap.

Once we see the difference I can rebuild the correct setting and test it in DEV before touching Production.

Holding the new process I built until we confirm this is the right fix — I do not want to apply a workaround if we can restore the correct setting that was there before.

Thanks,
Niranjan
