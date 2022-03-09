

### 1. Microscope Package Installation

[Microscope Package installation URL](https://login.salesforce.com/packaging/installPackage.apexp?p0=04t8d000000DXWKAA4)

The package is called **Microscope** and uses the namespace **mscope**

We recommend that you install the app for Administrators only. In normal circumstances the objects or reports are needed for administrative users only and not by end users of the org. Additional sharing of reports and objects might be needed for business analysis and in the case can be shared to other non-admin users using the standard platform folder and object sharing techniques (e.g. permission sets and groups).

One important consider post-install involves *Platform Cache*. A cache partition called *mscope.FrameworkCache* is packaging with the app but no space is allocated to it (doing so would cause installation to fail in orgs without platform cache available.) After installation, if you have platform cache in the org the allocated at least 1MB or Org Cache to get the performance benefits of using the Cache. Developer Edition orgs for example have no cache allocation so this is not going to be usable in those orgs. The app still functions however but is slightly slower as a result.

### 2. Microscope Demo Package Installation

There is also a package provided to help teams to learn the Microsope application. 

{% hint style="danger" %}
**This should never be installed in a production org** but used in disposable scratch, developer edition and dev sandboxes where people can experiment with the app.

For the full demo experience make sure you are using an org which includes Tableau CRM. This might be in a customer sandbox or you can sign up for Tableau CRM enabled org [here](https://trailhead.salesforce.com/en/promo/orgs/analytics-de) at time of writing.
{% endhint %}



[Microscope Demo Package installation URL](https://login.salesforce.com/packaging/installPackage.apexp?p0=04t8d000000DYKbAAO)



The package is called **Microscope Demo** and also uses the namespace **mscopedemo**

As for the main package, the demo org comes with a platform cache partition, this one is called *mscopedemo.FrameworkCache*. Allocate Org Cache to this to get the full performance of the demo calls (1MB should be enough).  

TODO: Add some contacts

TODO: create a custom permission called Demo_Custom_Permission and add it to the permission set called demoCustomPermissions

TODO: Add that need to use the new security architecture or get an error on the pages with lightning components that contain references to components from the mscope package

Can make it work by enabling the option: Session Settings -> Use Lightning Web Security for Lightning web components

TODO: Add that need to run NewOrgDataLoad.loadCustomSetting(); in an execute anonymous session. Post-install scripts are not allowed in unlocked packages so needs to be run manually.
### 2. Microscope Analytics Package Installation

Required Give Integration user FLS on all fields in Audit

[Microscope Analytics Package installation URL](https://login.salesforce.com/packaging/installPackage.apexp?p0=04tB0000000dnKBIAY)

