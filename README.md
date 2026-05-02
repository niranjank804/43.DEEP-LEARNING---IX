SKIPCHECK ;

###################################################
# Rule for }CellSecurity_Workforce Planning Input
# Created: 03/01/23 TK/QueBIT
#
# Modification History:
#
#
###################################################

# Rule 0000 L0000:No Location Security
['Forecast', 'L0000'] = S: 'READ';

# Rule 0100 Center Location Security
[ 'Forecast' ] = S: IF( DB( 'Center Location Security WFP', !}Groups, !Center WFP, !Location WFP, 'Access Processed' ) @= 'WRITE' %
                        DB( 'Center Location Security WFP', !}Groups, !Center WFP, !Location WFP, 'Access Processed' ) @= 'READ', CONTINUE, 'NONE' );

# Rule 0110 WFP Limited Restrictions
[ { 'STIP AMT', 'STIP AMT Override', 'STIP PCT', 'Non STIP Bonus Amt', 'Pay Rate', 'Allowance Rate', 'Base Annual Pay', 'Allowances' } ] = S:
  IF( (DB( '}ClientGroups', !}Groups, 'WorkForce Planning Limited_Write' ) @<>'' 
       % DB( '}ClientGroups', !}Groups, 'WorkForce Planning Limited_READ' ) @<>'' 
      % DB( '}ClientGroups', !}Groups, 'SCN_WorkForce Planning Limited_Write' ) @<>'' 
       % DB( '}ClientGroups', !}Groups, 'SCN_WorkForce Planning Limited_READ' ) @<>'') 
  & ( ELISANC( 'Employee', 'Existing',!Employee )= 1 % ELISANC( 'Employee', 'ALL EMPLOYEES Orphans',!Employee )= 1 )
  , 'NONE', CONTINUE );

# Rule 0200 Lock Cube for Groups in Global Variables if set to READ
[ ] = S: IF( DB( '}ClientGroups', !}Groups, 'Enterprise Admin' ) @<> '' & DB( 'Global Variables', 'Enterprise Admin', 'String Value' ) @= 'READ', 'READ', CONTINUE );
[ ] = S: IF( DB( '}ClientGroups', !}Groups, 'Enterprise Services' ) @<> '' & DB( 'Global Variables', 'Enterprise Services', 'String Value' ) @= 'READ', 'READ', CONTINUE );
[ ] = S: IF( DB( '}ClientGroups', !}Groups, 'EPA Support' ) @<> '' & DB( 'Global Variables', 'EPA Support', 'String Value' ) @= 'READ', 'READ', CONTINUE );
[ ] = S: IF( DB( '}ClientGroups', !}Groups, 'FP&A Admin' ) @<> '' & DB( 'Global Variables', 'FP&A Admin', 'String Value' ) @= 'READ', 'READ', CONTINUE );
[ ] = S: IF( DB( '}ClientGroups', !}Groups, 'Global Tech Admin' ) @<> '' & DB( 'Global Variables', 'Global Tech Admin', 'String Value' ) @= 'READ', 'READ', CONTINUE );
[ ] = S: IF( DB( '}ClientGroups', !}Groups, 'Global Technology' ) @<> '' & DB( 'Global Variables', 'Global Technology', 'String Value' ) @= 'READ', 'READ', CONTINUE );
[ ] = S: IF( DB( '}ClientGroups', !}Groups, 'Housing Admin' ) @<> '' & DB( 'Global Variables', 'Housing Admin', 'String Value' ) @= 'READ', 'READ', CONTINUE );
[ ] = S: IF( DB( '}ClientGroups', !}Groups, 'Housing Bus Owners' ) @<> '' & DB( 'Global Variables', 'Housing Bus Owners', 'String Value' ) @= 'READ', 'READ', CONTINUE );
[ ] = S: IF( DB( '}ClientGroups', !}Groups, 'Intl Admin' ) @<> '' & DB( 'Global Variables', 'Intl Admin', 'String Value' ) @= 'READ', 'READ', CONTINUE );
[ ] = S: IF( DB( '}ClientGroups', !}Groups, 'Intl Bus Owners' ) @<> '' & DB( 'Global Variables', 'Intl Bus Owners', 'String Value' ) @= 'READ', 'READ', CONTINUE );
[ ] = S: IF( DB( '}ClientGroups', !}Groups, 'Lifestyle Admin' ) @<> '' & DB( 'Global Variables', 'Lifestyle Admin', 'String Value' ) @= 'READ', 'READ', CONTINUE );
[ ] = S: IF( DB( '}ClientGroups', !}Groups, 'Lifestyle Bus Owners' ) @<> '' & DB( 'Global Variables', 'Lifestyle Bus Owners', 'String Value' ) @= 'READ', 'READ', CONTINUE );

# Rule 0300 Exisiting Write Security
['Forecast', { 'Transfer Center','Transfer Location','Termination Date','STIP % Override','Transfer Out Month','Merit Increase Override %','Merit Increase Override Month','Promotion and STIP Date','Promotion Title Description','Promotion Amount'} ] =S: IF (  ELISANC( 'Employee', 'Existing', !Employee ) = 1 & ELLEV( 'Center WFP', !Center WFP ) = 0 & ELLEV( 'Location WFP', !Location WFP ) = 0, 'WRITE', CONTINUE );

# Rule 0400 New\Replacement Write Security
[ 'Forecast',{ 'Entity Description','Salary Class','Pay Rate','Allowance Rate','Business Purpose','Currency Code Override','Job Code Description','FTE','Annual Hours','STIP PCT','Hire Date','Termination Date','Merit Increase Override Month','Merit Increase Override %','Promotion  and STIP Date','Promotion Title','Promotion Amount','Second Merit Increase Override Month','Second Merit Increase Override %' } ] = S: IF( ( ELISANC( 'Employee', 'New', !Employee ) = 1 % ELISANC( 'Employee', 'Replacement', !Employee ) = 1 ) & ELLEV( 'Center WFP', !Center WFP ) = 0 & ELLEV( 'Location WFP', !Location WFP ) = 0, 'WRITE', CONTINUE );

# Rule 0500 Other Resources Write Security
[ 'Forecast',{ 'Business Purpose','Hire Date','Currency Code Override','Termination Date','Entity Description','Headcount','Pay Rate','Allowance Rate','Weekly Hours' } ] = S: IF(  ELISANC( 'Employee', 'Other Resources', !Employee ) = 1 & ELLEV('Employee', !Employee ) = 0 & ELLEV( 'Center WFP', !Center WFP ) = 0 & ELLEV( 'Location WFP', !Location WFP ) = 0,  'WRITE', CONTINUE );

# Rule 0900 Final Security Rule
[] = S: 'READ';

FEEDERS;
