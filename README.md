##########################################################################
# SCN SCN Workforce Planning Summary - Rules
##########################################################################
#
#
# NOTES:
#
#
#
# MODIFICATION HISTORY:
# 11-18-25: Created from GFS WFP Rules GZ / QueBIT
##########################################################################

SKIPCHECK;

# Rule 0050 Limit Period Calculations for the Year
# -------------------------------------------
[ 'Forecast' ] = N:
IF( !Period @>= DB( '}ElementAttributes_Period', !Period, 'Year' ) | '01' & !Period @<= DB( '}ElementAttributes_Period', !Period, 'Year' ) | '12'
    , CONTINUE
    , STET
);

# Rule 0100 EOP Rule
# no feeder
[ ] = C: IF( DTYPE ( 'Period', !Period ) @= 'C' 
  & !Period@<>'All Time' 
  & DB( '}ElementAttributes_SCN Workforce Planning Account' , !SCN Workforce Planning Account , 'EOP' ) @= 'Y'
    , DB('SCN Workforce Planning Summary',!Version, !Scenario, !Location WFP,DB( '}ElementAttributes_Period', !Period, 'Final Month'),!Planning Currency,!Job Title,!Center WFP,!Employee,!SCN Workforce Planning Account)
    , CONTINUE ) ;

#Rule 0101 - STET Final Scenario - Static Scenario
[] = N: IF( !Scenario @= 'Final'
  ,STET
  ,CONTINUE);
  
#############
# Currency Translation
#############

# Rule 0300 GBP Conversion
[ 'Forecast', 'EUR to GBP' ] = N: IF(DB( '}ElementAttributes_SCN Workforce Planning Account', !SCN Workforce Planning Account, 'Translate Currency') @= 'Y', ['Planning Currency':'EUR'] * DB( 'Exchange Rates', !Version, 'EUR', !Period, 'Monthly Average Rate' ) \ DB( 'Exchange Rates', !Version, 'GBP', !Period, 'Monthly Average Rate' ), ['Planning Currency':'EUR'] );

# Rule 0310 EUR Conversion
[ 'Forecast', 'GBP to EUR' ] = N: IF(DB( '}ElementAttributes_SCN Workforce Planning Account', !SCN Workforce Planning Account, 'Translate Currency') @= 'Y', ['Planning Currency':'GBP'] * DB( 'Exchange Rates', !Version, 'GBP', !Period, 'Monthly Average Rate' ) \ DB( 'Exchange Rates', !Version, 'EUR', !Period, 'Monthly Average Rate' ), ['Planning Currency':'GBP'] );

# Rule 0320 USD Conversion
[ 'Forecast' ] = N:
  IF( SCAN( 'to USD', !Planning Currency) <> 0,
    IF(DB( '}ElementAttributes_SCN Workforce Planning Account', !SCN Workforce Planning Account, 'Translate Currency') @= 'Y'
      , DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period, SUBST( !Planning Currency, 1, 3), !Job Title, !Center WFP, !Employee, !SCN Workforce Planning Account ) * DB( 'Exchange Rates', !Version, SUBST( !Planning Currency, 1, 3), !Period, 'Monthly Average Rate' )
      , DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period, SUBST( !Planning Currency, 1, 3), !Job Title, !Center WFP, !Employee, !SCN Workforce Planning Account )
    )
  , CONTINUE );

####################################################################################
# BULK PLANNING
####################################################################################

# Rule 1100 FTE Bulk
[{'Employee Full Time Equivalents'}, 'Bulk Employee Reduction', 'Forecast' ] = N: [ 'Employee Head Count' ];
[{'New FTE'}, 'Bulk Employee New', 'Forecast' ] = N: [ 'New HC' ];
[{'Replacement FTE'}, 'Bulk Employee Replacement', 'Forecast' ] = N: [ 'Replacement HC' ];


# Rule 1200 Salary Attrition
# ------------------------------
[ 'Salary Attrition', {'Bulk Employee Reduction', 'Bulk Employee New', 'Bulk Employee Replacement'}, 'Forecast' ] = N:
  (DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period, !Planning Currency, !Job Title, !Center WFP, !Employee, 'Salaries-Regular' )
* DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, !Period, 'Blank Job Grade', !Center WFP, 'Attrition Rate' ) ) * -1;


# Rule 1210 Benefits Attrition
# --------------------------------
[ 'Benefits Attrition', {'Bulk Employee Reduction', 'Bulk Employee New', 'Bulk Employee Replacement'}, 'Forecast' ] = N:
   DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period, !Planning Currency, !Job Title, !Center WFP, !Employee, 'Total Annual Benefits No Attrition')
            * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, !Period, 'Blank Job Grade', !Center WFP, 'Attrition Rate' ) * -1;


# Rule 1230 Employee FTE Attrition
# --------------------------------
[ 'Employee FTE Attrition', {'Bulk Employee Reduction', 'Bulk Employee New', 'Bulk Employee Replacement'}, 'Forecast' ] = N:
   DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period, !Planning Currency, !Job Title, !Center WFP, !Employee, 'Total FTE No Attrition')
   * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, !Period, 'Blank Job Grade', !Center WFP, 'Attrition Rate' ) * -1;


# Rule 1240 FTE Attrition - C Level Rule
# ---------------------------
[ 'Employee FTE Attrition', {'Bulk Employee Reduction', 'Bulk Employee New', 'Bulk Employee Replacement'}, 'Forecast'] =
IF ( DTYPE ( 'Period', !Period ) @= 'C'
    , DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, DB( '}ElementAttributes_Period', !Period, 'Final Month' ), !Planning Currency, !Job Title , !Center WFP, !Employee, 'Employee FTE Attrition' )
    , CONTINUE
) ;


# Rule 1250 Employee HC Attrition
# --------------------------------
[ 'Employee HC Attrition', {'Bulk Employee Reduction', 'Bulk Employee New', 'Bulk Employee Replacement'}, 'Forecast' ] = N:
   DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period, !Planning Currency, !Job Title, !Center WFP, !Employee, 'Total Head Count No Attrition')
   * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, !Period, 'Blank Job Grade', !Center WFP, 'Attrition Rate' ) * -1;


# Rule 1260 Employee HC Attrition - C Level Rule
# ---------------------------
[ 'Employee HC Attrition', {'Bulk Employee Reduction', 'Bulk Employee New', 'Bulk Employee Replacement'}, 'Forecast'] =
IF ( DTYPE ( 'Period', !Period ) @= 'C'
    , DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, DB( '}ElementAttributes_Period', !Period, 'Final Month' ), !Planning Currency, !Job Title , !Center WFP, !Employee, 'Employee HC Attrition' )
    , CONTINUE
);

# Rule 1300 Employee Head Count
# no feeder
[ 'Employee Head Count', 'Bulk Employee Reduction', 'Forecast' ] = N:
  IF(DB('Workforce Planning Average Salary Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period',!Period,'Year'), !Job Title, !Center WFP, 'Enable Bulk Planning') = 1
      , STET
      , CONTINUE
 );

# Rule 1310 New HC
# no feeder
[ 'New HC', 'Bulk Employee New', 'Forecast' ] = N:
  IF(DB('Workforce Planning Average Salary Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period',!Period,'Year'), !Job Title, !Center WFP, 'Enable Bulk Planning') = 1
      , STET
      , CONTINUE
 );

# Rule 1320 Replacement HC
# no feeder
[ 'Replacement HC', 'Bulk Employee Replacement', 'Forecast' ] = N:
  IF(DB('Workforce Planning Average Salary Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period',!Period,'Year'), !Job Title, !Center WFP, 'Enable Bulk Planning') = 1
      , STET
      , CONTINUE
 );

# Base Salary
# --------------------------------

# Rule 1400 Base Salary Reduction
[ 'Base Salary', 'Bulk Employee Reduction', 'Forecast' ] = N:
 IF(DB('Workforce Planning Average Salary Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period',!Period,'Year'), !Job Title, !Center WFP, 'Enable Bulk Planning') = 1
      , ( ( [ 'Salaries-Regular', 'Existing' ] - [ 'Allowances', 'Existing' ] - [ 'Merit Increase', 'Existing' ] ) \ [ 'Employee Head Count', 'Existing' ] ) * [ 'Employee Head Count', 'Bulk Employee Reduction' ]
      , STET
 );

# Rule 1410 Base Salary New
[ 'Base Salary', 'Bulk Employee New', 'Forecast' ] = N:
 IF(DB('Workforce Planning Average Salary Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period',!Period,'Year'), !Job Title, !Center WFP, 'Enable Bulk Planning') = 1
      ,IF(DB('Workforce Planning Average Salary Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period',!Period,'Year'), !Job Title, !Center WFP, 'Avg Pay Rate Override New') = 0
             , ( ( [ 'Salaries-Regular', 'Existing' ] - [ 'Allowances', 'Existing' ] - [ 'Merit Increase', 'Existing' ] ) \ [ 'Employee Head Count', 'Existing' ] ) * [ 'New HC', 'Bulk Employee New' ]
             ,DB('Workforce Planning Average Salary Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period',!Period,'Year'), !Job Title, !Center WFP, 'Avg Pay Rate Override New') * [ 'New HC', 'Bulk Employee New' ] *
                IF( DB('Workforce Planning Center Location Assumptions', !Version, !Location WFP, DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' ) = 0
                  , 1 \ 12
                  , DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, !Period, 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' ) \ DB('Workforce Planning Center Location Assumptions', !Version, !Location WFP, DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' )
                )
        )
      , STET
 );

