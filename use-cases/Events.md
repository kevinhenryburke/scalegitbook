## Event-Based Processing

An important alternative to the fundamentally message-based architecture of invocation is the concept of a **Microscope Event**. 

A *Microscope Event* represents a change in the org that can be acted upon by the Microscope engine. Let's for example imagine a new Product is added to the catalogue. Different parts of the business may want to react to this, to alert a compliance team, to integrate to an inventory system or to notify a Sales team's chatter group. However, the team that creates the product record does not need to know all of these consequences, their job is to simply create the Product, so for them to have to create code to trigger each use case is putting the work to the wrong team and is building dependencies where they are not needed.

Our solution is built upon our invocation and service model, Event-driven architectures are often designed on top of message-driven architectures, and the emphasis for us is again in addressing and minimizing dependencies and complexity rather than in other benefits of some native Event-driven implementations.

![Events Model](Events.png)

## How to use an Event-Based model


An Event is represented by a Custom Metadata Type (CMP) called **Microscope Event** (API name: mscope__Event__mdt). If you want an invocation to be triggered by an event you need to populate the lookup on the **Invocation** CMT Record (mscope__Invocation__mdt) defined by the field *mscope__Microscope_Event__c*. Once that is done the invocation will be run whenever the related *Microscope Event* is fired. It's as simple as that. 

There is a volidation on the *Invocation CMT* which requires that any invocation trigger by an Event must have an asynchronous *Invocation Mechanism* (e.g. "Queueable" or "Async"). The reason is that by allowing any team to subscribe to an Event we could hit governor limits if we were to allow synchronsous invocations. 

Once setup firing the Evnet from Apex is simple

```
mscope.Event event = mscope.Event.initialize(eventName); 
event.fire(inputMessage);
```

You can see a simple example in the Demo Code (here)[https://github.com/kevinhenryburke/frictionless/blob/master/demo/force-app/components/sampleFireEvent/classes/SampleEventController.cls] of an Event called from an LWC.


Can call Event.fire(Event_CMT, InputData) in a PE Trigger, code, flow controller etc...

Reusable flow templates for stuff (i.e. flow elements ... ??) - new story

Would need validation - however Events should have input signatures only, not output
Validate that all listeners are async.

Would need to add Event to Invocation Details, perm setts, Audit field, CRMA field etc and have an Event correlation id etc

There's some advantages to this
Some of the "listeners" can be sync and some async
Package builders can create a subscription - and will even have their own invocation ids for testing
The Event CMT would need to be inside a "global" package, almost a base level package if it's global, or at a LOB level depending on scope of who is allowed to pick up the event.


