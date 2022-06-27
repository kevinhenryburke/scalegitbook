## Three Packages

There are 3 Packages each of which contains an App 

1. The __Microscope App__ is the main application, it contains all the fundamental artefacts of Microscope. This is a production-grade app and is required in all scenarios.

2. The __Microscope Demo App__. This is used for learning and playing with Microscope. It contains visual examples demonstrating key features on tuning the application. This is a demo and should never be installed in any org on the path to production.

3. The __Microscope Analytics__ App. This contains analytics dashboards for Microscope for Performance, Connections and Forensics. This is a production app but is optional and can only be used if *CRM Analytics* licenses are available in the production org.
### Dependencies: 

1. The Microscope App is self-contained and has no dependencies
2. The Demo App in dependendent on the Microscope App only
2. The Analytics App in dependendent on the Microscope App only, there is no dependency on the Demo Application.


