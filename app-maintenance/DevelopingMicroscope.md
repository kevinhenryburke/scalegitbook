# Developing the Scaleable Architecture Framework for Salesforce

This page outlines how to work on the Microscope, demo and the Analytics apps all in one scratch org instance. 

## Github access

This is a private repo. Repo info is below in clone command. You will need to authenticate to the repo with VS Studio.


## Branching Strategy

Create a branch when starting to work on something new. During the solo development phase I use the convention b*YYYYMMDD* (e.g. b20211219) for my branch names but these will need to feature specific in multi-developer mode


## Branching and getting code from repo to VS Code

Create new folder and open in VS Code. I give the folder the name of the branch I'm working on (e.g. b20211219)

At the top level of this folder is a file called *envVarSettings.sh*. This file contains a number of environment variables that allow us to build scratch orgs and packages.

To get the code we need for Microscope you should edit *envVarSettings.sh* as follows, so that the MYBRANCH fields matches your github branch name.


```
export MYBRANCH=b*YYYYMMDD*  (change as required for your build)
```


Open a new terminal in VS Code (via Terminal -> New Terminal) and run the following which checks out the correct branch into the folder (don't forget to include the full-stop at the end of the second command!):

```
source envVarSettings.sh
git clone https://github.com/kevinhenryburke/frictionless.git -b $MYBRANCH .
```

Check that the top level folder in VS Code (the folder representing your location on disk) contains the file *sfdx-project.json* , if so we're should be all good

If SFDX commands like push/pull are not available for you in the VSCode command palette, close the folder in VSCode and reopen it. This should now recognize that we have an SFDX project. However you will need to run "source envVarSettings.sh" again.

## Scratch org creation

For scratch orgs we use the naming convention  *expYYYYMMDD* with the date being the expiry date of the org. The *createScratch.sh* script (see below) defaults to 30 days. 

We need to set an environment variable to the alias that is used by the CLI to interact with the default scratch org. Edit the file  *envVarSettings.sh* again, this time editing the line


```
export MYSCRATCH=exp20220122 (change as required)
```

And then in a terminal window within VSCode again run to update our environment:

```
source envVarSettings.sh
```


Follow your normal DX process to authorize a DevHub. You can then create a default scratch org using these lines:

```
./deleteScratch.sh (if you need to delete a scratch org with the same name)
./createScratch.sh
```
*createScratch.sh* actually does a few more things than just create a scratch org, look in the file for the details. After running this, if the default scratch org is not set in the bottom bar of VS Code then click where it says "No Default Org Set" and set to be the alias you just created.


## Pushing the code and permissions


Push the code to the scratch org, this will push both the serviceBase, addOns (including Tableau CRM) and the demo. Assuming we are still in the top level folder run:

```
sfdx force:source:push -u $MYSCRATCH
```

We need to assign a permission set and create a small bit of demo data. 

```
cd demo/scripts
./permissions.sh
./imports.sh
```

Within the demo/scripts folder you can also run the *./populate.sh* script a few times. This will run 17 invocations each time and provide data for reports and dashboards. A small number of these invocation should fail to include some errors too. Another script, *./randomize.sh* , will spread out the times of some audit records that will give a better spread of audit times for demoing analytics. 

To do this quickly just run this:

```
./populate.sh;./populate.sh;./populate.sh;./populate.sh;./populate.sh;./populate.sh;./populate.sh;./populate.sh;./populate.sh;./populate.sh;./randomize.sh;./randomize.sh;./randomize.sh
```

## Checking all is ok

Run all the Apex unit tests in VS Code.

In the scratch org, go to the Apps “Service Framework Demo” and “Service Framework Dashboards” and run through the tabs.

In Analytics Studio run the dashboards "Service Connections", "Service Forensics" and "Service Performance" after running the jobs (see "Jobs to run" below)




## Creating Demo Package

We recommend doing all of the above to catch any errors

Then run from the *serviceBase* folder run

```
./createPackageVersion.sh
```

As this package has no dependencies the *sfdx-package.json* does not need to be updated. However there is one update we need to perform to allow packages we are layering on top of this to build. We head back to the top folder and *envVarSettings.sh*. This time we use the package version id (starting "04t...") and put this value against the MSCOPE_PACKAGE_VERSION_ID variable.




## Finessing a demo

Search for App Menu in Setup and move the Services Framework applications (e.g. Demo and Dashboards) to the top of the app list to allow quick access in the demo. This cannot at present be controlled by any setting available to VS Code to automate.

## Analytics App - Jobs to run


In the Scratch org, open Analytics Studio, go to Data Manager -> Connect and "Run Data Sync / Run Full Sync" for each of the Salesforce objects that are listed. Check the Monitor option to see when these have successfully run.

Next go to "Dataflows & Recipes" and select "Run Now" on the "Services Framework Dataflow" and again check the Monitor screen to see when this has run successfully. You may of course choose to schedule this to run periodically in your scratch instance too.

Finally check the "Services Performance", "Services Forensics" and "Service Connections" dashboards which should now be populated with data.


Note that the Analytics app contains metadata for the "Analytics Cloud Integration Profile". Ideally we would just want this to pushed via permission set. However as the integration jobs for Tableau CRM cannot be deployed without permissions for the Salesforce Objects and Fields, and there is no way to use a post deploy script for a package that is not managed, we would need to split the deploy and make deployments more complicated. When the Analytics app is eventually productionized this profile should not be included and permission sets used to permission the integration user.

# Troubleshooting

There are some parts of the demo that require a user name to run and share dashboards and Tableau CRM dashboards. These should be replaced when pushing to a scratch org with the username of the deploying user (https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_push_md_to_scratch_org.htm). However sometimes we need to update the username in the codebase, ideally prior to pushing the code. In VS Code search for all occurrences of _@example.com_ . Each of these will represent a user name. In the VS Code terminal run

```
sfdx force:user:list
```
Replace all of the _@example.com_ in the code base with the Username retrieved by this query and save the files. These files do not need to be committed back to the repo.

# Code Quality

We can use the PMD and CPD code quality tools to check the core build. We run this through the Salesforce CLI Scanner plugin in VS Code (https://forcedotcom.github.io/sfdx-scanner/) rather than directly

- Install the Salesforce CLI Scanner plugin
- in the VS Code terminal move to the *scripts* folder at the top of the project folder and for PMD run

```
./runPMD.sh 
```

- And for CPD run

```
./runCPD.sh 
```

- The results are output to the subfolder *.toolout* of the top folder of the project and the results are published to the files *scaleablepmd.csv* and *scaleablecpd.txt* respectively.










