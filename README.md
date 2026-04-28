#Section Prolog

#****Begin: Generated Statements***
#****End: Generated Statements****

#####################################################################################################################
#  DIM - Create Standard Subsets
#####################################################################################################################
#
# PURPOSE:
#   This process will Create a standard set of subsets to be used for reporting and input forms
#   This process will run nightly and assure that all standard subsets are up to date
#
# SUBSETS:
#  "0 Level Items" and "0 Level Items No Alias":  These sets will be created for each dimension, and will contain an alphabetical listing of all leaf level items under standard Alias if applicable.
#                           Note, the procedure will remove previous Level 0 Items subset, create a new subset using MDX, then sets it to a static subset for better performance.
#                           The MDX will sort the subset in ascending order by either name or ID for Level 0 (leaf) level items only.  It will then assign an alias if applicable.
#
# Other Items:   Other subsets needed will be called by dimension and then use mdx to create a dynamic subset and then set it to a static subset for better performance
#
# PARAMETERS: 
#
# MODIFICATION HISTORY:
#   Created : 12/07/17   GZ  
#   06-25-2018: Added code in Epilog to send out email in case of minor error RC/Assurant
#   10-18-2018: Added code to create Level 0 Item Active in Partner Cost Center  RC/Assurant
#   05-28-2019: Added Level 0 Items No Alias
#   02-06-2023: Cleaned/produce code to create subsets for all dims and updated for GFS structure / Monica D. 
#   09-03-2025: Added additional years to period subsets  RC.
#
#####################################################################################################################

# ===============================    PROCESS STATS  ====================================

sCube = 'Process Stats' ;
sProcess = GETPROCESSNAME;
sLogFile = GETPROCESSERRORFILEDIRECTORY | GETPROCESSERRORFILENAME;
sNow = TIMST( NOW, '\M \D, \Y at \H \p \i min \s sec' ) ;
sDate = TIMST( NOW, '\Y\m\d' );

# Grab Current Line Item
# -------------------------------------- 
CurrLine = CELLGETS ( sCube , 'All Index' , sDate , sProcess , 'Current Line Item' );

# Initial measures
# ---------------------------------
CELLPUTS( sNow , sCube , CurrLine , sDate , sProcess , 'Start Time' );
CELLPUTS( sNow , sCube , CurrLine , 'Latest' , sProcess , 'Start Time' );
CELLPUTS( 'Did not end' , sCube , CurrLine , sDate , sProcess , 'End Time' );
CELLPUTS( 'Did not end' , sCube , CurrLine , 'Latest' , sProcess , 'End Time' );

# User
# ------------------
vNowStart = NOW;
IF(SUBST(TM1USER(),1,2)@='R*');
  sUser = '*CHORE*';
ELSE;
  sUser = ATTRS( '}Clients' , TM1USER() , '}TM1_DefaultDisplayValue' );
ENDIF;
CELLPUTS( sUser , sCube , CurrLine , sDate , sProcess, 'Last Executed By' );
CELLPUTS( sUser , sCube , CurrLine , 'Latest', sProcess, 'Last Executed By' );

# Record Count
# -------------------------------
nRecordCount = 0;
CELLPUTN( nRecordCount , sCube , CurrLine , sDate , sProcess , 'Record Count' );
CELLPUTN( nRecordCount , sCube , CurrLine , 'Latest', sProcess, 'Record Count' );

# Skipped Records
# ------------------------------------
nRecordSkip = 0;
CELLPUTN( nRecordSkip , sCube , CurrLine , sDate , sProcess, 'Skipped Records' );
CELLPUTN( nRecordSkip , sCube , CurrLine , 'Latest', sProcess, 'Skipped Records' );

# Processed Records
# -----------------------------------------
nRecordProcess = 0;
CELLPUTN( nRecordProcess , sCube , CurrLine , sDate , sProcess , 'Processed Records' );
CELLPUTN( nRecordProcess , sCube , CurrLine , 'Latest', sProcess, 'Processed Records' );

# Parameters
# --------------------
vParamFilled = 'None' ;
CELLPUTS( vParamFilled, sCube , CurrLine , sDate , sProcess , 'Parameters' );
CELLPUTS( vParamFilled, sCube , CurrLine , 'Latest' , sProcess , 'Parameters' );

#########################################################################

vSource = CELLGETS( 'Global Variables', 'ODBC', 'String Value' ) ;
vSourcePWD = CELLGETS( 'Global Variables', 'ODBC PWD', 'String Value' ) ;
vSourceID = CELLGETS( 'Global Variables', 'ODBC ID', 'String Value' ) ;

cAlias = 'ID - Name' ;

#------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
################################
# CREATE CENTER WFP SUBSETS
################################
cCenterWFPDim = 'Center WFP' ;

# Create WFP Summary Center Subset
vSubset = 'WFP Summary Center';
IF( SUBSETEXISTS( cCenterWFPDim, vSubset ) = 0 );
  SUBSETCREATE( cCenterWFPDim, vSubset);
ENDIF;
SUBSETMDXSET(cCenterWFPDim, vSubset , '{DISTINCT({DESCENDANTS(['|cCenterWFPDim|'].['|cCenterWFPDim|'].[1:ALL CENTERS])})}' ) ;
SUBSETMDXSET( cCenterWFPDim, vSubset, '');
SUBSETALIASSET( cCenterWFPDim, vSubset, cAlias) ;   

#---------------------------------------------------------------------------------------------------------------------------------------------------
################################
# CREATE JOB TITLE SUBSETS
################################
cJobTitleDim = 'Job Title' ;

# Create 0 Level Items Name - ID Subset
vSubset = '0 Level Items Name - ID';
IF( SUBSETEXISTS( cJobTitleDim, vSubset ) = 0 );
  SUBSETCREATE( cJobTitleDim, vSubset);
ENDIF;
SUBSETMDXSET(cJobTitleDim, vSubset , '{TM1SORT( {TM1FILTERBYLEVEL( {TM1SUBSETALL( ['|cJobTitleDim|'] )}, 0)}, ASC)}' ) ;
SUBSETMDXSET( cJobTitleDim, vSubset, '');
SUBSETALIASSET( cJobTitleDim, vSubset, 'Name - ID') ;  


#------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
################################
# CREATE LOCATION WFP SUBSETS
################################
cLocationWFPDim = 'Location WFP' ;

# Create WFP Summary Location Subset
vSubset = 'WFP Summary Location';
IF( SUBSETEXISTS( cLocationWFPDim, vSubset ) = 0 );
  SUBSETCREATE( cLocationWFPDim, vSubset);
ENDIF;
SUBSETMDXSET(cLocationWFPDim, vSubset , '{DISTINCT({DESCENDANTS(['|cLocationWFPDim|'].['|cLocationWFPDim|'].[Location Control Total])})}' ) ;
SUBSETMDXSET( cLocationWFPDim, vSubset, '');
SUBSETALIASSET( cLocationWFPDim, vSubset, cAlias) ;  

#------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
################################
# CREATE FIXED ASSETS ACCOUNT SUBSETS
################################
cFAAccountDim = 'Fixed Assets Account';

# Create IDSW Accounts Subset
vSubset = 'IDSW Accounts';
IF( SUBSETEXISTS( cFAAccountDim, vSubset ) = 0 ) ;
  SUBSETCREATE( cFAAccountDim, vSubset) ;
ELSE ;
    SUBSETDELETEALLELEMENTS( cFAAccountDim, vSubset );
ENDIF;
SUBSETMDXSET (cFAAccountDim, vSubset , '{FILTER( {TM1SUBSETALL( ['|cFAAccountDim|'] )}, ['|cFAAccountDim|'].[IDSW Flag] = "Y")}' ) ;
SUBSETMDXSET( cFAAccountDim, vSubset, '') ;
SUBSETALIASSET( cFAAccountDim, vSubset, cAlias) ;

#------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
################################
# CREATE INCOME ACCOUNT SUBSETS
################################
cIncomeAccountDim = 'Income Account';
# Create Premium Input Accounts Subset
vSubset = 'Premium Input Accounts';
IF( SUBSETEXISTS( cIncomeAccountDim, vSubset ) = 0 );
  SUBSETCREATE( cIncomeAccountDim, vSubset);
