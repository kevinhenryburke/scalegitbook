## Packaging - Developer Stubs

An agile enterprise will not have developers working in large single monolithic environments but in smaller *partial* environments. The advantages of this approach are of course very well understood in our industry but a number of challenges have to be addressed whenever this pattern is adopted, principally concerning how to develop in environments where artifacts that will be called in production are not actually available in the smaller, partial, developer and test environments.

There are several use cases where an invocation may need to be implemented differently in different environments and the decoupling that *Microscope's* invocation model can be extended to handle each by implementing a number of **Stub Patterns**. 


1.	Early Development: **Temporary Development Stub**. Before a developer has a *real* implementation to work against she can define a simple stub to use to make a quick start on building screens and processes. Similary if working on a Proof of Concept we may never need a *full* implementation for an invocation. 

2. DX Development: **Absent Service Stub Class**. When developing a feature within a single DX (unlocked) package or scratch org the developers will not have the full org environment available to them. Some of the Services that the full implementation of the feature depends upon may not be present.

3.	Unit Testing: **Absent Service Stub Class** and **Temporary Development Stub**. If the Service is not present in the development environment the *Absent Service Stub* pattern will be applied to unit tests too. *Temporary Development Stubs* can also be used in unit tests (which will take priority over *Absent Service Stubs*) to provide consistent test execution from scratch org development through to production. In reality a mixture of both patterns might be used in a single test, for example with a *Temporary Development Stub* used to test a feature of an invocation being explictily tested but with *Absent Service Stub Classes* being used further down the stack. 

4.	Test Path to Production: **Absent Connection Stub**. Used to stub non-existing artefacts in test environments, especially integrations. These can be  implemented as standalone Apex classes. Alternatively we could use an Apex stub to connect to an online mocking service like *stoplight.io* or to an integration platform like *Mulesoft*. 


### Pattern Maintenance Summary

Before we start to look at the stub patterns, a quick word on who maintains them across environments in each use case. We note that the last use case differs from the others in that handling environmental differences is the responsibility of an environments team rather than a development team. We cover therefore cover this pattern when we discuss [Environment Management](./Environments.md)

The following table summarizes pattern maintenance.

| **Use Case** | **Pattern** | **Maintained By** | **Defined in Story** |
| --- | ----------- | --- |--- |
| Early Development | Temporary Development Stub | Developers add Custom Setting in org|No |
| DX Development | Absent Service Stub Class | Automatic triggering | Yes |
| Unit Testing | Temporary Development Stub |Developers add Custom Setting in test setup |No |
| Test Environment Differences | Absent Connection Stub |Environment Manager maintains Custom Settings |Yes |