# Rule 1420 Base Salary Replacement
[ 'Base Salary', 'Bulk Employee Replacement', 'Forecast' ] = N:
 IF(DB('Workforce Planning Average Salary Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period',!Period,'Year'), !Job Title, !Center WFP, 'Enable Bulk Planning') = 1
      , IF(DB('Workforce Planning Average Salary Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period',!Period,'Year'), !Job Title, !Center WFP, 'Avg Pay Rate Override Replacement') = 0
             , ( ( [ 'Salaries-Regular', 'Existing' ] - [ 'Allowances', 'Existing' ] - [ 'Merit Increase', 'Existing' ] ) \ [ 'Employee Head Count', 'Existing' ] ) * [ 'Replacement HC', 'Bulk Employee Replacement' ]
             ,DB('Workforce Planning Average Salary Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period',!Period,'Year'), !Job Title, !Center WFP, 'Avg Pay Rate Override Replacement') * [ 'Replacement HC', 'Bulk Employee Replacement' ] *
                IF( DB('Workforce Planning Center Location Assumptions', !Version, !Location WFP, DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' ) = 0
                  , 1 \ 12
                  , DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, !Period, 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' ) \ DB('Workforce Planning Center Location Assumptions', !Version, !Location WFP, DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' )
                )
        )
      , STET
 );

# Rule 1430 Allowances
# --------------------------------

 [ 'Allowances', 'Bulk Employee Reduction', 'Forecast' ] = N:
 IF(DB('Workforce Planning Average Salary Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period',!Period,'Year'), !Job Title, !Center WFP, 'Enable Bulk Planning') = 1
      , ( [ 'Allowances', 'Existing' ] \ [ 'Employee Head Count', 'Existing' ] ) * [ 'Employee Head Count', 'Bulk Employee Reduction' ]
      , STET
 );

[ 'Allowances', 'Bulk Employee New', 'Forecast' ] = N:
 IF(DB('Workforce Planning Average Salary Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period',!Period,'Year'), !Job Title, !Center WFP, 'Enable Bulk Planning') = 1
      ,IF(DB('Workforce Planning Average Salary Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period',!Period,'Year'), !Job Title, !Center WFP, 'Avg Allowances Override New') = 0
             , ( [ 'Allowances', 'Existing' ] \ [ 'Employee Head Count', 'Existing' ] ) * [ 'New HC', 'Bulk Employee New' ]
             ,DB('Workforce Planning Average Salary Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period',!Period,'Year'), !Job Title, !Center WFP, 'Avg Allowances Override New') * [ 'New HC', 'Bulk Employee New' ] *
                IF( DB('Workforce Planning Center Location Assumptions', !Version, !Location WFP, DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' ) = 0
                  , 1 \ 12
                  , DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, !Period, 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' ) \ DB('Workforce Planning Center Location Assumptions', !Version, !Location WFP, DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' )
                )
        )
      , STET
 );

# Rule 1440 Allowances Replacement
[ 'Allowances', 'Bulk Employee Replacement', 'Forecast' ] = N:
 IF(DB('Workforce Planning Average Salary Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period',!Period,'Year'), !Job Title, !Center WFP, 'Enable Bulk Planning') = 1
      , IF(DB('Workforce Planning Average Salary Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period',!Period,'Year'), !Job Title, !Center WFP, 'Avg Allowances Override Replacement') = 0
             , ( [ 'Allowances', 'Existing' ] \ [ 'Employee Head Count', 'Existing' ] ) * [ 'Replacement HC', 'Bulk Employee Replacement' ]
             ,DB('Workforce Planning Average Salary Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period',!Period,'Year'), !Job Title, !Center WFP, 'Avg Allowances Override Replacement') * [ 'Replacement HC', 'Bulk Employee Replacement' ] *
                IF( DB('Workforce Planning Center Location Assumptions', !Version, !Location WFP, DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' ) = 0
                  , 1 \ 12
                  , DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, !Period, 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' ) \ DB('Workforce Planning Center Location Assumptions', !Version, !Location WFP, DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' )
                )
        )
      , STET
 );

# Rule 1450 Merit Increase
# --------------------------------

 [ 'Merit Increase', 'Bulk Employee Reduction', 'Forecast' ] = N:
 IF(DB('Workforce Planning Average Salary Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period',!Period,'Year'), !Job Title, !Center WFP, 'Enable Bulk Planning') = 1
      , ( [ 'Merit Increase', 'Existing' ] \ [ 'Employee Head Count', 'Existing' ] ) * [ 'Employee Head Count', 'Bulk Employee Reduction' ]
      , STET
 );

[ 'Merit Increase', 'Bulk Employee New', 'Forecast' ] = N:
 IF(DB('Workforce Planning Average Salary Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period',!Period,'Year'), !Job Title, !Center WFP, 'Enable Bulk Planning') = 1
      , ( [ 'Merit Increase', 'Existing' ] \ [ 'Employee Head Count', 'Existing' ] ) * [ 'New HC', 'Bulk Employee New' ]
      , STET
 );

 [ 'Merit Increase', 'Bulk Employee Replacement', 'Forecast' ] = N:
 IF(DB('Workforce Planning Average Salary Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period',!Period,'Year'), !Job Title, !Center WFP, 'Enable Bulk Planning') = 1
      , ( [ 'Merit Increase', 'Existing' ] \ [ 'Employee Head Count', 'Existing' ] ) * [ 'Replacement HC', 'Bulk Employee Replacement' ]
      , STET
 );

# Rule 1460 Bonus-Non STIP
[ 'Bonus-Non STIP', {'Bulk Employee Reduction', 'Bulk Employee New', 'Bulk Employee Replacement'}, 'Forecast' ] = N:
  DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Job Title, !Center WFP, 'Non STIP Bonus Assumption' ) * ( [ 'Employee Head Count' ] + [ 'New HC' ] + [ 'Replacement HC' ] ) \ 12;

# Rule 1500 International Benefits
# -- Back out Allowances from Calc if they Benefits Basis does not Include them
# -----------------------

[ 'International Benefits', {'Bulk Employee Reduction', 'Bulk Employee New', 'Bulk Employee Replacement'}, 'Forecast' ] = N:
( [ 'Salaries-Regular' ] - ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0, 1, 0 ) ) ) * ( [ 'International Benefits', 'Existing' ] \ ( [ 'Salaries-Regular', 'Existing' ] - ( [ 'Allowances', 'Existing' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0, 1, 0 ) ) ) );

# US Employees Only
# -----------------------

# Rule 1600 Grp Ins Employee-Co Cost
# no feeder
[ 'Grp Ins Employee-Co Cost', {'Bulk Employee Reduction', 'Bulk Employee New', 'Bulk Employee Replacement'}, 'Forecast' ] = N:
IF(DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Grp Ins Employee-Co Cost Flat Rate' ) <> 0,
 (DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Grp Ins Employee-Co Cost Flat Rate' )\12) * 
  
  (
   ['Employee Full Time Equivalents']
   +
   ['Replacement FTE']
   +
   ['New FTE']
   +
   ['Employee FTE Attrition']
  ),
 ( [ 'Salaries-Regular' ] * ( [ 'Grp Ins Employee-Co Cost', 'Existing' ] \ [ 'Salaries-Regular', 'Existing' ] )));


# Rule 1620 401K Employee Match
# no feeder
[ '401K Employee Match', {'Bulk Employee Reduction', 'Bulk Employee New', 'Bulk Employee Replacement'}, 'Forecast' ] = N:
 [ 'Salaries-Regular' ] * ( [ '401K Employee Match', 'Existing' ] \ [ 'Salaries-Regular', 'Existing' ] );

# Rule 1630 401K Executive Plan
# no feeder
[ '401K Executive Plan', {'Bulk Employee Reduction', 'Bulk Employee New', 'Bulk Employee Replacement'}, 'Forecast' ] = N:
 [ 'Salaries-Regular' ] * ( [ '401K Executive Plan', 'Existing' ] \ [ 'Salaries-Regular', 'Existing' ] );

# Rule 1640 Employer SUTA
# no feeder
[ 'Employer SUTA', {'Bulk Employee Reduction', 'Bulk Employee New', 'Bulk Employee Replacement'}, 'Forecast' ] = N:
 [ 'Salaries-Regular' ] * ( [ 'Employer SUTA', 'Existing' ] \ [ 'Salaries-Regular', 'Existing' ] );

# Rule 1650 Employer FICA
# no feeder
[ 'Employer FICA', {'Bulk Employee Reduction', 'Bulk Employee New', 'Bulk Employee Replacement'}, 'Forecast' ] = N:
 [ 'Salaries-Regular' ] * ( [ 'Employer FICA', 'Existing' ] \ [ 'Salaries-Regular', 'Existing' ] );

# Rule 1660 Employer FUTA
# no feeder
[ 'Employer FUTA', {'Bulk Employee Reduction', 'Bulk Employee New', 'Bulk Employee Replacement'}, 'Forecast' ] = N:
 [ 'Salaries-Regular' ] * ( [ 'Employer FUTA', 'Existing' ] \ [ 'Salaries-Regular', 'Existing' ] );


####################################################################################
# EMPLOYEE CALCS
####################################################################################

# Rule 2000 Limit Center Calculations
# ------------------------------------
[ 'Forecast' ] = N:
IF ( !Center WFP @= DB ( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, !Period, !Center WFP, !Employee, 'Center' ) ,
CONTINUE,
0 ) ;

# Rule 2010 Limit Location Calculations
# ------------------------------------
[ 'Forecast' ] = N:
IF ( !Location WFP @= DB ( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, !Period, !Center WFP, !Employee, 'Location' ) ,
CONTINUE,
0 ) ;

# Rule 2020 Limit Job Title Calculations
# ------------------------------------
[ 'Forecast' ] = N:
IF ( !Job Title @= DB ( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, !Period, !Center WFP, !Employee, 'Job Code' ) ,
CONTINUE,
0 ) ;

# Rule 2030 Ensure rule only calculates for Active Employees
# ----------------------------------------------------------
[ 'Forecast'] = N:
IF( ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'Hire Date' ), 'TM1 First Day Serial Date' )
    <= ATTRN( 'Period', !Period, 'TM1 First Day Serial Date' )
    & ( ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'Termination Date' ), 'TM1 First Day Serial Date' )
    > ATTRN( 'Period', !Period, 'TM1 Last Day Serial Date' )
    % DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'Termination Date' ) @= '' ),
