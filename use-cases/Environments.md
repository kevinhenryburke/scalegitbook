# Environment Management

note stubbable service should just hold stuff that changes per enviroment like integrations or Salesforce functions

Stubbable Services are never called locally. Always these are decoupled 

## Remebering A Golden Rule - Custom Metadata vs Custom Settings

Our discussion on [Metadata and Settings](../vision/CMTCustomSettings.md) we introduced some golden rules and one in particular: Custom Metadata Type records are the same in all test environments and Custom Settings are only used when environmental differences occur. It is worth refreshing your knowledge of these principles as it will be very relevant here.

 ensure that service statuses are held in a custom setting (Service_Status__c). This 


## Handling Environmental Differenes


Service is in the package but not connected

Stubbable Downable


## Absent Connections in Environments





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

## Diffeent Connections parameters in Different Environments



----



If is test and service is stubbable then take the stub if there is one, regardless of if the custom setting is in play to use stubs. Ensures consistency

Down should run same in tests as production - down should never point to an external



### From My Issues

### 224

We want a list of services that are stubbed per environment when running through the path to live.
We also want a way in produciton to mark a service as down for alternative handling of incidents.

To comply with golden rule that CMTs are the same in all test environments ensure that service statuses are held in a custom setting (Service_Status__c). This makes environment management for stubs and scripts pretty simple and allows separate control for devs and environment managers. In an environment we run a load script that says what services are stubbed by entering these as records for the custom setting.

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

