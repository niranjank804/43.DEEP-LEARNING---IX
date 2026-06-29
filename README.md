Hi Lisa,

I looked into the International Benefits behavior that Agustín flagged and have completed an initial review of the rule logic.

My findings so far are:

* The International Benefits calculation for New and Replacement employees is salary-based and references the calculated Salaries-Regular value.
* For New employees, Salaries-Regular already incorporates the employee FTE as part of its calculation.
* Based on my initial review, the calculation path suggests the employee FTE may be influencing the benefit calculation more than once. I am currently validating the complete dependency chain to confirm whether this is expected behavior or an issue in the rule logic.
* For the New 002 example (15 FTE at a 32% benefit rate), the calculated result is approximately 480%, which aligns with the behavior Agustín reported.

I am tracing the full calculation flow (FTE → Salary → International Benefits) to determine exactly where the multiplier is being applied and whether the salary already fully represents the required FTE scaling.

Once that validation is complete, I'll provide a detailed write-up of the rule logic, dependency chain, and my recommendation before we determine the appropriate next steps.

Thanks,

Niranjan Patra