CONTINUE,
0 );

# Rule 2040 Ensure rule only calculates for Input Currency Code
# ----------------------------------------------------------
[ 'Forecast'] = N:
IF( DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'Currency Code' ) @= !Planning Currency,
CONTINUE,
0 );

#############
# Rule 2100 Base Salary
#############
# external feeder

[ 'Base Salary', 'Forecast' ] = N:
IF( ELISANC( 'Employee', 'Other Resources', !Employee ) = 0
  #True
  , DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Base Annual Pay' ) *
  IF(  ELISANC( 'Employee', 'Existing', !Employee ) = 1
    #True
	, 1
    #False
	, DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'FTE' )
     )  *
  IF( DB('Workforce Planning Center Location Assumptions', !Version, !Location WFP, DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' ) = 0
    # True
	, 1 \ 12
    # False
	, DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, !Period, 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' ) \ DB('Workforce Planning Center Location Assumptions', !Version, !Location WFP, DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' )
     )
  #False
  , CONTINUE );

#############
# Rule 2110 Allowances
#############
# external feeder

[ 'Allowances', 'Forecast' ] = N:
IF( ELISANC( 'Employee', 'Other Resources', !Employee ) = 0
  #True
  , DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Allowances' ) *
  IF(  ELISANC( 'Employee', 'Existing', !Employee ) = 1
    #True
	, 1
    #False
	, DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'FTE' )
     )  *
  IF( DB('Workforce Planning Center Location Assumptions', !Version, !Location WFP, DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' ) = 0
    # True
	, 1 \ 12
    # False
	, DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, !Period, 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' ) \ DB('Workforce Planning Center Location Assumptions', !Version, !Location WFP, DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' )
     )
  #False
  , CONTINUE );


#############
# Rule 2200 Employee Full Time Equivalents
#############

[ 'Employee Full Time Equivalents', 'Forecast' ] = N:
IF( [ 'Base Salary' ] <> 0 & ELISANC( 'Employee', 'Existing', !Employee ) = 1
    , DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'FTE' )
     , IF( [ 'Base Salary' ] <> 0 &
        ( ELISANC( 'Employee', 'New', !Employee ) = 1 % ELISANC( 'Employee', 'Replacement', !Employee ) = 1 ) &
        DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'FTE' ) < 0
        , DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'FTE' )
        , 0)
);

#############
# Rule 2210 Employee Head Count
#############

# Employee Head Count
# ----------------
[ 'Employee Head Count', 'Forecast' ] = N:
IF( [ 'Base Salary' ] <> 0 & ELISANC( 'Employee', 'Existing', !Employee ) = 1
    , 1
        , IF( [ 'Base Salary' ] <> 0 &
        ( ELISANC( 'Employee', 'New', !Employee ) = 1 % ELISANC( 'Employee', 'Replacement', !Employee ) = 1 ) &
        DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'FTE' ) < 0
        ,ROUND( ( DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'FTE' ) + -0.49999   )     )
        , 0)
);

#############
# Rule 2220 New FTE
#############

# New FTE
# -----------
[ 'New FTE', 'Forecast' ] = N:
IF( [ 'Base Salary' ] <> 0 & ELISANC( 'Employee', 'New', !Employee ) = 1 & DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'FTE' ) > 0
    , DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'FTE' )
    , 0
);

#############
# Rule 2230 Replacement FTE
#############

# Replacement FTE
# ----------------------
[ 'Replacement FTE', 'Forecast' ] = N:
IF( [ 'Base Salary' ] <> 0 & ELISANC( 'Employee', 'Replacement', !Employee ) = 1 & DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'FTE' ) > 0
    , DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'FTE' )
    , 0
);

#############
# Rule 2240 New HC
#############

# HeadCount New
# -----------
[ 'New HC', 'Forecast' ] = N:
IF( [ 'Base Salary' ] <> 0 & ELISANC( 'Employee', 'New', !Employee ) = 1 & DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'FTE' ) > 0
    , IF( ['New FTE'] <= 1
        , 1
        , - INT( - ['New FTE'] ) )
    , 0
);

#####################
# Rule 2250 Replacement HC
#####################

### Replacement HC - C Level Rule

# Replacement HC
# ----------------------
[ 'Replacement HC', 'Forecast' ] = N:
IF( [ 'Base Salary' ] <> 0 & ELISANC( 'Employee', 'Replacement', !Employee ) = 1 & DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'FTE' ) > 0
    , IF( ['Replacement FTE'] <= 1
        , 1
        , - INT( - ['Replacement FTE'] ) )
    , 0
);

####################################################################################
# Other Resources
####################################################################################

# Calculate Base Salary/Expenses for:

# Rule 2300 Temporary Help
# external feeder
# -----------------------------------------------------------------------------------
[ 'Temporary Help', 'Forecast' ] = N:
IF( ELISANC( 'Employee', 'Non-Technical Temporary Help', !Employee ) = 1,
DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Base Annual Pay' ) *
  IF( DB('Workforce Planning Center Location Assumptions', !Version, !Location WFP, DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' ) = 0
      , 1 \ 12
      , DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, !Period, 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' ) \ DB('Workforce Planning Center Location Assumptions', !Version, !Location WFP, DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' )
  )
, CONTINUE
);

# Rule 2310 IT Application Maintenance and Support Contracted Services
# external feeder
# -----------------------------------------------------------------------------------
[ 'IT Application Maintenance and Support Contracted Services', 'Forecast' ] = N:
IF( ELISANC( 'Employee', 'IT Support Contracted Services', !Employee ) = 1,
DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Base Annual Pay' ) *
  IF( DB('Workforce Planning Center Location Assumptions', !Version, !Location WFP, DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' ) = 0
      , 1 \ 12
      , DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, !Period, 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' ) \ DB('Workforce Planning Center Location Assumptions', !Version, !Location WFP, DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' )
  )
, CONTINUE
);

# Rule 2320 IT Application Development Contracted Services
# external feeder
# -----------------------------------------------------------------------------------
[ 'IT Application Development Contracted Services', 'Forecast' ] = N:
IF( ELISANC( 'Employee', 'New IT Development Contracted Services', !Employee ) = 1,
DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Base Annual Pay' ) *
  IF( DB('Workforce Planning Center Location Assumptions', !Version, !Location WFP, DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' ) = 0
      , 1 \ 12
      , DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, !Period, 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' ) \ DB('Workforce Planning Center Location Assumptions', !Version, !Location WFP, DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' )
  )
, CONTINUE
);

# Rule 2330 Temp Help FTE
# --------------------------------
[ 'Temp Help FTE', 'Forecast' ] = N:
IF( [ 'Temporary Help' ] <> 0
    , DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'FTE' )
    , 0
);

# Rule 2340 Tech Support Contractor FTE
# --------------------------------
[ 'Tech Support Contractor FTE', 'Forecast' ] = N:
IF( [ 'IT Application Maintenance and Support Contracted Services' ] <> 0
    , DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'FTE' )
    , 0
);

# Rule 2350 Tech Dev Contractor FTE
# --------------------------------
[ 'Tech Dev Contractor FTE', 'Forecast' ] = N:
IF( [ 'IT Application Development Contracted Services' ] <> 0
    , DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'FTE' )
    , 0
);

# Rule 2360 Temp Help HC
# ----------------
[ 'Temp Help HC', 'Forecast' ] = N:
IF( [ 'Temporary Help' ] <> 0
    , IF( DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'Headcount' ) = 0
          , 1
          ,  DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'Headcount' ) )
    , 0
);

# Rule 2370 Tech Support Contractor HC
# ----------------
[ 'Tech Support Contractor HC', 'Forecast' ] = N:
IF( [ 'IT Application Maintenance and Support Contracted Services' ] <> 0
    , IF( DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'Headcount' ) = 0
          , 1
          ,  DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'Headcount' ) )
    , 0
);

# Rule 2380 Tech Dev Contractor HC: Contractor Headcount
# ----------------
[ 'Tech Dev Contractor HC', 'Forecast' ] = N:
IF( [ 'IT Application Development Contracted Services' ] <> 0
    , IF( DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'Headcount' ) = 0
          , 1
          ,  DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'Headcount' ) )
    , 0
);

########################################################################################
# EMPLOYEE CALCS
########################################################################################

