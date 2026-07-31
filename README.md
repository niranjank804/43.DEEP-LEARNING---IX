As an EPA System Admin, I need all existing EPA JE Extract TI processes to be updated so that the GFS_USER_ID field is consistently populated with the constant value 'EPA_SYS', ensuring standardization across all downstream financial integrations and avoiding dependency on source user values.

Attached list of Extracts to be updated. 
Review all existing JE Extract TI processes that populate output files/tables
Current logic for GFS_USER_ID is blank
Replace assignment logic to hardcode 'EPA_SYS' for all records
Ensure change applies to all relevant extract variants
Validate no impact to other fields, mappings, or extract structure
Update any related parameter handling or staging cubes if needed
Example: This vBlank needs to be replaced with 'EPA_SYS'
Acceptance criteria:
All JE Extract TI processes populate GFS_USER_ID = 'EPA_SYS' for every record
Extract output files/tables reflect the updated value consistently across all scenarios and Period dimension selections
Existing extract structure, formatting, and downstream compatibility remains unchanged
Unit testing completed for at least one extract per variant confirming correct population
Code is reviewed and aligned with team standards (naming, readability, reuse where applicable)

