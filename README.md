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
                # IF(CELLGETS(vSourceCube,dUser,vElement1,vElement2,vTargetMeasure)@<>'WRITE');  
                CELLPUTS( dValue, vSourceCube, dUser, vElement1, vElement2, vTargetMeasure);
                # ENDIF;
            ENDIF;
            Idx2 = Idx2 + 1;
        END;
    ENDIF;
    Idx1 = Idx1 + 1;
END;
