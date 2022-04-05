## Digging into our example

In this section we will try to understand the simple example we started on the previous page. How is this working?

### Inspecting our invocation response

To shine some light, let's add one more code line and some debug lines to our example call in [Quick Start: A Decoupled Method Call](DecoupledMethod.md)

```

mscope.ServiceInvocation sinv = mscope.ServiceInvocation.initialize('ExampleDecoupledMethod');
String returnedValue = (String) sinv.invokeService('My Input');
System.debug(returnedValue);

mscope.InvocationDetails invocationDetails = sinv.getInvocationDetails();

System.debug('IsSuccess: ' + invocationDetails.IsSuccess);
System.debug('State: ' + invocationDetails.State);
System.debug('ErrorMessage: ' + invocationDetails.ErrorMessage);

```

We are retrieving an object called **mscope.InvocationDetails**, but what is it? In short, this is the key object in Microscope, it is created when initialize an invocation (so in the first line of our example) and it follows the invocation from inception through the service call and back to the invoker, picking up information all the way. The object is pretty big and holds many fields, you can inspect these in the [source class](https://github.com/kevinhenryburke/frictionless/blob/master/serviceBase/force-app/Framework/classes/invocation/InvocationDetails.cls) and we'll see a more user friendly way to see how this is populated very soon.

### Invocation Processing


Full mechanism for invoking a service, using the invocation details derived from its metadata 
Service invocation follows 4 main steps:
1. Retrieve Metadata
2. Validate
3. Execute
4. Audit




## Invocation Details Global Fields and where they get set

In our debug statements we've picked up some key information about the outcome of the invocation, whether it was successful, its State and an Error Message (this should be empty in the example). The object additionally holds information that was derived from the invocation, the time it ran, the running user, if cache was used to increase performance and a host of other information which we will cover in time.

### Retrieve Metadata

Some of these are derived when the Invocation is initialized (__Retrieve Metadata__) and are accessible to both the invoking and implementing code. We'll highlight those that are pertinent to our example where all of these are set in the Invocation CMT and derived when the invocation is initialized:


•	InvocationName: The name of the **Invocation CMT Record** that is referenced in the calling code. 
•	InvocationMechanism: The technical mechanism that will be used to run our invocation. The main options are *Sync* (synchronous invocation), *Async* (via Platform Event) and *Queueable*
•	InputDefinition / InputDefinitionNamespace: An Apex class, interface, literal or collection, that this invocation will provide to the Implementation
•	OutputDefinition / OutputDefinitionNamespace: An Apex class, interface, literal or collection, that this invocation will receive back from the Implementation 
•	ImplementingClass: In our example we have explicitly provided the implemenation class name to the invocation using the *Explicit Implementation* field in the Invocation CMT Record. The implementation can also be provided by custom metadata that represents decoupled services too but here we are defining the implementation explicitly, by-passing the Service Implementation / Method queries. We note that in an enterprise context we would want to do this only when the implementing class is under the control of the team owning this invocation record
ServiceName;Method;Iteration;TechnicalVersion;

### Validate


The (__Validate__) phase looks at our configuration setup for the invocation and confirms if it's valid and runnable. If not we set these fields, though we won't see that in our example as the configuration is valid for us.

•	ConfigurationValid
•	ConfigurationHasWarning
•	ConfigurationState
•	ConfigurationErrorMessage

### Execute


The (__Execute__) phase runs the implementation class and passes any output data back the invoker. Within the implementation class the following fields can be set and they are so imp

•	State: a string that is set to “SUCCESS” if the service call has been successful. Any other value signals a failure, but one where the semantics are "failed to process correctly". Explicitly in an implementation we should only ever set this to "SUCCESS", when we run into a 
•	BusinessOutcome: This is optional but can be set to pass back the outcome of an implementation call in a business context and to determine what a business process needs to do next. This is very vague, so let's try to clarity

{% hint style="info" %}

__State and Business Outcome__ 

Let's use an example where we search for a Contact in an external system and where the invoker needs to know what the outcome was in a clear and simple a way as possible, and may not with to directly query the output data resonse. If for example the invocation is from a flow then querying an output data structure really might be difficult to do and require a separate Apex action to accomplish it. 

Here's what the fields might show in these different scenarios:

1. We find the contact: State = "SUCCESS" / BusinessOutcome = "Contact Found" (or such like).
2. We make the call successfully but the contact is not found: State is still "SUCCESS" (the call ran so the invocation was successful), however  BusinessOutcome = "Contact Not Found In External System"
3. The callout fails: State = "FAILURE xxx" / BusinessOutcome = "External System Not Available"

With these return values a production manager knows that in the last case there's a production issue whereas the second case is not an issue for her.

The end user application will know that there is a distinction between the last two cases and can use the Outcome field to direct the user to different processes, one for when an individual data record is missing and one for when a whole integration is failing.

And a data quality manager might find a report based on the Audit records for the second case useful in resolving data issues on the external system

We can see an example of Business Outcomes being used in our Demo application in [Tab_Chaining_3_First_local](https://github.com/kevinhenryburke/frictionless/blob/master/demo/force-app/invocations/localInvocations/classes/Tab_Chaining_3_First_local.cls)

{% endhint %}


These fields are not set explicitly when an implementation is run but are populated when an error is raised in the implementation - see our [page on Raising Errors](ErrorRaising.md)
•	IsSuccess: a convenience parameter which is true if State is "SUCCESS", false otherwise 
•	IsFail: a convenience parameter, the boolean opposite of IsSuccess
•	ErrorMessage: returns any associated error information when State does not signal a success. We'll see how this is populated soon.
•	ErrorCode: The name of the **Service Error CMT Record** that holds the information on what to report for a raised error


### Audit Records

To see the invocation details after an invocation we'll switch on auditing for our invocation **ExampleDecoupledMethod**. Go to Custom Metadata Types from the Setup menu and select Edit against that record and enter the value **AuditSync** against the field *Audit Invocation* and save. Then run the sample code again for that exercise in execute anonymous.

This time we should have done a bit of extra processing. If you go to the App Selector (the 9 dots) and search for Audit and select the object *Service Framework Audit*, select the most recent record using the *Audit Full* list view you should now see a record that was created by your most recent code run. There's a field called *Invocation Details* and inside that there's some JSON, and this is how our Invocation Details were at the end of the transaction. 

Prettifying this, it will look something like this:

```

{
  "UserId":"xxx",
  "UseCache":false,
  "TransactionsRolledBack":false,
  "StaticParameters":{
    
  },
  "State":"SUCCESS",
  "ServiceTime":"2022-03-31T18:09:51.797Z",
  "ServiceEnd":"2022-03-31T18:09:51.804Z",
  "ServiceDuration":7,
  "Quiddity":"ANONYMOUS",
  "Purpose":"Business Function",
  "OutputDefinitionNamespace":"",
  "OutputDefinition":"String",
  "MetadataId":"m000C0000005BQ6QAM",
  "IsVariant":false,
  "IsValid":true,
  "IsTransactional":false,
  "IsSuccess":true,
  "IsStub":false,
  "IsOverride":false,
  "IsLocalInvocation":true,
  "IsFail":false,
  "IsAsyncCall":false,
  "InvocationTime":"2022-03-31T18:09:51.769Z",
  "InvocationPositionList":[
    1
  ],
  "InvocationName":"ExampleDecoupledMethod",
  "InvocationMetadataType":"Metadata Record",
  "InvocationMechanism":"Sync",
  "InvocationEnd":"2022-03-31T18:09:51.810Z",
  "InvocationDuration":41,
  "InvocationDetailsClass":"InvocationDetails",
  "InvocationContextId":"4i58Z2_D34VjeDkCag6Lb-",
  "InputDefinitionNamespace":"",
  "InputDefinition":"String",
  "InputCreationClassNamespace":"",
  "InitiatingContextTrackingId":"4i58Z2_D34VjeDkCag6Lb-",
  "ImplementingType":"ApexClass",
  "ImplementingClass":"ExampleDecoupledMethod",
  "IgnoreCacheValidation":false,
  "HasVariants":false,
  "ForceComputeCacheValidation":false,
  "ExternalInvocation":false,
  "ExplicitImplementation":"ExampleDecoupledMethod",
  "ConfigurationValid":true,
  "ConfigurationState":"SUCCESS",
  "ConfigurationHasWarning":false,
  "childInvocations":[
    
  ],
  "CacheUsed":false,
  "BubbleUpErrors":true,
  "AuditOutputDataSerialized":"IlJldHVybmluZyBiYWNrOiBNeSBJbnB1dCI=",
  "AuditInvocation":"AuditSync",
  "AuditInputDataSerialized":"Ik15IElucHV0Ig==",
  "AuditErrorsOnly":false,
  "AttemptCacheReadWrite":false
}

```

Some of this is self-explanatory, some of it certainly is not, but hopefully you will see that this is a very useful object to get to know and it is the key to understanding how this application works.
