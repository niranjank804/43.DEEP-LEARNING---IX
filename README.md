# Center subset — RESTORE all elements (parent rows needed for push-down)
vDim = 'Center';
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

# Location subset — RESTORE all elements (parent rows needed for push-down)
vDim = 'Location';
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
