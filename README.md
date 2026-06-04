Hi Lisa,

That is a very important point and it does change my thinking significantly. Thank you for raising it.

You are right — if all versions prior to 2026RF1 were visible to users without any issue, then the root cause cannot simply be a missing security process. If that were the problem, it would have affected every version, not just RF1. Something specific changed between when Plan was locked in February and when RF1 was created in May.

Before I run anything in DEV, I need to check three things:

1 — How was 2026RF1 created vs prior versions?
Was 2026RF1 created manually (inserted directly into the Version dimension), or was it created through a TI process? If prior versions were created via a process that also set their security entries, but RF1 was inserted manually without running that process, that would explain exactly what happened.

2 — What is currently in }ElementSecurity_Version?
Can someone check this cube in PAW and tell me: do prior versions (2026Plan, 2025RF1, etc.) have entries in }ElementSecurity_Version for all groups? And does 2026RF1 have no entries, or an entry with the wrong access level?

3 — Robert — what specifically did you do to fix it?
Did you manually add an entry to }ElementSecurity_Version for 2026RF1? Or did you run a process? And if a process — which one? This tells us exactly what was done for prior versions that was missed for RF1.

Once I have answers to these three questions I will have the complete picture and can give a definitive root cause and fix.

Holding off on running anything in DEV until we have this confirmed.

Thanks,
Niranjan Patra
