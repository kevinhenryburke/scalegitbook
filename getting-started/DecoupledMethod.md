### Before we start


We assume that Microscope has been installed, if not please do so using the [Installation instructions](../installation/Installation.md).

We also assume that you have some experience with Apex, how to create and edit classes and how to run code snippets in "Execute Anonymous". You also need to know what a Custom Metadata Type (CMT) is and how to add records to a CMT.

These exercises can all be done without an IDE, just using the Salesforce App's Developer Tools or, from VS Code, whatever your preference.

We also note that these initial examples cover Services that are both written in and invoked from **Apex**. We'll cover how this can be extended to **Flow** later.

## The simplest method call

We are going to build a simple decoupled method and call it. 
It will represent call to a fictitious Ratings system, it will expect a client's name as input and return a rating score (Good / Poor) if the client is found. We will build this example over the next pages and for now, to keep things really simple, we will assume that everyone's Rating is Good.


Let's start by asking ourselves what, in general, are the absolutely essential building blocks of any decoupled method call in Apex? 

We need (1) input and output parameters declared, (2) we need a method implemented inside of a class and we need (3) some code to call the method. These are all required. 
But we have also added an adjective: what do we mean by a **decoupled method**? 

We mean one where the name of the implementation of the method is not explicitly included in the alling mechanism, but derived using a metadata key. We will see how this simple device will allow the method to be renamed, versioned or repackaged without the calling code needing to change. 

Taking this additional step we will also need (4) some configuration to connect the method-calling mechanism to the implementation. So we have identified four separate items to implement a decoupled method. Let's try to do this with Microscope.


### 1. Firstly define the types of Input and Output

Every method needs input parameters and to return a value of a pre-defined type.

In our example let's keep it very simple and choose *String* for both the input customer name and output rating. *String* is an existing literal in Apex so there is no new work required building input or output structures in this case as these types already exist. 

{% hint style="info" %}
Generally input and output definitions might be Apex literals, classes, intefaces or SObjects or collections of these. We discuss the pros and cons at (TODO ADDLINK(Parameter Choices)). Also if the method you wish to decouple has more than one input parameter we suggest creating a class with each parameter as a named member variable and passing an instance of this class to the method as the single input parameter. This is clearer and more maintainable.
{% endhint %}

### 2. Create a class with a method to implement the functionality. 

We next define a class and implement our decoupled method within it. 

This class, and indeed any of our Apex method implementations of decoupled methods, will need to implement an interface *mscope.IService_Implementation* which has a single method *dispatch* and this will be our decoupled method

```
global inherited sharing class ExampleRating implements mscope.IService_Implementation {
 
    global Object dispatch(mscope.InvocationDetails invocationDetails, Object inputData) {
        String inputDataCast = (String) inputData;
        return 'Rating: Good';
    }
 
}

```

{% hint style="info" %}
In effect we are replacing a method in a class (with potentially other exposed business methods in that class) with a class that has just one public method within it, and always with the same name. So in defining our method we just reference a class name that satisfies an interface rather than the name of a method itself.

Also notice that *dispatch* has 2 parameters, we'll discuss the first parameter shortly. 
{% endhint %}



### 3. Create some configuration to hold the details of our choices above

We need config to hold the Input and Output Definitions and the implementing class to decouple the calling code from the method implementation.

Go to Custom Metadata Types from the Setup menu and create a new custom metadata record for the type *Invocation*. 

Enter values:
* Label: ExampleRating
* Input Definition: String
* Output Definition: String
* Invocation Mechanism: Sync
* Explicit Implementation Class: ExampleRating

You'll see a few other configuration options on the Invocation Custom Metadata Type which we will cover later but these values are enough to get us started in the simplest scenario.



### 4. Write a little bit of code to invoke the method

```

mscope.ServiceInvocation sinv = mscope.ServiceInvocation.initialize('ExampleRating');
String returnedValue = (String) sinv.invokeService('Barack Obama');
System.debug(returnedValue);

```

Run this in an execute anonymous session and hopefully you should see a debug line containing the text "Rating: Good". If so, you have successfully invoked a decoupled method.


### Summary - Is this useful?

What benefit is this? There is already a small benefit in decoupling a method like this. The calling code (our execute anonymous session) does not explicitly know the name of the class holding the implementation. We have the ability now to change the implementation without making any change to the calling code, but just by changing the value in the Custom Metadata Record for the field "Explicit Implementation". 

It also wasn't a huge amount of work, the method was implemented in its own class rather than as a method in a class that might contain other functionality. And we had to create a metadata record but that was about it. 

You might also have seen people implementing this pattern before, there's a few examples out there, so you might legitimately ask what's new here? The difference is that Microscope takes this concept a lot further than a simple decoupling/injection use case, this concept is central to a holistic approach to many use cases. It would be hard to argue for implementing an entire framework just for this decoupling option but we'll start to build up the benefits throughout these pages. 