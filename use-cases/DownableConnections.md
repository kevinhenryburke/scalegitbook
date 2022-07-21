
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
