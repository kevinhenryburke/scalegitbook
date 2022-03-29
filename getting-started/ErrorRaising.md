## Raising Errors from an Apex Service Implementation

For maximum clarity in an Enterprise Implementation we need to be able to trace errors in the live and test systems to their origin. This isn't possible if error statements are hidden in mountains of code. We instead create a Custom Metadata Type **Service_Error_Code__c** to hold information about errors that may be thrown and a record for each such potential error. 

There are several benefits to this approach:

- Can have errors visible to audit and can enrich in audit tables
- Each package can create its own errors in its own folder structures. At runtime error messages from all packages will be visible in one place to administrators, but may have been sourced from many packages.
- We can also reference the package in the CMT, and provide the admins a quick route to finding which team is best placed to help
- By using Custom Metadata we Can retrieve error codes information with zero SOQL from CMT.  

Something is lost too. We do not have compile time validation that the name of the CMT Record referenced in code actually exists in the org. But unit testing covers this and every error scenario should have a unit test, right? We'll see how to do that in a bit.


### Example - how to raise errors in Service Implementations

This example references the Demo Service Echo Method which you can find [here](https://github.com/kevinhenryburke/frictionless/tree/master/demo/force-app/service-Demo/method-echo-1)

So, you are writing an apex service method and you need to report and error back to the invoker based on the input they have provided, perhaps it is not formatted as you want or reference a resource that is missing. The first thing to do is create a Service Error Code CMT record. Let's look at the example for the echo service [mscope__Service_Error_Code.DEMO_ECHO_BAD_INPUT.md-meta.xml](https://github.com/kevinhenryburke/frictionless/blob/master/demo/force-app/service-Demo/method-echo-1/mscope__Service_Error_Code.DEMO_ECHO_BAD_INPUT.md-meta.xml):

We reference a few properties:

Severity tells the Microscope framework how to act when a custom service replies back referencing the error code. The options are "Error" and "Warning" which terminate and allow processing to continue respectively.

Package Loaded references the name of the Logical Package which loaded the error code.

Error Category is free text, however we recommend that all Apex Service errors use the category in the example: CustomServiceErrors

The fields *State*, *Message* the record name map to the Invocation Details fields *State*, *ErrorMessage* and *ErrorCode*.


We can see a coded example in the demo app in the class [Demo_echo_1_1.cls](https://github.com/kevinhenryburke/frictionless/blob/master/demo/force-app/service-Demo/method-echo-1/impl-1/Demo_echo_1_1.cls). The implementation takes an string input that it converts to an integer and show signal an error if that number is negative. These are the main lines we're interested in as they show how the error is raised:

```
invocationDetails.raiseError('DEMO_ECHO_BAD_INPUT');
invocationDetails.addErrorReference('Invocation', invocationDetails.InvocationName);
invocationDetails.addErrorReference('ImplementingClass', invocationDetails.ImplementingClass);
invocationDetails.addErrorReference('Input', inputDataCast.msg);
```

The method *raiseError* does a few things, but principally its job is to set the invocationDetail fields _State_, _ErrorMessage_ and _ErrorCode_ to be the values retrieve from the CMT Record for "DEMO_ECHO_BAD_INPUT". 

The method *addErrorReference* allows the developer to add some dynamic information to the _ErrorMessage_, it has a key value format and appends these to the _ErrorMessage_ to give more context. What to add here is up to the design team and dependent on context and what needs to be communicated back to any invoking party. 

### Unit Testing errors from Service Implementations.

We can see a unit test example for raising errors in the demo app in the class [Demo_echo_1_1_Test.cls](https://github.com/kevinhenryburke/frictionless/blob/master/demo/force-app/service-Demo/method-echo-1/impl-1/Demo_echo_1_1_Test.cls) in the test method **dispatch_NegativeInput**. These are the main lines we're interested in:

```
System.assert(invocationDetails.IsFail, 'bad input to service method should tell the invocation it failed');
System.assertEquals('DEMO_ECHO_BAD_INPUT', invocationDetails.ErrorCode, 'Implementation does not accept negative input and should report a bad input exception'); 
```

The first line tests that the method is reporting an error when the input is negative and the second is testing that the correct Error Code has been set in the Invocation Details.


