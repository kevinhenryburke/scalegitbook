This page outlines how to document Microscope. The documentation is hosted by [Gitbook](https://kevinhenryburke.gitbook.io/microscope/introduction/welcome), it is written in markdown and loaded to a branch in Github where it is synced to Gitbook as the source of the Microsope pages.

## Github access

The source for these apps is in a private repo on [Github](https://github.com/kevinhenryburke/scalegitbook). Repo info is below in clone command. You will need to authenticate to the repo within VS Studio to develop Microscope.

## Getting Documentation from the repo to VS Code

Create a new folder and open in VS Code. 

Open a new terminal in VS Code (via Terminal -> New Terminal) and run

```
git clone https://github.com/kevinhenryburke/scalegitbook.git  -b gitbooksync .

```

Any *Push to Origin* from VS Code will now become part of the live documentation set.


## Documentation Conventions


````


embed a video: 
{% embed url="https://www.loom.com/share/3bfa83acc9fd41b7b98b803ba9197d90" %}

embed an image:
put the image in the same folder as the md file and use this syntax:
![An unplanned journey](UnstructureOrg.png)

comment:
<!-- Use standard HTML block comments -->

create a link with link text:
From the [Mailchimp Marketing API docs](https://mailchimp.com/developer/marketing/docs/fundamentals/)

link to another page via a separate full width section
{% content-ref url="fundamentals/projects.md" %}
[projects.md](fundamentals/projects.md)
{% endcontent-ref %}


hint boxes: styles are: info,tip,danger,working
{% hint style="info" %}
**Good to know:** here's how to add an info
{% endhint %}

A bit if subtext with a line done the left hand side:
> add a greater than
> to each line
> and they will appear in a block

````

