Hi Sherry, Lisa, and Robert,

Just following up on my email from yesterday as I have not yet received a response. I want to make sure this does not get lost given the impact it has on users going forward.

To summarize what I still need before I can finalize and test the fix:

From Robert:
When you manually fixed 2026RF1 visibility — did you write directly to }ElementSecurity_Version, or did you run a process? And which groups did you grant access to? This is the most critical piece I am waiting on.

From Sherry:
During the security group clean-up on the cube to cube settings — was an Everyone group or any catch-all default group removed or modified? This is most likely what caused RF1 to have no automatic security after it was inserted.

Quick check — anyone:
Can someone open }ElementSecurity_Version in PAW and confirm whether prior versions (e.g. 2026Plan) have entries for all groups while 2026RF1 has none? This takes two minutes and will confirm the gap immediately without needing a backup restore.

I have the fix built and ready to test in DEV. I am just waiting on these answers to make sure I apply the right solution before running anything.

Could you please respond by end of day today so we can keep this moving? Happy to jump on a quick call if that is easier.

Thanks,
Niranjan Patra
