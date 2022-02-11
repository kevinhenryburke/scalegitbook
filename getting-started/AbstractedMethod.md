# What is MyProduct I ask?

{% hint style="info" %}
**The Simplest Case:** We abstract a method
{% endhint %}

Firstly define the types of Input and Output. In our example lets keep it very simple and choose *String* for both input nd output


{% hint style="info" %}
**What Options do I have for input parameters?:** Input and output parameters might be Apex literals, classes, intefaces or SObjects. We discuss the pros and cons at (ADDLINK(Parameter Choices)). Also if the method you wish to abstract has more than one input parameter we suggest creating a class with each parameter as a named member variable and passing an instance of this class to the method as the single input parameter. This is clearer and more maintainable.
{% endhint %}


A class to implement the functionality

> Implements a standard interface

Some configuration to record the IO and the method

> Invocation Custom Metadata Type

An little bit of code to invoke the method

> Code Example