ENDIF;
SUBSETMDXSET(cIncomeAccountDim, vSubset , '{TM1SORT( {TM1FILTERBYLEVEL( {FILTER( {TM1SUBSETALL( ['|cIncomeAccountDim|'] )}, ['|cIncomeAccountDim|'].[Premium Input Flag] = "Y")}, 0)}, ASC)}' ) ;
SUBSETMDXSET( cIncomeAccountDim, vSubset, '');
SUBSETALIASSET( cIncomeAccountDim, vSubset, cAlias) ;

vSubset = 'Premium Input Accounts No Alias';
IF( SUBSETEXISTS( cIncomeAccountDim, vSubset ) = 0 );
  SUBSETCREATE( cIncomeAccountDim, vSubset);
ENDIF;
SUBSETMDXSET(cIncomeAccountDim, vSubset , '{FILTER(TM1SubsetAll(['|cIncomeAccountDim|'].['|cIncomeAccountDim|']), (['|cIncomeAccountDim|'].['|cIncomeAccountDim|'].CURRENTMEMBER.PROPERTIES("Premium Input Flag") = "Y"))}' ) ;
SUBSETMDXSET( cIncomeAccountDim, vSubset, '');

# Create Rate Input Accounts Subset
vSubset = 'Rate Input Accounts';
IF( SUBSETEXISTS( cIncomeAccountDim, vSubset ) = 0 );
  SUBSETCREATE( cIncomeAccountDim, vSubset);
ENDIF;
SUBSETMDXSET(cIncomeAccountDim, vSubset , '{TM1SORT( {TM1FILTERBYLEVEL( {FILTER( {TM1SUBSETALL( ['|cIncomeAccountDim|'] )}, ['|cIncomeAccountDim|'].[Rates Input Flag] = "Y")}, 0)}, ASC)}' ) ;
SUBSETMDXSET( cIncomeAccountDim, vSubset, '');
SUBSETALIASSET( cIncomeAccountDim, vSubset, cAlias) ;

#------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
################################
# CREATE PERIOD SUBSETS
################################
cPeriodDim = 'Period';

# Create Current and Future Months Subset
vSubset = 'Current and Future Months';
IF( SUBSETEXISTS( cPeriodDim, vSubset ) = 0 ) ;
  SUBSETCREATE( cPeriodDim, vSubset) ;
ELSE ;
    SUBSETDELETEALLELEMENTS( cPeriodDim, vSubset );
ENDIF;
SUBSETMDXSET (cPeriodDim, vSubset , '{LastPeriods(-72, TM1Member ( ['|cPeriodDim|'].[Current Year Months].item(1),0 ) ) }' ) ;
SUBSETMDXSET( cPeriodDim, vSubset, '') ;
SUBSETALIASSET( cPeriodDim, vSubset, 'PeriodName') ;

# Create Current and Future Months and Years Subset
vSubset = 'Current and Future Months and Years';
IF( SUBSETEXISTS( cPeriodDim, vSubset ) = 0 ) ;
  SUBSETCREATE( cPeriodDim, vSubset) ;
ELSE ;
    SUBSETDELETEALLELEMENTS( cPeriodDim, vSubset );
ENDIF;
SUBSETMDXSET (cPeriodDim, vSubset , '{ {LastPeriods(-72, TM1Member ( ['|cPeriodDim|'].[Current Year Months].item(1),0 ) ) } ,{LastPeriods(-6, TM1Member ( ['|cPeriodDim|'].[Current Year].item(0),0))} }' ) ;
SUBSETMDXSET( cPeriodDim, vSubset, '') ;
SUBSETALIASSET( cPeriodDim, vSubset, 'PeriodName') ;

# Create Current and Future Years and Months Subset
vSubset = 'Current and Future Years and Months';
IF( SUBSETEXISTS( cPeriodDim, vSubset ) = 0 ) ;
  SUBSETCREATE( cPeriodDim, vSubset) ;
ELSE ;
    SUBSETDELETEALLELEMENTS( cPeriodDim, vSubset );
ENDIF;
SUBSETMDXSET (cPeriodDim, vSubset , '{ {LastPeriods(-6, TM1Member ( ['|cPeriodDim|'].[Current Year].item(0),0))} , {LastPeriods(-72, TM1Member ( ['|cPeriodDim|'].[Current Year Months].item(1),0 ) ) } }' ) ;
SUBSETMDXSET( cPeriodDim, vSubset, '') ;
SUBSETALIASSET( cPeriodDim, vSubset, 'PeriodName') ;

# Create Current Input Year Subset
vSubset = 'Current Input Year';
IF( SUBSETEXISTS( cPeriodDim, vSubset ) = 0 ) ;
  SUBSETCREATE( cPeriodDim, vSubset) ;
ELSE ;
    SUBSETDELETEALLELEMENTS( cPeriodDim, vSubset );
ENDIF;
SUBSETMDXSET (cPeriodDim, vSubset , '{STRTOMEMBER("['|cPeriodDim|'].[Input" + [Global Variables].([Global Variables].[Current Year],[Global Variables Measure].[String Value]) + "]")}' ) ;
SUBSETMDXSET( cPeriodDim, vSubset, '') ;


# Create Current Input Year and Months Subset
vSubset = 'Current Input Year and Months';
IF( SUBSETEXISTS( cPeriodDim, vSubset ) = 0 ) ;
  SUBSETCREATE( cPeriodDim, vSubset) ;
ELSE ;
  SUBSETDELETEALLELEMENTS( cPeriodDim, vSubset );
ENDIF;
SUBSETMDXSET (cPeriodDim, vSubset , '{{TM1FILTERBYLEVEL({TM1DRILLDOWNMEMBER({STRTOMEMBER("[Period].[" + [Global Variables].([Global Variables].[Current Year],[Global Variables Measure].[String Value]) + "]")}, ALL, RECURSIVE )}, 0)}, {STRTOMEMBER("[Period].[Input" + [Global Variables].([Global Variables].[Current Year],[Global Variables Measure].[String Value]) + "]")} }') ;
SUBSETMDXSET( cPeriodDim, vSubset, '') ;  


# Create Current Year Subset
vSubset = 'Current Year';
IF( SUBSETEXISTS( cPeriodDim, vSubset ) = 0 ) ;
  SUBSETCREATE( cPeriodDim, vSubset) ;
ELSE ;
    SUBSETDELETEALLELEMENTS( cPeriodDim, vSubset );
ENDIF;
SUBSETMDXSET (cPeriodDim, vSubset , '{STRTOMEMBER("['|cPeriodDim|'].[" + [Global Variables].([Global Variables].[Current Year],[Global Variables Measure].[String Value]) + "]")}' ) ;
SUBSETMDXSET( cPeriodDim, vSubset, '') ;


# Create Current Year Months Subset
vSubset = 'Current Year Months';
IF( SUBSETEXISTS( cPeriodDim, vSubset ) = 0 ) ;
  SUBSETCREATE( cPeriodDim, vSubset) ;
ELSE ;
    SUBSETDELETEALLELEMENTS( cPeriodDim, vSubset );
ENDIF;
SUBSETMDXSET (cPeriodDim, vSubset , '{{STRTOMEMBER("['|cPeriodDim|'].[" + [Global Variables].([Global Variables].[Current Year],[Global Variables Measure].[String Value]) + "]")},{TM1FILTERBYLEVEL({TM1DRILLDOWNMEMBER({STRTOMEMBER("['|cPeriodDim|'].[" + [Global Variables].([Global Variables].[Current Year],[Global Variables Measure].[String Value]) + "]")}, ALL, RECURSIVE )}, 0)}}' ) ;
SUBSETMDXSET( cPeriodDim, vSubset, '') ;

 
# Create Future Periods Subset
vSubset = 'Future Periods';
IF( SUBSETEXISTS( cPeriodDim, vSubset ) = 0 ) ;
  SUBSETCREATE( cPeriodDim, vSubset) ;
ELSE ;
    SUBSETDELETEALLELEMENTS( cPeriodDim, vSubset );
ENDIF;
SUBSETMDXSET (cPeriodDim, vSubset , 'EXCEPT(EXCEPT(TM1FILTERBYLEVEL(FILTER(TM1SUBSETALL(['|cPeriodDim|']) , ['|cPeriodDim|'].[Act/Fcst] = "Forecast") , 0) , {['|cPeriodDim|'].[No Period]}) , TM1FILTERBYPATTERN(TM1SUBSETALL(['|cPeriodDim|']) , "*input*"))' ) ;
SUBSETMDXSET( cPeriodDim, vSubset, '') ;


