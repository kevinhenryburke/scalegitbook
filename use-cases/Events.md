## Event-Based Processing

An important alternative to the fundamentally message-based architecture of invocations is the concept of a **Microscope Event**. 

A *Microscope Event* represents a change in the org that can configure to be acted upon by the Microscope engine. Let's for example imagine a new Product is added to the catalogue. Different parts of the business may want to react to this, to alert a compliance team, to integrate to an inventory system or to post to a Sales team's chatter group. However, the team that creates the product record does not need to know all of these consequences, their job is to simply create the Product record and that may be the scope of their ownerships in the governance model. For them to have to create code to trigger each use case is putting the work to the wrong team and is building dependencies where they are not needed. 

An event model has many use cases and can prove especially useful for external clients. Another example might occur in an Enterprise using a *Slack App* as a front end. A simple integration into Salesforce can consist of just emitting a *Microscope Event* behind the endpoint, allowing any part of the org to pick up on the change and perform whatever actions they need. An Event can also be fired from a Flow Controller, an Apex Trigger, an LWC Controller, a Platform Event or Change Data Capture (CDC) trigger, indeed pretty much anywhere one would want to. 

Our Event solution is built upon our invocation and service model but this is not unusual. Event-driven architectures are often designed on top of message-driven architectures, and the emphasis for us is again in addressing and minimizing dependencies and complexity and in increasing technical visbility rather than in other benefits of some native Event-driven implementations.

![Events Model](Events.png)

## How to use an Event-Based model


An Event is represented by a Custom Metadata Type (CMP) called **Microscope Event** (API name: mscope__Event__mdt). If you want an invocation to be triggered by an event you need to populate the lookup on the **Invocation** CMT Record (mscope__Invocation__mdt) defined by the field *mscope__Microscope_Event__c*. Once that is done the invocation will be run whenever the related *Microscope Event* is fired. It's as simple as that. 

The *Microscope Event* CMT also has fields that represent the package that contains the events metadata and the *Input Definition* of the Notifications that the event emits. As Events are asynchronous there is no need for an *Output Definition* to be provided.

There is a volidation rule on the *Invocation CMT* which requires that any invocation trigger by an Event must have an asynchronous *Invocation Mechanism* (e.g. "Queueable" or "Async"). The reason is that by allowing any team to subscribe to an Event we could hit governor limits if we were to allow synchronsous invocations. There is also a tooling validation that the Input Definition of each Invocation related to an event is castable from the Event's Input Definition.

Once setup firing the Evnet from Apex is simple

```
mscope.Event event = mscope.Event.initialize(eventName); 
event.fire(inputMessage);
```

You can see a simple example in the Demo Code (here)[https://github.com/kevinhenryburke/frictionless/blob/master/demo/force-app/components/sampleFireEvent/classes/SampleEventController.cls] of an Event called from an LWC.

## Teams creating and listening to Events

The team that creates an Event need to decide its **scope** within the org, do they want any part of the org, this will determine the package in which to load the Event's metadata. If the Event is usable across the full org we call it a **global event** and it should be created as part of a base package that is loaded before any LOB or Regional packages. In this way the Event can be referenced by any invocation created within the org. However it is also possible to restrict the Event to a particular LOB for example by loading its metadata in the LOB's lowest level package, authors of other LOB packages will not be able to reference this **local event** it in their invocations.

Provided am *Event* is visible within their scope, package builders can create a subscription via an Invocation CMT record referencing the Event. They will even be able to create test invoctions within their packages to see that their developments are behaving as they would like.

