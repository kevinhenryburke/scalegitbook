

## 1. Microscope Post Installation Checks

One important thing to consider post-install involves __Platform Cache__. A cache partition called __mscope.FrameworkCache__ is packaging with the app but no space is allocated to it (doing so would cause installation to fail in orgs without platform cache available.) After installation, if you have platform cache available in the org then allocate at least 1MB or Org Cache to get the performance benefits of using the Cache. Developer Edition orgs for example have no cache allocation so this is not going to be usable in those orgs. The app still functions however but is slightly slower as a result.


This one cache partition is used by all invocations across the org, regardless of namespaces. 

{% hint style="info" %}
Note also to check the partition size after each Microscope package upgrade, it may have been reset by a package push
{% endhint %}

