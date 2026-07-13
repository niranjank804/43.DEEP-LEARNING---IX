Hi Sherry,

Good catch. My current understanding is that the calculation method is controlled at the Center level rather than by an intersection.

For the allocation TI, my plan is to read the Grp Ins Alloc Mthd attribute from the Center dimension:

ATTRS('Center', dCenter, 'Grp Ins Alloc Mthd')

If the attribute is blank, the process defaults to Salary. If the value is Rate, the process will execute the rate-based calculation.

If the business requirement is actually for the method to vary by an intersection (rather than by Center), then I agree that a dimension attribute would not be the correct approach. In that case, we would need to use a configuration cube or another lookup mechanism instead.

Could you confirm that the method should be controlled at the Center level, or let me know which intersection should determine the calculation method?