# Rule 3000 Merit Increase
# ------------------
[ 'Merit Increase', 'Forecast' ] = N: 
IF( DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) = 0 % DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase Month' ) @= ''
  #True
  ,IF( ATTRN( 'Period', !Period,'TM1 First Day Serial Date') >= ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
    #True
    , ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' )
    #False
    ,0 )
  #False - CHECK 3rd Merit Increase % & Month
  ,IF( DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase %' ) = 0 % DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase Month' ) @= ''
    #True (Period > Effective Increase Month & Period < 2nd Merit Increase Month)
    ,IF( ATTRN( 'Period', !Period,'TM1 First Day Serial Date') >= ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
    & ATTRN( 'Period', !Period,'TM1 First Day Serial Date') < ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
      #True
      ,( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' )
      #False (If Period > Second Merit Increase)
      , IF( ATTRN( 'Period', !Period,'TM1 First Day Serial Date') >= ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
        #True
         ,
           ( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
            * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
            + ( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
            * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
            * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) )
        #False
         ,0) 
      )
    #FALSE - Check 4th Merit Increase %
    ,IF(DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fourth Effective Merit Increase %' ) = 0 % DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fourth Effective Merit Increase Month' ) @= '' 
      #True (Period > Effective Increase Month & Period < 2nd Merit Increase Month)
      ,IF( ATTRN( 'Period', !Period,'TM1 First Day Serial Date') >= ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
        & ATTRN( 'Period', !Period,'TM1 First Day Serial Date') < ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
          #True
          ,( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' )
          #False (If Period > Second Merit Increase & Period < Third Month)
          , IF( ATTRN( 'Period', !Period,'TM1 First Day Serial Date') >= ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
              & ATTRN( 'Period', !Period,'TM1 First Day Serial Date') < ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
            #True
             ,
               ( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                + ( ( [ 'Base Salary' ] + ( [ 'Allowances' ] 
                * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) )
            #False (IF Period > Third Effective Month)
             ,IF( ATTRN( 'Period', !Period,'TM1 First Day Serial Date') >= ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase Month' ), 'TM1 First Day Serial Date') 
                ,( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                +( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) ) 
                + ( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) )  
                * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase %' ) )
                ,0)              
            )
        )
        #CHECK 5th Merit Increase %
      ,IF(DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fifth Effective Merit Increase %' ) = 0 % DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fifth Effective Merit Increase Month' ) @= '' 
        #True (Period > Effective Increase Month & Period < 2nd Merit Increase Month)
        ,IF( ATTRN( 'Period', !Period,'TM1 First Day Serial Date') >= ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
          & ATTRN( 'Period', !Period,'TM1 First Day Serial Date') < ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
            #True
            ,( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' )
            #False (If Period > Second Merit Increase & Period < THird Month)
            , IF( ATTRN( 'Period', !Period,'TM1 First Day Serial Date') >= ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
                & ATTRN( 'Period', !Period,'TM1 First Day Serial Date') < ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
              #True
               ,
                 ( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                  * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                  + ( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                  * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                  * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) )
              #False (If Period > Third Merit Increase & Period < Fourth Month)
               , IF( ATTRN( 'Period', !Period,'TM1 First Day Serial Date') >= ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
                & ATTRN( 'Period', !Period,'TM1 First Day Serial Date') < ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fourth Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
                  #True
                   ,( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                      * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                      +( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                      * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                      * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) ) 
                      + ( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                      * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                      * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) )  
                      * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase %' ) )
                   ,IF( ATTRN( 'Period', !Period,'TM1 First Day Serial Date') >= ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fourth Effective Merit Increase Month' ), 'TM1 First Day Serial Date') 
                      ,( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                      * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                      +( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                      * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                      * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) ) 
                      +( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                      * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                      * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) ) 
                      * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase %' ) ) 
                      + ( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                      * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                      * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) )  
                      * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase %' ) )  
                      * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fourth Effective Merit Increase %' ) )
                      ,0)
                  )
                )
              )
         #CHECK 6th Merit Increase %
        ,IF(DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Sixth Effective Merit Increase %' ) = 0 % DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Sixth Effective Merit Increase Month' ) @= '' 
          #True (Period > Effective Increase Month & Period < 2nd Merit Increase Month)
          ,IF( ATTRN( 'Period', !Period,'TM1 First Day Serial Date') >= ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
            & ATTRN( 'Period', !Period,'TM1 First Day Serial Date') < ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
              #True
              ,( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' )
              #False (If Period > Second Merit Increase & Period < THird Month)
              , IF( ATTRN( 'Period', !Period,'TM1 First Day Serial Date') >= ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
                  & ATTRN( 'Period', !Period,'TM1 First Day Serial Date') < ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
                #True
                 ,
                   ( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                    * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                    + ( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                    * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                    * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) )
                #False (If Period > Third Merit Increase & Period < Fourth Month)
                 , IF( ATTRN( 'Period', !Period,'TM1 First Day Serial Date') >= ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
                  & ATTRN( 'Period', !Period,'TM1 First Day Serial Date') < ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fourth Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
                    #True
                     ,( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                        * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                        +( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                        * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                        * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) ) 
                        + ( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                        * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                        * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) )  
                        * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase %' ) )
                   #False (If Period > Fourth Merit Increase & Period < Fith Month)
                   , IF( ATTRN( 'Period', !Period,'TM1 First Day Serial Date') >= ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fourth Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
                    & ATTRN( 'Period', !Period,'TM1 First Day Serial Date') < ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fifth Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
                      #True
                       ,( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                          * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                          +( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                          * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                          * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) ) 
                          +( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                          * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                          * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) ) 
                          * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase %' ) ) 
                          + ( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                          * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                          * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) )  
                          * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase %' ) ) 
                          * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fourth Effective Merit Increase %' ) )
                        #IF Period > 5th
                        ,IF( ATTRN( 'Period', !Period,'TM1 First Day Serial Date') >= ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fifth Effective Merit Increase Month' ), 'TM1 First Day Serial Date') 
                            ,( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                            * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                            +( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                            * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                            * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) ) 
                            +( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                            * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                            * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) ) 
                            * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase %' ) ) 
                            +( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                            * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                            * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) ) 
                            * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase %' ) ) 
                            * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fourth Effective Merit Increase %' ) ) 
                            + ( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                            * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                            * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) )  
                            * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase %' ) )  
                            * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fourth Effective Merit Increase %' ) )  
                            * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fifth Effective Merit Increase %' ) )
                            ,0)
                          )
                        )
                    )
                  )
            ,IF( ATTRN( 'Period', !Period,'TM1 First Day Serial Date') >= ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
            & ATTRN( 'Period', !Period,'TM1 First Day Serial Date') < ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
              #True
              ,( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' )
              #False (If Period > Second Merit Increase & Period < THird Month)
              , IF( ATTRN( 'Period', !Period,'TM1 First Day Serial Date') >= ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
                  & ATTRN( 'Period', !Period,'TM1 First Day Serial Date') < ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
                #True
                 ,
                   ( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                    * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                    + ( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                    * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                    * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) )  
                #False (If Period > Third Merit Increase & Period < Fourth Month)
                 , IF( ATTRN( 'Period', !Period,'TM1 First Day Serial Date') >= ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
                  & ATTRN( 'Period', !Period,'TM1 First Day Serial Date') < ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fourth Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
                    #True
                     ,( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                        * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                        + ( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                        * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                        * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) )
                          + ( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                          * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                          * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) )  
                          * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase %' ) )
                   #False (If Period > Fourth Merit Increase & Period < Fith Month)
                   , IF( ATTRN( 'Period', !Period,'TM1 First Day Serial Date') >= ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fourth Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
                      & ATTRN( 'Period', !Period,'TM1 First Day Serial Date') < ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fifth Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
                      #True
                       ,( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                          * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                          +( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                          * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                          * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) ) 
                          +( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                          * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                          * (1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) ) 
                          * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase %' ) ) 
                            + ( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                            * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                            * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) )  
                            * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase %' ) ) 
                            * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fourth Effective Merit Increase %' ) )
                        #IF Period > 5th & Period < 6th Month)
                    , IF( ATTRN( 'Period', !Period,'TM1 First Day Serial Date') >= ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fifth Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
                        & ATTRN( 'Period', !Period,'TM1 First Day Serial Date') < ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Sixth Effective Merit Increase Month' ), 'TM1 First Day Serial Date')
                      #True
                       ,( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                          * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                          +( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                          * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                          * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) ) 
                          +( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                          * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                          * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) ) 
                          * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase %' ) )
                          +( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) )
                          * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                          * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) ) 
                          * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase %' ) ) 
                          * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fourth Effective Merit Increase %' ) )
                          + ( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                          * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                          * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) )  
                          * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase %' ) ) 
                          * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fourth Effective Merit Increase %' ) ) 
                          * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fifth Effective Merit Increase %' ) )
                            #IF Period > 6th
                            ,IF( ATTRN( 'Period', !Period,'TM1 First Day Serial Date') >= ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Sixth Effective Merit Increase Month' ), 'TM1 First Day Serial Date') 
                                ,( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                                * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                                +( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                                * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                                * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) ) 
                                +( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                                * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                                * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) ) 
                                * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase %' ) )
                                +( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                                * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                                * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) ) 
                                * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase %' ) ) 
                                * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fourth Effective Merit Increase %' ) )
                                + ( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                                * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                                * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) ) 
                                * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase %' ) ) 
                                * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fourth Effective Merit Increase %' ) ) 
                                * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fifth Effective Merit Increase %' ) )
                                + ( ( [ 'Base Salary' ] + ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,4,5,7' ) <> 0, 1, 0 ) ) ) 
                                * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Effective Merit Increase %' ) ) 
                                * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Second Effective Merit Increase %' ) )  
                                * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Third Effective Merit Increase %' ) )  
                                * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fourth Effective Merit Increase %' ) )  
                                * ( 1 + DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Fifth Effective Merit Increase %' ) )  
                                * DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Sixth Effective Merit Increase %' ) )
                                ,0)
                            ))))))))));

