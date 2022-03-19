
### How do you know you have a problem?

The following are potential consequences of the Accidental Architecture.
Any customer who finds themselves with any of these issue should start
to address them as soon as possible as these problems will only
increase.

-   New LOBs and GEOs are getting harder and harder to add. Different
    process requirements and back office system performing same function
    in different LOBs/GEOs are getting harder to implement.

-   Can\'t adopt modern development options like unlocked packages
    provided by Salesforce DX. There is no one strategy at present to
    break up a big org to adopt DX, it is a considerable technical
    challenge.

-   Can\'t or can only partially adopt Continuous Integration (CI).
    Complexities on merging profile access to fields and workflows on
    common objects become really hard and a big investment overhead for
    each release.

-   BAU requires highly skilled platform experts, these are expensive
    and hard to find and retain. 

-   Configuration becomes very hard to read as dozens of config rules
    and processes per object mask the functionality.

-   Questions like "what happens when we update an account" become
    almost impossible to answer. The impact of even a small change can
    be unknowable as a mass of workflow rules, flows, process builder
    configurations and trigger need to be analysed 

-   No one person or team has a full understanding of the
    implementation. Developments are constantly impaired by nasty
    surprises.

-   It becomes really hard to manage scenarios where different LOBs or
    GEOs require similar information from integrations to other systems
    but where these other systems and the mechanisms they use to provide
    this information vary across the estate

-   Technical debt - lack of standards and structure in code. Dead code
    in the build that is very hard to identify with certainty and hard
    to prove that removing it is safe. Repetitive code, near identical
    functionality implemented many times in many ways.

-   Unexplained governor limits breaches

-   You have an uneasy feeling in the pit of your stomach every time
    there's a significant release.

Many of these items are subjective, especially the last one and it is
crucially important to have measures too. The one metric to rule them
all is the ability to change, our number one goal is to be able to
respond to business change quickly in a risk-free manner. This guiding
metric is too coarse for practical use but we can break it down into
measurable chunks that can be tracked over time. Some of these might be
metrics commonly used in building software:

-   Deployment frequency -- how often do you push to production?

-   Lead time -- how long between a repository check-in and that work
    being live in production?

-   Mean time to recover -- resolution time for production problems.

-   Change failure rate -- Percentage of changes that result in a
    production problem

An organization exhibiting our warning signs above would certainly have
low deployment frequency and high lead time. Other metrics that get to
the heart of the complexity of your org and the impact of the build
complexity and team productivity include:

-   What percentage of stories require meetings or ratifying with a
    domain expert to determine impact on all business areas?

-   How long is an average story delayed waiting for approval by a
    Design Authority?

-   What percentage of stories need to be presented to a Design
    Authority representing multiple business areas?

-   How long to get new developers and admins fully-independent?

-   What percentage of developer time is spent dealing with production
    issues?

-   What percentage of stories do you need to defer to a developer who
    has worked the org for a long time?

-   What percentage of changes require full regression tests vs targeted
    regression tests vs just automated tests?

-   What is the average number of team member hours used in deploying a
    small, medium or large change?

Tracking these numbers will give a good reading of how ready you org is
for change.

