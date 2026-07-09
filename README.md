Hi,

The process completed successfully on my side as well.

Based on the recent changes for PBI 4071093, the process now only zeroes and loads periods that exist in the shared Open Periods dimension.

I don't have direct SQL access to verify whether June exchange rates are currently available in the table_PA_GFS_FX_Rates source table. During my Dev testing, the process completed successfully, but no source records were returned, so I was only able to validate the ZeroOut logic and process behavior.

Could you please confirm whether June FX rates are present in the OFSAA source table? If they are, I'll investigate why they're not being loaded. If not, then the process is behaving as expected.