# Rule 3010 Promotion Increase
#---------------------
['Promotion Increase', 'Forecast'] = N:
      IF( ATTRN( 'Period', !Period, 'TM1 First Day Serial Date') >=
        ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'Promotion and STIP Date'), 'TM1 First Day Serial Date') &
        DB( '}ElementAttributes_Period',  DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'Promotion and STIP Date'), 'Year') @= DB( '}ElementAttributes_Period', !Period, 'Year')  ,
     #THEN
        DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'Promotion Amount' ) *
        IF( DB('Workforce Planning Center Location Assumptions', !Version, !Location WFP, DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' ) = 0
          , 1 \ 12
          , DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, !Period, 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' ) \ DB('Workforce Planning Center Location Assumptions', !Version, !Location WFP, DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Pay Periods per Month' )
        )
     #ELSE
     ,0);

#Rule 3011 PTO Accruals
['PTO Accruals', 'Forecast'] = N:
  DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, !Period, 'Blank Job Grade', !Center WFP, 'Accrued PTO %' ) *
  DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period, !Planning Currency, !Job Title, !Center WFP, !Employee, 'Salaries-Regular' );

# Rule 3100 Salary Attrition
# ------------------------------
[ 'Salary Attrition', 'Forecast' ] = N:
   DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period, !Planning Currency, !Job Title, !Center WFP, !Employee, 'Salaries-Regular' ) *
   DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, !Period, 'Blank Job Grade', !Center WFP, 'Attrition Rate' ) * -1;

# Rule 3110 Benefits Attrition
#--------------------------------
['Benefits Attrition', 'Forecast' ] = N:
   DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period, !Planning Currency, !Job Title, !Center WFP, !Employee, 'Total Annual Benefits No Attrition') *
   DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, !Period, 'Blank Job Grade', !Center WFP, 'Attrition Rate' ) * -1;

# Rule 3120 Employee FTE Attrition
#--------------------------------
['Employee FTE Attrition', 'Forecast' ] = N:

IF( ELISANC( 'Employee', 'Existing', !Employee ) = 1  %  ELISANC( 'Employee', 'New', !Employee ) = 1  %  ELISANC( 'Employee', 'Replacement', !Employee ) = 1,  
  
   DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period, !Planning Currency, !Job Title, !Center WFP, !Employee, 'Total FTE No Attrition') *
   DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, !Period, 'Blank Job Grade', !Center WFP, 'Attrition Rate' ) * -1,
  
CONTINUE);



# Rule 3130 Employee HC Attrition:Employee HC Attrition
#--------------------------------
['Employee HC Attrition', 'Forecast' ] = N:

IF( ELISANC( 'Employee', 'Existing', !Employee ) = 1  %  ELISANC( 'Employee', 'New', !Employee ) = 1  %  ELISANC( 'Employee', 'Replacement', !Employee ) = 1,
  
   DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period, !Planning Currency, !Job Title, !Center WFP, !Employee, 'Total Head Count No Attrition') *
   DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, !Period, 'Blank Job Grade', !Center WFP, 'Attrition Rate' ) * -1,

CONTINUE);  

#############
# Bonus Calculations
#############

# Rule 3200 Bonus-Non STIP
# -------------------------------------
[ 'Bonus-Non STIP', 'Forecast' ] = N:
IF( DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Non STIP Bonus Amt' ) <> 0
    , IF( DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'FTE' ) > 1 %
      DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'FTE' ) < 0
          , DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Non STIP Bonus Amt' ) \ 12 *
            ( ['Employee Head Count'] + ['New HC'] + ['Replacement HC'] )
          , DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Center WFP, !Employee, 'Non STIP Bonus Amt' ) \ 12
     )
    , CONTINUE
);

# Rule 3210 Bonus-Incentive
# -- Back out Allowances from Calc if they Benefits Basis does not Include them
# ---------------------------------
[ 'Bonus-Incentive', 'Forecast' ] = N:
IF( DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'STIP % Override' ) = 0 %
 DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'Promotion and STIP Date')@= '' %
  ATTRN( 'Period', !Period, 'TM1 First Day Serial Date') <
  ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'Promotion and STIP Date'), 'TM1 First Day Serial Date')
     , IF( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'STIP Factor' ) <> 0
         , DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'STIP PCT' ) *
           ( IF( [ 'Salaries-Regular' ] <> 0  &  (SUBST( !Period , 5, 2) @= '01' % SUBST( !Period , 5, 2) @= '02'), 
            DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, DB( '}ElementAttributes_Period',!Period,'Year') | '03', !Planning Currency, !Job Title , !Center WFP, !Employee, 'Salaries-Regular' )
            ,[ 'Salaries-Regular' ])
            - ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,5,6,8' ) = 0, 1, 0 ) ) ) *
           DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'STIP Factor' )
         , IF( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'STIP Factor' ) = 0
               , DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'STIP PCT' ) * ( [ 'Salaries-Regular' ] - ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,5,6,8' ) = 0, 1, 0 ) ) )
               , 0
           )
     ),

 IF( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'STIP Factor' ) <> 0 
   &
   ATTRN( 'Period', !Period, 'TM1 First Day Serial Date') >=
   ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'Promotion and STIP Date'), 'TM1 First Day Serial Date')
        , DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'STIP % Override' ) *
        ( IF( [ 'Salaries-Regular' ] <> 0  &  (SUBST( !Period , 5, 2) @= '01' % SUBST( !Period , 5, 2) @= '02'), 
        DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, DB( '}ElementAttributes_Period',!Period,'Year') | '03', !Planning Currency, !Job Title , !Center WFP, !Employee, 'Salaries-Regular' )
        ,[ 'Salaries-Regular' ])
            - ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,5,6,8' ) = 0, 1, 0 ) ) ) *
           DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'STIP Factor' )
           , IF( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'STIP Factor' ) = 0 &
             ATTRN( 'Period', !Period, 'TM1 First Day Serial Date') >=
              ATTRN( 'Period', DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'Promotion and STIP Date'), 'TM1 First Day Serial Date')
        , DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'STIP % Override' ) *
           ( IF( [ 'Salaries-Regular' ] <> 0  &  (SUBST( !Period , 5, 2) @= '01' % SUBST( !Period , 5, 2) @= '02'), 
            DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, DB( '}ElementAttributes_Period',!Period,'Year') | '03', !Planning Currency, !Job Title , !Center WFP, !Employee, 'Salaries-Regular' )
            ,[ 'Salaries-Regular' ])
            - ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,5,6,8' ) = 0, 1, 0 ) ) )
	 ,0)
       ) 
);


# Rule 3220 Long Term Incentive Bonus
# LTIP is stored as USD in Employee Asumptions cube and must be converted
# -------------------------------------

[ 'Long Term Incentive Bonus', 'Forecast' ] = N:
IF( ['Base Salary'] <> 0 & ( SUBST( !Period, 5, 2 ) @= '01' % SUBST( !Period, 5, 2 ) @= '02' % SUBST( !Period, 5, 2 ) @= '03' )
,
  IF( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'LTIP Factor' ) = 0
  , DB( 'Workforce Planning Employee Assumptions', !Version, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Employee, 'LTIP Q1' ) \ 3 \ DB('Exchange Rates',!Version,!Planning Currency,!Period,'Monthly Average Rate')
  , DB( 'Workforce Planning Employee Assumptions', !Version, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Employee, 'LTIP Q1' ) \ 3 \ DB('Exchange Rates',!Version,!Planning Currency,!Period,'Monthly Average Rate') *
    DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'LTIP Factor' )
  )
, CONTINUE );

[ 'Long Term Incentive Bonus', 'Forecast' ] = N:
IF( ['Base Salary'] <> 0 & ( SUBST( !Period, 5, 2 ) @= '04' % SUBST( !Period, 5, 2 ) @= '05' % SUBST( !Period, 5, 2 ) @= '06' )
,
  IF( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'LTIP Factor' ) = 0
  , DB( 'Workforce Planning Employee Assumptions', !Version, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Employee, 'LTIP Q2' ) \ 3 \ DB('Exchange Rates',!Version,!Planning Currency,!Period,'Monthly Average Rate')
  , DB( 'Workforce Planning Employee Assumptions', !Version, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Employee, 'LTIP Q2' ) \ 3 \ DB('Exchange Rates',!Version,!Planning Currency,!Period,'Monthly Average Rate') *
    DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'LTIP Factor' )
  )
, CONTINUE );

[ 'Long Term Incentive Bonus', 'Forecast' ] = N:
IF( ['Base Salary'] <> 0 & ( SUBST( !Period, 5, 2 ) @= '07' % SUBST( !Period, 5, 2 ) @= '08' % SUBST( !Period, 5, 2 ) @= '09' )
,
  IF( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'LTIP Factor' ) = 0
  , DB( 'Workforce Planning Employee Assumptions', !Version, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Employee, 'LTIP Q3' ) \ 3 \ DB('Exchange Rates',!Version,!Planning Currency,!Period,'Monthly Average Rate')
  , DB( 'Workforce Planning Employee Assumptions', !Version, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Employee, 'LTIP Q3' ) \ 3 \ DB('Exchange Rates',!Version,!Planning Currency,!Period,'Monthly Average Rate') *
    DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'LTIP Factor' )
  )
, CONTINUE );

