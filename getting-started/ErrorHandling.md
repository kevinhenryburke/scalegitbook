## Adding a second call

This more complex example will show calling code responding to both Errors and Business Outcomes when querying a combination of systems.  

We will add a new Privacy / Sensitivity System to the mix which informs us whether a client is sensitive and needs special handling. It expects a client's name as its input and if found returns a boolean indicating whether this is a Sensitive client who needs special treatment. 

The business process depends on the output of calls to both the Sensitivity and Ratings systems and indeed on whether the client was found or the data, the process being:

* If the client is unavailable in either system then alert the Admin team (we mimic a system break by entereing 'Bad Call' as input to the services)
* If the client is Sensitive we assign her to the Sensitive Clients Team, regardless of rating.
* If the client is Unknown we assign her to the Sales Team.
* If the client is not sensitive then we assign her to the Client Risk Team if her rating is poor or to the Services Team otherwise.
* If we get an unexpected response we want the Admins to take a look at the client.

### Setting up the Services and Metadata

These parts are not as interesting to us as we have seen this before, but this is all essential to run the example.

Let's first create the Sensitivity Service implementation which is structured identically to our last version of the Ratings implementation. Both of these service implementations are raising errors and setting business outcomes.

```
global inherited sharing class ExampleSensitivity implements mscope.IImplementation {
 
    class SensitiveReply {
        String name;
        Boolean isSensitive;
    }

    static Map<String, SensitiveReply> SensitiveDB;
    
    static {
        SensitiveDB = new Map<String, SensitiveReply>();
        SensitiveReply sensitiveReply;
        sensitiveReply = new SensitiveReply();
        sensitiveReply.name = 'Barack Obama'; 
        sensitiveReply.isSensitive = true;
        SensitiveDB.put(sensitiveReply.name, sensitiveReply);

        sensitiveReply = new SensitiveReply();
        sensitiveReply.name = 'Homer Simpson'; 
        sensitiveReply.isSensitive = false;
        SensitiveDB.put(sensitiveReply.name, sensitiveReply);
    }

    static SensitiveReply querySensitive (String name) {
        return SensitiveDB.get(name);
    }

    global Object dispatch(mscope.InvocationDetails invocationDetails, Object inputData) {
        String inputDataCast = (String) inputData;

        // in a more real world example a full data structure with privacy related values would be the return value
        String returnValue = 'Not Relevant';
        
        // mimic a failure scenario
        if (inputDataCast == 'Bad Call') {
            invocationDetails.raiseError('SensitivitySystemError');
            invocationDetails.addErrorReference('Invocation', invocationDetails.InvocationName);        
        }
        else {
            SensitiveReply sensitiveReply = querySensitive(inputDataCast);

            if (sensitiveReply != null) {
                if (sensitiveReply.isSensitive) {
                    invocationDetails.BusinessOutcome = 'Sensitive Client';
                    returnValue = 'Sensitive as politically exposed';
                }
                else {
                    invocationDetails.BusinessOutcome = 'Standard Client';
                    returnValue = 'Normal Sensitivity';
                }
            }
            else {
                invocationDetails.BusinessOutcome = 'New Client';
            }
        }
        return returnValue;
    }
}
```

We next need to create some custom metadata to call the Senstivity method. We create an *Invocation CMT* record from the Setup menu in Salesforce:

Enter values:
```
* Label: ExampleSensitivity
* Input Definition: String
* Output Definition: String
* Invocation Mechanism: Sync
* Explicit Implementation: ExampleSensitivity
* Audit Invocation: AuditSync
```

And an Service Error invocation record:

```
* Label / Service Error Code Name: SensitivitySystemError
* State: Sensitivity System Error
* Message: Sensitivity System Error
* Severity: Error
* Error Category: CustomServiceError
```


## The Calling Code

As the calling code is getting functionally richer it's probably a good time to encapsulate it in a class. Let's create a class *ExampleMulitpleCalls*:

```
public inherited sharing class ExampleMultipleCalls {

    public static void call (String inputData) {

        // invoke the Sensitivity query
        mscope.ServiceInvocation sinvSensitivity = mscope.ServiceInvocation.initialize('ExampleSensitivity');
        String returnedValueSensitivity = (String) sinvSensitivity.invokeService(inputData);
        mscope.InvocationDetails invocationDetailsSensitivity = sinvSensitivity.getInvocationDetails();

        // handle errors it raises
        if (invocationDetailsSensitivity.IsFail) {
            System.debug('Sensitivity System Issue: Move contact owner to Admin alert queue');
            System.debug(invocationDetailsSensitivity.State);
            System.debug(invocationDetailsSensitivity.ErrorMessage);
            return;
        }

        // invoke the Rating query
        mscope.ServiceInvocation sinvRating = mscope.ServiceInvocation.initialize('ExampleRating');
        String returnedValueRating = (String) sinvRating.invokeService(inputData);
        mscope.InvocationDetails invocationDetailsRating = sinvRating.getInvocationDetails();

        // handle errors it raises
        if (invocationDetailsRating.IsFail) {
            System.debug('Sensitivity System Issue: Move contact owner to Admin alert queue');
            System.debug(invocationDetailsRating.State);
            System.debug(invocationDetailsRating.ErrorMessage);
            return;
        }

        // Success scenarios - set output data and business outcomes.
        if (invocationDetailsSensitivity.IsSuccess) {
            switch on invocationDetailsSensitivity.BusinessOutcome {
                when 'Sensitive Client' {
                    System.debug('Move contact owner to Sensitive Clients Team queue');
                }
                when 'New Client' {
                    System.debug('Move contact owner to Sales Team queue');
                }
                when 'Standard Client' {
                    switch on invocationDetailsRating.BusinessOutcome {
                        when 'Low-Rated Client' {
                            System.debug('Move contact owner to Client Risk Team queue');
                        }
                        when else {
                            System.debug('Move contact owner to Service Team queue');
                        }
                    }
                }
                when else {
                    System.debug('Move contact owner to Admin alert queue');
                }
            }
        }
    }
}
```

If all has gone well you should now see the Business Outcomes and Errors for different input.


```
ExampleMultipleCalls.call('Barack Obama');
ExampleMultipleCalls.call('Homer Simpson');
ExampleMultipleCalls.call('New Person');
ExampleMultipleCalls.call('Bad Call');
```

Going back to the *ExampleMultipleCalls.call()* method, it is getting longer but look at how clear the calling code is and imagine the benefits for a real-world complexity application.

In reality such services as checks against a Privacy or Ratings system would return complex data structures filled with information, not the little strings we have in these examples. In real-world scenarios you do not want client applications to have to sift through these structures to work out business processing. You want your calls to such services to be provided errors and outcomes in a single way that leaves as little work as possible to the calling application to do in interpreting the outcome. 

The ideal scenario, and recommended approach when using *Microscope* is for a service to perform these 3 key actions on top of the essential business work it is designed to perform:

1. Recognize errors that have occured within the Service's domain and raise and report on these in a standardized way
2. Allow some semantic interpretation of the data and map these to business process events in a way that is both standardized and trivial to interpret for a calling party.
3. Map complex structures to simple data classes, standard objects, literals or interfaces.

This last point leads us to input and output data structures. (TODO link)

