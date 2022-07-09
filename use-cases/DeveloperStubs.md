## Packaging - Developer Stubs

An agile enterprise will not have developers working in large single monolithic environments but in smaller *partial* environments. The advantages of this approach are of course very well understood in our industry but a number of challenges have to be addressed whenever this pattern is adopted, principally concerning how to develop in environments where artifacts that will be called in production are not actually available in the smaller, partial, developer and test environments.

There are several use cases where an invocation may need to be implemented differently in different environments and the decoupling that *Microscope's* invocation model provides is ideal for implementing each.

1.	Early Development: **Temporary Development Stub**. Before a developer has a *real* implementation to work against she can define a simple stub to use to make a quick start on building screens and processes.

2. DX Development: **Absent Service Stub**. When developing a feature within a single DX (unlocked) package or scratch org the developers will not have the full org environment available to them. Some of the Services that the full implementation of the feature depends upon may not be present.

3.	Unit Testing: Stubs can be used in unit tests to provide consistent test execution from scratch org development through to production, even when there are absent services or artifacts in the lower environments. 

4.	Test Path to Production: **Absent Connection Stub**. Used to stub non-existing artefacts in test environments, especially integrations. These can be  implemented as standalone Apex classes. Alternatively we could use an Apex stub to connect to an online mocking service like *stoplight.io* or to an integration platform like *Mulesoft*. 

{% hint style="info" %}
This last use case differs from the others in that handling environmental differences is the responsibility of an environments team rather than a development team. We cover therefore cover this pattern when we discuss [Environment Management](./Environments.md)
{% endhint %}



*Microscope* addresses these challenges by implementing a number of **Stub Patterns**. 



## Temporary Development Stub

![InvocationStub4](InvocationStub4.png)

This pattern covers two of our use cases above, 

We need to allow the developer the ability to choose how an invocation fired during a test might be run as, for example, an *Absent Service Stub* will run differently if it is run in a scratch or in a full code environment.

### How to use the Pattern

1. On your invocation's *Invocation* CMT Record populate the field *Scratch_Implementation_Setting__c* with a string value that will reference the name of a Custom Setting record in *mscope__Service_Environment_Setting__c*. This field value is a *permanent* setting that is available in all environments but which is only used if a Custom Setting record with the referenced name is present in the org.

2. Create the Custom Setting record in *mscope__Service_Environment_Setting__c* with its name set as the value above and with the *String_Value__c* field set to the name of an Apex Class that you wish to run as your temporary stub. 

As noted above, if the Custom Settingrecord is not present in the org, no *Temporary Development Stub* will be run. 
 
*Temporary Development Stubs* take precedence over not just the invocation but all other stub and override patterns too. There is no validation tooling provided at present for Scratch Stubs.

### Early Stage Development Scenario

The developer does not have an implementation of an invocation available to play with. 
* The developer/architect creates the *Invocation CMT* record for the invocation  with the *Scratch_Implementation_Setting__c* set as the name of a (to-be) custom setting.
* The developer creates the Custom Setting Record manually in the org that references a temporary class, neither of which are checked into the code repository, at least not a permanent part of the repo (checking into a feature branch and deleting before merge is ok).

### Unit Test Scenario

The developer wants a consistent execution of an invocation across all environments even when the *real* invocation might be different due to environmental differences.

* The developer/architect creates the *Invocation CMT* record for the invocation  with the *Scratch_Implementation_Setting__c* set as the name of a (to-be) custom setting.
* The developer creates the Custom Setting Record in the unit test setup code that references a implementing class to use in tests. For example

```

mscope__Service_Environment_Setting__c scratchStub = new mscope__Service_Environment_Setting__c();
scratchStub.Name = 'Tab_Packaging_3_Scratch_Stub' ;
scratchStub.mscope__String_Value__c = 'Tab_Packaging_3_Scratch_Stub' ;
insert scratchStub;
```
* The implementing class in this case should be decorated as *@IsTest@* unless there is some reason it should be available to the Apex runtime. The class will of course need to be checked in to the repository as it forms part of the test suite. This implementing class may also be an inner class of the test class holding the unit test, these are decisions for the developer to take on a case by case basis.

Togther these steps ensure that every run of the test will be executed the same wherever it is run.

## Absent Service Stub

![InvocationStub3](InvocationStub3.png)

This pattern is based on Service Presence. *Microscope* checks to see if the Service references in an Invocation CMT record is present in the org via a *Service CMT* record name. If the Service is present then we run that *real* implementation but if it is not, *Microscope* will pick up the name of an Apex class to run in its place if one stored in the Invocation CMT Record. 


If the Service is present then the *Absent Service Stub* is completely ignored, so if there's a bad configuration of the Service Method or Versions *Microscope* reports this as an error.