# Create Future Periods PeriodName Subset
vSubset = 'Future Periods PeriodName';
IF( SUBSETEXISTS( cPeriodDim, vSubset ) = 0 ) ;
  SUBSETCREATE( cPeriodDim, vSubset) ;
ELSE ;
    SUBSETDELETEALLELEMENTS( cPeriodDim, vSubset );
ENDIF;
SUBSETMDXSET (cPeriodDim, vSubset , 'EXCEPT(EXCEPT(TM1FILTERBYLEVEL(FILTER(TM1SUBSETALL(['|cPeriodDim|']) , ['|cPeriodDim|'].[Act/Fcst] = "Forecast") , 0) , {['|cPeriodDim|'].[No Period]}) , TM1FILTERBYPATTERN(TM1SUBSETALL(['|cPeriodDim|']) , "*input*"))' ) ;
SUBSETMDXSET( cPeriodDim, vSubset, '') ;
SUBSETALIASSET( cPeriodDim, vSubset, 'PeriodName') ;


# Create Forecast Input Years Subset
vSubset = 'Forecast Input Years';
IF( SUBSETEXISTS( cPeriodDim, vSubset ) = 0 ) ;
  SUBSETCREATE( cPeriodDim, vSubset) ;
ELSE ;
    SUBSETDELETEALLELEMENTS( cPeriodDim, vSubset );
ENDIF;
SUBSETMDXSET (cPeriodDim, vSubset , '{FILTER( {TM1FILTERBYPATTERN( {TM1SUBSETALL( ['|cPeriodDim|'] )}, "*input*")}, ['|cPeriodDim|'].[Act/Fcst] = "Forecast")} ' ) ;
SUBSETMDXSET( cPeriodDim, vSubset, '') ;


# Create Input Years Subset
vSubset = 'Input Years';
IF( SUBSETEXISTS( cPeriodDim, vSubset ) = 0 ) ;
  SUBSETCREATE( cPeriodDim, vSubset) ;
ELSE ;
    SUBSETDELETEALLELEMENTS( cPeriodDim, vSubset );
ENDIF;
SUBSETMDXSET (cPeriodDim, vSubset , '{TM1FILTERBYPATTERN( {TM1SUBSETALL( ['|cPeriodDim|'] )}, "Input*")}' ) ;
SUBSETMDXSET( cPeriodDim, vSubset, '') ;    
 

# Create Years Subset
vSubset = 'Years';
IF( SUBSETEXISTS( cPeriodDim, vSubset ) = 0 ) ;
  SUBSETCREATE( cPeriodDim, vSubset) ;
ELSE ;
    SUBSETDELETEALLELEMENTS( cPeriodDim, vSubset );
ENDIF;
SUBSETMDXSET (cPeriodDim, vSubset , '{ EXCEPT( {TM1FILTERBYLEVEL( {TM1SUBSETALL( ['|cPeriodDim|'] )}, 2)}, { ['|cPeriodDim|'].[All Time YTD], ['|cPeriodDim|'].[All Time QTD], ['|cPeriodDim|'].[All Time ROY] }) }' ) ;
SUBSETMDXSET( cPeriodDim, vSubset, '') ;   

# Create Fixed Assets Subset
vSubset = 'Fixed Assets';
IF( SUBSETEXISTS( cPeriodDim, vSubset ) = 0 ) ;
  SUBSETCREATE( cPeriodDim, vSubset) ;
ELSE ;
    SUBSETDELETEALLELEMENTS( cPeriodDim, vSubset );
ENDIF;

SUBSETMDXSET (cPeriodDim, vSubset , '{FILTER( {TM1SORT( TM1FILTERBYLEVEL ({TM1SUBSETALL( ['|cPeriodDim|'] )}, 0), ASC)}, ['|cPeriodDim|'].CurrentMember.Name >= "202001")}' ) ;
SUBSETMDXSET( cPeriodDim, vSubset, '') ; 

# vSize = DIMSIZ( cPeriodDim );
# WHILE( vSize > 0 );
#     vElement = DIMNM( cPeriodDim, vSize );
#     IF( ELLEV( cPeriodDim, vElement ) = 0 & vElement @>= '202001' & SUBST(vElement, 1, 5) @<> 'Input' & vElement @<> 'No Period' );
#         SUBSETELEMENTINSERT( cPeriodDim, vSubset, vElement, 0 );
#     ENDIF;
#     vSize = vSize - 1;
# END;



#------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
################################
# CREATE ASSET TYPE SUBSETS
################################
cAssetTypeDim = 'Asset Type' ;

# Create Forms Subset
vSubset = 'Forms';
IF( SUBSETEXISTS( cAssetTypeDim, vSubset ) = 0 ) ;
  SUBSETCREATE( cAssetTypeDim, vSubset) ;
ELSE ;
    SUBSETDELETEALLELEMENTS( cAssetTypeDim, vSubset );
ENDIF;
SUBSETMDXSET (cAssetTypeDim, vSubset , '{TM1SORT( {FILTER( {TM1SUBSETALL( ['|cAssetTypeDim|'] )}, ['|cAssetTypeDim|'].[Flag for Forms] = "Y")}, ASC)}' ) ;
SUBSETMDXSET( cAssetTypeDim, vSubset, '') ;    

# Create Forms Leased Facility Subset
vSubset = 'Forms Leased Facility';
IF( SUBSETEXISTS( cAssetTypeDim, vSubset ) = 0 ) ;
  SUBSETCREATE( cAssetTypeDim, vSubset) ;
ELSE ;
    SUBSETDELETEALLELEMENTS( cAssetTypeDim, vSubset );
ENDIF;
SUBSETMDXSET (cAssetTypeDim, vSubset , '{TM1SORT( { EXCEPT( {FILTER( {TM1SUBSETALL( ['|cAssetTypeDim|'] )}, ['|cAssetTypeDim|'].[Flag for Forms] = "Y")}, { ['|cAssetTypeDim|'].[BLDG] }) }, ASC)}' ) ;
SUBSETMDXSET( cAssetTypeDim, vSubset, '') ;    

# Create Forms Owned Facility Subset
vSubset = 'Forms Owned Facility';
IF( SUBSETEXISTS( cAssetTypeDim, vSubset ) = 0 ) ;
  SUBSETCREATE( cAssetTypeDim, vSubset) ;
ELSE ;
    SUBSETDELETEALLELEMENTS( cAssetTypeDim, vSubset );
ENDIF;
SUBSETMDXSET (cAssetTypeDim, vSubset , '{TM1SORT( { EXCEPT( {FILTER( {TM1SUBSETALL( ['|cAssetTypeDim|'] )}, ['|cAssetTypeDim|'].[Flag for Forms] = "Y")}, { ['|cAssetTypeDim|'].[LHIMPR] }) }, ASC)}' ) ;
SUBSETMDXSET( cAssetTypeDim, vSubset, '') ;    

#------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
################################
# CREATE CLIENT SUBSETS
################################
cClientDim = 'Client' ;

# Create Client Alloc Clients Subset
vSubset = 'Client Alloc Clients';
IF( SUBSETEXISTS( cClientDim, vSubset ) = 0 ) ;
  SUBSETCREATE( cClientDim, vSubset) ;
ELSE ;
    SUBSETDELETEALLELEMENTS(cClientDim, vSubset );
ENDIF;
SUBSETMDXSET (cClientDim, vSubset , '{FILTER( {TM1FILTERBYLEVEL( {TM1SUBSETALL( ['|cClientDim|'] )}, 0)}, ['|cClientDim|'].[Client Alloc Flag] = "Y")}' ) ;
SUBSETMDXSET( cClientDim, vSubset, '') ;  

#------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
################################
# CREATE FIXED ASSETS ACCOUNT SUBSETS BASED ON ASSET TYPE
################################
cAccountDim = 'Fixed Assets Account' ;

# Create Gain Loss Account Subset
vSubset = 'Gain Loss Accounts';
IF( SUBSETEXISTS( cAccountDim, vSubset ) = 0 );
    SUBSETCREATE( cAccountDim, vSubset);
ELSE;
    SUBSETDELETEALLELEMENTS( cAccountDim, vSubset );
ENDIF;

