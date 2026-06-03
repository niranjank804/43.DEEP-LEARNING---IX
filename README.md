As an EPA System Admin, I need the Entity DIM build restored to include the Sweep Entity Hierarchy, so that the hierarchy structure is correctly rebuilt and aligned with expected downstream reporting and validation.

Update the existing DIM TI process to reintroduce logic for loading the Sweep Entity Hierarchy based on the provided build screenshots. When adding hierarchy relationships, ensure a valid TotalParent# is assigned by selecting the next available sequence number (do not overwrite existing values).

The process should maintain current structure, avoid duplication, and ensure consistency with existing entity relationships.

Image

Image

Image
Image
Accepttance criteria:
Sweep Entity Hierarchy is included in the Entity DIM build and executes without error
New hierarchy members are added using the next available TotalParent# in sequence
Existing hierarchy relationships remain unchanged and are not overwritten
DIM build runs successfully end-to-end with no data loss or duplication
Hierarchy structure matches expected results based on provided screenshots
Changes are reviewed/tested and validated against dimension outputs before completion
Code follows team standards and is clear for peer review (per team agreement)




