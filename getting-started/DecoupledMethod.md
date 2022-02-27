
Let's start by creating a simple decoupled method call. In fact the simplest possible example and ask ourselves what are building blocks of any method call? We need input and output parameters declared, we need a method implemented inside of a class and we need some code to call the method. 

And do we mean by an decoupled method? We mean one where the precise implementation of the method is unknown to the calling mechanism, allowing the method to be renamed, versioned or repackaged without the calling code needing to change. If we want to take this additional step we will also need some configuration to connect the method-calling mechanism to the implementation.

So we have identified four separate tasks. Let's now try to implement this in a Microsope example. 


### 1. Firstly define the types of Input and Output

Every method needs input parameters and to return a value of a pre-defined type.

In our example let's keep it very simple and choose *String* for both input and output so there is no work required in this case as these types already exist. 

{% hint style="info" %}
Generally input and output definitions might be Apex literals, classes, intefaces or SObjects. We discuss the pros and cons at (ADDLINK(Parameter Choices)). Also if the method you wish to decouple has more than one input parameter we suggest creating a class with each parameter as a named member variable and passing an instance of this class to the method as the single input parameter. This is clearer and more maintainable.
{% endhint %}

### 2. Create a class with a method to implement the functionality. 

We next define a class and implement our decoupled method within it. 

This class, and indeed any of our Apex method implementations of decoupled methods, will need to implement an interface *IService_Implementation* which has a single method *dispatch* and this will be our decoupled method

```
global inherited sharing class ExampleDecoupledMethod implements mscope.IService_Implementation {
 
    public Object dispatch(mscope.InvocationPropertiesF1 invocationDetails, Object inputData) {
        String inputDataCast = (String) inputData;
        return 'Returning back: ' + inputDataCast;
    }
 
}

```

{% hint style="info" %}
In effect we are replacing a method in a class, with potentially other methods in that class, with a class that has just one public method within it, and always with the same name. So in defining our method we just reference a class name that satisfies an interface rather than the name of a method itself.

Also notice that *dispatch* has 2 parameters, we'll discuss the first parameter shortly. And also that the inputData to the method is encapsulated in one parameter as mentioned above.
{% endhint %}



### 3. Create some configuration to hold the details of our choices above, 

Namely config to hold the Input and Output Definitions and the implementing class.

Go to Custom Metadata Types from the Setup menu and create a new custom metadata record for the type *Invocation*. 

Enter values:
* Label: ExampleDecoupledMethod
* Input Definition: String
* Output Definition: String
* Explicit Implementation: ExampleDecoupledMethod

You'll see many other configuration options on the Invocation Custom Metadata Type which we will cover later but these values are enough to get us started in the simplest scenario.



### 4. Write a little bit of code to invoke the method

```

mscope.ServiceInvocation sinv = mscope.ServiceInvocation.initialize('ExampleDecoupledMethod');
String returnedValue = (String) sinv.invokeService('My Input');
System.debug(returnedValue);

```

Run this in an execute anonymous session and hopefully you should see a debug line containing the text "Returning back: My Input". If so, you have successfully invoked an decoupled method.


### Summary - Is this useful?

What benefit is this? There is already a small benefit in decoupling a method like this. The calling code (our execute anonymous session) does not explicitly know the name of the class holding the implementation. We have the ability now to change the implementation without making any change to the calling code, but just by changing the value in the Custom Metadata Record for the field "Explicit Implementation". 

It also wasn't a huge amount of work, the method was implemented in its own class rather than as a method in a class that might contain other functionality. And we had to create a metadata record but that was about it. It would be hard to argue for implementing an entire framework just for this benefit though and we'll start to build up the benefits in the next sections. 