vSize = DIMSIZ( cAssetTypeDim );
WHILE( vSize > 0 );
    vElement = DIMNM( cAssetTypeDim, vSize );
    IF( ELLEV( cAssetTypeDim, vElement ) = 0 & ATTRS(cAssetTypeDim, vElement, 'Gain Loss Account')@<>'');
        vATTRS = ATTRS(cAssetTypeDim, vElement, 'Gain Loss Account');
        SUBSETELEMENTINSERT( cAccountDim, vSubset, vATTRS, 0 );
    ENDIF;
    vSize = vSize - 1;
END;

# Create Depreciation Account Subset
vSubset = 'Depreciation Accounts';
IF( SUBSETEXISTS( cAccountDim, vSubset ) = 0 );
    SUBSETCREATE( cAccountDim, vSubset);
ELSE;
    SUBSETDELETEALLELEMENTS( cAccountDim, vSubset );
ENDIF;

vSize = DIMSIZ( cAssetTypeDim );
WHILE( vSize > 0 );
    vElement = DIMNM( cAssetTypeDim, vSize );
    IF( ELLEV( cAssetTypeDim, vElement ) = 0 & ATTRS(cAssetTypeDim, vElement, 'Depreciation Account')@<>'');
        vATTRS = ATTRS(cAssetTypeDim, vElement, 'Depreciation Account');
        SUBSETELEMENTINSERT( cAccountDim, vSubset, vATTRS, 0 );
    ENDIF;
    vSize = vSize - 1;
END;

#------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
################################
# CREATE FIXED ASSETS ENTITIES SUBSET
################################
cEntityDim = 'Entity' ;

# Create Fixed Assets Entities Subset
vSubset = 'Fixed Assets Entities';
IF( SUBSETEXISTS( cEntityDim, vSubset ) = 0 ) ;
  SUBSETCREATE( cEntityDim, vSubset) ;
ELSE ;
    SUBSETDELETEALLELEMENTS(cEntityDim, vSubset );
ENDIF;
SUBSETMDXSET (cEntityDim, vSubset , '{FILTER( {TM1FILTERBYLEVEL( {TM1SUBSETALL( ['|cEntityDim|'] )}, 0)}, ['|cEntityDim|'].[FA Ent Flag] = "Y")}' ) ;
SUBSETMDXSET( cEntityDim, vSubset, '') ;  
#Section Metadata

#****Begin: Generated Statements***
#****End: Generated Statements****

#Section Data

#****Begin: Generated Statements***
#****End: Generated Statements****


################################
# CREATE SUBSETS
################################
#========================
# Source all applicable dimensions on the server
#========================
# subsetcreatebymdx use mdx to create the subset
vDim = '}Dimensions';
IF ( dDim @= 'Client' ) ;
  cAlias = 'Name - ID' ;
ELSE ;
  cAlias = 'ID - Name' ;
ENDIF ;
IF ( DIMSIZ ( dDim ) <> 0 & ELLEV ( vDim, dDim ) = 0 & SCAN( '}' , dDim ) = 0 & dDim @<> 'Period' ) ;

  # Create 0 Level Items Subsets
  cLevel0 = '0 Level Items' ;
  IF ( SUBSETEXISTS (dDim , cLevel0 ) = 0 ) ;
    SUBSETCREATE ( dDim , cLevel0 ) ;
  ENDIF ;
    # dims without TBA elems
    IF ( DIMIX ( dDim , 'TBA ' | dDim ) = 0 ) ;  
      SUBSETMDXSET ( dDim , cLevel0 , '{TM1SORT( {TM1FILTERBYLEVEL( {TM1SUBSETALL( ['|dDim|'] )}, 0)}, ASC)}') ;
      SUBSETMDXSET( dDim , cLevel0 , '') ;
    # dims with TBA elems
    ELSE ;
      SUBSETMDXSET ( dDim , cLevel0 , '{Union( {TM1FILTERBYPATTERN( {TM1SUBSETALL( ['|dDim|'] )}, "*TBA '|dDim|'*")}, {TM1SORT( {TM1FILTERBYLEVEL( {TM1SUBSETALL( ['|dDim|'] )}, 0)}, ASC)} )}') ;
      SUBSETMDXSET( dDim , cLevel0 , '') ;
    ENDIF ; 
  # Turn on Alias if it exists
  IF ( DIMIX ( '}ElementAttributes_' | dDim , cAlias ) <> 0 ) ;
    SUBSETALIASSET( dDim , cLevel0 , cAlias ) ;
  ENDIF ;
  
  
  # Create 0 Level Items No TBA Subsets for dims with TBA elems
  cLevel0NoTBA = '0 Level Items No TBA' ;
  IF ( DIMIX ( dDim , 'TBA ' | dDim ) <> 0 ) ;
    IF( SUBSETEXISTS (dDim , cLevel0NoTBA ) = 0 ) ;
        SUBSETCREATE ( dDim , cLevel0NoTBA ) ;
    ENDIF ;
    SUBSETMDXSET ( dDim , cLevel0NoTBA , '{TM1SORT( { EXCEPT( {TM1FILTERBYLEVEL( {TM1SUBSETALL( ['|dDim|'] )}, 0)}, { ['|dDim|'].[TBA '| dDim|'] }) }, ASC)}') ;
    SUBSETMDXSET( dDim , cLevel0NoTBA , '') ;
    # Turn on Alias if it exists
    IF ( DIMIX ( '}ElementAttributes_' | dDim , cAlias ) <> 0 ) ;
      SUBSETALIASSET( dDim , cLevel0NoTBA , cAlias ) ;
    ENDIF ;
  ENDIF ;
  
  
  # Create 0 Level Items No Alias Subsets only for Dims where cAlias exists
  IF ( DIMIX ( '}ElementAttributes_' | dDim , cAlias ) <> 0 ) ;
    cLevel0NoAlias = '0 Level Items No Alias' ;
    IF ( SUBSETEXISTS (dDim , cLevel0NoAlias ) = 0 ) ;
      SUBSETCREATE ( dDim , cLevel0NoAlias ) ;
    ENDIF ;
    # dims without TBA elems
    IF ( DIMIX ( dDim , 'TBA ' | dDim ) = 0 ) ;
      SUBSETMDXSET ( dDim , cLevel0NoAlias , '{TM1SORT( {TM1FILTERBYLEVEL( {TM1SUBSETALL( ['|dDim|'] )}, 0)}, ASC)}') ;
      SUBSETMDXSET( dDim , cLevel0NoAlias , '') ;
    # dims with TBA elems
    ELSE ;
      SUBSETMDXSET ( dDim , cLevel0NoAlias , '{Union( {TM1FILTERBYPATTERN( {TM1SUBSETALL( ['|dDim|'] )}, "*TBA '|dDim|'*")}, {TM1SORT( {TM1FILTERBYLEVEL( {TM1SUBSETALL( ['|dDim|'] )}, 0)}, ASC)} )}') ;
      SUBSETMDXSET( dDim , cLevel0NoAlias , '') ;
    ENDIF ;  
  ENDIF ;
  

  
ENDIF ;



################################
# CREATE PERIOD SUBSETS
################################
IF ( dDim @= 'Period' ) ;

  # Create 0 Level Items Subsets
  cLevel0 = '0 Level Items' ;
  IF ( SUBSETEXISTS (dDim , cLevel0 ) = 0 ) ;
    SUBSETCREATE ( dDim , cLevel0 ) ;
  ENDIF ;
  SUBSETMDXSET ( dDim , cLevel0 , '{TM1SORTBYINDEX( {TM1FILTERBYLEVEL( {TM1SUBSETALL( ['|dDim|'] )}, 0)}, ASC)}') ;
  SUBSETMDXSET( dDim , cLevel0 , '') ;
  SUBSETALIASSET( dDim , cLevel0 , 'PeriodName' ) ;
  
  # Create 0 Level Items No Alias Subsets
  cLevel0NoAlias = '0 Level Items No Alias' ;
  IF ( SUBSETEXISTS (dDim , cLevel0NoAlias ) = 0 ) ;
    SUBSETCREATE ( dDim , cLevel0NoAlias ) ;
  ENDIF ;
  SUBSETMDXSET ( dDim , cLevel0NoAlias , '{TM1SORTBYINDEX( {TM1FILTERBYLEVEL( {TM1SUBSETALL( ['|dDim|'] )}, 0)}, ASC)}') ;
  SUBSETMDXSET( dDim , cLevel0NoAlias , '') ;
  
