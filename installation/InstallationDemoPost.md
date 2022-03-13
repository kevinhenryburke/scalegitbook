

## 1. Microscope Demo Post Installation Checks

### 1. Setup the Demo


* If this is a new org then add some contacts, some of the demo components query the Contact object.

* Create a custom permission called Demo_Custom_Permission and add it to the permission set called demoCustomPermissions. 

* Open an execute anonymous session via the Developer Console and execute the script below (Post-install scripts are not allowed in unlocked packages so needs to be run manually).
) 

```
mscopedemo.NewOrgDataLoad.loadCustomSetting();
```

### Finessing

Search for App Menu in Setup and move the Microscope applications (Microscope Demo and Microscope Live) to the top of the app list to allow quick access in the demo. 

You may also want to run through all of the tabs (we will run through what each of these does), check things are working. This has the added benefit of creating some data that will be useful for demonstrating the Analytics App.
