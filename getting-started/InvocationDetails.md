## Digging into our example

In this section we will try to understand the simple example we started on the previous page, [Decoupled Calls](DecoupledMethod.md). 

{% hint style="tip" %}
Whilst reading this page you might want to open [Decoupled Calls](DecoupledMethod.md) in an adjacent tab or window, there will be some referencing to it in what follows.
{% endhint %}

### Inspecting our invocation response

To shine some light on how the example works, let's add one more code line and some debug lines to our example call

```

mscope.ServiceInvocation sinv = mscope.ServiceInvocation.initialize('ExampleRating');
String returnedValue = (String) sinv.invokeService('Barack Obama');
System.debug(returnedValue);

mscope.InvocationDetails invocationDetails = sinv.getInvocationDetails();

System.debug('IsSuccess: ' + invocationDetails.IsSuccess);
System.debug('State: ' + invocationDetails.State);
System.debug('ErrorMessage: ' + invocationDetails.ErrorMessage);

```
As well as our (in this case) String-valued output we are also retrieving in Line 5 an object called **mscope.InvocationDetails** from our invocation, but what is it? In short, this is the key data object in Microscope, an instance is created whenever we initialize an invocation (so in the first line of our example) and that instance drives the invocation through the service call and back to the invoker, picking up useful information all the way. The object's coded definition is pretty big and holds many fields, you can inspect these in the [source class](https://github.com/kevinhenryburke/frictionless/blob/master/serviceBase/force-app/Framework/classes/invocation/InvocationDetails.cls) but for now we'll focus on just the key fields as they relate to our example on this page and ignore the rest for now. 

In the invoking code above, our debug statements highlight some of this key information about the invocation that we have gleaned from InvocationDetails, e.g. whether it was successful, its State and any Error Message. But let's find out where this comes from.

# Invocation Processing

The process of invoking a service follows 4 main phases:

1. Retrieve Metadata
2. Validate
3. Execute
4. Audit

Looking at the invoking code again, the first two phases are covered by the *initialize()* method:

```
mscope.ServiceInvocation sinv = mscope.ServiceInvocation.initialize('ExampleRating');
```

The third and fourth phases are covered by the *invokeService()* method:

```
String returnedValue = (String) sinv.invokeService('Barack Obama');
```

## (Phase 1) Retrieve Metadata

Using the *InvocationName* as its input we retrieve information from the **Invocation CMT Record** we created as part of the example. A number of fields are set on InvocationDetails at this point but we'll highlight those that are pertinent to our example:

*	InvocationName: The name of the **Invocation CMT Record** that is referenced in the calling code. 
*	InvocationMechanism: The technical mechanism that will be used to run our invocation. The main options are *Sync* (synchronous invocation), *Async* (via Platform Event) and *Queueable*
*	InputDefinition / InputDefinitionNamespace: An Apex class, interface, literal or collection, that this invocation will provide to the Implementation
*	OutputDefinition / OutputDefinitionNamespace: An Apex class, interface, literal or collection, that this invocation will receive back from the Implementation 
*	ImplementingClass: In our example we have explicitly provided the implemenation class name to the invocation using the *Explicit Implementation* field in the Invocation CMT Record. The implementation can also be provided by custom metadata that represents decoupled services too, which can provide a lot more development lifecycle and deployment options, but here we are choosing the implementation explicitly.

This phase additionally sets dynamic information information the time it was kicked off and the running user and information derived from the invocation, for exampe if cache should be used to increase performance and information such as a Service Name, Method, Iteration and TechnicalVersion which we will cover in time.

## (Phase 2) Validate


The (__Validate__) phase looks at our configuration setup for the invocation and confirms if it's valid and runnable. A lot of checks are run, for example is the Implementing Class really a class that exists in the org and which satisfies any required interfaces? If not we set these fields to report back any errors or warnings, though we won't see that in our example as the configuration is valid as specified.

*	ConfigurationValid: if this is false the configuration won't be runnable and service invocations will be aborted without running *invokeService()*
*	ConfigurationHasWarning: if this is false the configuration is not ideal but is runnable and invocations will be perfored. 
*	ConfigurationState: This is the invocations's State as it was at the end of the *validate* process. It reflects configuration issues but not processing issues.
*	ConfigurationErrorMessage: The invocations's ErrorMessage as it was at the end of the *validate*.


## (Phase 3) Execute


The (__Execute__) phase runs the implementation class and passes any output data back the invoker. Within the implementation class the following fields should be set to pass back important information about the processing.

*	State: a string that is set to “SUCCESS” if the service call has been successful. Any other value signals a failure, but one where the semantics are "failed to process correctly" rather than “not the hoped for business outcome”, we leave the task of reporting Business Status to the Business Outcome field. Explicitly in an implementation we should only ever set State to "SUCCESS" as this is best populated using Microscope’s Error Reporting framework. See [Error Raising](ErrorRaising.md) for the recommended way to report errors.
*	BusinessOutcome: This is optional but can be set to pass back the outcome of an implementation call in a business context to help determine what a business process needs to do next. This is very vague, so let's try to clarity

{% hint style="info" %}

__State and Business Outcome__ 

Let's use an example where we search for a Contact in an external system and where the invoker needs to know what the outcome was in as clear and simple a way as possible, ideally without having to parse the output data response. If for example the invocation is from a flow then querying an output data structure really might be difficult to do and require a separate Apex action to accomplish it. 

Here's what the fields might show in these different scenarios:

1. *We find the contact*: State = "SUCCESS" / BusinessOutcome = "Contact Found" (or such like).
2. *We make the call successfully but the contact is not found*: State is still "SUCCESS" (the call ran so the invocation was successful), however  BusinessOutcome = "Contact Not Found In External System"
3. *The callout fails*: State = "FAILURE: External System Not Available" because this is a processing issue, not a data gap, BusinessOutcome = "External System Not Available" .

There's a lot of benefits to doing this.

* With these return values a production manager knows that in the last case there's a production issue whereas the second case is not an issue for her. For the production issues where State is not “SUCCESS” alerts can be configured on the Audit table.
* The end user application will know that there is a distinction between the last two cases and can use the Outcome field to direct the user to different processes, one for when an individual data record is missing and one for when a whole integration is failing.
* The end user application developer can treat the responses from all services in the **same way**. It is hard to over-state what a benefit this standardisation is in reducing errors in complex applications: having one way to report errors, one way to pass back information on how to process in different business outcomes.
* And a data quality manager might find a report based on the Audit records for the second case useful in resolving data issues on the external system

We can see an example of Business Outcomes being used in our Demo application in [Tab_Chaining_3_First_local](https://github.com/kevinhenryburke/frictionless/blob/master/demo/force-app/invocations/localInvocations/classes/Tab_Chaining_3_First_local.cls)

{% endhint %}


The next set of fields are not set explicitly when an implementation is run but are populated when an error is raised in the implementation - see our [page on Raising Errors](ErrorRaising.md)
*	IsSuccess: a convenience parameter which is true if State is "SUCCESS", false otherwise 
*	IsFail: a convenience parameter, the boolean opposite of IsSuccess
*	ErrorMessage: returns any associated error information when State does not signal a success. We'll see how this is populated soon.
*	ErrorCode: The name of the **Service Error CMT Record** that holds the information on what to report for a raised error (see [Error Raising](ErrorRaising.md))


## (Phase 4) Audit

To see the invocation details after an invocation we'll switch on auditing for our invocation **ExampleRating**. Go to Custom Metadata Types from the Setup menu and select Edit against that record and enter the value **AuditSync** against the field *Audit Invocation* and save. Then run the sample code again for that exercise in execute anonymous.

This time we should have done a bit of extra processing, with an Audit process now configured Microscope sends a copy of the InvocationDetails, InputData and the OutputData to the Audit tables. If you go to the App Selector (the 9 dots in the top left of the Salesforce page) and search for Audit and select the object *Service Framework Audit*, find the most recent record using the *Audit Full* list view and you should now see a record that was created by your most recent code run. There's a field called *Invocation Details* and inside that there's some JSON, and this is how our Invocation Details looked at the end of the transaction. 

Prettifying this, it will look something like this:

```

{
  "UserId":"0058d000001l5cDAAQ",
  "UseCache":false,
  "TransactionsRolledBack":false,
  "StaticParameters":{
    
  },
  "State":"SUCCESS",
  "ServiceTime":"2022-04-12T10:04:59.355Z",
  "ServiceEnd":"2022-04-12T10:04:59.361Z",
  "ServiceDuration":6,
  "Quiddity":"ANONYMOUS",
  "Purpose":"Business Function",
  "OutputDefinitionNamespace":"",
  "OutputDefinition":"String",
  "MetadataId":"m008d000000MKyWAAW",
  "IsVariant":false,
  "IsTransactional":false,
  "IsSuccess":true,
  "IsStub":false,
  "IsOverride":false,
  "IsLocalInvocation":true,
  "IsFail":false,
  "IsAsyncCall":false,
  "InvocationTime":"2022-04-12T10:04:59.343Z",
  "InvocationPositionList":[
    1
  ],
  "InvocationName":"ExampleRating",
  "InvocationMetadataType":"Metadata Record",
  "InvocationMechanism":"Sync",
  "InvocationEnd":"2022-04-12T10:04:59.363Z",
  "InvocationDuration":20,
  "InvocationDetailsClass":"InvocationDetails",
  "InvocationContextId":"4iJT3M4MayD728G-mQyVC-",
  "InputDefinitionNamespace":"",
  "InputDefinition":"String",
  "InputCreationClassNamespace":"",
  "InitiatingContextTrackingId":"4iJT3M4MayD728G-mQyVC-",
  "ImplementingType":"ApexClass",
  "ImplementingClass":"ExampleRating",
  "IgnoreCacheValidation":false,
  "HasVariants":false,
  "ForceComputeCacheValidation":false,
  "ExternalInvocation":false,
  "ExplicitImplementation":"ExampleRating",
  "ConfigurationValid":true,
  "ConfigurationState":"SUCCESS",
  "ConfigurationHasWarning":false,
  "childInvocations":[
    
  ],
  "CacheUsed":false,
  "BubbleUpErrors":true,
  "AuditOutputDataSerialized":"IlJhdGluZzogR29vZCI=",
  "AuditInvocation":"AuditSync",
  "AuditInputDataSerialized":"IkJhcmFjayBPYmFtYSI=",
  "AuditErrorsOnly":false,
  "AttemptCacheReadWrite":false
}

```

Some of these attributes are self-explanatory, some of it certainly is not, but hopefully you will see that this is a very useful object to get to know and it is the key to understanding how this application works.

You will also notice that many of the values in InvocationDetails are also replicated in individual fields on each Audit Record. These fields are populated directly from the InvocationDetails record as part of the Audit process and form the basis for our Business Intelligence techniques that we will see later on. 
