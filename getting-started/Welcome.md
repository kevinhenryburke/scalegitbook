
**What is Microscope?:** 


## What is Microscope? (and when to use it)


Microscope is an architecture which may be adopted by  Large Implementations of the Core Salesforce Platform with big plans for expansion. 

It has particular benefits for those working within a common org instance spanning multiple business units or geographies. Such implementations have often struggled as complexity and dependencies increase in their orgs, new feature velocity falls. Microscope's design takes into account the key reasons this happens and is focused on making an org as readable and easy to work with no matter how many programs are working within it. 

Microscope requires a rethink of many of the practices that delivery teams, architects and sponsors currently adopt. It is designed to allow a greater connection between the Shapers of a program (the sponsors, program managers, business analysts, designers) and those delivering the end product (developers, admins, testers, release teams). This benefit however requires an investment of time to adopt from all parties, but it is as investment that will pay for itself many times over in the years to follow.

Microscope may also be thought of as a collection of capabilities for some of the common tasks an enterprise delivery brings. An exmample might be that a team uses Microscope just to handle external interfaces on the path to production, allowing stubbed responses to call that are made in environments where the external is not available or fully functional in test environments. This is a simple problem for Microscope and could be an organization's only use of the application or be a first step towards a fuller adoption. We will see several such capabilities as we go into the details of how this app works.


{% hint style="info" %}

**Why is this app called Microscope?:** 

We hope, that like a real microscope, this app will show its users a clear view of the structure of what they are working with, for us a Salesforce implementation. 

The term also may also create a connection in the user's mind with Microservices architectures. This app does not provide a true microservice architecture, that's not its intention, but there is certainly an conceptual overlap.

And finally, Microscope can be read as providing small scope for changes and for artifacts that can be built using this architecture. This architecture facilitates a connections between scoped stories and build artifacts, and managing scope and maximizing the independence of scoped changes is at its core.
{% endhint %}

## When not use Microscope


{% hint style="danger" %}

Please be aware thAT Microscope is aimed specifically at large, complex implementations. A lot of the recommendations are irrelevant and, in some cases, suboptimal for a Small or Medium implementation with no large growth aspirations as it inverts some of the recommendations of Clicks-Not-Code and requires ongoing developer-level support. 

Microscope addresses the Salesforce Lightning Platform, so is applicable to custom developmments on the Salesforce Platform, Sales, Service and Experience Clouds for example. There is also a simple Tableau CRM application for analyzing audited data. In does not address any other platforms within the Salesforce product suite.  

This document addresses build complexity but not data volumes and when we talk about a *Scaleable Architecture* we are addressing the flexibility of the design and the ability to respond to business change. We do consider API limits in our discussiones on some design decisions and tool choices. However performance considerations arising from large data volumes and the challenges of scaling high user volumes, concurrency, search, data retrieval, sharing and reporting, all of which are equally important topics, are not covered by Microscope.

{% endhint %}



## How To Learn

The best way to learn the capabilities of the Microscope application is to use the demo application. The package overview is detailed at [Microscope Package Overview](../installation/PackageOverview.md)

TODO Need to document the Demo package tabs etc

The quickest route to starting to develop is to jump in with the [Simplest Use Case](DecoupledMethod.md)


### Acknowledgements

Many thanks are due to Leon de Beer, Matthew Leete, John Davies, Greg Scarrott, Felix Lindsay and Craig Bradshaw, some of the finest architects working on the Salesforce Platform, for sharing their expertise and for many valuable comments