[ 'Long Term Incentive Bonus', 'Forecast' ] = N:
IF( ['Base Salary'] <> 0 & ( SUBST( !Period, 5, 2 ) @= '10' % SUBST( !Period, 5, 2 ) @= '11' % SUBST( !Period, 5, 2 ) @= '12' )
,
  IF( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'LTIP Factor' ) = 0
  , DB( 'Workforce Planning Employee Assumptions', !Version, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Employee, 'LTIP Q4' ) \ 3 \ DB('Exchange Rates',!Version,!Planning Currency,!Period,'Monthly Average Rate')
  , DB( 'Workforce Planning Employee Assumptions', !Version, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), !Employee, 'LTIP Q4' ) \ 3 \ DB('Exchange Rates',!Version,!Planning Currency,!Period,'Monthly Average Rate') *
    DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'LTIP Factor' )
  )
, CONTINUE );


#########################################################
# INTERNATIONAL SPECIFIC
#########################################################

# Rule 3300 International Benefits
# -- Back out Allowances from Calc if they Benefits Basis does not Include them
# -----------------------

[ 'International Benefits', 'Forecast' ] = N:
IF( DB( 'Mapping Center Location', !Location WFP, !Center WFP, 'Default Entity' ) @<> 'TBA Entity'
  , DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Elective Benefits %' )  
       * 
      ( ( [ 'Salaries-Regular' ] - ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0, 1, 0 ) ) ) 
         + ['PTO Accruals'] + ['Total Annual Bonus']) *
       IF( DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'FTE' ) > 1 %
            DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'FTE' ) < 0
               , ( ['Employee Head Count'] + ['New HC'] + ['Replacement HC'] )
               , 1
        )
    , 0
) ;

# [ 'International Benefits', 'Forecast' ] = N:
# IF( DB( 'Mapping Center Location', !Location WFP, !Center WFP, 'Default Entity' ) @<> 'TBA Entity'
#     , ( ( [ 'Salaries-Regular' ] - ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0, 1, 0 ) ) ) * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Regulatory Benefits %' ) )+
#       ( ( [ 'Salaries-Regular' ] - ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0, 1, 0 ) ) ) * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Elective Benefits %' ) ) +
#        DB( 'Workforce Planning Center Location Assumptions', !Version, !Scenario, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'International Benefits Flat Rate' ) \ 12 *
#        IF( DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'FTE' ) > 1 %
#             DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'FTE' ) < 0
#                , ( ['Employee Head Count'] + ['New HC'] + ['Replacement HC'] )
#                , 1
#          )
#     , 0
# ) ;

#########################################################
# US Employees Only
#########################################################

# Rule 3400 US Employees Only
# -----------------------
[ 'Forecast' ] = N:
IF( DB( 'SCN Workforce Planning Input', !Version, !Scenario, !Location WFP, 'Input '| DB( '}ElementAttributes_Version', !Version, 'Version Year' ), !Center WFP, !Employee, 'Currency Code' ) @= 'USD'
    , CONTINUE
    , STET
);

# Rule 3410 FTE
[ 'FTE' , 'Forecast' ] = N: IF( ['Total FTE No Attrition'] >= 0,
- INT( - ['Total FTE No Attrition'] ),
ROUND(  ( ['Total FTE No Attrition'] + -0.49999) ));


# Employer FICA
# -------------------------------------

# Rule 3500
[ 'Employer FICA', 'Forecast' ] = N:
IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0
# Benefits Not based on Total
,
  IF( ELISANC( 'Employee', 'Existing', !Employee ) = 1 & DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' )
  <= DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA Wage Base' )
  , [ 'Salaries-Regular Less Allowances' ] * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA %' )
  , CONTINUE )
# Benefits Based on Total
,
  IF( ELISANC( 'Employee', 'Existing', !Employee ) = 1 & DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' )
  <= DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA Wage Base' )
  , [ 'Salaries-Regular' ] * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA %' )
  , CONTINUE )
);

# Rule 3510
[ 'Employer FICA', 'Forecast' ] = N:
IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0
# Benefits Not based on Total
,
  IF( ELISANC( 'Employee', 'Existing', !Employee ) = 1 & ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' ) - [ 'Salaries-Regular Less Allowances' ] )
  < DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA Wage Base' )
  , ( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA Wage Base' )
  - ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' )
  -  [ 'Salaries-Regular Less Allowances' ] )
  ) * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA %' )
  , CONTINUE )
# Benefits Based on Total
,
  IF( ELISANC( 'Employee', 'Existing', !Employee ) = 1 & ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' ) - [ 'Salaries-Regular' ] )
  < DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA Wage Base' )
  , ( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA Wage Base' )
  - ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' )
  -  [ 'Salaries-Regular' ] )
  ) * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA %' )
  , CONTINUE )
);

# Rule 3520
[ 'Employer FICA', 'Forecast' ] = N:
IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0
# Benefits Not based on Total
,
  IF( ELISANC( 'Employee', 'New', !Employee ) = 1 & ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' ) \ ['FTE'] )
  <= DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA Wage Base' )
  , [ 'Salaries-Regular Less Allowances' ] * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA %' )
  , CONTINUE
  )
# Benefits Based on Total
,
  IF( ELISANC( 'Employee', 'New', !Employee ) = 1 & ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' ) \ ['FTE'] )
  <= DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA Wage Base' )
  , [ 'Salaries-Regular' ] * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA %' )
  , CONTINUE
  )
);

# Rule 3530
[ 'Employer FICA', 'Forecast' ] = N:
IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0
# Benefits Not based on Total
,
  IF( ELISANC( 'Employee', 'New', !Employee ) = 1 & ( ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' ) \ ['FTE'] ) - ( [ 'Salaries-Regular Less Allowances' ] \ ['FTE'] ) )
  < DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA Wage Base' )
  ,( ( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA Wage Base' )
  - (   ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' ) \ ['FTE'] )
  -  ( [ 'Salaries-Regular Less Allowances' ] \ ['FTE'] ) )
  ) * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA %' ) ) * ['FTE']
  , CONTINUE )
# Benefits Based on Total
,
  IF( ELISANC( 'Employee', 'New', !Employee ) = 1 & ( ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' ) \ ['FTE'] ) - ( [ 'Salaries-Regular' ] \ ['FTE'] ) )
  < DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA Wage Base' )
  ,( ( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA Wage Base' )
  - (   ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' ) \ ['FTE'] )
  -  ( [ 'Salaries-Regular' ] \ ['FTE'] ) )
  ) * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA %' ) ) * ['FTE']
  , CONTINUE )
);

# Rule 3540
[ 'Employer FICA', 'Forecast' ] = N:
IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0
# Benefits Not based on Total
,
  IF( ELISANC( 'Employee', 'Replacement', !Employee ) = 1 & ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' ) \ ['FTE'] )
  <= DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA Wage Base' )
  , [ 'Salaries-Regular Less Allowances' ] * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA %' )
  , CONTINUE )
# Benefits Based on Total
,
  IF( ELISANC( 'Employee', 'Replacement', !Employee ) = 1 & ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' ) \ ['FTE'] )
  <= DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA Wage Base' )
  , [ 'Salaries-Regular' ] * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA %' )
  , CONTINUE )
);

# Rule 3550
[ 'Employer FICA', 'Forecast' ] = N:
IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0
# Benefits Not based on Total
,
  IF( ELISANC( 'Employee', 'Replacement', !Employee ) = 1 & ( ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' ) \ ['FTE'] ) - ( [ 'Salaries-Regular Less Allowances' ] \ ['FTE'] ) )
  < DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA Wage Base' )
  ,( ( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA Wage Base' )
  - (   ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' ) \ ['FTE'] )
  -  ( [ 'Salaries-Regular Less Allowances' ] \ ['FTE'] ) )
  ) * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA %' ) ) * ['FTE']
  , CONTINUE )
# Benefits Based on Total
,
  IF( ELISANC( 'Employee', 'Replacement', !Employee ) = 1 & ( ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' ) \ ['FTE'] ) - ( [ 'Salaries-Regular' ] \ ['FTE'] ) )
  < DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA Wage Base' )
  ,( ( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA Wage Base' )
  - (   ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' ) \ ['FTE'] )
  -  ( [ 'Salaries-Regular' ] \ ['FTE'] ) )
  ) * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FICA %' ) ) * ['FTE']
  , CONTINUE )
);


# Employer FUTA
# ---------------------------------------

# Rule 3600
[ 'Employer FUTA', 'Forecast' ] = N:
IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0
# Benefits Not based on Total
,
  IF( ELISANC( 'Employee', 'Existing', !Employee ) = 1 & DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' )
  <= DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA Wage Base' )
  , [ 'Salaries-Regular Less Allowances' ] * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA %' )
  , CONTINUE )
# Benefits Based on Total
,
  IF( ELISANC( 'Employee', 'Existing', !Employee ) = 1 & DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' )
  <= DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA Wage Base' )
  , [ 'Salaries-Regular' ] * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA %' )
  , CONTINUE )
);

# Rule 3610
[ 'Employer FUTA', 'Forecast' ] = N:
IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0
# Benefits Not based on Total
,
  IF( ELISANC( 'Employee', 'Existing', !Employee ) = 1 & ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' ) - [ 'Salaries-Regular Less Allowances' ] )
  < DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA Wage Base' )
  , ( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA Wage Base' )
  - ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' )
  -  [ 'Salaries-Regular Less Allowances' ] )
  ) * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA %' )
  , CONTINUE )
# Benefits Based on Total
,
  IF( ELISANC( 'Employee', 'Existing', !Employee ) = 1 & ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' ) - [ 'Salaries-Regular' ] )
  < DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA Wage Base' )
  , ( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA Wage Base' )
  - ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' )
  -  [ 'Salaries-Regular' ] )
  ) * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA %' )
  , CONTINUE )
);

# Rule 3620
[ 'Employer FUTA', 'Forecast' ] = N:
IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0
# Benefits Not based on Total
,
  IF( ELISANC( 'Employee', 'New', !Employee ) = 1 & ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' ) \ ['FTE'] )
  <= DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA Wage Base' )
  , [ 'Salaries-Regular Less Allowances' ] * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA %' )
  , CONTINUE )
# Benefits Based on Total
,
  IF( ELISANC( 'Employee', 'New', !Employee ) = 1 & ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' ) \ ['FTE'] )
  <= DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA Wage Base' )
  , [ 'Salaries-Regular' ] * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA %' )
  , CONTINUE )
);

