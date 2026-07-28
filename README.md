Hi Team,

I have completed the Proof of Concept for **Scenario DIM Security Rule Removal** and documented the results.

The POC included:

* Documenting the existing Scenario DIM security rule and current READ/WRITE behavior.
* Removing the security rule in the lower environment.
* Validating end-user access after rule removal.
* Testing manual security assignment in `}ElementSecurity_Scenario`.
* Verifying READ vs WRITE behavior.
* Executing data load, calculation, allocation, reporting, and input validation tests.
* Validating the behavior for newly created Scenarios.
* Assessing any dependencies and documenting recommendations.

**Summary of Findings:**

* Removing the rule affects both **READ and WRITE** access because blank security defaults to **NONE**.
* Once equivalent manual security values are assigned, end-user behavior matches the original rule-based implementation.
* No impact was observed on calculations, allocations, TurboIntegrator processes, data loads, or reporting.
* New Scenarios require a security assignment followed by execution of the **System - Refresh Security** process before they become visible to non-admin users.

Based on the POC results, manual Scenario security management is a viable approach, provided the Scenario creation/maintenance process assigns the required security values and executes the existing **System - Refresh Security** process.

The completed POC document is attached for your review. Please let me know if you have any questions or if any additional validation is required.

Thanks,
Niranjan Patra

