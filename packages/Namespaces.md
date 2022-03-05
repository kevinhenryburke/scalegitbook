### Namespaces

It is important to understand the use of Namespaces in Microscope.

## Developer provides namespace explicitly

Input and Output Definitions on both the Invocation and Service sides - you need to provide your own namespace explicitly for these, or leave blank if you are using a Platform artefact or your package has no namespace. The rationale is that input and output structures cross package boundaries so both invocation and service packages need the ability to process input and output from other packages.

The same reasoning applies to Platform Events used by this framework, these span packages and so the developer is free to provide an explicit namespace.

## Implicitly use the namespace from the hosting package


Configured processing classes must always have the same namespace as the package within which they are declared. This keeps control of these classes with the teams that own the packages and prevents any updates taking the package-owning team by surprise.

### Invocation Side: these use the invocation package namespace

Invocation -> Stub Implementation Class
Invocation -> Explicit Implementation Class
Invocation -> Explicit Implementation Flow
Invocation -> Input Audit Override Class
Invocation -> Output Audit Override Class

### Service Side: these use the service package namespace

Service Method -> Down Service Method
Service Method -> Stub Service Method
Service Implementation -> Implementing Class
Service Implementation -> Implementing Flow


### Technical Notes

When flows are used to implement services they are launched from a class *ServiceFlowImplementation* within the *mscope* namespace. Behind the scenes *mscope* is used to call the launching class but the flow must be associated with the invocation or service package namespace, whichever is relevant. 
