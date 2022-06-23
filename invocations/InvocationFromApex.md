## Invocations From Apex

Configuring invocations to be run from Apex provides the most options compared to other mechanisms. This freedom however means the discussions on what to use is longer. We'll start with a preamble that some readers may choose to skim and get to the recommendations that follow.

### Preamble - Apex Interfaces and Classes

Let’s first look at **Apex Interfaces**  as a solution option. Interfaces are designed for several of the wish list items we discussed in [Invocation Input and Output](./InputOutput.md), they provide a view of the high-level functionality of data, a specification of what it can do for you and its rules of engagement, without going into any details of how the work is done. This separation of specification from implementation allows us a lot of additional scope for versioning and decoupling.

Within the Lightning Platform interfaces allow us to use different options for implementing in different contexts. For example, we can implement an interface differently for running tests, for stubs whilst developing or mocking interfaces in sandboxes and for end-state production processing. This flexibility, together with the ability to create an instance of an interface’s implementing class dynamically by providing only its name also allow us to have strict packaging borders.

However, it’s not all positives. Interfaces add extra files which is a minor irritant. We will always need a concrete class to implement an interface so the interface will always be an overhead in that respect. And what if we want to return data from an SObject, would we always want to wrap objects in interfaces? Almost certainly not. Just as significantly though, some elements in Salesforce will operate on a class but not an interface. For example, the decorators @AuraEnabled @InvocableMethod need to be placed on concrete non-virtual top-level (not inner) classes which reduces our scope for abstraction in the context of LWC and Flow for example. Flows also require a concrete class to be the home of Input and Output parameters.

So these quite significant drawbacks might lead us to choose **Apex Classes** instead of interfaces to define input and output data. The class could contain some exposed variables with decorators to allow convenient flow and LWC access and Properties instead of methods which would reduce get/set accessor code that is required by the interface option. However, any such I/O defining class would need to be available in any org (scratch, sandbox, production) which contains code that either invokes or defines the services, as would all of these classes’ dependencies. We might also risk starting a chain of dependencies and losing the independence of our Business Applications, with each package sitting  atop a monolithic package of I/O classes. Worse still, if we need to change one of these classes we introduce a risk to the larger build and create a situation where a change in one place will, at very least, require a full regression test of many others. This approach could also limit our ability to use stubs, mocks and testing variants. Interfaces are better in this regard, they do also create dependencies between packages but the dependency is on a definition, not an implementation, so we can eliminate the concurrent change risk and reduce the footprint of the dependency compared to using concrete classes.  

## High Level Reccommendation

The Interface and Apex class options both have clear merits in different contexts and we recommend supporting both. 

* For a strict business area or external call boundary the extra cost of providing a minimum dependency *Apex Interface* is worthwhile. This is especially true if interacting with classes defined in a Managed Package, failing to wrap these in an interface could make removing the package at a later stage a complex process.
* For any service is going to be useful in flows, screens or will never be called from outside of its package using an *Apex Class* as the input or output is simpler, less code and is easier to connect to Salesforce’s low code and screen building technologies. The classes should be dedicated to precisely this purpose, we need to implement them so that acting as Input/Output to service method calls is their only purpose, this will reduce the number of times that these have to change to a minimum. In this case there is very little loss compared to using an interface
* If a service just need an *Apex Literal* (like a String or number) as input or output then by all means use the literal as input and output, this will be understood by an package.
* Using an *SObject* as either input or output to a service will be safe if it is a standard SObject containing standard fields as this will be understood in any Salesforce package. In these cases there is not risk of a field defined in one package not being understood in another in a partial code base org. If a service is only called in its own package then this will also be safe but if we are crossing package boundaries it will only be safe if the package providing the service is a dependency for the package invoking the service. If that’s not the case wrapping the SObject in an interface is likely the best way to proceed.

All of the above mechanisms are supported by the framework so the developer has plenty of choice. We will use the term Interface to mean any of the above when used for service input or output in what follows and hope that the context will make it clear what we mean.

## TODO TODO TODO TODO Details from 9.4.3



We will go into more detail on how to decide which to use in Section 9.4.3 but for now here’s some recommendations.
