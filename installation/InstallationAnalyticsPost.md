## Analytics App - add permissions to the Integration User

To allow the import data jobs to run we need to add the Permission Set __microscopeAnalyticsIntegration__ to the Tableau CRM 'Integration User'. This can be scripted of course if teams prefer that.



## Analytics App - Jobs to run

In the org, open Analytics Studio, go to Data Manager -> Connections and select "Run Data Sync / Run Full Sync" in the drop down on the far right of the screen for each of the Salesforce objects that are listed. Check the Monitor option to see when these have successfully run.

Dataflows are being deprecated by Salesforce and *Microscope* is yet to move to using Recipes. To access the Dataflow, click on "Manage Dataflows" in the bottom left corner of the Jobs Monitor Screen, This takes you into the old "Data Manager" screen, click on "Dataflows & Recipes* and select "Run Now" on the "Services Framework Dataflow" and again check the Monitor screen to see when this has run successfully. You may of course choose to schedule this to run periodically in your scratch instance too.

Finally check the "Services Performance", "Services Forensics" and "Service Connections" dashboards which should now be populated with data.
