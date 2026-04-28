#Section Prolog

#****Begin: Generated Statements***
#****End: Generated Statements****

##########################################################################
# Security - Set Center Location Access
##########################################################################
#
# PURPOSE:
#   Push Security Access down for Center and Location to the rollups and leaf elements
#
# ASSUMPTIONS:
#
# PARAMETERS:
#    
# MODIFICATION HISTORY:
#   Created : 02/15/23   TK/QueBIT 
#
##########################################################################

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
IF( DIMIX( '}Clients', TM1USER() ) = 0 );
    sUser = TM1USER();
ELSE;
    sUser = ATTRS( '}Clients' , TM1USER() , '}TM1_DefaultDisplayValue' ) ;
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
vParamFilled = '' ;
CELLPUTS( vParamFilled, sCube , CurrLine , sDate , sProcess , 'Parameters' );
CELLPUTS( vParamFilled, sCube , CurrLine , 'Latest' , sProcess , 'Parameters' );

#===================================================================================== 

# Process Variables
# -------------------------------

vSourceCube= 'Center Location Security';
vTargetCube= vSourceCube;
vSourceMeasure = 'Access Input';
vTargetMeasure = 'Access Processed';

cTempFlag = 1 ;

# Turn Off Cube Logging
CELLPUTS ( 'NO' , '}CubeProperties', vTargetCube , 'LOGGING' ) ;

##############
# Create ZeroOut
##############

vZeroOutView = 'zTI_ZeroOut ' | GETPROCESSNAME();
vZeroOutSubset = vZeroOutView;

# Create View
# -----------------------
IF( VIEWEXISTS( vTargetCube, vZeroOutView ) = 1 );
    VIEWDESTROY( vTargetCube, vZeroOutView );
ENDIF;
VIEWCREATE( vTargetCube, vZeroOutView , cTempFlag ) ;

# Create Measure
# -------------------------
vDim = 'Center Location Security Measure';
IF( SUBSETEXISTS( vDim, vZeroOutSubset ) = 0 );
    SUBSETCREATE( vDim, vZeroOutSubset , cTempFlag );
ELSE;
    SUBSETDELETEALLELEMENTS( vDim, vZeroOutSubset );
ENDIF;

SUBSETELEMENTINSERT( vDim, vZeroOutSubset, vTargetMeasure, 0 );

VIEWSUBSETASSIGN( vTargetCube, vZeroOutView, vDim, vZeroOutSubset );

##############
# Create SourceView
##############

vSourceView = 'zTI_Source ' | GETPROCESSNAME();
vSourceSubset = vSourceView;

# Create View
# -----------------------
IF( VIEWEXISTS( vSourceCube, vSourceView ) = 1 );
    VIEWDESTROY( vSourceCube, vSourceView );
ENDIF;
VIEWCREATE( vSourceCube, vSourceView, cTempFlag );

# Set View Extract Properties
# ------------------------------------------
VIEWEXTRACTSKIPCALCSSET( vSourceCube, vSourceView, 0);
VIEWEXTRACTSKIPRULEVALUESSET( vSourceCube, vSourceView, 0 );
VIEWEXTRACTSKIPZEROESSET( vSourceCube, vSourceView, 1 );

# Create }Clients
# -------------------------
vDim = '}Clients';
IF( SUBSETEXISTS( vDim, vSourceSubset ) = 0 );
    SUBSETCREATE( vDim, vSourceSubset , cTempFlag );
ELSE;
    SUBSETDELETEALLELEMENTS( vDim, vSourceSubset );
ENDIF;

vSize = DIMSIZ( vDim );
WHILE( vSize > 0 );
    vElement = DIMNM( vDim, vSize );
    SUBSETELEMENTINSERT( vDim, vSourceSubset, vElement, 0 );
    vSize = vSize - 1;
END;

# Create Center
# -------------------------
vDim = 'Center';
IF( SUBSETEXISTS( vDim, vSourceSubset ) = 0 );
    SUBSETCREATE( vDim, vSourceSubset , cTempFlag );
ELSE;
    SUBSETDELETEALLELEMENTS( vDim, vSourceSubset );
ENDIF;

# vMDX_CenterDIM = '{TM1FILTERBYLEVEL(TM1SUBSETALL([Center]) , 0)}';
# SubsetCreatebyMDX(vDim, vSourceSubset, vMDX_CenterDIM);


vSize = DIMSIZ( vDim );
WHILE( vSize > 0 );
    vElement = DIMNM( vDim, vSize );
        SUBSETELEMENTINSERT( vDim, vSourceSubset, vElement, 0 );
    vSize = vSize - 1;
END;

# Create Location
# -------------------------
vDim = 'Location';
IF( SUBSETEXISTS( vDim, vSourceSubset ) = 0 );
    SUBSETCREATE( vDim, vSourceSubset , cTempFlag );
