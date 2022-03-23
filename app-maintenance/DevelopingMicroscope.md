
This page outlines how to work on the Microscope and its Demo apps. 

## Github access

The source for these apps is in a private repo on [Github](https://github.com/kevinhenryburke/frictionless). Repo info is below in clone command. You will need to authenticate to the repo within VS Studio to develop Microscope.
## Branching Strategy

Create a branch when starting to work on something new. During the solo development phase I use the convention b*YYYYMMDD* (e.g. b20211219) for my branch names but these will need to feature specific in multi-developer mode
## Branching and getting code from repo to VS Code

Create new folder and open in VS Code. I give the folder the name of the branch I'm working on (e.g. b20211219)

Open a new terminal in VS Code (via Terminal -> New Terminal) and export a variable holding the branch name:

```
export MYBRANCH=b*YYYYMMDD*  (change as required for your build)
```


Next check out the correct branch into the folder with the following (don't forget to include the full-stop at the end of the second command!):

```
git clone https://github.com/kevinhenryburke/frictionless.git -b $MYBRANCH .
```

Once this has run, the top level of this folder is a file called *envVarSettings.sh*. This file contains a number of environment variables that allow us to build scratch orgs and packages.



Check that the top level folder in VS Code (the folder representing your location on disk) contains the file *sfdx-project.json* , if so we're should be all good

If SFDX commands like push/pull are not available for you in the VSCode command palette, close the folder in VSCode and reopen it. This should now recognize that we have an SFDX project. However you will need to run the export MYBRANCH variable command again.
## Scratch org creation

For scratch orgs we use the naming convention  *expYYYYMMDD* with the date being the expiry date of the org. The *createScratch.sh* script (see below) defaults to 30 days. 

We need to set an environment variable to the alias that is used by the CLI to interact with the default scratch org. Edit the file  *envVarSettings.sh* again, editing the line

```
export MYSCRATCH=exp20220122 (change as required)
```

And then in a terminal window within VSCode again run to update our environment:

```
source envVarSettings.sh
```

Follow your normal DX process to authorize a DevHub. For Microscope the DevHub is accessed via the login *mscope@devhub.org*. You can then create a default scratch org:

```
sfdx config:set defaultusername=$MYSCRATCH
./createScratch.sh
```
*createScratch.sh* actually does a few more things than just create a scratch org, look in the file for the details. After running this, if the default scratch org is not set in the bottom bar of VS Code then click where it says "No Default Org Set" and set to be the alias you just created.
## Pushing the code and permissions

Push the code to the scratch org, this will push both the serviceBase and the demo. Assuming we are still in the top level folder run:

```
sfdx force:source:push -u $MYSCRATCH
```

We need to assign some permissions and create a small bit of demo data. 

```
cd demo/scripts
./permissions.sh
./imports.sh
```

Within the demo/scripts folder you can also run the *./massPopulate.sh* script a few times. This will run 17 invocations via that *populate.sh* script and perform 3 runs of *./randomize.sh* to spread out the times of some audit records for demoing analytics. 



## Checking all is ok

Run all the Apex unit tests in VS Code.

In the scratch org, go to the Apps “Service Framework Demo” and “Service Framework Dashboards” and run through the tabs.

In Analytics Studio run the dashboards "Service Connections", "Service Forensics" and "Service Performance" after running the jobs (see "Jobs to run" below)


# Troubleshooting

There are some parts of the demo that require a user name to run and share dashboards and Tableau CRM dashboards. These should be replaced when pushing to a scratch org with the username of the deploying user (https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_push_md_to_scratch_org.htm). However sometimes we need to update the username in the codebase, ideally prior to pushing the code. In VS Code search for all occurrences of _@example.com_ . Each of these will represent a user name. In the VS Code terminal run

```
sfdx force:user:list
```
Replace all of the _@example.com_ in the code base with the Username retrieved by this query and save the files. These files do not need to be committed back to the repo.

# Deleting a scratch org


When you have finished you development it is good practice to delete you scratch org if it won't be used any more. Do so with this command, run at the top level of the VS Code structure:

```
./deleteScratch.sh
```

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










