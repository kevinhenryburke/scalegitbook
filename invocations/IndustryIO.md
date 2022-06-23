## Salesforce Industries Example - Calling Microscope from a FlexCard

We'll run through an example of calling an invocation from a FlexCard. This relies heavily on [Map I/O](./InvocationComplexIO.md). This example reqruires that you have the *Omnistudio* package installed in the org. And the *Microscope* package of course!

### Microscope Set up

First we need to set up an invocation called *MicroscopeGenericVL* with the following parameters set:

* Input Definition	Map<String,Object>
* Output Definition	Map<String,Object>
* Invocation Mechanism	Sync
* Explicit Implementation Class	MicroscopeMapInMapOut

This final class needs to be implemented which you can do as below. Note that we are using *Map<String,Object>* as both input and output in this toy example 

```
global class MicroscopeMapInMapOut implements mscope.IService_Implementation {        
    global Object dispatch(mscope.InvocationDetails invocationDetails, Object inputData) {
        Map<String,Object> outMap = new Map<String,Object>();
        outMap.put('outputParam', 'hola');
        return outMap;
    }
}
```

Next create a class in the org for the FlexCard to talk to. The key thing to mention here is that this is a **Generic Class* that can be used for any FlexCard communication. The input arguments for the *invokeMethod* represent the 3 Map<String,Object> structures (inputMap, outMap, options) that the FlexCard uses to talk to Apex. 
The method reads in an invocation name from the *options* map and runs the invocation specified using the *inputMap* values. The inovcation output is then mapped to the *outMap* structure to be returned to the FlexCard.

```

global class MicroscopeGenericInvocation implements omnistudio.VlocityOpenInterface {

    global Boolean invokeMethod(String methodName, Map<String,Object> inputMap, Map<String,Object> outMap, Map<String,Object> options){

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
            system.debug('The error is ' + e.getMessage());
        }
        return true;
        
    }
}

Note that the Invocation Details generated during the invocation run is also returned (serialized) in the *outMap* so this is also passed back and available to the FlexCard.

```

### Flex Card set up

Now in the org create a FlexCard, selecting Data Source Type to be *Apex Remote*. On the next page select the *Remote Class* and *Remote Method* to point to the generic class we set up above.

*  *Remote Class* to be *MicroscopeGenericInvocation* 
*  *Remote Method* to be *invokeMethod*. 

For this example there is no need to set any input map fields but the options map is used to pass the name of the invocation through to the remote class and method. Here we need to specify one key/value pair:

* Key: invocationName
* Value: MicroscopeGenericVL

You can further add an Output Field for the output parameter *outputParam*. When the FlexCard is loaded it should return the message "Hola" in this field back from the implementation. This was set in the *Microscope* implementation line 

```
outMap.put('outputParam', 'hola');
```

You can also add displays from Invocation Details, for example add an output field to the card with output *invocationDetails.UserId* and you will see the running user id of the invocation returned.

