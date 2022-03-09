

cd scripts/

./installDependencies.sh

You'll need to wait and run the suggested report till the package is installed.

Once it is it will give you a package version id "04t....". 

Check sfdx-project.json references this package version in dependencies

cd ..

sfdx force:source:push

Can run all the demo imports and settings that we set in the previous page too here.



## Creating Demo Package

We recommend doing all of the above to catch any errors

Then run

```
./createPackageVersion.sh
```