ELSE;
    SUBSETDELETEALLELEMENTS( vDim, vSourceSubset );
ENDIF;

# vMDX_CenterDIM = '{TM1FILTERBYLEVEL(TM1SUBSETALL([Location].[Location]) , 0)}';
# SubsetCreatebyMDX(vDim, vSourceSubset, vMDX_CenterDIM);

vSize = DIMSIZ( vDim );
WHILE( vSize > 0 );
    vElement = DIMNM( vDim, vSize );
        SUBSETELEMENTINSERT( vDim, vSourceSubset, vElement, 0 );
    vSize = vSize - 1;
END;

# Center Location Security Measure
# --------------------------------------------------------
vDim = 'Center Location Security Measure';
IF( SUBSETEXISTS( vDim, vSourceSubset ) = 0 );
    SUBSETCREATE( vDim, vSourceSubset , cTempFlag );
ELSE;
    SUBSETDELETEALLELEMENTS( vDim, vSourceSubset );
ENDIF;

SUBSETELEMENTINSERT( vDim, vSourceSubset, vSourceMeasure, 0 );

VIEWSUBSETASSIGN( vSourceCube, vSourceView, vDim, vSourceSubset );

# Zero Out View
# ------------------------
VIEWZEROOUT( vTargetCube, vZeroOutView );

# Assign new view as Datasource
# -----------------------------------------------
DATASOURCENAMEFORSERVER = vSourceCube;
DATASOURCECUBEVIEW = vSourceView; 


#Section Metadata

#****Begin: Generated Statements***
#****End: Generated Statements****
#Section Data

#****Begin: Generated Statements***
#****End: Generated Statements****

#Process Statistics Data Records Started
# ---------------------------------------------------------------
 nRecordCount = nRecordCount + 1;


# # Loop Through Center and Location to define Security Intersections

vDim1 = 'Center';
 vMaxIDX1 = DIMSIZ( vDim1);
vDim2 = 'Location';
 vMaxIDX2 = DIMSIZ( vDim2);

Idx1 = 1;
# Loop Through Center

WHILE(Idx1 <= vMaxIdx1 );
  vElement1 = DIMNM( vDim1, Idx1);
  IF (dCenter @= vElement1 % ELISANC(vDim1, dCenter,vElement1)=1);

Idx2 = 1;
# Loop Through Location

WHILE(Idx2 <= vMaxIdx2 );
  vElement2 = DIMNM( vDim2, Idx2);
  IF (dLocation @= vElement2 % ELISANC(vDim2, dLocation,vElement2)=1);
    IF(CELLGETS(vSourceCube,dUser,vElement1,vElement2,vTargetMeasure)@<>'WRITE');  
     CELLPUTS( dValue, vSourceCube, dUser, vElement1, vElement2, vTargetMeasure);
    ENDIF;
  ENDIF;
  Idx2 = Idx2 + 1;
END;
  ENDIF;
  Idx1 = Idx1 + 1;
END;
# IF((ELLEV( vDim1, dCenter )= 0) & ELLEV(vDim2, dLocation )=0);
#     IF(CELLGETS(vSourceCube,dUser,dCenter,dLocation,vTargetMeasure)@<>'WRITE');  
#         CELLPUTS( dValue, vSourceCube, dUser, dCenter,dLocation, vTargetMeasure);
#     ENDIF;

# ENDIF;
# IF(ELLEV( vDim1, dCenter )<> 0);

#     vChildCount_1 = 1;
#     vNumberChild_1=ELCOMPN( vDim1, dCenter );
#     WHILE(vChildCount_1<=vNumberChild_1);
#         vChild = ELCOMP( vDim1, dCenter, vChildCount_1);

#         CELLPUTS( dValue, vSourceCube, dUser, vChild,dLocation, vTargetMeasure);
#         vChildCount_1 =vChildCount_1 + 1;
#     END;
# ENDIF;    
# IF(ELLEV(vDim2, dLocation )<>0);
#     vChildCount_2 = 1;
#     vNumberChild_2=ELCOMPN( vDim1, dCenter );
#     WHILE(vChildCount_2<=vNumberChild_2);
#         vChild_2 = ELCOMP( vDim1, dCenter, vChildCount_2 );
#         CELLPUTS( dValue, vSourceCube, dUser, vChild,dLocation, vTargetMeasure);
#         vChildCount_2 =vChildCount_2 + 1;
#     END;
# ENDIF;



#Section Epilog

#****Begin: Generated Statements***
#****End: Generated Statements****

# Turn On Cube Logging
CELLPUTS ( 'YES' , '}CubeProperties', vTargetCube , 'LOGGING' ) ;


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

