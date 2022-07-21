## Absent Service Stub Pattern in Partial Code Environments.

![InvocationStub3](InvocationStub3.png)

This pattern is based on Service Presence in an org which does not have the full code base. *Microscope* checks to see if the Service is referenced in an *Invocation CMT record* is present in the org via a *Service CMT* record name. 

* If the Service is present then we run that *real* implementation and the *Absent Service Stub Class* value is completely ignored
* If it is not, *Microscope* will pick up the name of the Apex class stored in the field *Absent Service Stub Class* of the *Invocation CMT Record* and run this in place of the Service method referenced in the *Invocation CMT Record*. 

Note that this pattern is never triggered by *Local Invocations*, it can only be triggered if a Service name is referenced in the *Invocation CMT record*.


