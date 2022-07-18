# Environment Management - Handling Environmental Differenes

Environmental differences, caused by differnet integrations, data availability or a variety of other reasons, is an unavoidable fact of Enterprise development. Very often in large-scale implementations of Salesforce these differences are handled by opaque script or managed by code switches that are not visible or obvious to anyone.

Visibility and transparency are key to everything in *Microscope* and in our approach we provide a clear view of these variations: where they occur, what values any differences have been assigned in a particular environment and a clear story process delineating the roles and responsibilities for each team in the process. Any user has a clear list of services that are changed per environment.


### Remebering A Golden Rule - Custom Metadata vs Custom Settings

Our discussion on [Metadata and Settings](../vision/CMTCustomSettings.md) we introduced some golden rules and one in particular: Custom Metadata Type records are the same in all test environments and Custom Settings are only used when environmental differences occur. It is worth refreshing your knowledge of these principles as it will be very relevant here.

## Absent Connections in Environments in Full Code Environments 

This a key Enterprise use case in Enterprises: how to develop and test against integrations that may be absent in the early stages of the path to production. In ths case we are talking about a full-code environment, the mechanism for connecting to an external source is present, but the external source is not.

We tackle these situations using the *Service Model* only and do not allow local invocations to access external systems directly as doing so would take away a lot of control options. 
Whenever such a new connection is identified it must have its own unique **Service CMT** Record associated to it with the field *Stubbable__c* checked.

Every method associated to this service is considered to be stubbable. For each related *Service Method* record we populate the field *Stub_Service_Method__c* with the name of a method to run when the Service's *Stubbable__c* is checked. This method will need to implement the input and output definitions specified by the *Service Method* as it will need to run in its place.

This completes the *static* part of our setup but we also need a mechanism to tell *Microscope* which methods, the real or the stubs, to run for the Service in each environment. In keeping with our golden rule, this is impleented in a Custom Setting which is called **Service Runtime**. This setting has a reference to the Service and also a field called *Status_Override__c*. This can hold one of three values
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

## Downable Connections in Production

We also want a way in produciton to mark a service as **Down** for appropriate handling of incidents and scheduled downtime. This use case is very often forgotten in frameworks and custom developments, with failing connections left to make countless futile calls to services that are not available to them and then reporting back to end users with either generic messages or worse still, letting users wait for jobs to timeout or flashing technical exception information back users, or indeed, customers.

*Microscope* handles this situation rather better by following a very similar pattern to the *Absent Connection Stub* pattern. We allow for each method that is part of a Service that might experience downtime to have defined alongside it a method that is called whenever the environment manager has marked the service as temporarily dwon. 

To use this functionality:

* In the *Service CMT* Record for the Service, check the field *Downable__c* 
* For each related *Service Method* record we populate the field *Down_Service_Method__c* with the name of a method to run when the Service is marked as Down. 
* Write the *Down Method*. This method will need to implement the input and output definitions specified by the *Service Method* as it will need to run in its place.

When this is called into action, the Production Environment Manager creates a **Service Runtime** record, referencing the Service and setting the field *Status_Override__c* to **Down**.

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

### 224



The custom setting is referenced whenever a Service CMT states that a service is Stubbable" or Downable. Services not marked with either of these attributes do not check the custom setting.

Invocation side

If a service is "downable" then create a pre-check on status from the custom setting before calling.
If status has changed then don't use the cache value but recompute service validations
If service is not downable , there is no need to do the preliminary check

Service side

If a service is "stubbable" then we delegate service to the stub if the custom setting says that the service is stubbed in this environment

Validations

For downable a validation rule that all methods provide a down method
For stubbable if set then have a validation that all methods provide a stub method

### 117

Problems: who writes stubs / too many custom stubs written per environment and provided at the per invocation level, no option to stub an implementation across the board.

service is down is set at top level (a downable service)
service should also be set as stubable (a stubbable service)

per environment: have a list of services in a LCS that are stubbed in a per environment script (don't change the metadata).
** New artifact **

for an implementation: have a configured implementation for when service down and a default method if left empty (which acts like my general down)

Also have a configured value when a service is stubbed at the service level. This stub is written by the service provider as part of their contract when writing a stubbable service (they might provide more than one). Not always required, perhaps we have a "service stubbable" flag at the service level.
All other stubs written by the invoking team.

The down and stubbable options may not change at the same rate as the service. These need to live in their own part of the code base. If method specific then need to live in the methods folders but not necessarily the per-implementation part

any other overrides are provided by an OCS / SCS - decide later?

Documentation: who owns stubs - process part, team responsibilities. Clearer now who writes and owns which stubs I hope.

Documentation at a high level:
THERE IS A GOLDEN RULE
EVERYTHING THAT IS REFERENCED IN THE SERVICE SIDE IS CREATED BY THE SERVICE SIDE
EVERYTHING REFERENCED IN INVOCATION METADATA / SETTINGS IS CREATED BY THE INVOCATION TEAM

SERVICE METHOD IS THE CONTRACT WITH THE INVOCATION
SERVICE IMPLEMENTATION MAY BE CONSIDERED TO BE PRIVATE TO THE INVOCATION

### 309

Invocation and Stub Logic

Invocation record has one of the following filled

Service Name
Implementing Class
(In time) Implementing Flow
Stub Class
Stub CS exist and says use Stub

try to use stub, if not stub class then fail
Service Populated and Service not exist

try to use stub, if not stub class then fail
Service Populated and Service exists

Check for overrides
if no override, run service
Implementing Class populated

Check for overrides
if no override, run implementing class
Else fail

In dev/test if we call a service in a test method should we always use the stub???? Does the Stub implementation double as what gets called in a unit test - surely not always?
Risk is we run different in different environments but do we want that?

This (below) is important

Perhaps solution for tests is this:
Build up a hierarchical of dependencies of packages
If an invocation crosses from a package to a non-dependent package then it uses the stub
Might want to build up a cache of the hierarchy of structures?

So in non-test - check if service exists, if so use it.

For tests, check if service exists, if it is and it is associated to a target package and if that target package is a dependent of the source invocation package then call it, otherwise call the invocation stub.
For this reason every invocation to a non-dependent package must come with a stub if it is to be called in a unit test. However if a dev does not do this they will find out in the developer space as the unit test will fail there due to a missing implementation.

This is the sort of dependency I've been trying to break though .....

Implementing:

Think about variants (these are in different packages potentially, how is that going to work)

## Different Connections in Environments (different page?)

