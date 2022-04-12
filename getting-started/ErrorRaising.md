## Raising Errors from an Apex Service Implementation

For maximum clarity in an Enterprise Implementation we need to be able to trace errors in the live and test systems to their origin. This isn't possible if error statements are hidden in mountains of code. We have instead created a Custom Metadata Type **Service_Error_Code__mdt** to hold information about errors that may be thrown and developers can a record for each such potential error in this object. 

There are several benefits to this approach:

- Information about Errors is visible to audit processing and can enrich audit records.
- Each package can create its own errors in its own folder structures. At runtime error messages from all packages will be visible in one place to administrators that may have been sourced from many packages.
- We can reference the package in the CMT records, and provide the admins a quick route to finding which team is best placed to help when an issue arises.
- By using Custom Metadata we can retrieve error codes information with zero SOQL from CMT and as CMTs are cached its very performant.  

Something is lost too however. We do not have compile-time validation that the name of the CMT Record referenced in code actually exists in the org. But unit testing can cover this and every error scenario should have a unit test, right? We'll see how to do that in a bit.


### How to raise errors in Service Implementations

We will look at the Demo Service Echo Method which you can find [here](https://github.com/kevinhenryburke/frictionless/tree/master/demo/force-app/service-Demo/method-echo-1) to show how Errors can be raised in Microscope and what to do when you are  writing an apex service method and you need to report an error back to the invoker. This might be an error based on the input they have provided, for example if it is not formatted as you want, or if it references a resource that is missing. 

**Step 1: Create a Custom Metadata Record for the Error**

The first thing to do is create a Service Error Code CMT record. Let's look at the example for the echo service [mscope__Service_Error_Code.DEMO_ECHO_BAD_INPUT.md-meta.xml](https://github.com/kevinhenryburke/frictionless/blob/master/demo/force-app/service-Demo/method-echo-1/mscope__Service_Error_Code.DEMO_ECHO_BAD_INPUT.md-meta.xml):

The CMT Record has a few properties:

- The fields *State*, *Message* the record name map to the Invocation Details fields *State*, *ErrorMessage* and *ErrorCode*.
- Severity tells the Microscope framework how to act when a custom service replies back referencing the error code. The options are "Error" and "Warning" which terminate and allow processing to continue (with a warning) respectively.
- Package Loaded references the name of the Logical Package which loaded the error code.
- Error Category is free text, however we recommend that all Apex Service errors use the category in the example: "CustomServiceErrors"

**Step 2: Raise the Error in your code**

We can see a coded example in the demo app in the class [Demo_echo_1_1.cls](https://github.com/kevinhenryburke/frictionless/blob/master/demo/force-app/service-Demo/method-echo-1/impl-1/Demo_echo_1_1.cls). This class is used in the Microscope Demo App too, in the Errors tab (click on *Show Meta* on each components to see which one has this class as its *Implementing Class*)

The implementation takes an string input that it converts to an integer and show signal an error if that number is negative. These are the main lines we're interested in as they show how the error is raised:

```
invocationDetails.raiseError('DEMO_ECHO_BAD_INPUT');
invocationDetails.addErrorReference('Invocation', invocationDetails.InvocationName);
invocationDetails.addErrorReference('ImplementingClass', invocationDetails.ImplementingClass);
invocationDetails.addErrorReference('Input', inputDataCast.msg);
```

The method *raiseError* does a few things, but principally its job is to set the invocationDetail fields _State_, _ErrorMessage_ and _ErrorCode_ to be the values retrieved from the CMT Record for "DEMO_ECHO_BAD_INPUT". 

The method *addErrorReference* allows the developer to add some dynamic information to the _ErrorMessage_, it has a key/value format and appends these to the _ErrorMessage_ to give more context. What to add here is up to the design team and dependent on context and what needs to be communicated back to any invoking party. 

**Step 3: Unit Test errors raised by the Service Implementation**

We can see a unit test example for raising errors in the demo app in the class [Demo_echo_1_1_Test.cls](https://github.com/kevinhenryburke/frictionless/blob/master/demo/force-app/service-Demo/method-echo-1/impl-1/Demo_echo_1_1_Test.cls) in the test method **dispatch_NegativeInput**. These are the main lines we're interested in:

```
System.assert(invocationDetails.IsFail, 'bad input to service method should tell the invocation it failed');
System.assertEquals('DEMO_ECHO_BAD_INPUT', invocationDetails.ErrorCode, 'Implementation does not accept negative input and should report a bad input exception'); 
```

The first line tests that the method is reporting an error when the input is negative and the second is testing that the correct Error Code has been set in the Invocation Details.

### Extending our Exercise

To extend our example, let's first create a Service Error CMT record. Go to "Manage Records" on that Custom Metadata Type in the Setup menu and click New and enter these value:

- Label / Service Error Code Name: RatingSystemError
- State: Rating System Error
- Message: Rating System Error
- Severity: Error
- Error Category: CustomServiceError

Then update the class *ExampleRating* to include a check on the input the method receives, and raise an error if the input is the string 'Bad Call' (to simulate an error in processing). 

```
global inherited sharing class ExampleRating implements mscope.IService_Implementation {
 
    global Object dispatch(mscope.InvocationDetails invocationDetails, Object inputData) {
        String inputDataCast = (String) inputData;
        String returnValue;
        
        if (inputDataCast == 'Bad Call') {
            invocationDetails.raiseError('RatingSystemError');
            invocationDetails.addErrorReference('Invocation', invocationDetails.InvocationName);        
        }
        else {
            returnValue = 'Rating: ' + inputDataCast;
        }
        return returnValue;
    }
}
```

We can then see how an error being returned by changing the invoking code to send the string 'Bad Call' to the service:


```
mscope.ServiceInvocation sinv = mscope.ServiceInvocation.initialize('ExampleRating');
String returnedValue = (String) sinv.invokeService('Bad Call');
System.debug(returnedValue);
mscope.InvocationDetails invocationDetails = sinv.getInvocationDetails();
System.debug('IsSuccess: ' + invocationDetails.IsSuccess);
System.debug('State: ' + invocationDetails.State);
System.debug('ErrorMessage: ' + invocationDetails.ErrorMessage);
```

You should see different responses in the debug statements, or if you prefer, take a look at the Audit record for the invocation details in the latest record.

```
{
...
  "State":"FAILURE: Rating System Error",
  "IsFail":true,
  "ErrorMessage":"Rating System Error (RatingSystemError). Invocation: ExampleRating",
  "ErrorCode":"RatingSystemError",
...
}
```
