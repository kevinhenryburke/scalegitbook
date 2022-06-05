
# Linking Stories and Implementation Reality

A key part of the value of *Microscope*
 is that it aligns Design, Process and Build. Treating these as separate entities allows an information gap to grow between those defining and those delivering inside an org that results in a build-up of complexity that can become very hard to rectify. Inside *Microscope* works towards creating a *Build Map*, a high-level view of the org that can be understood by all people involved in designing, managing and delivering into an org. We introduced this concept in our discussion on [Connections, Process and Build](../solution/ConnectionsProcessBuild.md)

In order to facilitate and maintain this visbility *Microscope* puts a lot more work into the Storying process than is common in Salesforce programs. This allows control of the overall structure of the build to lie with the Designers and Architects of the system rather than passing on this responsibility to development, environment and test teams. We add a number of details to the process of creating a story to communicate how a new development should be constructed - it is extra work but it means that relationships within the org are no longer hidden in the code and tooling.

*Microscope* also encourages the use of versioning of artifacts and separation of concerns. This allows our stories to be more independent with fewer dependenciesa which reduces technical debt, allows for targeted testing and reduces the need for system-wide regression testing for every new and updated feature.



## Story Relationships

The key to building a useful and accurate *Build Map* is that Requirements and Design capture the relationships between different functional areas in the Org. We will use the concepts of Connection, Package and Business Process from [Visible Connections, Key Concepts](../solution/VisibleConnections.md) to show how to story our Build Connections.


To facilitate this, 
Stories should have a one : (zero or many) relationship with an object in the issue tracking tool that represents **Connections**.

How to configure this in whatever issue tracking tool you are using is decision for your teams to make. The implementation detail is not what is important to us here, but the existence of the relation is! We will call this subsidiary object a **Connection Sub-Story**.



## Connection Sub-Story Details and Tasks


A Connection Sub-Story should contain the following information. In addition tasks should also be created. These are included in the description of each element. IF the Issue Tracking system allows it then it would be very nice if this could be done via an automated process in the issue tracking software, however these can be created manually per Connection Sub-Story.

- Indicate if the connection is local or not

- Package Level: The invocation and service packages. 

    (Tasks: If either is a new package then all those details too).

- The Build Process that the invocation belongs to.

- Connection Level: Stories should state if this is an upgrade or is a new connection
If it is an upgrade then need to indicate if this a change of signature from the existing active versions?

- Invocation side: All fields on invocation CMT Record, except those that are using a default value that is defined for the field. 

    (Tasks: Changes should be assigned to the Architect responsible for the Invocation Package).


- Service side: All fields on Service-Side CMT Records, except those that are using a default value that is defined for the field. 

    (Tasks: if Service-side metadata is required a task to create these metadata items should be assigned to the Architect responsible for the Service Package
    
    If this is an updated version of the implementation,then we need a task to create the base line for the new implementation. This might be copying over the folder structure for the previous version of the Apex Implementation and to rename these folders and classes)

- Environment management: If this is not a local connection then we may need to have requirements about environment configuration and stubs in environments on the path to production. A list of Custom Settings per environment is required, as is the definition of an Environment Stub methods required. 
    
    (Tasks: A task for develop and Environment Stub methods. These should be assigned to the development team for the Invocation or Service Packages, depending on whether the Connection is local or not.

    Creation of any additional parameters (Service Method Static) that is required per environment, we should have one task per environment.
)

- Invocation Side: Pilot Custom Setting Overrides: If the feature is initially devleped as a pilot feature then the override information for the feature needs to be outlined.




- Invocation Side: The Developer Stub field: Stub Implementation Class (if required) if populated needs to have its functionality specified 

    (Task: ifa Developer Stub is required then a taks needs to be created for the Invocation Development Team to implement this)
Difference is not service side)


