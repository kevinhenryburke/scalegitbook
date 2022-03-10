

## 1. Microscope Demo Post Installation Checks

### 1. Setup


TODO: Add some contacts

TODO: create a custom permission called Demo_Custom_Permission and add it to the permission set called demoCustomPermissions

TODO: Add that need to run NewOrgDataLoad.loadCustomSetting(); in an execute anonymous session. Post-install scripts are not allowed in unlocked packages so needs to be run manually.

### Finessing

Search for App Menu in Setup and move the Services Framework applications (e.g. Demo and Dashboards) to the top of the app list to allow quick access in the demo. This cannot at present be controlled by any setting available to VS Code to automate.

You may also want to run through all of the tabs (we will run through what each of these does), check things are working and create some data that will be useful for demonstrating the Analytics App.
