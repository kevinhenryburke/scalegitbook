
## A Golden Rule - Custom Metadata vs Custom Settings

*Microscope* has a golden rule: **Custom Metadata Type records are the same in all test environments and Custom Settings are only used when environmental differences occur**. These differences might be either because different static values are required across environments or because a temporary setting is needed to meet a short-term production need.

When we need a setting which may need to differ across environments, a Custom Metadata Type field is used to hold the name of a Custom Setting that will hold the different values. This way we make the fact that there is an environment setting visible (via a CMT Record) whilst allowing the variations in the Custom Setting.

Custom Metadata is controlled by Architects and implemented by Developers. Architects inform Environment managers about the need for different settings and environment managers control and script these for each environment.