ENDIF ;

 
################################
# CREATE CURRENCY and PLANNING CURRENCY SUBSETS
################################
IF ( dDim @= 'Currency' % dDim @= 'Planning Currency' ) ;
  
  # Create Currency Codes Subset
  vSubset = 'Currency Codes';
  IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
     SUBSETCREATE( dDim, vSubset);
  ENDIF;

  SUBSETMDXSET(dDim, vSubset , '{TM1FILTERBYLEVEL({INTERSECT({ EXCEPT( {TM1SUBSETALL( ['|dDim|'] )},{TM1FILTERBYPATTERN( {TM1SUBSETALL( ['|dDim|'] )}, "*to*")})},{ EXCEPT( {TM1SUBSETALL( ['|dDim|'] )},{TM1FILTERBYPATTERN( {TM1SUBSETALL( ['|dDim|'] )}, "*calc*")})})}, 0)}' ) ;
  SUBSETMDXSET( dDim, vSubset, '');
  #SUBSETALIASSET( dDim, vSubset, cAlias) ;
  
  # Create Reporting Currencies Subset
  vSubset = 'Reporting Currencies';
  IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
     SUBSETCREATE( dDim, vSubset);
  ENDIF;

  SUBSETMDXSET(dDim, vSubset , '{TM1FILTERBYPATTERN( {TM1SUBSETALL( ['|dDim|'] )}, "*Reporting*")}' ) ;
  SUBSETMDXSET( dDim, vSubset, '');
  
  # Create Reporting and Currency Codes Subset for Planning Currency only 
  IF ( dDim @= 'Planning Currency' ) ;
    vSubset = 'Reporting and Currency Codes';
    IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
       SUBSETCREATE( dDim, vSubset);
    ENDIF;

    SUBSETMDXSET(dDim, vSubset , '{INTERSECT( {INTERSECT({ EXCEPT( {TM1SUBSETALL( ['|dDim|'] )},{TM1FILTERBYPATTERN( {TM1SUBSETALL( ['|dDim|'] )}, "*to*")})},
    { EXCEPT( {TM1SUBSETALL( ['|dDim|'] )},{TM1FILTERBYPATTERN( {TM1SUBSETALL( ['|dDim|'] )}, "*calc*")})})},
    { EXCEPT( {TM1SUBSETALL( ['|dDim|'] )},{TM1FILTERBYPATTERN( {TM1SUBSETALL( ['|dDim|'] )}, "*control*")})})}' ) ;
    SUBSETMDXSET( dDim, vSubset, '');
    SUBSETALIASSET( dDim, vSubset, cAlias) ;
  ENDIF ;
  
ENDIF ;

################################
# CREATE ACCOUNT SUBSETS
################################
IF ( dDim @= 'Account' % dDim @= 'Partner Account' ) ;

# Create General Expense Accounts Subset
vSubsetTemp = 'General Expense Accounts Temp';
vSubset = 'General Expense Accounts';
IF( SUBSETEXISTS( dDim, vSubsetTemp ) = 0 );
  SUBSETCREATE( dDim, vSubsetTemp);
ELSE;
  SUBSETDELETEALLELEMENTS( dDim, vSubsetTemp );
ENDIF;
IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
  SUBSETCREATE( dDim, vSubset);
ELSE;
  SUBSETDELETEALLELEMENTS( dDim, vSubset );
ENDIF; 
  
vGEDim = 'General Expense Account' ;
vSize = DIMSIZ( vGEDim );
WHILE( vSize > 0 );
  vElement = DIMNM( vGEDim, vSize );
  IF( DIMIX( dDim, vElement ) <> 0 & DIMIX( vGEDim, vElement ) <> 0 );
    SUBSETELEMENTINSERT( dDim, vSubsetTemp, vElement, 0 );
  ENDIF;
  vSize = vSize - 1;
END;
  
SUBSETALIASSET( dDim, vSubsetTemp, cAlias) ;
SUBSETMDXSET( dDim, vSubset,'{HIERARCHIZE( {Account.['|vSubsetTemp|']})}');
SUBSETMDXSET( dDim, vSubset, '');

SUBSETALIASSET( dDim, vSubset, cAlias) ;
SUBSETDESTROY( dDim, vSubsetTemp);

# Create Chargeback Account Subset
vSubset = 'Chargeback Account';
IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
  SUBSETCREATE( dDim, vSubset);
ENDIF;

SUBSETMDXSET( dDim, vSubset , '{DESCENDANTS(['|dDim|'].['|dDim|'].[CHGBK MGT FEE] , 99 , LEAVES)}' ) ;
SUBSETMDXSET( dDim, vSubset, '');

# Create ESS Chargeback Account Subset
vSubset = 'ESS Chargeback Account';
IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
  SUBSETCREATE( dDim, vSubset);
ENDIF;

SUBSETMDXSET( dDim, vSubset , '{FILTER( {TM1FILTERBYLEVEL( {TM1SUBSETALL( ['|dDim|'] )}, 0)}, ['|dDim|'].[ESS Allocation Flag] = "Y")}' ) ;
SUBSETMDXSET( dDim, vSubset, '');

# Create GT Chargeback Account Subset
vSubset = 'GT Chargeback Account';
IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
  SUBSETCREATE( dDim, vSubset);
ENDIF;

SUBSETMDXSET( dDim, vSubset , '{FILTER( {TM1FILTERBYLEVEL( {TM1SUBSETALL( ['|dDim|'] )}, 0)}, ['|dDim|'].[GT Allocation Flag] = "Y")}' ) ;
SUBSETMDXSET( dDim, vSubset, '');

# Create Invest Income Account Subset
vSubset = 'Invest Income Account';
IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
  SUBSETCREATE( dDim, vSubset);
ENDIF;

SUBSETMDXSET( dDim, vSubset , '{FILTER( {TM1SUBSETALL( ['|dDim|'] )}, ['|dDim|'].[Invest Inc Alloc Flag] = "Y")}' ) ;
SUBSETMDXSET( dDim, vSubset, '');


# Create Managerial Account Subset
vSubset = 'Managerial Accts';
IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
  SUBSETCREATE( dDim, vSubset);
ENDIF;

SUBSETMDXSET( dDim, vSubset , '{FILTER(DISTINCT({DESCENDANTS({['|dDim|'].['|dDim|'].[1:MANAGERIAL ACCTS]})}), (STRTOVALUE(['|dDim|'].['|dDim|'].CURRENTMEMBER.PROPERTIES("ELEMENT_LEVEL")) <> 0))}' ) ;
SUBSETMDXSET( dDim, vSubset, '');
ENDIF;

#------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

################################
# CREATE CENTER & PARTNER CENTER SUBSETS
################################
IF ( dDim @= 'Center' % dDim @= 'Partner Center' ) ;

# Create Profit Centers Subset
vSubset = '0 Level Profit Centers';
IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
  SUBSETCREATE( dDim, vSubset);
ENDIF;
SUBSETMDXSET(dDim, vSubset , '{TM1FILTERBYLEVEL(FILTER(TM1SubsetAll(['|dDim|'].['|dDim|']), (['|dDim|'].['|dDim|'].CURRENTMEMBER.PROPERTIES("Profit Center") = "Y")), 0)}' ) ;
SUBSETMDXSET( dDim, vSubset, '');
SUBSETALIASSET( dDim, vSubset, cAlias) ;

# Create ESS Source Centers Subset
vSubset = 'ESS Source Centers';
IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
  SUBSETCREATE( dDim, vSubset);
ENDIF;
SUBSETMDXSET(dDim, vSubset , '{FILTER(TM1SubsetAll(['|dDim|'].['|dDim|']), (['|dDim|'].['|dDim|'].CURRENTMEMBER.PROPERTIES("ESS Source Flag") = "Y"))}' ) ;
SUBSETMDXSET( dDim, vSubset, '');
SUBSETALIASSET( dDim, vSubset, cAlias) ;  

# Create ESS Recip Centers Subset
vSubset = 'ESS Recip Centers';
IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
  SUBSETCREATE( dDim, vSubset);
