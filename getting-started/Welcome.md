

## A Holistic Scalable Architectural Framework for Salesforce

Microscope is an architecture franework which may be adopted by any enterprise planning or already working within a Large Scale Implementation of the Core Salesforce Platform. 

It has particular benefits within an org spanning multiple business units or geographies with parallel, complex implementations which need to minimize their inter-dependencies. Such implementations have often struggled because as complexity and dependencies increase in their orgs they become opaque, no one has a structural overview of how things relate, new feature velocity falls and bugs become commonplace. 

Microscope's design takes into account the key reasons this happens and is focused on making an org readable and easy to work with for all roles, no matter how many programs are working within it. 

Microscope requires a rethink of many of the practices that delivery teams, architects and sponsors currently adopt. It is designed to allow a greater connection between the Shapers of a program (the sponsors, program managers, business analysts, designers) and those delivering the end product (developers, admins, testers, release teams). This benefit however requires an investment of time to adopt from all parties, but it is as investment that will pay for itself many times over in the years to follow.

{% hint style="info" %}

**Why is this app called Microscope?:** 

We hope, that like a real microscope, this app will show its users a clear view of the structure of what they are working with, for us a Salesforce implementation. 

The term also may also create a connection in the user's mind with Microservices architectures. This app does not provide a true microservice architecture, that's not its intention, but there is certainly an conceptual overlap.

And finally, Microscope can be read as providing small scope for changes and for artifacts that can be built using this architecture. This architecture facilitates a connections between scoped stories and build artifacts, and managing scope and maximizing the independence of scoped changes is at its core.
{% endhint %}


## A Single Approach for many Use Cases

It can however be hard for Enterprises to adopt such a fundamental architectural shift and Microscope can be very useful even if it is only adopted for a small number of use cases. 
Microscope addresses the common challenges an enterprise delivery brings and a team can adopt this architecture gradually, start small and take it as far as makes sense for the enterprise. 

An example might be that a team uses Microscope just to handle external interfaces on the path to production, allowing stubbed responses to call that are made in environments where the external system is not available or fully functional in test environments. Or an organization might want a unified way to call Apex from flows or to implement controllers for LWC components in a configurable and easily testable way. 

These are simple tasks for Microscope that developers can adopt quickly and just one of these might be an organization's only use of the application and it would still bring benefits. See the section on *Use Cases* for a more comprehensive list, but as you get familiar with the application you will think of still more.

{% hint style="info" %}
It is importat that we do not think of Microscope as a Swiss Army Knife, as a set of disparate tools for Enterprise development. It does help to solve a wide array of challenges but by adopting a single strategy and applying it to each.

Rather than a Swiss Army Knife, a  better analogy comes from Children's TV. In the UK there is a Science Fiction series called *Doctor Who*, about a time-traveling, human looking alien who can seemingly defy most laws of physics at will. Often when set with a challenge, The Doctor will pull out a *Sonic Screwdriver* and point it at whatever needs to be fixed and, sure enough, whatever outcome was desired comes to pass. 

{% endhint %}


{% embed url="https://www.youtube.com/watch?v=FOixJvAgERQ" %}








## Supporting Gradual Adoption and Snowballing Benefits


An enterprise can gradually add new use cases as and when they see the value of the initial implementations. These initial implementations can then become first steps towards a fuller adoption of this framework. As the number of features used increases, and because so many functionalities are built using the same techniques, the value-add of the framework increases. In time we will hit a benefits loop:

![Gradual Adoption has Snowballing Benefits](SnowballingAdoption.png
)





## When not to use Microscope


{% hint style="danger" %}

Please be aware that Microscope is aimed specifically at large, complex implementations. A lot of the recommendations are irrelevant and, in some cases, suboptimal for a Small or Medium implementation with no large growth aspirations as it in some cases inverts some of the recommendations of Clicks-Not-Code and requires ongoing developer-level support. 

Microscope addresses the Salesforce Lightning Platform, so is applicable to custom developmments on the Salesforce Platform, Sales, Service and Experience Clouds for example. There is also a packaged *CRM Analytics* application for analyzing audited data. In does not address any other platforms within the Salesforce product suite.  

This document addresses build complexity but not data volumes and when we talk about a *Scaleable Architecture* we are addressing the flexibility of the design and the ability to respond to business change. We do consider API limits in our discussiones on some design decisions and tool choices. However performance considerations arising from large data volumes and the challenges of scaling high user volumes, concurrency, search, data retrieval, sharing and reporting, all of which are equally important topics, are not covered by Microscope.

{% endhint %}



## How To Learn

The best way to learn the capabilities of the Microscope application is to use the demo application. The package overview is detailed at [Microscope Package Overview](../installation/PackageOverview.md)

TODO Need to document the Demo package tabs etc

The quickest route to starting to develop is to jump in with the [Simplest Use Case](DecoupledMethod.md)


### Acknowledgements

Many thanks are due to Leon de Beer, Matthew Leete, John Davies, Greg Scarrott, Felix Lindsay and Craig Bradshaw, some of the finest architects working on the Salesforce Platform, for sharing their expertise and for many valuable comments