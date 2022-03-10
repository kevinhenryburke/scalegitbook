


## 3. Microscope Analytics Package Installation

Before installing the Microsope Analytics Package we need to add some permissions to the "Analytics Cloud Integration Profile". The data integration jobs for Tableau CRM cannot be deployed unless the permissions for the accessed Salesforce Objects and Fields are already in place. The same result could also be achieved by specifying the relevant FLS in a permission set that we assign to the integration user.

The Analytics Cloud Integration User needs to have read permissions on all fields on the *Service Framework Audit* object, which is a part of the Microscope main package.

Once this is done we can install the package:

[Microscope Analytics Package installation URL](https://login.salesforce.com/packaging/installPackage.apexp?p0=04tB0000000dnKBIAY)