ENDIF;
SUBSETMDXSET(dDim, vSubset , '{FILTER(TM1SubsetAll(['|dDim|'].['|dDim|']), (['|dDim|'].['|dDim|'].CURRENTMEMBER.PROPERTIES("ESS Recip Flag") = "Y"))}' ) ;
SUBSETMDXSET( dDim, vSubset, '');
SUBSETALIASSET( dDim, vSubset, cAlias) ;  

# Create GT Source Centers Subset
vSubset = 'GT Source Centers';
IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
  SUBSETCREATE( dDim, vSubset);
ENDIF;
SUBSETMDXSET(dDim, vSubset , '{FILTER(TM1SubsetAll(['|dDim|'].['|dDim|']), (['|dDim|'].['|dDim|'].CURRENTMEMBER.PROPERTIES("GT Source Flag") = "Y"))}' ) ;
SUBSETMDXSET( dDim, vSubset, '');
SUBSETALIASSET( dDim, vSubset, cAlias) ; 

# Create GT Recip Centers Subset
vSubset = 'GT Recip Centers';
IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
  SUBSETCREATE( dDim, vSubset);
ENDIF;
SUBSETMDXSET(dDim, vSubset , '{FILTER(TM1SubsetAll(['|dDim|'].['|dDim|']), (['|dDim|'].['|dDim|'].CURRENTMEMBER.PROPERTIES("GT Recip Flag") = "Y"))}' ) ;
SUBSETMDXSET( dDim, vSubset, '');
SUBSETALIASSET( dDim, vSubset, cAlias) ; 

# Create PC Source Centers Subset
vSubset = 'PC Source Centers';
IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
  SUBSETCREATE( dDim, vSubset);
ENDIF;
SUBSETMDXSET(dDim, vSubset , '{FILTER(TM1SubsetAll(['|dDim|'].['|dDim|']), (['|dDim|'].['|dDim|'].CURRENTMEMBER.PROPERTIES("PC Source Flag") = "Y"))}' ) ;
SUBSETMDXSET( dDim, vSubset, '');
SUBSETALIASSET( dDim, vSubset, cAlias) ; 

# Create PC Recip Centers Subset
vSubset = 'PC Recip Centers';
IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
  SUBSETCREATE( dDim, vSubset);
ENDIF;
SUBSETMDXSET(dDim, vSubset , '{FILTER(TM1SubsetAll(['|dDim|'].['|dDim|']), (['|dDim|'].['|dDim|'].CURRENTMEMBER.PROPERTIES("PC Recip Flag") = "Y"))}' ) ;
SUBSETMDXSET( dDim, vSubset, '');
SUBSETALIASSET( dDim, vSubset, cAlias) ; 

# Create 0 Level Items Active Subset
vSubset = '0 Level Items Active';
IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
  SUBSETCREATE( dDim, vSubset);
ENDIF;
SUBSETMDXSET(dDim, vSubset , '{TM1SORT( {TM1FILTERBYLEVEL( {FILTER( {TM1SUBSETALL( ['|dDim|'] )}, ['|dDim|'].[Inactive] = "")}, 0)}, ASC)}' ) ;
SUBSETMDXSET( dDim, vSubset, '');
SUBSETALIASSET( dDim, vSubset, cAlias) ;

# Create C Level Profit  Subset
vSubset = 'C Level Profit Centers';
IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
  SUBSETCREATE( dDim, vSubset);
ENDIF;
SUBSETMDXSET(dDim, vSubset , '{FILTER(TM1SubsetAll(['|dDim|'].['|dDim|']), (['|dDim|'].['|dDim|'].CURRENTMEMBER.PROPERTIES("PC Confine Flag") = "Y"))}' ) ;
SUBSETMDXSET( dDim, vSubset, '');
SUBSETALIASSET( dDim, vSubset, cAlias) ;

# Create C Level Profit  Subset - No Alias
vSubset = 'C Level Profit Centers No Alias';
IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
  SUBSETCREATE( dDim, vSubset);
ENDIF;
SUBSETMDXSET(dDim, vSubset , '{FILTER(TM1SubsetAll(['|dDim|'].['|dDim|']), (['|dDim|'].['|dDim|'].CURRENTMEMBER.PROPERTIES("PC Confine Flag") = "Y"))}' ) ;
SUBSETMDXSET( dDim, vSubset, '');

# Create Corporate Centers Subset
vSubset = 'Corporate Centers';
IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
  SUBSETCREATE( dDim, vSubset);
ENDIF;
SUBSETMDXSET(dDim, vSubset , '{FILTER( {TM1SUBSETALL( ['|dDim|'] )}, ['|dDim|'].[Corporate Center] = "Y")}');
SUBSETMDXSET( dDim, vSubset, '');
SUBSETALIASSET( dDim, vSubset, cAlias) ;

# Create Corporate PC Subset
vSubset = 'Corporate Profit Centers';
IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
  SUBSETCREATE( dDim, vSubset);
ENDIF;
SUBSETMDXSET(dDim, vSubset , '{FILTER( {TM1SUBSETALL( ['|dDim|'] )}, ['|dDim|'].[Corporate PC] = "Y")}');
SUBSETMDXSET( dDim, vSubset, '');
SUBSETALIASSET( dDim, vSubset, cAlias) ;

# Create Corporate PC Subset - No Alias
vSubset = 'Corporate Profit Centers No Alias';
IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
  SUBSETCREATE( dDim, vSubset);
ENDIF;
SUBSETMDXSET(dDim, vSubset , '{FILTER( {TM1SUBSETALL( ['|dDim|'] )}, ['|dDim|'].[Corporate PC] = "Y")}');
SUBSETMDXSET( dDim, vSubset, '');

ENDIF;

#------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

################################
# CREATE ENTITY & PARTNER ENTITY SUBSETS
################################
IF ( dDim @= 'Entity' % dDim @= 'Partner Entity' ) ;

# first create subsets and if they exist, clear them
vSize = DIMSIZ( dDim );
WHILE( vSize > 0 );
  vElement = DIMNM( dDim, vSize );
  # look up Local Currency 
  vCurrencyCode = ATTRS ( dDim, vElement , 'Local Currency' ) ;
  # create subsets on Entity Dim
  IF ( vCurrencyCode @<> '' ) ;
    vSubset = vCurrencyCode | ' Leaf Entities temp';
    vSubsetFinal = vCurrencyCode | ' Leaf Entities';
    IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
      SUBSETCREATE( dDim, vSubset, 0 );
    ELSE;
      SUBSETDELETEALLELEMENTS( dDim, vSubset );
    ENDIF;
    IF( SUBSETEXISTS( dDim, vSubsetFinal ) = 0 );
      SUBSETCREATE( dDim, vSubsetFinal, 0 );
    ELSE;
      SUBSETDELETEALLELEMENTS( dDim, vSubsetFinal );
    ENDIF; 
  ENDIF ;
  vAltCurrencyCode = ATTRS ( dDim, vElement , 'Alternate Currency' ) ;
  # create subsets on Entity Dim
  IF ( vAltCurrencyCode @<> '' ) ;
    vSubset = vAltCurrencyCode | ' Leaf Entities temp';
    vSubsetFinal = vAltCurrencyCode | ' Leaf Entities';
    IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
      SUBSETCREATE( dDim, vSubset, 0 );
    ELSE;
      SUBSETDELETEALLELEMENTS( dDim, vSubset );
    ENDIF;
    IF( SUBSETEXISTS( dDim, vSubsetFinal ) = 0 );
      SUBSETCREATE( dDim, vSubsetFinal, 0 );
    ELSE;
      SUBSETDELETEALLELEMENTS( dDim, vSubsetFinal );
    ENDIF; 
  ENDIF ;
  vSize = vSize - 1;
END;

