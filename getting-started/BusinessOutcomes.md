## Specifying Business Outcomes

As we've seen ( [Invocation Details](InvocationDetails.md), [Raising Errors](ErrorRaising.md) ), the *State* field allows an implementation class to report back to the invoker that something has gone wrong with processing. We also mentioned another concept, a *Business Outcome* that can be used as simple mechanism to communicate from a business perspective what happened when an implementation ran.

This is already a very useful standardization, if used across an Enterprise then it will greatly ease how quickly people can switch from developing in one part of the business to another  It will also reduce the complexity required for applications to respond to the data they receive when they invoke other parts of the org or external interfaces.. It is particularly useful when invoking a service implementation from a Flow[TODO reference to flow page when written]. When a service is invoked from a flow we return two parameters, the output data and the invocation details, and as the business outcome is visible to the flow it can be used in future decision gates with nothing more complicated than a string equality check. It is simple and powerful.

When working out how to classify business outcomes we need to think about the __Business Process__. The ideal business outcomes list should be something that we can map to a list of paths that may be followed at decision points in the process. So for example if an implementation gives a rating as High Value or Low Value for a customer and these trigger different processes in the business process then these might be good choices for business outcomes from the service.

## Extending our Example

We can extend our toy Rating example to show how to add Business Outcomes. We'll add a data structure inside the class to hold a few names and their ratings (obviously in a real-world context such information would be retrieved via a query to a data source).

The business team has told us that the customer's Rating status is of interest to our business process so we use this measure at the Business Outcome for this method. We can update our method to support this with a few extra lines which set the BusinessOutcome to be either 'High-Rated Client' and 'Low-Rated Client' .

```
global inherited sharing class ExampleRating implements mscope.IService_Implementation {
 
    class RatingReply {
        String name;
        String rating;
    }

    static Map<String, RatingReply> RatingDB;
    
    static {
        RatingDB = new Map<String, RatingReply>();
        RatingReply ratingReply;
        ratingReply = new RatingReply();
        ratingReply.name = 'Barack Obama'; 
        ratingReply.rating = 'Good'; 
        RatingDB.put(ratingReply.name, ratingReply);

        ratingReply = new RatingReply();
        ratingReply.name = 'Homer Simpson'; 
        ratingReply.rating = 'Poor'; 
        RatingDB.put(ratingReply.name, ratingReply);
    }

    static RatingReply queryRating (String name) {
        return RatingDB.get(name);
    }

    global Object dispatch(mscope.InvocationDetails invocationDetails, Object inputData) {
        String inputDataCast = (String) inputData;
        // in a more real world example a full data structure with rating related values would be the return value
        String returnValue = 'Not Relevant';
        
        // mimic a failure scenario
        if (inputDataCast == 'Bad Call') {
            invocationDetails.raiseError('RatingSystemError');
            invocationDetails.addErrorReference('Invocation', invocationDetails.InvocationName);        
        }
        else {
            RatingReply ratingReply = queryRating(inputDataCast);

            if (ratingReply != null) {
                if (ratingReply.rating == 'Poor') {
                    invocationDetails.BusinessOutcome = 'Low-Rated Client';
                }
                else {
                    invocationDetails.BusinessOutcome = 'High-Rated Client';
                }
                returnValue = 'Rating:  ' + ratingReply.rating;
            }
            else {
                invocationDetails.BusinessOutcome = 'New Client';
            }
        }
        return returnValue;
    }
}
```


We can now call this and add a little extra debug, but of course we can look at the newly created audit record too, there's a Business Outcome field on the page which should now be populated.

```
mscope.ServiceInvocation sinv = mscope.ServiceInvocation.initialize('ExampleRating');
String returnedValue = (String) sinv.invokeService('Barack Obama');
System.debug(returnedValue);
mscope.InvocationDetails invocationDetails = sinv.getInvocationDetails();
System.debug('IsSuccess: ' + invocationDetails.IsSuccess);
System.debug('State: ' + invocationDetails.State);
System.debug('ErrorMessage: ' + invocationDetails.ErrorMessage);
System.debug('BusinessOutcome: ' + invocationDetails.BusinessOutcome);
```

Try changing 'Barack Obama' to 'Homer Simpson', 'Bad Call' or any different input and rerun you should see some different States, Error Messages and Business Outcomes.

There are of course instances where a calling application two consider Business Outcomes in more than one category. For example the same system might provide Rating and Current Balance information in one call where both values drive the ongoing business process. Ideally we would split these into 2 methods with separate calls that provide us with one business outcome each. But this might mean extra processing and callouts that might impact on performance.  In these cases then the application is of course able to parse out whatever it needs from the output data to create the input to the business process that it needs to follow. 







