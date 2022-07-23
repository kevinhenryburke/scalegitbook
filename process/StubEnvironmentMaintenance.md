
## Stub and Environment Maintenance - Who Does What and When

Before we start to look at the stub patterns, a quick word on who maintains them across environments in each use case. We note that the last use case differs from the others in that handling environmental differences is the responsibility of an environments team rather than a development team. We cover therefore cover this pattern when we discuss [Environment Management](./Environments.md)

The following table summarizes pattern maintenance.

| **Use Case** | **Pattern** | **Maintained By** | **Defined in Story** |
| --- | ----------- | --- |--- |
| Early Development | Temporary Development Stub | Developers add Custom Setting in org|No |
| DX Development | Absent Service Stub Class | Automatic triggering | Yes |
| Unit Testing | Temporary Development Stub |Developers add Custom Setting in test setup |No |
| Test Environment Differences | Absent Connection Stub |Environment Manager maintains Custom Settings |Yes |

