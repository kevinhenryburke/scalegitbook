
# Linking Stories and Implementation Reality

A key part of the value of *Microscope*
 is that it aligns Design, Process and Build. Treating these as separate entities allows an information gap to grow between those defining and those delivering inside an org that results in a build-up of complexity that can become very hard to rectify. 
 
 Internally *Microscope* works towards creating a *Build Map*, a high-level view of the org that can be understood by everyone involved in designing, managing and delivering into an org. We introduced this concept in our discussion on [Connections, Process and Build](../solution/ConnectionsProcessBuild.md)

In order to facilitate and maintain this visbility *Microscope* puts a lot more work into the Storying process than is common in Salesforce programs. This allows the control of the overall structure of the build to lie with the Designers and Architects of the system rather than passing on this responsibility to development, environment and test teams. A number of details that are so often not specified are added to the process of creating a story to communicate how a new development should be constructed: it is extra work but it means that relationships within the org are no longer hidden in the code and tooling.

*Microscope* also encourages the use of versioning of artifacts and separation of concerns. This allows our stories to be more independent with fewer dependenciesa which reduces technical debt, allows for targeted testing and reduces the need for system-wide regression testing for every new and updated feature.



## Story Relationships

The key to building a useful and accurate *Build Map* is that Requirements and Design capture the relationships between different functional areas in the Org. We will use the concepts of Connection, Package and Business Process from [Visible Connections, Key Concepts](../solution/VisibleConnections.md) to show how to story our *Build Connections*.


To facilitate this, 
Stories should have a one : (zero, one or many) relationship with an object represents *Connections* in the issue tracking tool. A *Connection* in build terms is just an *Invocation*.

It is a decision for your teams to make as to how to configure this in whatever issue tracking tool you are using. The implementation detail is not what is important to us here, but the existence of the relationship is! We will call this subsidiary object a **Connection Sub-Story**.



## Stories and Tasks

Before we run through at a high-level the key items that need to be specified for a connection, we'll just say a word about task assignment. For some of these items *tasks* or *work items* or *issues* should also be created and when this is relevant these are included in the description of each element below. 

If the Issue Tracking system allows it then it would be very nice if this could be done via an automated process in the issue tracking software and the *tasks* added to the relevants teams backlogs. However these can be created manually per *Connection Sub-Story*.

Some rules of thumb for task assignments:

1. Every task should be assigned to the team owning the package where the task is implemented 
2. Metadata changes should be assigned to the Architects responsible for the packages. The architects should also be responsible for creating the folder structures that hold the invocation and service metadata and implementing classes to ensure consistency across the developments.
3. Class and flow implementations and stubs should be assigned to the Development team for the appropriate package. 
4. Environment changes should be assigned to the appropriate Environment Management team. 


## Connection Properties

An Connection Sub-Story should outline the following information depending on the properties of the story. These items will help to determine which of the subsections for invocation-side and service-side the story will also need to outline.

- Indicate if the connection is local or cross-package

- State if this is an upgrade or is a new connection
If it is an upgrade then we need to indicate if this a change of signature from the existing active versions

- IF this is a new development, is an *Event-Based model* appropriate? If the connection is asynchronous and represents a change in the system that may have multiple consumers, either now or in the foreseeable future, then definining it as a *Microsoft Event* and attaching the first invocation to that Event might have longer-term benefits. For more info, *Microscope Events* are outlined [here](../use-cases/Events.md).  

- Business Outcomes for each call (see [Business Outcomes](../getting-started/BusinessOutcomes.md))

- The Business Process that the invocation belongs to (again see [Business Outcomes](../getting-started/BusinessOutcomes.md))

- Error Codes: Any Errors that can be anticipated and need to be handled by a Connection should be defined in the story definition so that records can be created in the *Service_Error_Code__mdt* CMT. Details on what this entails can be found at [Error Raising](../getting-started/ErrorRaising.md). Note that the package reference for the Error Code should be the package where the error can be raised, so the *Invocation Package* for a local connection and the *Service Package* for a cross-package connection.


### Invocation Side

- Package Level: The invocation package. If this is a new package then all those details too, plus a task to create the Package metadata.

- Invocation side: All fields on the *Invocation CMT* Record, except those that are using a default value that is defined for the field. 



#### Invocation Side: Local Invocations Addtional Items

- The Explicit Implementation fields, *Explicit Implementation Class* and *Explicit Implementation Flow*, if populated, need to have a task for the Invocation Development Team to implement this.


#### Invocation Side: Cross-Package 

- The Developer Stub field, *Absent Service Stub Class*, if populated needs to have its functionality specified. A task needs to be created for the Invocation Development Team to implement this.


### Service Side: Cross-Package 

- Package Level: The service package. If this is a new package then all those details too, plus a task to create the Package metadata.

- Retired versions: if older versions of the service can be retired, we need to specify when these can be cleaned up, and tasks created for job. See the section on this page on *Removing Connections* for a list of what types of artifacts might be removed.

- Versions: new Business Iteration, new Technical Version

- All fields on Service-Side CMT Records, except those that are null or using a default value. If Service-side metadata is required a task to create these metadata items should be assigned to the Architect responsible for the Service Package
    
    If this is an updated version of the implementation, then we need a task assigned to the architects to create the base line for the new implementation. This might be copying over the folder structure for the previous version of the Apex Implementation and to rename these folders and classes)

###  Environment management

- Service Side: If this requiremnt connects to an external system that may differ across environments we need requirements about environment configuration and stubs on the path to production. A list of Custom Settings per environment is required, as is the definition of an Environment Stub methods required. 

    If environmental diffeences are required the connection should not be local and should be build using the Service model. A task needs to be added to create any new Service CMT that is required and assigned to the Architect for the Service team.

    A task for develop any Environment Stub methods. These should be assigned to the development team for the Invocation or Service Packages, depending on whether the Connection is local or not.

    Creation of any additional parameters (Service Method Static) that is required per environment, we should have one task per environment.

- Invocation Side: Pilot Custom Setting Overrides: If the feature is initially devleped as a pilot feature then the override information for the feature needs to be outlined.

## Removing Connections

Of course stories may also involve removal of existing connections. In these cases the story should mention all artifacts that can be removed from the build, and tasks be created for their removal. Removal artifacts may include:

- Custom Metdata Type Records representing Invocations, Service Methods, Error Codes, Service Implementations etc

- Apex Classes (including Test Classes) and Flows that implement invocations

- Custom Settings for Invcation Overrides, Environment Stubs etc.


