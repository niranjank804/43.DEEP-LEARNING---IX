Hi Lisa,

I completed the trace of the full calculation flow (FTE → Salary → International Benefits). Below is the dependency chain with the relevant rule logic as evidence.

Step 1 — Workforce Planning Input cube, "Base Annual Pay" (Rule 2000)

For New/Replacement employees the rule annualizes the entered pay rate only — FTE appears solely as a validation condition, not as a multiplier:

IF Salary Class = 'Hourly'  → Pay Rate × Annual Hours
IF Salary Class = 'Monthly' → Pay Rate × 12
ELSE                        → Pay Rate
So Base Annual Pay is held on a per-1-FTE basis.

Step 2 — Workforce Planning Summary cube, "Base Salary" (Rule 2100)

This is the single point where FTE is applied:

Base Salary = Base Annual Pay × ( 1 if Existing, else FTE ) × pay-period factor
For the example line: Base Annual Pay × 15 ÷ 12 per month. Base Salary then rolls up into Salaries-Regular (together with Allowances, Merit and Promotion Increase).

Step 3 — Workforce Planning Summary cube, "International Benefits" (Rule 3300)

International Benefits =
    ( Salaries-Regular − Allowances backout ) × Regulatory Benefits %
  + ( Salaries-Regular − Allowances backout ) × Elective Benefits %
  + International Benefits Flat Rate ÷ 12 × Head Count
The percentage components reference Salaries-Regular only — there is no second FTE multiplication in this rule. The flat-rate component multiplies by head count, which is the intended per-head treatment.

Conclusion

FTE is applied exactly once, at the Base Salary step. For the New 002 example (15 FTE, 32% combined benefit rate), the benefit equals 32% of the full 15-FTE salary; it reads as ~480% only when compared against a single person's pay rate (32% × 15). This matches the behavior you described as expected — benefits scaling with the FTE count on the line. Consistent with this, the summary view in Agustín's original screenshot shows International Benefits of ~934M ARS against Salaries-Regular of ~2,631M ARS for Jan-2026, an effective rate of ~35.5% — in line with a normal benefits rate at the aggregate level, not an inflated one.
