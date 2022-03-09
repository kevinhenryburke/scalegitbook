## Setting up the loopback demo

The full integration demo requires 2 scratch orgs to be setup, a source (s) and a target (t).
The loopback integration is a quicker setup requiring just one scratch org. We'll describe the full 2 org scenario here, the loopback example can follow the same steps but just with a single org acting as both source (s) and target (t).

We'll suppose that we have the source and target orgs aleady set up with corresponding VS Code folders

Note that we'd prefer to use .forceignore rather than some of the scripts in what follows but the VS Code is a bit flaky at picking up changes in this file

__Step 1: Activate the integration related metadata__

In the Source root folder, go to demo/scripts and run the script ./copySourceIntegrationFiles.sh. This will create these files:

```
demo/force-app/authproviders/Framework_Loopback_AP.authprovider-meta.xml
demo/force-app/namedCredentials/Framework_Loopback_NC.namedCredential-meta.xml
```

In the Target root folder, go to demo/scripts and run the script ./copySourceIntegrationFiles.sh, creating this file

```
demo/force-app/connectedApps/Framework_Loopback_CA.connectedApp-meta.xml
```
The new files created are included in the .gitignore file so changes made to these will not be included in source tracking by default.

__Step 2: Create a unique consumer key and share across both orgs__

Create a unique string of between 10 and 20 characters. For example run the unix command "date +%s" in your terminal. This will be our consumer key.

In the s folder structure: Open the Framework_Loopback_AP.authprovider-meta.xml file and replace the consumer key value with in the file with our new consumer key value.

In the t folder structure: Open the Framework_Loopback_CA.connectedApp-meta.xml file and replace the consumer key value with in the file with our new consumer key value.

__Step 3: Update the callback URL__

In a terminal window in the s folder structure run "sfdx force:user:display -u $MYSCRATCH". Copy the value of "Instance URL"

Then in the t folder structure, open the file Framework_Loopback_CA.connectedApp-meta.xml and replace the string "https://sourceurlreplace.com" with the value of the source instance URL. Make sure not to overwrite the rest of the URL path (/services/authcallback/Framework_Loopback_AP), this should remain. 

In the t folder structure, push the changes to the org.

__Step 4: Update the callback URL__

In a terminal window in the t folder structure run "sfdx force:user:display -u $MYSCRATCH". Copy the value of "Instance URL"

In folder s replace the string "https://targeturlreplace.com" in the file Framework_Loopback_NC.namedCredential-meta.xml with this real target instance URL.

In the s folder structure, push the changes to the org.

__Step 5: Target Org__

Open the target org. In App Manager click View against "Framework_Loopback_CA". Select "Click to Reveal" against the consumer secret and copy the value to your clipboard.

__Step 6: Source Org__

Open the source org, open the Framework_Loopback_AP auth provider and paste the copied consumer secret from the previous step into the consumer secret field and save.

Edit the named credential Framework_Loopback_NC (don't make any changes, just click edit and then save). This will initiate a validation of the credential which will require logging in to the org using the target org's username / password and accepting OAuth scopes. (You can read these values from running "sfdx force:user:display -u $MYSCRATCH" in the t folder structure)

Finally on the "External Invocation" tab in the demo on the source org we should see data returned from the target org.