# now that temp subsets are created and empty, populate them 
vSize = DIMSIZ( dDim );
WHILE( vSize > 0 );
  vElement = DIMNM( dDim, vSize );
  # look up Local Currency 
  vCurrencyCode = ATTRS ( dDim, vElement , 'Local Currency' ) ;
  # create subsets on Entity Dim
  IF ( vCurrencyCode @<> '' ) ;
    vSubset = vCurrencyCode | ' Leaf Entities temp';
  ENDIF ;  
  # populate subset USD 
  IF ( ELLEV( dDim, vElement ) = 0 );
    SUBSETELEMENTINSERT( dDim, 'USD Leaf Entities temp', vElement, 0 );
  ENDIF ;
  # populate subset EUR 
  IF ( vElement @= 'TBA Entity' % ELLEV( dDim, vElement ) = 0 & ( vCurrencyCode @= 'EUR' % vCurrencyCode @= 'GBP' ) ); 
    SUBSETELEMENTINSERT( dDim, 'EUR Leaf Entities temp', vElement, 0 );  
  ENDIF ;
  # populate subset GBP 
  IF ( vElement @= 'TBA Entity' % ELLEV( dDim, vElement ) = 0 & ( vCurrencyCode @= 'GBP' % vCurrencyCode @= 'EUR' ) ) ;
     SUBSETELEMENTINSERT( dDim, 'GBP Leaf Entities temp', vElement, 0 );
  ENDIF ; 
  # populate subsets except USD and GBP and EUR
  IF ( ELLEV( dDim, vElement ) = 0 & ( vCurrencyCode @<> 'USD' & vCurrencyCode @<> 'GBP' & vCurrencyCode @<> 'EUR' ) ) ;
    IF( vCurrencyCode @<> '' & vCurrencyCode @= SUBST(vSubset , 1 , 3) );
      SUBSETELEMENTINSERT( dDim, vSubset, vElement, 0 );
      IF ( SUBSETELEMENTEXISTS ( dDim, vSubset, 'TBA Entity' ) = 0 ) ;
        SUBSETELEMENTINSERT( dDim, vSubset, 'TBA Entity', 0 );
      ENDIF ;
    ENDIF;
  ENDIF ;
  SUBSETALIASSET( dDim, vSubset, cAlias) ;
  vSize = vSize - 1;
END;

# now that temp subsets are created, create the final
vSize = DIMSIZ( dDim );
WHILE( vSize > 0 );
  vElement = DIMNM( dDim, vSize );
  # look up Local Currency 
  vCurrencyCode = ATTRS ( dDim, vElement , 'Local Currency' ) ;
  # create subsets on Entity Dim
  IF ( vCurrencyCode @<> '' ) ;
    vSubset = vCurrencyCode | ' Leaf Entities temp';
    vSubsetFinal = vCurrencyCode | ' Leaf Entities';
    SUBSETMDXSET(dDim, vSubsetFinal, '{TM1SORT( {TM1SubsetToSet(['|dDim|'].['|dDim|'] , "'|vSubset|'")}, ASC)}' ) ;
    SUBSETMDXSET( dDim, vSubsetFinal, '');
    SUBSETALIASSET( dDim, vSubsetFinal, cAlias) ;
  ENDIF ;  
  # look up Alt Currency 
  vAltCurrencyCode = ATTRS ( dDim, vElement , 'Alternate Currency' ) ;
  # create subsets on Entity Dim
  IF ( vAltCurrencyCode @<> '' ) ;
    vSubset = vAltCurrencyCode | ' Leaf Entities temp';
    vSubsetFinal = vAltCurrencyCode | ' Leaf Entities';
    SUBSETMDXSET(dDim, vSubsetFinal, '{TM1SORT( {TM1SubsetToSet(['|dDim|'].['|dDim|'] , "'|vSubset|'")}, ASC)}' ) ;
    SUBSETMDXSET( dDim, vSubsetFinal, '');
    SUBSETALIASSET( dDim, vSubsetFinal, cAlias) ;
  ENDIF ;  
  vSize = vSize - 1;
END;

# now that final subsets are created, delete the temp
cEntitySubs = '}Subsets_Entity' ;
vSize = DIMSIZ( cEntitySubs );
WHILE( vSize > 0 );
  vSubset = DIMNM( cEntitySubs, vSize );
  IF ( DIMIX ( 'Currency' , SUBST ( vSubset , 1 , 3 ) ) <> 0 & SUBST ( vSubset , 19 , 4 ) @= 'temp' ) ;
    SUBSETDESTROY( dDim, vSubset ) ;
  ENDIF ;   
  vSize = vSize - 1;
END;

# Create Invest Income  Subset
vSubset = 'Invest Income Entities';
IF( SUBSETEXISTS( dDim, vSubset ) = 0 );
  SUBSETCREATE( dDim, vSubset);
ENDIF;
SUBSETMDXSET(dDim, vSubset , '{FILTER( {TM1SUBSETALL( ['|dDim|'] )}, ['|dDim|'].[Inv Inc Flag] = "Y")}' ) ;
SUBSETMDXSET( dDim, vSubset, '');
SUBSETALIASSET( dDim, vSubset, cAlias) ;  

# Create Entity Recip Entities Subset
vSubset = 'Entity Recip Entities';
IF( SUBSETEXISTS(dDim , vSubset ) = 0 ) ;
  SUBSETCREATE( dDim, vSubset) ;
ELSE ;
    SUBSETDELETEALLELEMENTS(dDim, vSubset );
ENDIF;
SUBSETMDXSET (dDim, vSubset , '{FILTER( {TM1FILTERBYLEVEL({TM1SUBSETALL( ['|dDim|'] )}, 0)}, ['|dDim|'].[Entity Recip Flag] = "Y")}' ) ;
SUBSETMDXSET( dDim, vSubset, '') ;  

# Create Entity GAAP Subset
vSubset = 'Entity GAAP Entities';
IF( SUBSETEXISTS(dDim , vSubset ) = 0 ) ;
  SUBSETCREATE( dDim, vSubset) ;
ELSE ;
    SUBSETDELETEALLELEMENTS(dDim, vSubset );
ENDIF;
SUBSETMDXSET (dDim, vSubset , '{DISTINCT({DESCENDANTS(['|dDim|'].['|dDim|'].[1:ENTITY GAAP])})}' ) ;
SUBSETMDXSET( dDim, vSubset, '') ;  

# Create Entity STAT Subset
vSubset = 'Entity STAT Entities';
IF( SUBSETEXISTS(dDim , vSubset ) = 0 ) ;
  SUBSETCREATE( dDim, vSubset) ;
ELSE ;
    SUBSETDELETEALLELEMENTS(dDim, vSubset );
ENDIF;
SUBSETMDXSET (dDim, vSubset , '{DISTINCT({DESCENDANTS(['|dDim|'].['|dDim|'].[2:ENTITY STAT])})}' ) ;
SUBSETMDXSET( dDim, vSubset, '') ; 

# Create 0 Level No TBA No Alias Subset
vSubset = '0 Level Items No TBA No Alias';
IF( SUBSETEXISTS(dDim , vSubset ) = 0 ) ;
  SUBSETCREATE( dDim, vSubset) ;
ELSE ;
    SUBSETDELETEALLELEMENTS(dDim, vSubset );
ENDIF;
SUBSETMDXSET (dDim, vSubset , 'EXCEPT(TM1FILTERBYLEVEL(TM1SUBSETALL(['|dDim|'].['|dDim|']) , 0) , {['|dDim|'].['|dDim|'].[1:ENTITY GAAP Orphans^TBA Entity]})' ) ;
SUBSETMDXSET( dDim, vSubset, '') ; 

ENDIF;


#------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

################################
# CREATE PRODUCT AND PARTNER PRODUCT SUBSETS
################################
IF ( dDim @= 'Product' % dDim @= 'Partner Product' ) ;

# Create Plan Products No Alias Subset
vSubset = 'Plan Products No Alias' ;
IF( SUBSETEXISTS( dDim, vSubset ) = 0 ) ;
  SUBSETCREATE( dDim, vSubset) ;
ENDIF;
# MDX here will exclude TBA elem then union so that TBA will show up first for dropdowns
SUBSETMDXSET (dDim, vSubset ,'{UNION({['|dDim|'].[TBA Product]},{ EXCEPT( {FILTER( {TM1FILTERBYLEVEL( {TM1SUBSETALL( ['|dDim|'] )}, 0)}, ['|dDim|'].[Plan Product] = "Y")}, { ['|dDim|'].[TBA Product] }) })}' ) ;
SUBSETMDXSET( dDim, vSubset, '') ;
 
# Create Plan Products Subset 
vSubset = 'Plan Products' ;
IF( SUBSETEXISTS( dDim, vSubset ) = 0 ) ; 
  SUBSETCREATE( dDim, vSubset) ;
