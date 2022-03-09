

## 1. Demo Setup


TODO: Add some contacts

TODO: create a custom permission called Demo_Custom_Permission and add it to the permission set called demoCustomPermissions

TODO: Add that need to run NewOrgDataLoad.loadCustomSetting(); in an execute anonymous session. Post-install scripts are not allowed in unlocked packages so needs to be run manually.

## Finessing a demo

Search for App Menu in Setup and move the Services Framework applications (e.g. Demo and Dashboards) to the top of the app list to allow quick access in the demo. This cannot at present be controlled by any setting available to VS Code to automate.

You may also want to run through all of the tabs (we will run through what each of these does), check things are working and create some data that will be useful for demonstrating the Analytics App.


## Analytics App - Jobs to run

In the org, open Analytics Studio, go to Data Manager -> Connect and "Run Data Sync / Run Full Sync" for each of the Salesforce objects that are listed. Check the Monitor option to see when these have successfully run.

Next go to "Dataflows & Recipes" and select "Run Now" on the "Services Framework Dataflow" and again check the Monitor screen to see when this has run successfully. You may of course choose to schedule this to run periodically in your scratch instance too.

Finally check the "Services Performance", "Services Forensics" and "Service Connections" dashboards which should now be populated with data.


Note that the Analytics app contains metadata for the "Analytics Cloud Integration Profile". Ideally we would just want this to pushed via permission set. However as the integration jobs for Tableau CRM cannot be deployed without permissions for the Salesforce Objects and Fields, and there is no way to use a post deploy script for a package that is not managed, we would need to split the deploy and make deployments more complicated. When the Analytics app is eventually productionized this profile should not be included and permission sets used to permission the integration user.



