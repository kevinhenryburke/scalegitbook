# Packaging Microscope and its related applications

## Building the Microscope Package

As a starting position make sure you have a scratch org created and that any changes are fully tested and that Apex unit tests are written and passing. Instructions on creating branches and scratch orgs for Microscope are [here](/app-maintenance/DevelopingMicroscope.md)

From the top level folder of the VS project, open a terminal and run

```
cd serviceBase
./createPackageVersion.sh
```

*createPackageVersion.sh* does a bit more than just create a new package version, look at the script for the details. However when it completes successfully you will be presented with a new Subscriber Package Version Id. We need to update a few things with this id.

Firstly use it as the argument to *updatePackageVersionRefs* which lives in the same folder we used to create the package.

```
./updatePackageVersionRefs.sh <new id>
```
This script updates file references in the build structure to point to the new package version.

Finally, if this new package version is to become the new installable baseline for the build, in the documentation project go to Installation.md and in the section "Microscope Package Installation" update the *Microscope Package installation URL* to point to this new Package Version Id.

## Building the Microscope Demo Package

The demo application can be developed in tandem with the core app using the common scratch org. To create the demo package, starting at the top of the folder structure in VS Code, open a terminal and run:

```
cd demo
./createPackageVersion.sh
```


The *createPackageVersion.sh* script performs a few extra steps, check the script to see these. Most of these involve making changes to namespaces from the scratch org code prior to building the package version and then reverting those changes back after the package is built.

When that script completes you will be presented with a new Subscriber Package Version Id for the demo app. As for the main Microscope app we need to do a couple of things with this id

Firstly use it as the argument to *updatePackageVersionRefs* which lives in the same folder we used to create the package.

```
./updatePackageVersionRefs.sh <new id>
```
This script updates file references in the build structure to point to the new package version.

If this new package version is to become the new installable demo, in the documentation project go to Installation.md and in the section "Microscope Demo Package Installation" update the *Microscope Demo Package installation URL* to point to this new Package Version Id.

## Building the Microscope Analytics Package

TODO