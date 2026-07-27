Hi Lisa,

Quick question on the Scenario security POC (the rule-removal PBI).

To test whether removing the }ElementSecurity_Scenario rule exposes Final to WRITE, I need to run the test as a non-admin user who already has WRITE access to a cube that has the Scenario dimension (e.g. the SCN planning cubes). Admin accounts bypass element security, so they can't validate this.

The test user I have doesn't have WRITE access to any Scenario cube, so I can't observe the READ-vs-WRITE change through him.

Could you help me with one of these:

Point me to a non-admin user (or group) that has WRITE on the SCN Scenario cubes that I can use for testing, or
Confirm which security group the real planners belong to, so I can test with an account in that group?
Also worth flagging: it looks like Cognos\Everyone has no WRITE cube access, which would mean the Final=READ rule has no practical effect for the Everyone group — real write access seems to be controlled by the functional planner group(s). Wanted to confirm that's your understanding too.

Thanks!
Niranjan