ENDIF;
# MDX here will exclude TBA elem then union so that TBA will show up first for dropdowns
SUBSETMDXSET (dDim, vSubset , '{UNION({['|dDim|'].[TBA Product]},{ EXCEPT( {FILTER( {TM1FILTERBYLEVEL( {TM1SUBSETALL( ['|dDim|'] )}, 0)}, ['|dDim|'].[Plan Product] = "Y")}, { ['|dDim|'].[TBA Product] }) })}' ) ;
SUBSETMDXSET( dDim, vSubset, '') ;
SUBSETALIASSET( dDim, vSubset, cAlias) ;

# Create Plan Products No TBA Subset 
vSubset = 'Plan Products No TBA' ;
IF( SUBSETEXISTS( dDim, vSubset ) = 0 ) ; 
  SUBSETCREATE( dDim, vSubset) ;
ENDIF;
# MDX here will exclude TBA elem 
SUBSETMDXSET (dDim, vSubset , '{ EXCEPT( {FILTER( {TM1FILTERBYLEVEL( {TM1SUBSETALL( ['|dDim|'] )}, 0)}, ['|dDim|'].[Plan Product] = "Y")}, { ['|dDim|'].[TBA Product] }) }' ) ;
SUBSETMDXSET( dDim, vSubset, '') ;
SUBSETALIASSET( dDim, vSubset, cAlias) ;

# Create Prod Recip Products Subset 
vSubset = 'Prod Recip Products' ;
IF( SUBSETEXISTS( dDim, vSubset ) = 0 ) ; 
  SUBSETCREATE( dDim, vSubset) ;
ENDIF;
 
SUBSETMDXSET (dDim, vSubset , '{FILTER( {TM1FILTERBYLEVEL( {TM1SUBSETALL( ['|dDim|'] )}, 0)}, ['|dDim|'].[Prod Recip Flag] = "Y")}' ) ;
SUBSETMDXSET( dDim, vSubset, '') ;
SUBSETALIASSET( dDim, vSubset, cAlias) ;

# Create C Level Products Subset 
vSubset = 'C Level Products' ;
IF( SUBSETEXISTS( dDim, vSubset ) = 0 ) ; 
  SUBSETCREATE( dDim, vSubset) ;
ENDIF;
 
SUBSETMDXSET (dDim, vSubset , '{FILTER(TM1SubsetAll(['|dDim|'].['|dDim|']), (['|dDim|'].['|dDim|'].CURRENTMEMBER.PROPERTIES("Prod Confine Flag") = "Y"))}' ) ;
SUBSETMDXSET( dDim, vSubset, '') ;

ENDIF;
#------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------



#Section Epilog

#****Begin: Generated Statements***
#****End: Generated Statements****

#===========================           FINAL CODE IN EPILOG        ==============================

sLogFile = GETPROCESSERRORFILEDIRECTORY | GETPROCESSERRORFILENAME;
sNow = TIMST( NOW,'\M \D, \Y at \H \p \i min \s sec');
vNowEnd = NOW;

# Calculate Duration
# ----------------------------
vDelta = ( vNowEnd - vNowStart ) ;
vDuration = vDelta * 1440 ;
vDurMin = INT(vDuration);
IF (vDurMin = 0) ;
  vDurSec = ROUND ( vDuration * 60 ) ;
ELSE;
  vDurSec = ROUND (MOD ( vDuration , vDurMin ) * 60) ;
ENDIF;

sDuration = NUMBERTOSTRING(vDurMin) | ' min ' | NUMBERTOSTRING(vDurSec) | ' sec' ;

# Duration
# --------------
CELLPUTS( sDuration , sCube , CurrLine , sDate , sProcess , 'Duration' );
CELLPUTS( sDuration , sCube , CurrLine , 'Latest' , sProcess , 'Duration' );

# End Time
# ----------------
CELLPUTS( sNow , sCube , CurrLine , sDate , sProcess , 'End Time' );
CELLPUTS( sNow , sCube , CurrLine , 'Latest' , sProcess , 'End Time' );

# Record Count
# ----------------------
CELLPUTN( nRecordCount , sCube , CurrLine , sDate , sProcess , 'Record Count' );
CELLPUTN( nRecordCount , sCube , CurrLine , 'Latest' , sProcess , 'Record Count' );

# Skipped Records
# ---------------------------
CELLPUTN( nRecordSkip , sCube , CurrLine , sDate , sProcess , 'Skipped Records' );
CELLPUTN( nRecordSkip , sCube , CurrLine , 'Latest' , sProcess , 'Skipped Records' );

# Processed Records
# -------------------------------
nRecordProcess = nRecordCount - nRecordSkip;
CELLPUTN( nRecordProcess , sCube , CurrLine , sDate , sProcess , 'Processed Records' );
CELLPUTN( nRecordProcess , sCube , CurrLine , 'Latest' , sProcess , 'Processed Records' );

#=====================================================================================

#===========================           SEND EMAIL EPILOG CODE    ==============================
ProcessEmail = 'System - Send Email' ; 
sENote = CELLGETS ( 'Global Variables', 'Email Notifications' , 'String Value' ) ; 
sLogFileDir = CELLGETS ( 'Global Variables', 'Log Directory', 'String Value' ) ; 
sTo =  CELLGETS ( 'Global Variables', 'To Email Address', 'String Value' ) ; 
sEnvType = CELLGETS ( 'Global Variables', 'TM1 Server Name', 'String Value' ) ; 
sSuccessSub = CELLGETS ( 'Global Variables', 'Success Subject', 'String Value' ) ; 
sFailSub = CELLGETS ( 'Global Variables', 'Failure Subject', 'String Value' ) ; 
sSuccessMessage = CELLGETS ( 'Global Variables', 'Success Message' , 'String Value' ) ; 
sFailMessage = CELLGETS ( 'Global Variables', 'Failure Message' , 'String Value' ) ; 



IF ( sENote @= 'On'
       % sENote @= 'Errors Only' ) ; 

    IF ( GETPROCESSERRORFILENAME @<> '' );
         sLogFName = sLogFileDir | '\' | GETPROCESSERRORFILENAME ;

         IF ( vParamFilled @<> '' ) ; 
                 vMessage = sFailMessage  | ' ' | sProcess |' on ' | sNow | '.'  | 
                                           '""<br><br>""' | 'Parameters used: ' | vParamFilled | '.' ; 
         ELSE; 
                 vMessage = sFailMessage  | ' ' | sProcess |' on ' | sNow | '.'  ; 
         ENDIF;

         EXECUTEPROCESS ( ProcessEmail , 
                                                        'pTo' , sTo , 
                                                        'pSubject' , sEnvType | ' ' | sFailSub , 
                                                        'pMessage' , vMessage   , 
                                                        'pCC' , '' , 
                                                        'pBCC' , '' , 
                                                        'pAttach' , sLogFName  ) ; 
     ELSE;
       IF ( sENote @= 'On' ) ; 
             IF ( vParamFilled @<> '' ) ; 
                    vMessage = sSuccessMessage | ' ' | sProcess |' on ' | sNow | '.'  | 
                                                '""<br><br>""' | 'Parameters used: ' | vParamFilled | '.' ; 
            ELSE; 
                    vMessage = sSuccessMessage | ' ' | sProcess |' on ' | sNow | '.'  ; 
            ENDIF;

         EXECUTEPROCESS ( ProcessEmail , 
                                                        'pTo' , sTo , 
                                                        'pSubject' , sEnvType | ' ' | sSuccessSub , 
                                                        'pMessage' , vMessage   , 
                                                        'pCC' , '' , 
                                                        'pBCC' , '' , 
                                                        'pAttach' , sLogFName  ) ; 
        ENDIF;

     ENDIF;

ENDIF;

#===========================           END OF SEND EMAIL CODE    ==============================

#===========================         LOG TI PROCESS EPILOG CODE  ==============================

# sLogDate = 'GETDATE()';

# ODBCSource = vSource;
# ODBCOpen(ODBCSource, vSourceID, vSourcePWD);

# ODBCOutput(ODBCSource, 
# 'INSERT INTO [FinDM_PA].[dbo].[table_PA_TIProcess_Log]([PROCESS_NAME],[COMPLETION_TIME])', 
# 'VALUES ('
#  | CHAR(39) | sProcess  | CHAR(39) | ', '  
#  | sLogDate | ' );');
# ODBCClose(ODBCSource);

#===========================  END OF LOG TI PROCESS EPILOG CODE  ==============================
