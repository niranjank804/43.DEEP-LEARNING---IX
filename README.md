Hi Sherry,

The requested logic has been implemented.

Any rate whose to conversion date falls between the 1st and 15th of the month is now mapped to the previous month. I implemented the logic for all currencies since it is based on the date rule rather than being CAD-specific.

I reran the process in Dev and verified the results:

76 records processed with no skipped records.
CAD MTD (0.712205) and QTD (0.722628) now load to Jun-2026 instead of Jul-2026.
Other currencies continue to load correctly (for example, EUR MTD 1.16026 loads to Jun-2026).
Jul-2026 is now empty, as expected, since the current source data belongs to the June reporting period.
Please let me know if you'd prefer the logic restricted to CAD only. Otherwise, the changes are ready for validation.

Thanks,

Niranjan Patra
