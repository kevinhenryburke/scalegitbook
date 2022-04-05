### Quick Start - Introducing Invocation Details and Audit


### Inspecting our invocation response

Let's add one more line to our example call in [Quick Start: A Decoupled Method Call](DecoupledMethod.md)

```

mscope.ServiceInvocation sinv = mscope.ServiceInvocation.initialize('ExampleDecoupledMethod');
String returnedValue = (String) sinv.invokeService('My Input');
System.debug(returnedValue);
// new calls start here
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




### Invocation Details Global Fields

In our debug statements we've picked up some key information about the outcome of the invocation, whether it was successful, its State and an Error Message (this should be empty in the example). The object additionally holds information that was derived from the invocation, the time it ran, the running user, if cache was used to increase performance and a host of other information which we will cover in time.

Some of these are derived when the Invocation is __Initiated__ and are accessible to both the invoking and implementing code. We'll highlight those that are pertinent to our example where all of these are set in the Invocation CMT and derived when the invocation is initialized:


•	InvocationName: The name of the **Invocation CMT Record** that is referenced in the calling code. 
•	InvocationMechanism: The technical mechanism that will be used to run our invocation. The main options are *Sync* (synchronous invocation), *Async* (via Platform Event) and *Queueable*
•	InputDefinition / InputDefinitionNamespace: An Apex class, interface, literal or collection, that this invocation will provide to the Implementation
•	OutputDefinition / OutputDefinitionNamespace: An Apex class, interface, literal or collection, that this invocation will receive back from the Implementation 
•	ImplementingClass: In our example we have explicitly provided the implemenation class name to the invocation using the *Explicit Implementation* field in the Invocation CMT Record. The implementation can also be provided by custom metadata that represents decoupled services too but here we are defining the implementation explicitly, by-passing the Service Implementation / Method queries. We note that in an enterprise context we would want to do this only when the implementing class is under the control of the team owning this invocation record

•	BusinessOutcome

•	State: a string that is set to “SUCCESS” if the service call has been successful. Any other value signals a failure.
•	IsSuccess: a convenience parameter which is true if State is "SUCCESS", false otherwise 
•	IsFail: a convenience parameter, the boolean opposite of IsSuccess
•	ErrorMessage: returns any associated error information when State does not signal a success. We'll see how this is populated soon.
•	ErrorCode: The name of the **Service Error CMT Record** that holds the information on what to report for a raised error



### Introduction to Audit Records

To see the invocation details after an invocation we'll switch on auditing for our invocation **ExampleDecoupledMethod**. Go to Custom Metadata Types from the Setup menu and select Edit against that record and enter the value **AuditSync** against the field *Audit Invocation* and save. Next run the sample code again in execute anonymous.

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
