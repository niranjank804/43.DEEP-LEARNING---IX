Hi Sherry,

I started working on PBI 4040751 and reviewed the existing Workday load process.

As discussed, I removed the Skip Zero Out flag from the following Transfer measures:

Transfer Center
Transfer Location
Transfer Out Month
Transfer In Month
Transfer Approval

I then ran the Workforce Planning Input - Load from Workday process to validate the change. However, the process aborted with the following error:

Cube "WFP Center Alt Location Override" Not Found

I investigated further and found that the process is attempting to read from the WFP Center Alt Location Override cube using the dynamic location override logic that was added as part of PBI 3951796.

To verify the issue, I checked the environments and found:

The WFP Center Alt Location Override cube is not present in the current Dev environment.
The same cube is also not present in the current Production environment.
The WFP Override Measures dimension is also not present in the current Dev environment.

I then checked the manual backup that I created while implementing PBI 3951796, and both of the following objects are available in that backup:

WFP Center Alt Location Override
WFP Override Measures

Since the Workday load process depends on these objects, I'm unable to complete the validation for PBI 4040751.

Could you please confirm how you would like me to proceed?

Should I recreate these objects in Dev based on the original PBI 3951796 implementation?
Or would you prefer that they be restored from the backup?

Once I have your confirmation, I'll proceed with the implementation and complete the testing.

Thanks,

Niranjan Patra
