## Setting up an Example

The more complex example will show calling code responding to both Errors and Business Outcomes when querying a combination of systems.  

We will add a new Privacy / Sensitivity System to the mix which inform us whether a client is sensitive and needs special handling. It expects a client's name as its input and if found returns a boolean as to whether the client indicating whether it is a Sensitive client who needs special treatment. 

The business process depends on the output of calls to both the Sensitivity and Ratings systems and indeed on whether the client was found or the data, the process being:

* If the client either system is unavailable then alert the Admin team (we mimic a system break by entereing 'Bad Call' as input to the services)
* If the client is Sensitive we assign her to the Sensitive Clients Team, regardless of rating.
* If the client is Unknown we assign her to the Sales Team.
* If the client is not sensitive then we assign her to the Client Risk Team if her rating is poor or to the Services Team otherwise.
* If we get an unexpected response we want the Admins to take a look at the client.

### Setting up the Services and Metadata

These parts are not as interesting to us as we have seen this before, but this is all essential to run the example.

Let's first create the Sensitivity Service implementation which is structured identically to our last version of the Ratings implementation. Both of these service implementations are raising errors and setting business outcomes.

```
Codey Block 1
```

TODO Link to Github for the two classes



We next need to create some custom metadata to call the Senstivity method. We create an *Invocation CMT* record from the Setup menu in Salesforce:

Enter values:
* Label: ExampleSensitivity
* Input Definition: String
* Output Definition: String
* Invocation Mechanism: Sync
* Explicit Implementation: ExampleSensitivity

TODO Link to Github for the two invocation metadata records


## The Callling Code

```
Codey Block 3
```


Look how clear the calling code is and imagine the benefits for a real-world complexity application.

In reality such services as checks against a Privacy or Ratings system would return complex data structures filled with information, not the little strings we have in these examples. In these real-world examples you do not want client applications to have to sift through these structures to work out business processing. 

The ideal scenario is for the data to understood inside a service which performs  3 key actions:

1. Recognize errors that have occured within the Service's domain and raise and report on these in a standardized way
2. Allow some semantic interpretation of the data and map these to business process events in a way that is both standardized and trivial to interpret for a calling party.
3. Map complex structures to simple data claases, standard objects, literals or interfaces.

This last point leads us to input and output data structures.

