## Chaining Invocations in Flow

We have seen on [Handling Responses - Apex](ErrorHandling.md) how to combine multiple invocations of services in Apex to implement a business process in a class called *ExampleMulitpleCalls*. Here we will provide guidance on how to implement the same Use Case in flow. We will implement a Screen Flow but the steps for headless flow types are the same.

{% hint style="tip" %}
Whilst running through this example you might want to open [Handling Responses - Apex](ErrorHandling.md) in an adjacent tab or window - we won't repeat the requirement here and it is enlightening to see the similarities between the Apex and Flow cases.
{% endhint %}

### Step 1: Input Data

First off, create a screen flow called *Chaining Invocations Rating Example* and as a first element create a screen with API name *UserEntryScreen*. In that screen add a text box with API name *FullName*. You can optionally set a default value of *Barack Obama*. Whatever is entered in this box will be the input to our two service invocations.

### Step 2: Invoke the Sensitivity service

This is where it starts to get more interesting. After the *UserEntryScreen* element add an Action. In the New Action screen you will see a category called **Microscope Actions**. Select this and in the Action search box select *Microscope: String to String Invocation*. 

{% hint style="info" %}

What is this? In order to invoke Apex from flow, a class with an *@InvocableMethod* needs to be referenced. This class must specify the input and output types that the flow must use to call Apex. In this case we want to invoke a service with a *String => String* signature, hence the name of the Microscope Action we have selected. 

If you are interested in how this works, you can see the implementation of this action in [ActionStringString.cls](https://github.com/kevinhenryburke/frictionless/blob/master/serviceBase/force-app/Framework/classes/flow/actions/reusable/ActionStringString.cls). You'll see an inner class called *InputFromFlow* which holds the parameters that Flow will provide. This has two members:

* The name of the Invocation we want to run
* The input data, in this case a String

The invocable method takes this input, initializes an invocation using the invocation name provided in the input, and calls the *invokeService()* method on this. 
If you look back at [Handling Responses - Apex](ErrorHandling.md) this is precisely the same pattern that was used there, but here the developer does not need to worry about the invocation call directly as the Action Apex class takes care of it.

Finally the invocable method populates a response (another inner class, *OutputToFlow*) which will be available to the flow as the output parameters. This also consists of two members

* The Invocation Details that we created when the invocation was initialized and enriched as the service was run. 
* The output data, in this case a String

Using the returned Invocation Details, the flow can investigate the State and Business Outcome attributes which will guide how the flow should continue processing.

{% endhint %}


{% hint style="warning" %}

Because each *@InvocableMethod* needs to explicitly specify both an input and output parameter list to a flow, there is a strong chance that a project team will need to write their own invocable methods as Microscope can only provide common use cases. The list of common cases will grow in time but if you are using a bespoke data structure then unless the platform starts to allow generic *Object* as input and output there will be a need for custom classes.

However once one is written for an input / output pair then it is resuable for any calls with the same parameters. The *Microscope: String to String Invocation* is reusable for any invocation with that signature for example. More useful however is the Spring 20 innovation [Build Invocable Actions That Work for Multiple Objects](https://help.salesforce.com/s/articleView?id=release-notes.rn_forcecom_flow_fbuilder_dynamic_types.htm&type=5&release=224). As a result of this Microscope can provide the generic Microscope Action *Microscope: SObject to SObject Invocation*, which can handle any *SObject* as input or output. This can cover a large number of use cases.
{% endhint %}

Back to the example, after selecting the *Microscope: String to String Invocation* we enter the new action screen which we populate as below. We'll call the flow element *ExampleSensitivity* (the same name as the invocation it calls) and also use that value for the *InvocationName* input parameter. The *InputData* parameter is the value we entered in the first screen's text box

![Configuring the First Invocation Actions](SensitivityAction.png)

### Step 3: Routing depending on Business Outcome

Looking back again at [Handling Responses - Apex](ErrorHandling.md), after the invocation we had a switch statement with a number of options and we'll do the same in Flow but with a Decision element. We can define our Outcome Options within the Decision element as:

1. Outcome: There was an error: in the "Condition Requirements to Execute Outcome" box, set the *Resource* to be the output from the *ExampleSensitivity* invocation action / InvocationDetails / State and complete the condition by specifying that this does not equal "SUCCESS". Create a screen on this decision path to that affect to show it was followed and routed to the Admin Team and we are done
2. Outcome: Sensitive Client: use the output from the ExampleSensitivity action but select InvocationDetails / BusinessOutcome as the Resource to compare this time. If this value is "Sensitive Client" then we route to the Sensitive Client Team and we can put a screen to indicate that has happened. This is the end of this processing line.
3. Outcome: New Client: InvocationDetails / BusinessOutcome = 'New Client' and route to the Sales Team, again adding a screen to that affect if you wish.
4. Default Outcome: If the client is a standard client we need to look at their rating. This is the next step.

![Using Business Outcomes](DecisionGate.png)


### Step 4: Invoke the Rating service for Standard Clients


In the Default Outcome path create another new *Microscope Actions*. Select this and in the Action search box select *Microscope: String to String Invocation*. This time name the flow element *ExampleRating*, use that value also for the *InvocationName* input parameter and again the *InputData* parameter is the value we entered in the first screen's text box.

### Step 5: Perform different actions for different Rating Business Outcomes.

Put a Decision Gate below the *ExampleRating* which this time has two outcome paths:

1. Low-Rated Client: Use the output from the ExampleRating invocation action and check InvocationDetails / BusinessOutcome and check if the value is *Low-Rated Client*. If so we put a screen on this path to show that this is routed to the Client Risk Team.
2. Default outcome: Otherwise add a screen to state we are routing to the Services Team. 

### Step 6: Test the Flow

Try the flow with our examples from the Apex Implementation: Barack Obama, Homer Simpson, Bad Call and with a random name. Hopefully you will get taken to the correct end screen in each case.

## Summary / Identified Patterns

After we are done our flow will looks something like this. 

![Full Example Flow](ExampleFlow.png)

Notice the repeating pattern of an Apex Action calling an invocation followed by a Decision Gate which looks at a combination of the Invocation Details' *State* and *BusinessOutcome* parameters to route the user down the right path. This is a common pattern that is used repeatedly. 

Also note we have no Fault paths from our Apex Actions. This is because Exceptions are caught by the framework so will never be returned to the flow. The implicit Fault path is in fact contained in the Decision Gate always having as its first outcome a path for when InvocationDetails.State != "SUCCESS". This captures all potential errors in processing.





