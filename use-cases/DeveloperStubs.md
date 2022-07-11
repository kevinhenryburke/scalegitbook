## Packaging - Developer Stubs

An agile enterprise will not have developers working in large single monolithic environments but in smaller *partial* environments. The advantages of this approach are of course very well understood in our industry but a number of challenges have to be addressed whenever this pattern is adopted, principally concerning how to develop in environments where artifacts that will be called in production are not actually available in the smaller, partial, developer and test environments.

There are several use cases where an invocation may need to be implemented differently in different environments and the decoupling that *Microscope's* invocation model can be extended to handle each by implementing a number of **Stub Patterns**. 


1.	Early Development: **Temporary Development Stub**. Before a developer has a *real* implementation to work against she can define a simple stub to use to make a quick start on building screens and processes. Similary if working on a Proof of Concept we may never need a *full* implementation for an invocation. 

2. DX Development: **Absent Service Stub Class**. When developing a feature within a single DX (unlocked) package or scratch org the developers will not have the full org environment available to them. Some of the Services that the full implementation of the feature depends upon may not be present.

3.	Unit Testing: **Temporary Development Stub**. Stubs can be used in unit tests to provide consistent test execution from scratch org development through to production, even when there are absent services or artifacts in the lower environments. 

4.	Test Path to Production: **Absent Connection Stub**. Used to stub non-existing artefacts in test environments, especially integrations. These can be  implemented as standalone Apex classes. Alternatively we could use an Apex stub to connect to an online mocking service like *stoplight.io* or to an integration platform like *Mulesoft*. 




{% hint style="info" %}
This last use case differs from the others in that handling environmental differences is the responsibility of an environments team rather than a development team. We cover therefore cover this pattern when we discuss [Environment Management](./Environments.md)
{% endhint %}

### Pattern Maintenance Summary

The following table summarizes the patterns and who maintains them.

| **Use Case** | **Pattern** | **Maintained By** |
| --- | ----------- | --- |
| Early Development | Temporary Development Stub | Developers add Custom Setting in org|
| DX Development | Absent Service Stub Class | Automatic triggering |
| Unit Testing | Temporary Development Stub |Developers add Custom Setting in test setup |
| Test Environment Differences | Absent Connection Stub |Environment Manager maintains Custom Settings |





## Temporary Development Stub Pattern

![InvocationStub4](InvocationStub4.png)

This pattern covers two of our use cases above, 

We need to allow the developer the ability to choose how an invocation fired during a test might be run as, for example, an *Absent Service Stub Class* will run differently if it is run in a scratch or in a full code environment.

### How to use the Pattern

1. On your invocation's *Invocation* CMT Record populate the field *Temporary_Development_Stub_Setting__c* with a string value that will reference the name of a Custom Setting record in *mscope__Service_Environment_Setting__c*. This field value is a *permanent* setting that is available in all environments but which is only used if a Custom Setting record with the referenced name is present in the org.

2. Create the Custom Setting record in *mscope__Service_Environment_Setting__c* with its name set as the value above and with the *String_Value__c* field set to the name of an Apex Class that you wish to run as your temporary stub. 

As noted above, if the Custom Settingrecord is not present in the org, no *Temporary Development Stub* will be run. 
 
*Temporary Development Stubs* take precedence over not just the invocation but all other stub and override patterns too. There is no validation tooling provided at present for Scratch Stubs.

### Early Stage Development Scenario

The developer does not have an implementation of an invocation available to play with. 
* The developer/architect creates the *Invocation CMT* record for the invocation  with the *Temporary_Development_Stub_Setting__c* set as the name of a (to-be) custom setting.
* The developer creates the Custom Setting Record manually in the org that references a temporary class, neither of which are checked into the code repository, at least not a permanent part of the repo (checking into a feature branch and deleting before merge is ok).

### Unit Test Scenario

The developer wants a consistent execution of an invocation across all environments even when the *real* invocation might be different due to environmental differences.

* The developer/architect creates the *Invocation CMT* record for the invocation  with the *Temporary_Development_Stub_Setting__c* set as the name of a (to-be) custom setting.
* The developer creates the Custom Setting Record in the unit test setup code that references an implementing class to use in tests. For example

```

mscope__Service_Environment_Setting__c scratchStub = new mscope__Service_Environment_Setting__c();
scratchStub.Name = 'Tab_Packaging_3_Scratch_Stub' ;
scratchStub.mscope__String_Value__c = 'Tab_Packaging_3_Scratch_Stub' ;
insert scratchStub;
```

Together these steps ensure that every run of the test will be executed the same wherever it is run.

{% hint style="info" %}
Some notes on this use case

* The implementing class in unit test scenarios should be decorated as *@IsTest* unless it is being re-used as part of the Apex runtime in some environment. The class will of course need to be checked in to the repository as it forms part of the test suite. This implementing class may also be an inner class of the test class holding the unit test, these are decisions for the developer to take on a case by case basis.

* We note that in a more complex test it is possible that we may wish to stub invocations that are not directly called by the test but are potentially some way down the call stack. Having convenience setup methods for heavily used temporary development stubs for invocations that developers can reuse in their test methods might be beneficial. 
{% endhint %}



## Absent Service Stub Pattern

![InvocationStub3](InvocationStub3.png)

This pattern is based on Service Presence. *Microscope* checks to see if the Service references in an *Invocation CMT record* is present in the org via a *Service CMT* record name. 

* If the Service is present then we run that *real* implementation and the *Absent Service Stub Class* value is completely ignored
* If it is not, *Microscope* will pick up the name of the Apex class stored in the field *Absent Service Stub Class* of the *Invocation CMT Record* and run this in place of the Service method referenced in the *Invocation CMT Record*. 

Note that this pattern is never triggered by *Local Invocations*, it can only be triggered if a Service name is referenced in the *Invocation CMT record*.


