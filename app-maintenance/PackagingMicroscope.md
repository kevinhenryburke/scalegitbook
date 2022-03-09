# Packaging Microscope and its related applications

## Building the Microscope Package

As a starting position make sure you have a scratch org created and that any changes are fully tested and that Apex unit tests are written and passing. Instructions on creating branches and scratch orgs for Microscope are [here](/app-maintenance/DevelopingMicroscope.md)

From the top level folder of the VS project, open a terminal and run

```
cd serviceBase
./createPackageVersion.sh
```

*createPackageVersion.sh* does a bit more than just create a new package version, look at the script for the details. However when it completes you will be presented with a new Subscriber Package Version Id. We need to update a few things with this id:

1. Go to the top level folder and the file  *envVarSettings.sh*. Edit this file, putting the new Package Version Id to be the value of the variable MSCOPE_PACKAGE_VERSION_ID
2. Go to the folder /demo and open the *sfdx-project.json* file. In the packageDirectories / Dependencies section update the package referenced to be this new Subscriber Package Version Id.
3. Similarly, from the top folder navigate to addOns/Analytics and in the *sfdx-project.json* file edit the dependency package to point to this new Subscriber Package Version Id.
4. Finally, if this new package version is to become the new installable baseline for the build, in the documentation project go to Installation.md and in the section "Microscope Package Installation" update the *Microscope Package installation URL* to point to this new Package Version Id.

## Building the Microscope Demo Package

The demo application can be developed in tandem with the core app using the common scratch org. However when building the package we will make a couple of tweaks to the process in [Developing Microscope](/app-maintenance/DevelopingMicroscope.md).

1. Check out your development branch to a new folder structure. 
2. Instead of opening VS code at the top of the folder structure, go down a level and open VS code at the *demo* folder.
3. Run ./createPackageVersion.sh 

When that script completes you will be presented with a new Subscriber Package Version Id for the demo app. As for the main Microscope app we need to do a couple of things with this id

1. At the top level of our demo project there is another *envVarSettings.sh* which in this case is specific to the demo package. Edit this file, putting the new Package Version Id to be the value of the variable MSCOPE_DEMO_PACKAGE_VERSION_ID
4. If this new package version is to become the new installable demo, in the documentation project go to Installation.md and in the section "Microscope Demo Package Installation" update the *Microscope Demo Package installation URL* to point to this new Package Version Id.

## Building the Microscope Analytics Package

TODO