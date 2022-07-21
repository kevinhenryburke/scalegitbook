# Environment Management - Handling Environmental Differenes

Environmental differences, caused by different integrations, data availability or a variety of other reasons, is an unavoidable fact of Enterprise development. Very often in large-scale implementations of Salesforce these differences are handled by opaque script or managed by code switches that are not visible or obvious to anyone.

Visibility and transparency are key to everything in *Microscope* and in our approach we provide a clear view of these variations: where they occur, what values any differences have been assigned in a particular environment and a clear story process delineating the roles and responsibilities for each team in the process. Any user has a clear list of services that are changed per environment.


### Remembering A Golden Rule - Custom Metadata vs Custom Settings

Our discussion on [Metadata and Settings](../vision/CMTCustomSettings.md) we introduced some golden rules and one in particular: Custom Metadata Type records are the same in all test environments and Custom Settings are only used when environmental differences occur. It is worth refreshing your knowledge of these principles as it will be very relevant here.


