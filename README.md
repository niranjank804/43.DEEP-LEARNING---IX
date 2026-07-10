Hi Sherry and Robert,

I checked the actual records read by the process from the FX source.

All currencies have conversion rates dated 2026-06-16, and those were loaded into Jun-2026 correctly. The only exception is CAD. Its two records (MTD and QTD) are dated 2026-07-01 in the source, so the process maps them to Jul-2026. That's why CAD appears in July instead of June.

The process derives the target period directly from the "to conversion date" field, so it is loading the data exactly as provided by the source.

Sherry: Could you please confirm whether the CAD conversion date of 2026-07-01 is correct, or whether it should have been dated in June like the other currencies? If the business rule is to map rates dated on the first day of the following month back to the previous month, please confirm so we can implement the appropriate logic.

Robert: Could you also confirm which SQL Server instance and database the vdbdw-finance ODBC DSN on the Assurant GFS Dev TM1 server points to? The FX load reads 76 rows from table_PA_GFS_FX_Rates through that DSN, but when I query the same table in SSMS, I get 0 rows. I suspect my SSMS is connected to a different SQL Server, so I'd like to verify I'm looking at the same data source.

Thanks,

Niranjan Patra
