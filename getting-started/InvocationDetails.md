### Quick Start - Introducing Invocation Details and Audit


### Inspecting our invocation response

Let's add one more line to our example call in (Quick Start: A Decoupled Method Call)[DecoupledMethod.md]

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

We are retrieving an object called **mscope.InvocationDetails**, but what is it? In short, this is the key object in Microscope, it is created when initialize an invocatino (so in the first line of our example) and it follows the invocation from inception through the service call and back to the invoker, picking up information all the way. The object is pretty big and holds many fields, you can inspect these in the (source class)[https://github.com/kevinhenryburke/frictionless/blob/master/serviceBase/force-app/Framework/classes/invocation/InvocationDetails.cls] and we'll see a more user friendly way to see how this is populated very soon.

In our debug statements we've picked up some key information about the outcome of the invocation, whether it was successful, its State and an Error Message (we should not have one in this example). The object additionally holds information that was derived from the invocation, the time it ran, the running user, if cache was used to increase performance and a host of other information which we will cover in time.

To see the invocation details after an invocation we'll switch on auditing for our invocation **ExampleDecoupledMethod**. Go to Custom Metadata Types from the Setup menu and select Edit against that record and enter the value **AuditSync** against the field *Audit Invocation* and save. Next run the sample code again in execute anonymous.

This time we should have done a bit of extra processing. If you go to the App Selector (the 9 dots) and search for Audit and select the object *Service Framework Audit*, select the most recent record using the *Audit Full* list view you should now see a record that was created by your most recent code run.



