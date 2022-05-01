## Complex Input and Output

The Invocation metadata type has only provision for only a single input and output argument. But what if our I/O is more complicated than this and has multiple attributes for either input or output? 

The obvious option (considering just input, output is the same argument) is to create a class with all these input attributes as member variables and mapping *inputData* to these. However this does have the overhead of requiring us to create a custom class.

Another option we should consider is that *Map<String, Object>* is a valid input and output structure for a service, so can be used as generic multi-parameter IO without the need for creating a custom class. 
Indeed, I/O in this format is the standard for Omnistudio, the Callable Interface in Apex and it is also handy for integration and dynamic I/O scenarios.

This too has its drawbacks. An invocation using *Map<String, Object>* would not have the same robust type validation as specifying a class and may therefore be more prone to breaking at runtime, especially if components are being developed by different teams and we are dealing the very large attributes sets common in the enterprise. However we can't ignore Maps as input or output, the use cases mentioned above are critical to many enterprises so we must be able to handle it, but also we can try to make it more robust.

In the rest of this page we partially solve this Map I/O issue by introducing a validation process to make sure the invocation will match the implementation definitions for *Map<String, Object>* input and output. We conclude with recommendations for our different use cases.

## Map I/O and Data Validation

The Framework has an interface [IMultiArgMap](https://github.com/kevinhenryburke/frictionless/blob/master/serviceBase/force-app/Framework/interfaces/IMultiArgMap.cls) which has a method *getMap()* returning a *Map<String,Type>* instance. 

We also have a section in Invocation CMT page layout called *Multiple Arguments I/O**. When we have an invocation with *Map<String, Object>* input for example (the output use case is directly analogous) then we can elect to validate the input by checking the box *Check Map Input Field Validity*. Below this box are fields to define the namespace and class (API name *Input_Multi_Arg_Map_Class__c*) that will be used to validate the input.

The Apex Class defined in *Input_Multi_Arg_Map_Class__c* is an implementation of IMultiArgMap and each time the invocation fires we check the key set of the *inputData* and check that every element expected by the *Input_Multi_Arg_Map_Class__c* class, specified by the output of its *getMap)* method, is present in the *inputData*. If any key is not present the invocation is flagged with a *Warning* but allowed to continue. Similarly for output we check that the *outputData( from each invocation class specified in *Output_Multi_Arg_Map_Class__c* if the *Check Map Output Field Validity* box is checked.

Any Developer Stubs or Invocation Overrides will be subject to the same checks, thus adding some assurance that moving from a stub to an *real* invocation will not create any key set mismatches.


### Configuration Validation for Services

If the invocation we wish to check is using a *Service Method* configuration then there is an additional guardrail that is performed as a configuration check and shows in the Configuration Dashboard. The  *Service Method* Metadata Type also has *Input_Multi_Arg_Map_Class__c* and *Output_Multi_Arg_Map_Class__c* fields. When an invocation has *Check Map Input Field Validity* set and a service configuration then there is a configuration check that *Input_Multi_Arg_Map_Class__c* on the Invocation and the Service Method are set to the same value. The same holds for the output configuration.

In all cases the team that writes the implementation is also responsible for ensuring the IMultiArgMap classes exist.



# Recommendations for Complex Input and Output

## Apex Implementations:
For invocations called and implemented in Apex we would always recommend using objects to encapsulate input and output over a Map<String,Object>. This gives compile-time validation that all the input and output parameters match up, with the dual benefits of 100% reliability and no need to check the input and output in each call.

## Flow:
Flow does not support the use of generic mechanisms using calls to Map<String,Object>.
Documented at: https://github.com/kevinhenryburke/frictionless/issues/352
Started example as Tab_Versions_5

### Industries:
This is one of the main use cases
Here it is possible and advantageous to do in a standard way.
The validation on the invocation side is useful - is this just a separate method call per invocation.

