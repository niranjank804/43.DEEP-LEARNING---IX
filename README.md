Hi Robert,

The lower environment servers have been restarted successfully and are now up and running.

I opened a support case with IBM. They identified that the Admin Server had been started using an incorrect service account after the production refresh. IBM corrected the service account, and the affected TM1 servers started successfully. They also recommended adding "RestartAdminSvr=F" to the "tm1s.cfg" file to help prevent this issue in the future.

Please let me know if you notice any further issues.

Thanks,
Niranjan Patra
Hi Jill,

Thank you for your assistance.

The servers have started successfully, and everything appears to be working as expected now. I appreciate your help in identifying and resolving the issue with the service account and updating the configuration.

Thanks again for your support.

Best regards,
Niranjan Patra
