# Environment Management - Handling Environmental Differenes

Environmental differences, caused by different integrations, data availability or a variety of other reasons, is an unavoidable fact of Enterprise development. Very often in large-scale implementations of Salesforce these differences are handled by opaque script or managed by code switches that are not visible or obvious to anyone.

Visibility and transparency are key to everything in *Microscope* and in our approach we provide a clear view of these variations: where they occur, what values any differences have been assigned in a particular environment and a clear story process delineating the roles and responsibilities for each team in the process. Any user has a clear list of services that are changed per environment.


### Remembering A Golden Rule - Custom Metadata vs Custom Settings

Our discussion on [Metadata and Settings](../vision/CMTCustomSettings.md) we introduced some golden rules and one in particular: Custom Metadata Type records are the same in all test environments and Custom Settings are only used when environmental differences occur. It is worth refreshing your knowledge of these principles as it will be very relevant here.

## Absent Connections in Environments in Full Code Environments 

This a key Enterprise use case in Enterprises: how to develop and test against integrations that may be absent in the early stages of the path to production. In ths case we are talking about a full-code environment, the mechanism for connecting to an external source is present, but the external source is not.

We tackle these situations using the *Service Model* only and do not allow local invocations to access external systems directly as doing so would take away a lot of control options. 
Whenever such a new connection is identified it must have its own unique **Service CMT** Record associated to it with the field *Stubbable__c* checked.

Every method associated to this service is considered to be stubbable. For each related *Service Method* record we populate the field *Stub_Service_Method__c* with the name of a method to run when the Service's *Stubbable__c* is checked. This method will need to implement the input and output definitions specified by the *Service Method* as it will need to run in its place.

This completes the *static* part of our setup but we also need a mechanism to tell *Microscope* which methods, the real or the stubs, to run for the Service in each environment. In keeping with our golden rule, this is impleented in a Custom Setting which is called **Service Runtime**. This setting has a reference to the Service and also a field called *Status_Override__c* which can hold one of three values:
* **Active** - this is only used in unit tests, it's not required in the runtime environment.
* **Stubbed** - Stub Methods are to be used for each invocation of this Service
* **Down** - the Service is *Down*, a production incident, and *Down* Service Methods will be used as alternative processing.

There is no need to add a *Service Runtime* Custom Setting record unless a service is being stubbed in an environment, these records are added in exceptional cases only, when the environment to integrate to is not present in the org. In a production org we would expect this Custom Setting to be empty in normal circumstances.

With this mechanism an Environment Manager should be able to easily create and maintain a load script of *Service Runtime* records for each environment to signify which services are stubbed each.

{% hint style="info" %}

Some uses cases for Absent Services:

Missing Integrations - Stub Methods may emulate the behaviour of the service in a simple or sophisticated way, for example the could do anything from pushing back the same response to all queries or trying to act functionally like the real integration to give a good feel for testing or user training.

Salesforce functions - local development might be used in lower environments and the Function in higher environments. The stub method in our model holds the local development and the real implementation the Salesforce Functions implementation.

Limited data in an org - if users are developing in smaller orgs which may be lacking in full reference data a stub could be used as an alternative.

{% endhint %}

### Technical Nuances

* The *Service Runtime* custom setting is only checked if one to the *Stubbable__c* or *Downable__c* fields (the Down service equivalent, we'll see this soon) is checked. 
* If a service is "downable" then *Microscope* checks the *Service Runtime* for a Down status on every call. If the status has changed then we don't reuse the *Microscope* cached Invocation Details values which may now be invalid. If service is not downable , there is no need to do the preliminary check
* For Downable services there is a validation rule that all methods provide a down method
For stubbable if set then have a validation that all methods provide a stub method
* The Service Stub and Down eethod implementations should live in the Service Methods folder, in the subfolder for the method they relate to.

## Downable Connections in Production

We also want a way in produciton to mark a service as **Down** for appropriate handling of incidents and scheduled downtime. This use case is very often forgotten in frameworks and custom developments, with failing connections left to make countless futile calls to services that are not available to them and then reporting back to end users with either generic messages or worse still, letting users wait for jobs to timeout or flashing technical exception information back users, or indeed, customers.

*Microscope* handles this situation rather better by following a very similar pattern to the *Absent Connection Stub* pattern. We allow for each method that is part of a Service that might experience downtime to have defined alongside it a method that is called whenever the environment manager has marked the service as temporarily dwon. 

To use this functionality:

* In the *Service CMT* Record for the Service, check the field *Downable__c* 
* For each related *Service Method* record we populate the field *Down_Service_Method__c* with the name of a method to run when the Service is marked as Down. 
* Write the *Down Method*. This method will need to implement the input and output definitions specified by the *Service Method* as it will need to run in its place.

When this is called into action, the Production Environment Manager creates a **Service Runtime** record, referencing the Service and setting the field *Status_Override__c* to **Down**.

Note that there is one major difference between Absent Connection Stubs and Down Stubs in that the latter signifies an error scenario in *Microscope*. Invokers can check if a **SERVICE_IS_DOWN** has been received using the techniques we saw in [Error Handling](../getting-started/ErrorHandling.md).

{% hint style="tip" %}
Pro-tip: If you want to have some configurable text in a Down message, then reference a custom setting in the Down Method. The envionment mananger can then keep users updated by changing that text custom setting. 
{% endhint %}

## Absent Connections in Unit Tests

Apex Unit tests can never make callouts to external services. Because of this, and to ensure consistency across environments, *Microscope* will never attempt to run the *Implementation Class* of a Stubbable Service Method but will always run the Stub Method in unit tests, even if the connection is actually available in the runtime environment.  


{% hint style="info" %}
Developers do need to be aware that potentially different code will run in a partial environment in unit test code that tries to invoke an  *Absent Service*, and a full-code environment where the Service is present. However precisely the same code will run in each full-code environment as the Stub Methods will be identical in each full-code environment .
{% endhint %}

If for some reason a developer needs to invoke the *Implementation Class* for a Service Method instead of a Stub in a unit test, then an entry for the *Service Runtime* for the Service can be added to the test setup.

```
Service_Runtime__c ss = new Service_Runtime__c();
ss.name = 'ServiceName';
ss.Status_Override__c = 'Active';
insert ss;
```




## Different Connection parameters in Different Environments

Method Static


----



If is test and service is stubbable then take the stub if there is one, regardless of if the custom setting is in play to use stubs. Ensures consistency

Down should run same in tests as production - down should never point to an external



### From My Issues


There is a good point in this

Service Presence and tests for development stubs

If set up as a Service Invocation
If Serivce not present then run a Absent Service Stub automatically in Unit Tests?

## Different Connections in Environments (different page?)

