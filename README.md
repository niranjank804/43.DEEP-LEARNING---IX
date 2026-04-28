#Section Prolog

#****Begin: Generated Statements***
#****End: Generated Statements****

################################################
# DIM - Employee
################################################
#
# PURPOSE:
#    This process builds the employee dimension from a .csv file
#    
# ASSUMPTIONS:
#
# PARAMETERS:
#    pYN - Y to Run
#
# NOTES
#  This process uncouples the whole dimension EXCEPT the orphan bucket   
#
# MODIFICATION HISTORY:
#  12-01-2017: Created - DN / QueBIT
#  03-12-2018:  Added Bulk Employee item
#  02-12-2020:  Updated to load from Workday - RC/Assurant
#  02-21-2023: Updated for GFS Structure - NA/QueBIT
#
##################################################

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
vParamFilled = 'pYN: ' | pYN ;
CELLPUTS( vParamFilled, sCube , CurrLine , sDate , sProcess , 'Parameters' );
CELLPUTS( vParamFilled, sCube , CurrLine , 'Latest' , sProcess , 'Parameters' );

#===================================================================================== 

IF( pYN @= 'N' );
    PROCESSQUIT();
ENDIF;

# Process Variables
# -------------------------------------
vDim = 'Employee';
vBlank = 'Blank Employee';
vTotalParent1 = 'ALL EMPLOYEES';
vTotalParent2 = 'Other Resources';
vTotalParent3 = 'New';
vTotalParent4 = 'Replacement';
vTotalParent5 = 'Existing';
vTotalParent6 = 'Contingent Worker';
vParent1 = 'Non-Technical Temporary Help';
vParent2 = 'IT Support Contracted Services';
vParent3 = 'New IT Development Contracted Services';
vControlParent = vDim | ' Control Total' ;
vFile = 'WFP Test Data.txt';
vSource = CELLGETS( 'Global Variables', 'File Source Directory', 'String Value' ) | '\Data Loads\' | vFile;

# Assign Datasource
# ---------------------------
DATASOURCENAMEFORSERVER = vSource;

#Dimension Exists 
#------------------------
IF(DIMENSIONEXISTS(vDim)=0);
   DIMENSIONCREATE(vDim);
ENDIF;

# Delete Node Hierarchy
# -------------------------------------
IF( DIMIX( vDim, vTotalParent1 ) <> 0 ); 
    EXECUTEPROCESS ( 'System - Delete Node Hierarchy',
        'pDimensionName' , vDim,
        'pRootNodeElement' , vTotalParent1,
        'pIncludeNLevel' , 0,
        'pSkipIfMultipleAncestors' , 1,
        'pUncoupleOnly' , 1 ) ;
ENDIF;

# Delete Control Total Hierarchy
# -------------------------------------
IF( DIMIX( vDim, vControlParent ) <> 0 ); 
    EXECUTEPROCESS ( 'System - Delete Node Hierarchy',
        'pDimensionName' , vDim,
        'pRootNodeElement' , vControlParent,
        'pIncludeNLevel' , 0,
        'pSkipIfMultipleAncestors' , 1,
        'pUncoupleOnly' , 1 ) ;
ENDIF;

# Insert C Level Elements 
# ----------------------
IF( DIMIX( vDim, vTotalParent1 ) = 0 );
  DIMENSIONELEMENTINSERT( vDim, '', vTotalParent1, 'C' );
ENDIF;

IF( DIMIX( vDim, vTotalParent2 ) = 0 );
  DIMENSIONELEMENTINSERT( vDim, '', vTotalParent2, 'C' );
ENDIF;

DIMENSIONELEMENTINSERT( vDim, '', vTotalParent3, 'C' );
DIMENSIONELEMENTINSERT( vDim, '', vTotalParent4, 'C' );
DIMENSIONELEMENTINSERT( vDim, '', vTotalParent5, 'C' );
DIMENSIONELEMENTINSERT( vDim, '', vTotalParent6, 'C' );
# -------------------------------------
DIMENSIONELEMENTINSERT( vDim , '','Bulk ' | vDim, 'N' );
DIMENSIONELEMENTINSERT( vDim , '','Bulk ' | vDim | ' Reduction', 'N' );
DIMENSIONELEMENTINSERT( vDim , '','Bulk ' | vDim | ' New', 'N' );
DIMENSIONELEMENTINSERT( vDim , '','Bulk ' | vDim | ' Replacement', 'N' );

# Add Existing employee bucket to Parent
# -----------------------------------------------
DIMENSIONELEMENTCOMPONENTADD( vDim , vTotalParent1 , vTotalParent5 , 1 );

# Add Contingent Worker bucket to Parent
# -----------------------------------------------
DIMENSIONELEMENTCOMPONENTADD( vDim , vTotalParent1 , vTotalParent6 , 1 );

