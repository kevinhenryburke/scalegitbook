## Salesforce Industries Example - Calling Microscope from a FlexCard

We'll run through an example of calling an invocation from a *FlexCard*. This relies heavily on using Maps as input and output which we discussed in [Map I/O](./InvocationComplexIO.md). This example requires that you have the *Omnistudio* package installed in the org. And the *Microscope* package of course!

### Microscope Set up

First we need to set up an invocation called *OSInvocationExample* with the following parameters set:

* Input Definition:	Map<String,Object>
* Output Definition:	Map<String,Object>
* Invocation Mechanism:	Sync
* Explicit Implementation Class:	OSImplementationExample

This Implementation Class needs to be implemented which you can do as below. Note that we are using *Map<String,Object>* as both input and output in this toy example as these are the structures that will be passed to us by the *FlexCard*s

```
global class OSImplementationExample implements mscope.IService_Implementation {        
    global Object dispatch(mscope.InvocationDetails invocationDetails, Object inputData) {
        Map<String,Object> outMap = new Map<String,Object>();
        outMap.put('outputParam', 'hola');
        return outMap;
    }
}
```

Next create a class in the org for the *FlexCard* to talk to. The key thing to mention here is that this is a **Generic Class** that can be used for any *FlexCard* communication. The input arguments for the *invokeMicroscope* represent the 3 *Map<String,Object>* structures (*inputMap*, *outMap*, *options*) that the *FlexCard* uses to talk to Apex. 
The method reads in an invocation name from the *options* map and runs the invocation specified using the *inputMap* values. The invocation output is then mapped to the *outMap* structure to be returned to the *FlexCard*.

```

global class MicroscopeGenericOSRemote implements omnistudio.VlocityOpenInterface {

    global Boolean invokeMicroscope(String methodName, Map<String,Object> inputMap, Map<String,Object> outMap, Map<String,Object> options){

        try {
            String invocationName = (String) options.get('invocationName');
            mscope.ServiceInvocation sinv = mscope.ServiceInvocation.initialize(invocationName);  
            
            Object outputData = sinv.invokeService(inputMap);

            mscope.InvocationDetails invDetails = sinv.getInvocationDetails();            

            // add invocation details to the outMap
            outMap.put('invocationDetails',invDetails);

            // and then add in the implementation's output (the real output) to the outMap
            if (invDetails.State == 'SUCCESS'){
                Map<String,Object> returnMap = (Map<String,Object>) outputData;
                for (String s : returnMap.keyset()){
                    outMap.put(s,returnMap.get(s));
               }
            }            
        } catch(Exception e) {
            // Improve error handling before productionizing
            system.debug('The error is ' + e.getMessage());
        }
        return true;
        
    }
}

Note that the Invocation Details object generated during the invocation run is also added (serialized) in the *outMap* so this is also passed back and available to the *FlexCard*.

```

### Flex Card set up

Now in the org create a *FlexCard*, selecting Data Source Type to be *Apex Remote*. On the next page select the *Remote Class* and *Remote Method* to point to the generic class we set up above.

*  *Remote Class* to be *MicroscopeGenericOSRemote* 
*  *Remote Method* to be *invokeMicroscope*. 

For this example there is no need to set any input map fields but the options map is used to pass the name of the invocation through to the remote class and method. Here we need to specify one key/value pair:

* Key: invocationName
* Value: OSInvocationExample

You can further add an Output Field for the output parameter *outputParam*. When the *FlexCard* is loaded it should return the message "Hola" in this field back from the implementation. This was set in the *Microscope* implementation line 

```
outMap.put('outputParam', 'hola');
```

The *outMap* contains a mix of entries, some are added generically and others, like *outputParam* above, within the specific implementation. You can add values from Invocation Details to your *FlexCard*, for example add an output field to the card with output *invocationDetails.UserId* and you will see the running user id of the invocation returned. The invocation details were added to the *outMap* once, generically, in the *MicroscopeGenericOSRemote,invokeMicroscope* call.



