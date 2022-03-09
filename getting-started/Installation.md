

### 1. Microscope Package Installation

[Microscope Package installation URL](https://login.salesforce.com/packaging/installPackage.apexp?p0=04t8d000000DXWKAA4)

The package is called **Microscope** and uses the namespace **mscope**

TODO Recommend to install for Administrators only.

TODO Sharing of the reports / dashboards folders

TODO need to add a cache allocation to FrameworkCache Platform of at least 1MB to get the performance benefit of platform cache
Dev Edition orgs have no cache allocation so this is not going to be usable in those orgs. The app still functions however but is slightly slower as a result.



### 2. Microscope Demo Package Installation

There is also a package provided to help teams to learn the Microsope application. This should never be installed in a production org but used in disposable scratch, developer edition and dev sandboxes where people can experiment with the app.

For the full demo experience make sure you are using an org which includes Tableau CRM. This might be in a customer sandbox or you can sign up for Tableau CRM enabled org [here](https://trailhead.salesforce.com/en/promo/orgs/analytics-de) at time of writing.



[Microscope Demo Package installation URL](https://login.salesforce.com/packaging/installPackage.apexp?p0=04t8d000000DXefAAG)

The package is called **Microscope Demo** and also uses the namespace **mscopedemo**


TODO: Adding a Platform Cache Partitions
Org Cache Allocation / Organization
1MB
Dev Edition orgs have no cache allocation
scratch org has a partition but it is namespaced so need to work around that


TODO: Add some contacts

TODO: create a custom permission called Demo_Custom_Permission and add it to the permission set called demoCustomPermissions

TODO: Add that need to use the new security architecture or get an error on the pages with lightning components that contain references to components from the mscope package

Can make it work by enabling the option: Session Settings -> Use Lightning Web Security for Lightning web components

TODO: Add that need to run NewOrgDataLoad.loadCustomSetting(); in an execute anonymous session. Post-install scripts are not allowed in unlocked packages so needs to be run manually.
### 2. Microscope Analytics Package Installation

Required Give Integration user FLS on all fields in Audit

[Microscope Analytics Package installation URL](https://login.salesforce.com/packaging/installPackage.apexp?p0=04tB0000000dnKBIAY)

