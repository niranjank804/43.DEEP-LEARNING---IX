SKIPCHECK ;

###################################################
# Rule for Financial Summary
# Created: 08/28/18 CW/QueBIT
#
# Modification History:
#  01/31/23: Updated for GFS Structure MD/QueBIT
# 10/03/2024: Updated for new dimensionality / GZ QueBIT
#  07/30/2025: Updated EOP logic
#
###################################################


# Rule 0100 EOP Rule
# no feeder
[ {'Final Amount' , 'Allocations' , 'Original Amount'} ] = C: 
  IF ( DTYPE ( 'Period', !Period ) @= 'C' & !Period@<>'All Time' & !Version@<>'Actual' & DB( '}ElementAttributes_Account' , !Account , 'EOP' ) @= 'Y'
       , DB('Financial Summary',!Version,!Currency,!Location,DB( '}ElementAttributes_Period', !Period, 'Final Month'),!Entity,!Affiliate, !Charging Center, !Center,!Client,!Product,!Account,!Financial Summary Measure)
       , CONTINUE ) ;



[ 'Actual',{'Final Amount' , 'Allocations' , 'Original Amount'} ] = C: 
  IF ( DTYPE ( 'Period', !Period ) @= 'C' & 
       (SCAN('Q1', !Period)<> 0  %  SCAN('Q2', !Period)<> 0  %  SCAN('Q3', !Period)<> 0  %  SCAN('Q4', !Period)<> 0  % LONG(!Period)= 4)           & 
	   DB( '}ElementAttributes_Account' , !Account , 'EOP' ) @= 'Y'
			, DB('Financial Summary',!Version,!Currency,!Location,DB( '}ElementAttributes_Period', !Period, 'Final Month') | ' YTD', !Entity, !Affiliate, !Charging Center, !Center,!Client,!Product,!Account,!Financial Summary Measure)
			, CONTINUE ) ;



# Rule 0200 Update Forecast with Actual Periods
[ 'Forecast'] = N: IF( !Period@<= DB( 'Global Variables', 'Last Actual Period', 'String Value' ) & DB('}ElementAttributes_Account', !Account, 'EOP') @<> 'Y' , ['Actual'], CONTINUE );


FEEDERS ;

# Rule 0200
[ 'Actual'] => DB( 'Financial Summary', IF( !Period@<= DB( 'Global Variables', 'Last Actual Period', 'String Value' ), 'Forecast', '' ), !Currency, !Location, !Period, !Entity, !Affiliate, !Charging Center, !Center, !Client, !Product, !Account, !Financial Summary Measure);

