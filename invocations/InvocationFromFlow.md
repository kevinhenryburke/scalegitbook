Apex Invocations are a great way to handle complex functionality, callouts or heavy-duty processing required of a flow. Apex is called via an *Apex Action* which consists of a class, a method and Input/Output types with some special properties (see [here](https://help.salesforce.com/articleView?id=sf.flow_ref_elements_apex_invocable.htm&type=5) for example). Here we will extend this concept and show how to create actions to run service invocations. We will create **Invocation Actions**, Apex Actions that wrap and call Invocations that can be configured for use in a flow directly from the Flow Palette. 

{% hint style="info" %}
An *Invocation Action* is an easy and efficient way to invoke a service from a flow. Developers are of course also free to invoke a service within the logic of any method acting as an Apex Action for a flow, by explicitly creating a *ServiceInvocation* object and running the *invokeService()* method on this. 
{% endhint %}


### Invocation Actions

We have already seen an example of an Invocation Action in our introduction when we discussed [Chaining Invocations in a Flow](../getting-started/ExampleFlow.md) but we'll go into more detail now and look at this from the ground up. 

An important thing to consider is what is different about a regular Apex Apction and 
one that operates as an Invocation Action and in reality the only significant differences are in terms of input and output:

* An Invocation Action should take in an invocation name and business input so that the controller can be instructed as to which invocation to call and what input to provide to that call.
* An Invocation Action should return the Invocation Details generated when executing the invocation(to let the flow know what happened and what to do next) alongside the business output.

In general, Apex Actions in Flows can have multiple input and output parameters, we must support this but this, and the fact that we cannot use all generic input types, create challenges to some options that might at first seem like good choices:

* Flow invocations cannot use generic Apex Objects so cannot use List&lt;Object&gt; as input or output parameters, they can reference generic Apex SObjects but not Objects. 
* Also they do not accept Maps as arguments which precludes a number of generic use cases (e.g. input and output using Map&lt;String,Object&gt style data types). 
* Flow invocations don't allow input or output data objects to be passed as individual arguments in Apex, if we need to process more than one object type we need to reference each as a member of a single class. So if for example we want to receive back information from an Apex invocation as to its success *in addition to* a newly created Contact, we have to create a new class which contains both a Contact **and** the Invocation Details together. This leads to the creation of a lot of (usually inner) classes which serve just this one purpose.

All these points in combination means that a fully generic Invoation Action that covers all use cases is not going to be possible: we are going to need to be tailor these to some extent.

## Anatomy of an Invocation Action

### Input 

Based on the discussions above we can start to determine what an Invocation Action should look like.

We need an input class which has:

- A member variable **InvocationName** (type String) which holds the name of the invocation. This is populated by the flow.
- One or more member variables required as business input to the invocation 

As with all input and output classes to flow, any member that we wish to appear in the Flow configuration screens must be declared as an @InvocableVariable.

This class will service two purposes for use - as the type of a List it is the input to the Action's **execute** invocable method and singularly it is the inputData to the **invokeService()** method.

The input class can be either:

1. An inner class *InputFromFlow* within the flow action class for input, or *OutputToFlow* for output, where these are more specific to this particular Invocation Action. A good example of an Invocation Action is the class [DemoMultiArgInvocableMethod](https://github.com/kevinhenryburke/frictionless/blob/master/demo/force-app/flows/Demo_Multi_Arg/classes/DemoMultiArgInvocableMethod.cls)
2. A reusable input class. For example, see the usage of the class [FlowInputSObject](https://github.com/kevinhenryburke/frictionless/blob/402c241c367eb7943a33a7459c1e6ca03b1c0c4a/serviceBase/force-app/Framework/classes/flow/actions/inputs/FlowInputSObject.cls)  in the Invocation Action [ActionSObjectSObject](https://github.com/kevinhenryburke/frictionless/blob/master/serviceBase/force-app/Framework/classes/flow/actions/reusable/ActionSObjectSObject.cls). *FlowInputSObject* can be used as the input for **any** flow action with a SObject as input. 

Each input class is basically the business input plus the invocation name and each output class is essentially the business outputs plus Invocation Details. Please follow these links for the up to date list of Invocation Action [Inputs](https://github.com/kevinhenryburke/frictionless/tree/master/serviceBase/force-app/Framework/classes/flow/actions/inputs) and [Outputs](https://github.com/kevinhenryburke/frictionless/tree/master/serviceBase/force-app/Framework/classes/flow/actions/outputs) 

### Output 

We also need an output class which has:

- A member variable **invocationDetails** which holds the *mscope.InvocationDetails* after the invocation has run. This is passed back to the flow to inspect for error and routing options.
- One or more member variables provided as business output from the invocation 

This class is also multi-purpose - as a List as the output of the **execute** invocable method and singularly as the outputData from the **invokeService()** method.

The same considerations and options are true for output classes as input classes in regards to being reusable, standalone classes or inner classes in the Invocation Action class.

### Execute 

Inspecting any of the example methods above (e.g. [ActionSObjectSObject](https://github.com/kevinhenryburke/frictionless/blob/master/serviceBase/force-app/Framework/classes/flow/actions/reusable/ActionSObjectSObject.cls)) we see that the execute method takes the same form. It is an @InvocableMethod and the input and output are Lists of the types discussed above. The body loops through the input lists and calls the invocations requested, building up the output structure on completion of each call.

[//]: TODO - this needs bulkification: https://github.com/kevinhenryburke/frictionless/issues/374


### Standard Invocation Actions

If two Invocation Action flow elements have the **same input and output signatures** they can use **the same action**. We have seen above that we can reuse flow input classes and flow output classes and we can extend this reuse further and define some **Standard Invcation Actions** which are reusable. The up to date list of Standard Invocation Actions can be found [here](https://github.com/kevinhenryburke/frictionless/tree/master/serviceBase/force-app/Framework/classes/flow/actions/reusable)

A good example to look at is the class [ActionSObjectSObject](https://github.com/kevinhenryburke/frictionless/blob/master/serviceBase/force-app/Framework/classes/flow/actions/reusable/ActionSObjectSObject.cls) which can be used for any flow element that needs to map SObject -> SObject in an Invocation Action. This class is interesting to look at in detail, it uses both a reusable input and output classes for the *execute* method and the logic of that method is a template for any bespoke Invocation Action that you may need to write.

### Wiring up an Invocation Action in a Flow

This is covered in [Invocations from Flow](../getting-started/ExampleFlow.md)

Note that in that example, all of our Standard Invocation Actions appeared when we entered the *New Action* screen and selected the category *Microscope Actions*. Referring to [ActionSObjectSObject](https://github.com/kevinhenryburke/frictionless/blob/master/serviceBase/force-app/Framework/classes/flow/actions/reusable/ActionSObjectSObject.cls) again, this category is populated by those actions which have *Microscope Actions* set as the category in the *InvocableMethod* decorator

```
@InvocableMethod(label='Microscope: SObject to SObject Invocation' description='Microscope: SObject to SObject Invocation' category= 'Microscope Actions')
```

### Versioning an Invocation Action

Provided the signature of the service method (and therefore the related Apex Action)  remains the same then new functionality provided by the call does not need to result in a new version of the flow: we just update Invocation Metadata. 

If the signature changes and we need a new version of the input or output class, we may need a separate invocation and for the existing flow to be saved as a new flow and then updated with the new functionality. If this is likely to be a common occurrence in a frequently changing service then the option we mentioned earlier of invoking the flow from inside a standard Apex Action might be a good choice.


## Benefits Realized

Now that we have our Apex Action encapsulated in a Microscope Invocation we get all the other advantages of the framework without changing any code or the flow. This includes developer stubs, overrides, environment stubs, quiddity, user security restrictions. We also have a way of having the functionality implementing a Flow Action located in a different package from the flow itself, and the capacity for regional variations, all for a small piece of work that follows a fully standardized pattern. . 


