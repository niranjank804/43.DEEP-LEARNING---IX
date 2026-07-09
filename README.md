Hi,

Thanks for checking.

The current implementation only loads periods that are present in the shared Open Periods dimension. I verified that 202606 is currently an open period, so June is eligible to be loaded.

I don't have SQL access to verify the contents of table_PA_GFS_FX_Rates. Could someone please confirm whether June (202606) exchange rates are available in the source table? If the data exists, I'll investigate why it isn't being loaded. If there are no June records in the source, the process would complete successfully but no rates would be loaded.

Thanks,
Niranjan
