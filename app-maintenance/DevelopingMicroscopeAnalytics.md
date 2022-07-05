
This page outlines how to work on the Microscope Analytics app. 

# Setting up Analytics Development

## Repeating Same Early Steps as Main Package

We start by performing the exact same steps as we do for working on the [Microscope and Demo Apps](DevelopingMicroscope.md) 

Create a branch when starting to work on something new, e.g. using the convention b*YYYYMMDD* (e.g. b20211219) 

Create new folder and open in VS Code, for example use the same folder name as the branch name.

Open a new terminal in VS Code and export a variable holding the branch name:

```
export MYBRANCH=b*YYYYMMDD*  (change as required for your build)
```


Next check out the correct branch into the folder with the following (don't forget to include the full-stop at the end of the second command!):

```
git clone https://github.com/kevinhenryburke/frictionless.git -b $MYBRANCH .
```


## Deviating from the Main Package

We close VS Code's window and Open instead at the subfolder addOns/Analytics

## Scratch org creation

For Analytics app we do not use a namespace, the reason being that this app is intended to be a starting point for companies rather than a supported app. So we can use any Dev Hub for our development. 

For scratch orgs we use the naming convention  *expYYYYMMDDa* with the date being the expiry date of the org and "a" appended to give us an easy way to recognise that the scratch org is being used for Analyitcs. The *createScratch.sh* script (see below) defaults to 30 days. 


We need to set an environment variable to the alias that is used by the CLI to interact with the Analytics default scratch org. Following the procedures again that we used for setting up a scratch org for developing *Microscope*, edit the file in the top level Analytics folder, *myVarSettings.sh* again, editing the line

```
export MYSCRATCH=exp20220122a (change as required)
```

And then, just as we did for creating a  in a terminal window within VSCode again run to update our environment:

```
source myVarSettings.sh
source envVarSettings.sh
```

Note that as for developing the *Microscope* app, *envVarSettings.sh* is maintained by package creation scripts, you don't need to change it, just run the *source* command.

Follow your normal DX process to authorize a DevHub. For Microscope Analytics app development we do not require a namespace so we can use any no-namespace Dev Hub.

```
./createScratch.sh
```

After running this, if the default scratch org is not set in the bottom bar of VS Code then click where it says "No Default Org Set" and set to be the alias you just created.

## Installing Dependencies - Microscope and Demo

We install the Microscope core package and the demo app into our scratch org. Although the latter is not strictly needed it does help to populate some data for the Analytics app.


```
cd scripts
./installMicroscope.sh
```

And once the package is insalled

```
./installDemo.sh
```

Note that if you wish to run invocations using the Microscope Demo App itself you may need to make the Security Settings change as documented in the [Microscope Demo Package Installation](../installation/InstallationDemo.md) for the LWC components to render properly. Also you need to run through the steps for 
[Microscope Demo Package Post Installation](../installation/InstallationDemoPost.md) to ensure that the correct permission sets and custom seetings are in place for the demo. 

## Pushing the code and permissions


Staying inside the *scripts* folder we next run 

```
./permissions.sh
```
This will add a permission set with API name __microscopeAnalyticsIntegration__ to the *CRM Analytics* 'Integration User'. 


Then push the *CRM Analytics* artefacts to the scratch org:

```
cd ..
sfdx force:source:push -u $MYSCRATCH
```




## Creating Demo data and Running the Analytics jobs


Within the Analytics scripts folder you can run the *./massPopulate.sh* script a few times. This will run about 400 invocations with a smattering of errors, spread acorss a number of dates for demoing reports and analytics. This is the same process we use in [Developing Microscope](../app-maintenance/DevelopingMicroscope.md)


Running import jobs and dataflows is the same as for the Analytics App post installation, as outlined in [Microscope Analytics Package Post Installation](../installation/InstallationAnalyticsPost.md)



## Checking all is ok


To check the packages installs are good, in the scratch org, go to the Apps “Service Framework Demo” and “Service Framework Dashboards” and run through the tabs.

In Analytics Studio run the dashboards "Service Connections", "Service Forensics", "Service Trends" and "Service Performance" after running the jobs (see section on running Analytics jobs above).


# Troubleshooting

There are some parts of the demo that require a user name to run and share dashboards and *CRM Analytics* dashboards. These should be replaced when pushing to a scratch org with the username of the deploying user (https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_push_md_to_scratch_org.htm). However sometimes we need to update the username in the codebase, ideally prior to pushing the code. In VS Code search for all occurrences of _@example.com_ . Each of these will represent a user name. In the VS Code terminal run

```
sfdx force:user:list
```
Replace all of the _@example.com_ in the code base with the Username retrieved by this query and save the files. These files do not need to be committed back to the repo.

# Deleting a scratch org


When you have finished you development it is good practice to delete you scratch org if it won't be used any more. Do so with this command, run at the top level of the VS Code structure:

```
./deleteScratch.sh
```