# Rule 3630
[ 'Employer FUTA', 'Forecast' ] = N:
IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0
# Benefits Not based on Total
,
  IF( ELISANC( 'Employee', 'New', !Employee ) = 1 & ( ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' ) \ ['FTE'] ) - ( [ 'Salaries-Regular Less Allowances' ] \ ['FTE'] ) )
  < DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA Wage Base' )
  ,( ( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA Wage Base' )
  - (   ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' ) \ ['FTE'] )
  -  ( [ 'Salaries-Regular Less Allowances' ] \ ['FTE'] ) )
  ) * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA %' ) ) * ['FTE']
  , CONTINUE )
# Benefits Based on Total
,
  IF( ELISANC( 'Employee', 'New', !Employee ) = 1 & ( ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' ) \ ['FTE'] ) - ( [ 'Salaries-Regular' ] \ ['FTE'] ) )
  < DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA Wage Base' )
  ,( ( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA Wage Base' )
  - (   ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' ) \ ['FTE'] )
  -  ( [ 'Salaries-Regular' ] \ ['FTE'] ) )
  ) * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA %' ) ) * ['FTE']
  , CONTINUE )
);

# Rule 3640
[ 'Employer FUTA', 'Forecast' ] = N:
IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0
# Benefits Not based on Total
,
  IF( ELISANC( 'Employee', 'Replacement', !Employee ) = 1 & ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' ) \ ['FTE'] )
  <= DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA Wage Base' )
  , [ 'Salaries-Regular Less Allowances' ] * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA %' )
  , CONTINUE )
# Benefits Based on Total
,
  IF( ELISANC( 'Employee', 'Replacement', !Employee ) = 1 & ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' ) \ ['FTE'] )
  <= DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA Wage Base' )
  , [ 'Salaries-Regular' ] * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA %' )
  , CONTINUE )
);

# Rule 3650
[ 'Employer FUTA', 'Forecast' ] = N:
IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0
# Benefits Not based on Total
,
  IF( ELISANC( 'Employee', 'Replacement', !Employee ) = 1 & ( ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' ) \ ['FTE'] ) - ( [ 'Salaries-Regular Less Allowances' ] \ ['FTE'] ) )
  < DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA Wage Base' )
  ,( ( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA Wage Base' )
  - (   ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' ) \ ['FTE'] )
  -  ( [ 'Salaries-Regular Less Allowances' ] \ ['FTE'] ) )
  ) * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA %' ) ) * ['FTE']
  , CONTINUE )
# Benefits Based on Total
,
  IF( ELISANC( 'Employee', 'Replacement', !Employee ) = 1 & ( ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' ) \ ['FTE'] ) - ( [ 'Salaries-Regular' ] \ ['FTE'] ) )
  < DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA Wage Base' )
  ,( ( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA Wage Base' )
  - (   ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' ) \ ['FTE'] )
  -  ( [ 'Salaries-Regular' ] \ ['FTE'] ) )
  ) * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'FUTA %' ) ) * ['FTE']
  , CONTINUE )
);


# Employer SUTA
# -----------------------------------------------

# Rule 3700
[ 'Employer SUTA', 'Forecast' ] = N:
IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0
# Benefits Not based on Total
,
  IF( ELISANC( 'Employee', 'Existing', !Employee ) = 1 & DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' )
  <= DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA Wage Base' )
  , [ 'Salaries-Regular Less Allowances' ] * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA %' )
  , CONTINUE )
# Benefits Based on Total
,
IF( ELISANC( 'Employee', 'Existing', !Employee ) = 1 & DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' )
  <= DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA Wage Base' )
  , [ 'Salaries-Regular' ] * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA %' )
  , CONTINUE )
);

# Rule 3710
[ 'Employer SUTA', 'Forecast' ] = N:
IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0
# Benefits Not based on Total
,
  IF( ELISANC( 'Employee', 'Existing', !Employee ) = 1 & ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' ) - [ 'Salaries-Regular Less Allowances' ] )
  < DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA Wage Base' )
  , ( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA Wage Base' )
  - ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' )
  -  [ 'Salaries-Regular Less Allowances' ] )
  ) * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA %' )
  , CONTINUE )
# Benefits Based on Total
,
  IF( ELISANC( 'Employee', 'Existing', !Employee ) = 1 & ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' ) - [ 'Salaries-Regular' ] )
  < DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA Wage Base' )
  , ( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA Wage Base' )
  - ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' )
  -  [ 'Salaries-Regular' ] )
  ) * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA %' )
  , CONTINUE )
);

# Rule 3720
[ 'Employer SUTA', 'Forecast' ] = N:
IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0
# Benefits Not based on Total
,
  IF( ELISANC( 'Employee', 'New', !Employee ) = 1 & ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' ) \ ['FTE'] )
  <= DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA Wage Base' )
  , [ 'Salaries-Regular Less Allowances' ] * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA %' )
  , CONTINUE )
# Benefits Based on Total
,
  IF( ELISANC( 'Employee', 'New', !Employee ) = 1 & ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' ) \ ['FTE'] )
  <= DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA Wage Base' )
  , [ 'Salaries-Regular' ] * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA %' )
  , CONTINUE )
);

# Rule 3730
[ 'Employer SUTA', 'Forecast' ] = N:
IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0
# Benefits Not based on Total
,
  IF( ELISANC( 'Employee', 'New', !Employee ) = 1 & ( ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' ) \ ['FTE'] ) - ( [ 'Salaries-Regular Less Allowances' ] \ ['FTE'] ) )
  < DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA Wage Base' )
  ,( ( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA Wage Base' )
  - (   ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' ) \ ['FTE'] )
  -  ( [ 'Salaries-Regular Less Allowances' ] \ ['FTE'] ) )
  ) * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA %' ) ) * ['FTE']
  , CONTINUE )
# Benefits Based on Total
,
  IF( ELISANC( 'Employee', 'New', !Employee ) = 1 & ( ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' ) \ ['FTE'] ) - ( [ 'Salaries-Regular' ] \ ['FTE'] ) )
  < DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA Wage Base' )
  ,( ( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA Wage Base' )
  - (   ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' ) \ ['FTE'] )
  -  ( [ 'Salaries-Regular' ] \ ['FTE'] ) )
  ) * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA %' ) ) * ['FTE']
  , CONTINUE )
);

# Rule 3740
[ 'Employer SUTA', 'Forecast' ] = N:
IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0
# Benefits Not based on Total
,
  IF( ELISANC( 'Employee', 'Replacement', !Employee ) = 1 & ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' ) \ ['FTE'] )
  <= DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA Wage Base' )
  , [ 'Salaries-Regular Less Allowances' ] * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA %' )
  , CONTINUE )
# Benefits Based on Total
,
  IF( ELISANC( 'Employee', 'Replacement', !Employee ) = 1 & ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' ) \ ['FTE'] )
  <= DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA Wage Base' )
  , [ 'Salaries-Regular' ] * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA %' )
  , CONTINUE )
);

# Rule 3750
[ 'Employer SUTA', 'Forecast' ] = N:
IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0
# Benefits Not based on Total
,
IF( ELISANC( 'Employee', 'Replacement', !Employee ) = 1 & ( ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' ) \ ['FTE'] ) - ( [ 'Salaries-Regular Less Allowances' ] \ ['FTE'] ) )
< DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA Wage Base' )
,( ( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA Wage Base' )
- (   ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' ) \ ['FTE'] )
-  ( [ 'Salaries-Regular Less Allowances' ] \ ['FTE' ] ) )
) * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA %' ) ) * ['FTE']
, CONTINUE )
# Benefits Based on Total
,
IF( ELISANC( 'Employee', 'Replacement', !Employee ) = 1 & ( ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' ) \ ['FTE'] ) - ( [ 'Salaries-Regular' ] \ ['FTE'] ) )
< DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA Wage Base' )
,( ( DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA Wage Base' )
- (   ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', !Planning Currency, 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' ) \ ['FTE'] )
-  ( [ 'Salaries-Regular' ] \ ['FTE' ] ) )
) * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'SUTA %' ) ) * ['FTE']
, CONTINUE )
);


###############
# Other Calculations
###############

# Rule 3800 Grp Ins Employee-Co Cost
# ---------------------------------------------
[ 'Grp Ins Employee-Co Cost', 'Forecast' ] = N:
IF( ELISANC( 'Employee', 'Existing', !Employee ) = 1  %  ELISANC( 'Employee', 'New', !Employee ) = 1  %  ELISANC( 'Employee', 'Replacement', !Employee ) = 1,
 IF(DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Grp Ins Employee-Co Cost Flat Rate' ) <> 0,
 (DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Grp Ins Employee-Co Cost Flat Rate' )\12) *  
  (
   ['Employee Full Time Equivalents']
   +
   ['Replacement FTE']
   +
   ['New FTE']
   +
   ['Employee FTE Attrition']
  ),
 ( [ 'Salaries-Regular' ] - ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0, 1, 0 ) ) )  * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, 'Grp Ins Employee-Co Cost %' ))
