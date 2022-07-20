# Principles 

However a company approaches this challenge, and whether they are
attempting to avoid or respond to the issues we've outlined, some core
principles emerge. We subdivide these principles into groups:

### Business Alignment Principles

1.  A Large implementation must be structured to be understandable in
    the context of the business

2.  There must be a means to segment the implementation so developed
functionality maps to a section of the business process within a
business area.

3. Each functional area should be aligned to an architect, analysis, development and test team in potentialy a many-to-one relationship. Any functionality that crosses between different functional areas should allow each functional area to be developed by the aligned team independently with a contract between the two defined by an overarching architecture function.  


4.  All data must have clear business and technical stewardship. Where
data is stored in more than one system a System of Record needs to
be identified for each relevant object.

5.  The parts of the org where the businesses intersect must be
protected

6.  Apart from changes to common functions the business areas should
have autonomy over their processes and delivery schedule.

### Change Principles

1.  The overall Change Process should be easy to understand with each
    individual and team quickly able to determine the boundaries of
    their role.

2.  The scope of any change should be definable to a build segment. No
    change should ever impact anything outside of its scope

3.  No change in one functional area should ever enforce a simultaneous
    change or enforce any downtime in another area. The timelines for
    necessary changes to a standard process should never be determined
    by the technical implementation.

4.  No change in one functional area should ever delay a change in
    another area. Unless the changes are part of one process the order
    in which they are deployed should be irrelevant, they must be
    independent unless there is an unavoidable functional dependency.

5.  Implementing a new updated version of a service should not touch any
    code artefact for existing versions. After implementation the only
    changes to a version's code should be bug fixes.

6.  If there are problems with the new version of a service, reverting
    to the previous version should be quick and not require downtime for
    a code rollback re-deploy.

7.  The implementation should support the ability to switch
    functionality to or from a third-party application with the minimum
    of risk or rework.

8.  Regression testing the scope of a change should only require
    regression testing the enclosing business area.

9.  An enterprise should try to place itself in the best position for
unforeseeable major restructuring (mergers, acquisitions, splits)
that might occur in the future, in particular to give us as many Org
Strategy options as possible (multi-org / single org).


### Build Principles

1.  The architecture should reflect the business it supports, protect
    critical common parts of the build but allow quick development in
    flexibility in tooling in other areas.

2.  The architecture should not place undue restrictions on hosting
    built functionalities, where possible we should allow applications
    to be hosted in different Salesforce instances.

3.  The architecture should advocate a correct way of implementing any
    type of functionality in a context, for example one mechanism to
    allow visibility of data or one mechanism to update fields. A single
    piece of functionality should not be split across multiple
    mechanisms. 

4.  In the vast majority of cases functionality should be accessible via
the same mechanism for Engagement Layers, Integrations and to other
services and the directory of exposed functions be readable from the
build itself. 

5.  Implementing within the chosen framework should not take
significantly longer than any point solution that breaks the rules.
If this is not the case then a developer under time pressure will
inevitably break the rules.

6. Code and artefacts that are no longer used should be trivial to
remove from the build.


### Adoption Principles

1.  For an existing org, the architecture should be adoptable
    immediately alongside the current implementation. Changing
    everything in one go is impossible, the legacy will take time to
    refactor, however preventing new initiatives from adding technical
    debt without delaying the business must be an achievable goal. 

2.  Any architectural principles must not enforce a pre-determined set
    of standards but be flexible to work within the company's corporate,
    or for existing orgs currently-adopted standards.

3.  The architecture must be easy to understand and the mechanisms to
    conform to it by technical teams should not be onerous to learn.

## TODO Benefits - review from docx

There are many benefits of adopting these principles:

-   Clean mapping of the build to the business, preventing functional
    tangles provides a clear delineation of the business owner for each
    artefact and process. Reduces the need to check several teams or
    business leads when making a single change.

-   Clearly exposed endpoint functionality for use by all layers of the
    architecture, whether data, integration, engagement etc.

-   Dev teams can work safely and independently within well-defined
    parameters

-   Clearly defined scopes for testing allow tests to be more focused on
    content and independent of anything else that is due for release. 

-   Safer releases, reduced time to deploy with far fewer dependencies
    and frequently zero downtime for new versions

-   Unified model makes dev and admin onboarding as efficient as
    possible and allows teams to transfer quickly to focus on different
    areas. They do not need to know the entire build to be useful. This
    is also true if a customer is forced to be multi-org, applying the
    same architectural core will allow teams to be rapidly deployed to
    where the business most needs them.

These result in significant Returns On Investment. The diagram shows
some of the key factors that reduce ROI, the colour coding is:

-   Common high-level areas are in **grey** which are subdivided
    further.

-   Those this architecture directly helps with technically are in
    **light blue**.

-   The **green** items are those that are helped indirectly

-   The **pink** items are under the control of the enterprise but this
    architecture can help to facilitate improvements

-   The **orange** items are indirectly positively impacted

-   Opaque items are beyond our reach.
