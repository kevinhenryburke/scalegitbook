## Specifying Business Outcomes

As we've seen [last 2 pages], the State field allows an implementation class to pass back to the invoker that somethings has gone wrong with processing. We also mentioned another concept, a *Business Outcome* that can be used as simple mechanism to communicate from a business perspective what happened when an implementation ran.

This is a very useful standardization, if used across an Enterprise then it will greatly ease how quickly people can switch from developing in one part of the business to another and will massively reduce the complexity required for applications to respond to the data they receive when they invoke other parts of the org or external interfaces. It is particularly useful when invoking a service implementation from a Flow, [TODO reference to flow page when written], when a service is invoked from a flow we return two parameters, the output data and the invocation details, and as the business outcome is visible to the flow it can be used in future decision gates with nothing more complicated as a string equality check. It is simple and powerful.

When working out how to classify business outcomes we need to think about the business process. The ideal business outcomes list should be something that we can map to a list of paths that may be followed at decision points in our process. So for example if an implementation gives a rating as High Value or Low Value for a customer and these trigger different processes in the business process then these might be good choices for business outcomes from the service.

We can extend our example to show this in action. Suppose that our method retrieves information about an individual and in the process determines if they are a Sensitive figure and we know that this is of interest to our business process. We can update our method like this.

```
global inherited sharing class ExampleDecoupledMethod implements mscope.IService_Implementation {
 
    global Object dispatch(mscope.InvocationDetails invocationDetails, Object inputData) {
        String inputDataCast = (String) inputData;
        String returnValue;
        
        if (inputDataCast == 'Failure') {
            // How to respond to a failure in processing
            invocationDetails.raiseError('ExampleErrorCode');
            invocationDetails.addErrorReference('Invocation', invocationDetails.InvocationName);        
        }
        else {
            // How to respond to a business routing
            // For example if a process needs to know if a client is sensitive
            if (inputDataCast == 'Barack Obama' || inputDataCast == 'Angela Merkel'){
                invocationDetails.BusinessOutcome = 'Sensitive Client';
            }
            else {
                invocationDetails.BusinessOutcome = 'Standard Client';
            }
            returnValue = 'Returning back: ' + inputDataCast;
        }
        return returnValue;
    }
}
```

We note that we have not changed the output of the method at all, we have just highlighted an extra attribute

We can call now call this and add a little extra debug, but of course we can look at the newly created audit record too, there's a Business Outcome field on the page.

```
mscope.ServiceInvocation sinv = mscope.ServiceInvocation.initialize('ExampleDecoupledMethod');
String returnedValue = (String) sinv.invokeService('Barack Obama');
System.debug(returnedValue);
mscope.InvocationDetails invocationDetails = sinv.getInvocationDetails();
System.debug('IsSuccess: ' + invocationDetails.IsSuccess);
System.debug('State: ' + invocationDetails.State);
System.debug('ErrorMessage: ' + invocationDetails.ErrorMessage);
System.debug('BusinessOutcome: ' + invocationDetails.BusinessOutcome);
```

There are of course instances where a calling application needs a level of granularity that the service does not provide to drive its processing.. In these cases then the application is of course able to parse out whatever it needs from the output data to create the business process that it needs to follow. 