,CONTINUE);

# # Rule 3820 401k Employee Match
# ---------------------------------------
[ '401k Employee Match', 'Forecast' ] = N:
( [ 'Salaries-Regular' ] - ( [ 'Allowances' ] * IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0, 1, 0 ) ) )  * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, '401K Employee Match %' );

# 401k Executive Plan
# -------------------------------------

# Rule 3900
[ '401k Executive Plan', 'Forecast' ] = N: IF( ['FTE'] > 1 , STET, CONTINUE);

# Rule 3910
[ '401k Executive Plan', 'Forecast' ] = N:
IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0
# Benefits Not based on Total
,
IF( SUBST(!Period,5,2) @= '01' % SUBST(!Period,5,2) @= '02'
  ,IF( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' )
  > DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, '401K Exec Plan Limit' )
  , [ 'Salaries-Regular Less Allowances' ] * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, '401K Executive Plan %' )
  , CONTINUE )
  ,CONTINUE )
# Benefits Based on Total
,
  IF( SUBST(!Period,5,2) @= '01' % SUBST(!Period,5,2) @= '02'
  ,IF( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' )
  > DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, '401K Exec Plan Limit' )
  , [ 'Salaries-Regular' ] * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, '401K Executive Plan %' )
  , CONTINUE )
  ,CONTINUE )
);

# Rule 3920
[ '401k Executive Plan', 'Forecast' ] = N:
IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0
# Benefits Not based on Total
,
  IF( SUBST(!Period,5,2) @= '03'
  , IF(    ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' ) +
   DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP,  DB( '}ElementAttributes_Period', !Period, 'Year' ), 'USD', 'All Job Titles', !Center WFP, !Employee, 'Bonus-Incentive' ) )
  > DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, '401K Exec Plan Limit' )
  ,( [ 'Salaries-Regular Less Allowances' ] + DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP,  DB( '}ElementAttributes_Period', !Period, 'Year' ), 'USD', 'All Job Titles', !Center WFP, !Employee, 'Bonus-Incentive' ) )
  * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, '401K Executive Plan %' )
  , CONTINUE )
  ,CONTINUE )
# Benefits Based on Total
,
  IF( SUBST(!Period,5,2) @= '03'
  , IF(    ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' ) +
   DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP,  DB( '}ElementAttributes_Period', !Period, 'Year' ), 'USD', 'All Job Titles', !Center WFP, !Employee, 'Bonus-Incentive' ) )
  > DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, '401K Exec Plan Limit' )
  ,( [ 'Salaries-Regular' ] + DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP,  DB( '}ElementAttributes_Period', !Period, 'Year' ), 'USD', 'All Job Titles', !Center WFP, !Employee, 'Bonus-Incentive' ) )
  * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, '401K Executive Plan %' )
  , CONTINUE )
  ,CONTINUE )
);

# Rule 3930
[ '401k Executive Plan', 'Forecast' ] = N:
IF( SCAN( DB( '}ElementAttributes_Location WFP', !Location WFP, 'Benefits Basis' ), '2,3,4,8' ) = 0
# Benefits Not based on Total
,
  IF( SUBST(!Period,5,2) @<> '01' & SUBST(!Period,5,2) @<> '02' & SUBST(!Period,5,2) @<> '03'
  , IF(    ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular Less Allowances' ) +
   DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP,  DB( '}ElementAttributes_Period', !Period, 'Year' ), 'USD', 'All Job Titles', !Center WFP, !Employee, 'Bonus-Incentive' ) )
  > DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, '401K Exec Plan Limit' )
  , [ 'Salaries-Regular Less Allowances' ] * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, '401K Executive Plan %' )
  , STET )
  ,STET )
# Benefits Based on Total
,
  IF( SUBST(!Period,5,2) @<> '01' & SUBST(!Period,5,2) @<> '02' & SUBST(!Period,5,2) @<> '03'
  , IF(    ( DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period | ' YTD', 'USD', 'All Job Titles', !Center WFP, !Employee, 'Salaries-Regular' ) +
   DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP,  DB( '}ElementAttributes_Period', !Period, 'Year' ), 'USD', 'All Job Titles', !Center WFP, !Employee, 'Bonus-Incentive' ) )
  > DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, '401K Exec Plan Limit' )
  , [ 'Salaries-Regular' ] * DB( 'Workforce Planning Center Location Assumptions', !Version, !Location WFP, 'Input ' | DB( '}ElementAttributes_Period', !Period, 'Year' ), 'Blank Job Grade', !Center WFP, '401K Executive Plan %' )
  , STET )
  ,STET )
);

########################################################################################
 
FEEDERS;

# Currency Translation
# -----------------

# Rule 0300
[ 'Forecast', 'Planning Currency':'EUR' ] => ['EUR to GBP'];

# Rule 0310
[ 'Forecast', 'Planning Currency': 'GBP' ] => ['GBP to EUR'];

# Rule 0320
[ 'Forecast' ] => DB( 'SCN Workforce Planning Summary', !Version, !Scenario, !Location WFP, !Period, IF( LONG( !Planning Currency ) = 3, !Planning Currency | ' to USD', '' ), !Job Title, !Center WFP, !Employee, !SCN Workforce Planning Account );

# Rule 1400 - 1450 Bulk Planning
# -----------------
[ { 'Employee Headcount', 'New HC', 'Replacement HC' }, { 'Bulk Employee Reduction', 'Bulk Employee New', 'Bulk Employee Replacement'}, 'Forecast'] => [ 'Base Salary' ], [ 'Allowances' ], [ 'Merit Increase' ];

# RULE 1100, 1230, 1240, 1300, 1310, 1320, 2200 - 2250 FTE and HC
# -----------------

[ 'Base Salary', 'Forecast' ] => [ 'Employee Full Time Equivalents' ], [ 'Employee Head Count' ];

[ 'Base Salary', 'Forecast' ] => [ 'Replacement FTE' ], [ 'Replacement HC' ];

[ 'Base Salary', 'Forecast' ] => [ 'New FTE' ], [ 'New HC'];

[ 'Base Salary', 'Forecast' ] => [ 'Employee FTE Attrition' ], [ 'Employee HC Attrition'];

[ 'Base Salary', 'Forecast' ] => [ 'FTE' ];

# Other Resources
# -----------------
# Rule 2330, 2360
[ 'Temporary Help', 'Forecast' ] => [ 'Temp Help FTE' ], [ 'Temp Help HC' ] ;
# Rule 2340, 2370
[ 'IT Application Maintenance and Support Contracted Services', 'Forecast' ] => [ 'Tech Support Contractor FTE' ], [ 'Tech Support Contractor HC' ];
# Rule 2350, 2380
[ 'IT Application Development Contracted Services', 'Forecast' ] => [ 'Tech Dev Contractor FTE' ], [ 'Tech Dev Contractor HC' ];


# Rule 3000 Merit Increase
# -----------------
[ 'Base Salary', 'Forecast' ] => [ 'Merit Increase' ];

# Rule 3010 Promotion Increase
# -----------------
[ 'Base Salary', 'Forecast' ] => [ 'Promotion Increase' ];

# Rule 3011 PTO Accruals
# -----------------
[ 'Base Salary', 'Forecast' ] => [ 'PTO Accruals' ];

# Rule 1200, 3100 Salary Attrition
# -----------------
[ 'Base Salary', 'Forecast' ] => [ 'Salary Attrition' ];

# Rule 1210, 3110 Benefits Attrition
# -----------------
[ 'Base Salary', 'Forecast' ] => [ 'Benefits Attrition' ];

# Rule 1460, 3200 Bonus-Non STIP
# -----------------
[ 'Base Salary', 'Forecast' ] => [ 'Bonus-Non STIP' ];

# Rule 3210 Bonus-Incentive
# -----------------
[ 'Base Salary', 'Forecast' ] => [ 'Bonus-Incentive' ];

# Rule 3220 Long Term Incentive Bonus
# -----------------
[ 'Base Salary', 'Forecast' ] => [ 'Long Term Incentive Bonus' ];

# Rule 1500, 3300 International Benefits
# -----------------
[ 'Base Salary', 'Forecast' ] => [ 'International Benefits' ];

# -----------------------
# US Employees Only
# -----------------------

# Rule 1650, 3500 - 3550 FICA
# -----------------
[ 'Base Salary', 'USD', 'Forecast' ] => [ 'Employer FICA' ];

# Rule 1660, 3600 - 3650 FUTA
# -----------------
[ 'Base Salary', 'USD', 'Forecast'] => [ 'Employer FUTA' ];

# Rule 1640, 3700 - 3750 SUTA
# -----------------
[ 'Base Salary', 'USD', 'Forecast' ] => [ 'Employer SUTA' ];

# Rule 1600, 3800 Grp Ins Employee-Co Cost
# -----------------
[ 'Base Salary', 'USD', 'Forecast' ] => [ 'Grp Ins Employee-Co Cost' ];

# Rule 1620, 3820 401k Employee Match
# -----------------
[ 'Base Salary', 'USD', 'Forecast' ] => [ '401k Employee Match' ];

# Rule 1630, 3900 - 3930 401k Executive Plan
# -----------------
[ 'Base Salary', 'USD', 'Forecast' ] => [ '401k Executive Plan' ];
