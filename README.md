#Section Prolog

#****Begin: Generated Statements***
#****End: Generated Statements****

################################################
# Security - Master
################################################
#
# PURPOSE:
#    This process runs all of the processes for Dimension updates used in Nightly and Daily processing
#
# PARAMETERS:
#    pYN - Y to Run
#
# MODIFICATION HISTORY:
# 06/26/2023: Created - TK / QueBIT
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
vParamFilled = 'None' ;
CELLPUTS( vParamFilled, sCube , CurrLine , sDate , sProcess , 'Parameters' );
CELLPUTS( vParamFilled, sCube , CurrLine , 'Latest' , sProcess , 'Parameters' );

#===================================================================================== 

EXECUTEPROCESS( 'Security - Set Center Location Access' );
EXECUTEPROCESS( 'Security - Set Center Location Access WFP' );
EXECUTEPROCESS( 'System - Refresh Security' );
#Section Metadata

#****Begin: Generated Statements***
#****End: Generated Statements****
#Section Data

#****Begin: Generated Statements***
#****End: Generated Statements****
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