# Add C level Elements to Parent
# -----------------------------------------------
DIMENSIONELEMENTCOMPONENTADD( vDim , vTotalParent1 , vTotalParent2 , 1 );
DIMENSIONELEMENTCOMPONENTADD( vDim , vTotalParent1 , vTotalParent3 , 1 );
DIMENSIONELEMENTCOMPONENTADD( vDim , vTotalParent1 , vTotalParent4 , 1 );

# Add C Level Childern to Other Resources 
DIMENSIONELEMENTCOMPONENTADD( vDim , vTotalParent2 , vParent1 , 1 );
DIMENSIONELEMENTCOMPONENTADD( vDim , vTotalParent2 , vParent2 , 1 );
DIMENSIONELEMENTCOMPONENTADD( vDim , vTotalParent2 , vParent3 , 1 );

# Add N Level Elements to their Parents
# -------------------------------------
vSize = DIMSIZ( vDim );
WHILE(vSize >0);
  vElement = DIMNM( vDim, vSize);
  IF(ELLEV(vDim, vElement)=0);
  # New Employees
    IF (SUBST(vElement,1,3)@='New' & SUBST(vElement,1,6)@<>'NEW IT'  );
        DIMENSIONELEMENTCOMPONENTADD( vDim, vTotalParent3, vElement, 1 );
  # Replacement Employees      
    ELSEIF (SUBST(vElement,1,3)@='Rep' );
        DIMENSIONELEMENTCOMPONENTADD( vDim, vTotalParent4, vElement, 1 );
  # Non - Techincal Employees
    ELSEIF (SUBST(vElement,1,5)@='Non-T' );
        DIMENSIONELEMENTCOMPONENTADD( vDim, vParent1, vElement, 1 );
  # IT Employees
    ELSEIF (SUBST(vElement,1,3)@='IT ' );
        DIMENSIONELEMENTCOMPONENTADD( vDim, vParent2, vElement, 1 );
  # New IT Employees 
    ELSEIF (SUBST(vElement,1,6)@='NEW IT' );
        DIMENSIONELEMENTCOMPONENTADD( vDim, vParent3, vElement, 1 );
    ENDIF;
  ENDIF;
vSize = vSize - 1;
END;

# Create Attributes
# -----------------------------
ATTRINSERT( vDim, '', 'ID - Name', 'A' );
ATTRINSERT( vDim, '', 'Name - ID', 'A' );

# Set Dimension Order
# -------------------------------------
DIMENSIONSORTORDER( vDim, 'ByName', 'Ascending', 'ByHierarchy', 'Ascending' );

#Section Metadata

#****Begin: Generated Statements***
#****End: Generated Statements****

# Process Statistics Data Records Started
# ---------------------------------------------------------------
nRecordCount = nRecordCount + 1;

# Skipped Records
# ---------------------------
IF( dEmployeeID @= '' );
    nRecordSkip = nRecordSkip + 1;
    ITEMSKIP;
ENDIF;

# Insert Employee
# ----------------------
IF( DIMIX( vDim, dEmployeeID ) = 0 );
    DIMENSIONELEMENTINSERT( vDim , '' , dEmployeeID, 'N' );
ENDIF;

# Create Hierarchy Existing Employees and Contingent Workers 
# ---------------------------------------------------------------
IF (dWorkerType @= 'Contingent Worker');
 DIMENSIONELEMENTCOMPONENTADD( vDim, vTotalParent6, dEmployeeID, 1);
ELSE;
  DIMENSIONELEMENTCOMPONENTADD( vDim , vTotalParent5 , dEmployeeID , 1 );
ENDIF;

#Section Data

#****Begin: Generated Statements***
#****End: Generated Statements****

# Skip blanks
# ------------------------
IF( dEmployeeID @= '' );
  ITEMSKIP;
ENDIF;

# Attributes
# ------------
vName = TRIM( dFirstname ) | ' ' | TRIM( dLastname );
ATTRPUTS( dEmployeeID | ':' | vName, vDim, dEmployeeID, 'ID - Name' );
ATTRPUTS( vName | ':' | dEmployeeID, vDim, dEmployeeID, 'Name - ID' );


#Section Epilog

#****Begin: Generated Statements***
#****End: Generated Statements****

#Execute Cleanup Process
#--------------------------------
EXECUTEPROCESS('DIM  - Orphans Master', 'pDimensionName', vDim);
EXECUTEPROCESS('DIM - Create Control Total', 'pDim', vDim);
EXECUTEPROCESS('DIM - Clear Empty Parents', 'pDim', vDim);
IF(HIERARCHYEXISTS(vDim, 'Leaves') = 1);
  EXECUTEPROCESS('Dim - Update Leaves Hierarchy', 'pDim', vDim);
ENDIF;

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
