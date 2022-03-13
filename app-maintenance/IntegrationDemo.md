## Setting up the integration and loopback demo

The full integration demo requires 2 scratch orgs to be setup, a local org and a remote org.
The loopback integration is a quicker setup requiring just one  org. We'll describe the full 2 org scenario here, the loopback example can follow the same steps but just with a single org acting as both local and remote.

We'll suppose that we have the local and remote orgs aleady set up with Microscope's main package and demo package both installed.

__Step 1: Authorize Local and Remote Orgs in VS Code__

In VS Code open 2 new windows. Open one windwo at the folder /environments/PermademoLocal (this the local/source org for our intergration) and the other at /environments/PermademoRemote (our remote/target org.)

Authorize Local and Remote Orgs in the respectively VS Code window.

Note that the PermademoLocal folder has these files:

```
force-app/authproviders/Framework_Loopback_AP.authprovider-meta.xml
force-app/namedCredentials/Framework_Loopback_NC.namedCredential-meta.xml
```

The PermademoRemote folder has this file:

```
force-app/connectedApps/Framework_Loopback_CA.connectedApp-meta.xml
```
These files created are included in the .gitignore file so changes made to these will not be included in source tracking by default.

__Step 2: Create a unique consumer key and share across both orgs__

Create a unique string of between 10 and 20 characters. For example run the unix command "date +%s" in your terminal. This will be our consumer key.

In the local folder structure: Open the Framework_Loopback_AP.authprovider-meta.xml file and replace the consumer key value with our new consumer key value.

In the remote folder structure: Open the Framework_Loopback_CA.connectedApp-meta.xml file and replace the consumer key value in the file with our new consumer key value.

__Step 3: Update the callback URL__

In a terminal window in the local folder structure run "sfdx force:user:display". Copy the value of "Instance URL"

Then in the remote folder structure, open the file Framework_Loopback_CA.connectedApp-meta.xml and replace the string "https://sourceurlreplace.com" with the value of the local instance URL. Make sure not to overwrite the rest of the URL path (/services/authcallback/Framework_Loopback_AP), this should remain. 

In the remote folder structure right click on the file Framework_Loopback_CA.connectedApp-meta.xml and select "SFDX: Deploy Source to Org".

__Step 4: Update the callback URL__

In a terminal window in the remote folder structure run "sfdx force:user:display". Copy the value of "Instance URL"

In the local folder replace the string "https://targeturlreplace.com" in the file Framework_Loopback_NC.namedCredential-meta.xml with this real remote instance URL.

In the local folder structure, push the changes to the org by selecting both the named credential and authentication provider files individually in VS Code and deploy source to org as we did above for the remote org.

__Step 5: Remote Org__

Open the remote org. In App Manager click View against "Framework_Loopback_CA". Select "Click to Reveal" against the consumer secret and copy the value to your clipboard.

__Step 6: Source Org__

Open the local org, click edit on the Framework_Loopback_AP auth provider and paste the copied consumer secret from the previous step into the consumer secret field and save.

Edit the named credential Framework_Loopback_NC (don't make any changes, just click edit and then save). This will initiate a validation of the credential which will require logging in to the org using the remote org's username / password and accepting OAuth scopes. (You can read these values from running "sfdx force:user:display -u $MYSCRATCH" in the remote folder structure)

Finally on the "External Invocation" tab in the demo on the local org we should see data returned from the remote org.


