


## 2. Microscope Demo Package Installation

There is also a package provided to help teams to learn the Microsope application. 

[Microscope Demo Package installation URL](https://login.salesforce.com/packaging/installPackage.apexp?p0=04t8d000000DYKbAAO)


{% hint style="danger" %}
**This should never be installed in a production org** but used in disposable scratch, developer edition and dev sandboxes where people can experiment with the app.

For the full demo experience make sure you are using an org which includes Tableau CRM. This might be in a customer sandbox or you can sign up for Tableau CRM enabled org [here](https://trailhead.salesforce.com/en/promo/orgs/analytics-de) at time of writing.
{% endhint %}

The package is called **Microscope Demo** and uses the namespace **mscopedemo**

This package contains demo components for Microscope and the **Microscope Demo** app.

### Recommendations

The Demo pages will throw errors without a change to Session Settings. We need to enable the option: Session Settings -> Use Lightning Web Security for Lightning web components. This setting enables a newer security architecture which, amongst other things, allows LWC components to host other LWC components from different namespaces without wrapping them in Aura components.
