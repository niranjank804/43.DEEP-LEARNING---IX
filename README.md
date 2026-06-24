Niranjan, I have put together a view for the FTE from Assurant QA… it may need to be tweaked for the Segment information, but the MDX will get you FTE counts through May. I have saved the book in the User Reports folder and named it Financial Summary Cube - FTE Counts Actual (in case we need to tweak it).

SELECT TM1IGNORE_BADTUPLES NON EMPTY 
   {
      EXCEPT(
         {
            [Period].[Period].[202601 YTD],
            [Period].[Period].[202602 YTD],
            [Period].[Period].[202603 YTD],
            [Period].[Period].[202604 YTD],
            [Period].[Period].[202605 YTD],
            [Period].[Period].[202606 YTD],
            [Period].[Period].[202607 YTD],
            [Period].[Period].[202608 YTD],
            [Period].[Period].[202609 YTD],
            [Period].[Period].[202610 YTD],
            [Period].[Period].[202611 YTD],
            [Period].[Period].[202612 YTD]
         }, 
         {
            [Period].[Period].[202606 YTD], 
            [Period].[Period].[202607 YTD], 
            [Period].[Period].[202608 YTD], 
            [Period].[Period].[202609 YTD], 
            [Period].[Period].[202610 YTD], 
            [Period].[Period].[202611 YTD], 
            [Period].[Period].[202612 YTD]
         }, ALL)
   } ON 0, TM1IGNORE_BADTUPLES NON EMPTY 
   {
      EXCEPT(
         {
            DRILLDOWNMEMBER(
               {
                  [Center].[Center].[1:GLOBAL CENTERS]
               }, 
               {
                  [Center].[Center].[1:GLOBAL CENTERS], 
                  [Center].[Center].[1:PLANNING MGR CENTERS], 
                  [Center].[Center].[1:MANAGERIAL CC], 
                  [Center].[Center].[1:MANAGERIAL REPORTED], 
                  [Center].[Center].[1:BUSN CC]
               }, RECURSIVE)
         }, 
         {
            [Center].[Center].[1:GLOBAL CENTERS], 
            [Center].[Center].[1:PLANNING MGR CENTERS], 
            [Center].[Center].[1:MANAGERIAL CC]
         }, ALL)
   } ON 1 
FROM
   [Financial Summary] 
WHERE (
   [Version].[Version].[Actual], 
   [Financial Summary Measure].[Financial Summary Measure].[Original Amount], 
   [Currency].[Currency].[Currency Control Total], 
   [Account].[Account].[1:A900001^A900001-01], 
   [Location].[Location].[Location Control Total], 
   [Entity].[Entity].[Entity Control Total], 
   [Product].[Product].[1:MANAGERIAL PRODUCTS], 
   [Client].[Client].[Top:SUPERGROUPS AND REGROUPS], 
   [Charging Center].[Charging Center].[1:GLOBAL CENTERS], 
   [Affiliate].[Affiliate].[ALL AFFILIATES])



1.Salary account number - is it A700001-01?
Salary account is A700001-01, however that data currently doesn’t exist in the System as there is another filter to that data based on a field we do not maintain. I’m working with the OFSAA team to get that file built. 

2.Amount Class for salary driver - B000 only or both?
For the Salary data it will only be Amount Class B000 which is already filtered on the file I’m working on getting from OFSAA. The Amount Class difference when using Cash B000 versus Accrual B100 is based on the Group Insurance data we are allocating which is in account A700018-00 (first part of Section 4 of the Requirements document)

3.Target cube - are we using existing Actual Allocation cube or building a new one?
In speaking with @Sherry Pellegrini yesterday, she was thinking that the Allocation cube would likely work for the Target Cube. 
