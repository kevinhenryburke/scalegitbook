
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


