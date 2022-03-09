

## 1. Microscope Package Installation

The main package is called **Microscope** and uses the namespace **mscope**. The latest recommended version can be installed via this link:

[Microscope Package installation URL](https://login.salesforce.com/packaging/installPackage.apexp?p0=04t8d000000DXWKAA4)



### Recommendations

We recommend that you install the app for Administrators only. In normal circumstances the objects or reports are needed for administrative users only and not by end users of the org. Additional sharing of reports and objects might be needed for business analysis and in the case can be shared to other non-admin users using the standard platform folder and object sharing techniques (e.g. permission sets and groups).

One important consider post-install involves *Platform Cache*. A cache partition called *mscope.FrameworkCache* is packaging with the app but no space is allocated to it (doing so would cause installation to fail in orgs without platform cache available.) After installation, if you have platform cache in the org the allocated at least 1MB or Org Cache to get the performance benefits of using the Cache. Developer Edition orgs for example have no cache allocation so this is not going to be usable in those orgs. The app still functions however but is slightly slower as a result.

## 2. Microscope Demo Package Installation

There is also a package provided to help teams to learn the Microsope application. 

[Microscope Demo Package installation URL](https://login.salesforce.com/packaging/installPackage.apexp?p0=04t8d000000DYKbAAO)


{% hint style="danger" %}
**This should never be installed in a production org** but used in disposable scratch, developer edition and dev sandboxes where people can experiment with the app.

For the full demo experience make sure you are using an org which includes Tableau CRM. This might be in a customer sandbox or you can sign up for Tableau CRM enabled org [here](https://trailhead.salesforce.com/en/promo/orgs/analytics-de) at time of writing.
{% endhint %}






The package is called **Microscope Demo** and also uses the namespace **mscopedemo**

This package contains demo components for Microscope and the **Microscope Demo** app.

### Recommendations

The Demo pages will throw errors without a change to Session Settings. We need to enable the option: Session Settings -> Use Lightning Web Security for Lightning web components. This setting enables a newer security architecture which, amongst other things, allows LWC components to host other LWC components from different namespaces without wrapping them in Aura components.

As for the main Microscope package, the demo package comes with a platform cache partition, this one is called *mscopedemo.FrameworkCache*. Allocate Org Cache to this to get the full performance of the demo calls (1MB should be enough).  

## 3. Microscope Analytics Package Installation

Before installing the Microsope Analytics Package we need to add some permissions to the "Analytics Cloud Integration Profile". The data integration jobs for Tableau CRM cannot be deployed unless the permissions for the accessed Salesforce Objects and Fields are already in place. The same result could also be achieved by specifying the relevant FLS in a permission set that we assign to the integration user.

The Analytics Cloud Integration User needs to have read permissions on all fields on the *Service Framework Audit* object, which is a part of the Microscope main package.

Once this is done we can install the package:

[Microscope Analytics Package installation URL](https://login.salesforce.com/packaging/installPackage.apexp?p0=04tB0000000dnKBIAY)



