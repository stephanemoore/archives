# API Reviews

## Questions to ask during API reviews

This section enumerates a series of topics and questions an API creator, maintainer, or reviewer may wish to
ask themselves when evaluating an API. It aims to document thoughts and thought processes that may be helpful
in designing, maintaining, or reviewing an API.

### Surface Area

The maintainability of an API is often strongly correlated with the surface area that
it exposes to its clients. Minimal APIs generally tend to be much easier to maintain over time.

Questions to ask:

* Does the API provide all the functionality that a typical developer might be interested in?
* Does the API provide too much functionality such that a developer could use it to achieve unintended behavior?

### Resilience
The best APIs survive over long periods of time despite changes in requirements or host environments.
The best way to ensure the longevity of an API is to anticipate potential changes in requirements or dependencies.

Questions to ask:

* Could the API be easily adapted to small or large changes in requirements?
* Could the API be easily adapted to small or large changes in system libraries or system dependencies?

### Adaptability
Individual clients often have unique environments adapted for the needs of their teams, clients, or products.
Client code can make assumptions within their respective environments but shared APIs should avoid making
assumptions that are invalid for their target audience.

Questions to ask:

* Has the API been tested in a generic environment?
* Does the API make any strong assumptions about the environment in which it will be used? Can those assumptions be removed?

### Minimizing Cognitive Overhead

Many APIs use some form of conceptual model to describe behavior and functionality. It is often prudent and
beneficial to use conceptual models that are easily understood by clients.

Questions to ask:

* Are there existing popular, ubiquitous, or native APIs with similar concepts or objectives? If so, can the proposed API adopt similar conventions to minimize cognitive context-switching overhead?
* Does the API have any unintuitive behaviors? Are these unintuitive behaviors documented or avoidable?

### Migration

In many situations an API may intend to replace another API. In these situations it is often prudent to ensure that clients can easily migrate from the old API to the new API.

Questions to ask:
* Is there an existing API that serves a similar purpose? If so, is there a way to design the new API to make it easy for clients to transition away from the old API without compromising the focus of the new API?
* Does the new API expose all the functionality available from the old API? If not, is there clear and explicit documentation or messaging advising clients how to handle functionality that is not provided?

### Subclassing

Many APIs expose classes to encapsulate their state and functionality. The polymorphism enabled by exposing classes in APIs can lead to unexpected or unintended scenarios.

Questions to ask:

* Are there any specific behaviors that subclasses need to maintain or respect? Are they documented and/or enforced?
* Are methods with critical functionality documented or properly annotated to indicate that subclass overrides must invoke the superclass implementation?
* Are there any unexposed interfaces that subclasses would require?
* If a class does not support subclassing does it have appropriate safeguards or declarations to prevent subclassing?
* Is the API useful without subclassing? If not, can the API be restructured to offer customizations through delegation or other mechanisms as an alternative to subtype polymorphism?

### Testability

An important aspect to maintaining an API is being able to detect bugs and regressions.

Questions to ask:

* Is the exposed functionality of the API easy to test? Can modifications be made to the API to facilitate manual and/or automated testing?
* Is the API heavily exposed to residual or accumulated state? Can the API be modified to reduce exposure to residual or accumulated state and simplify the complexity of testing and/or recreating reported bugs?
* Is the API's behavior stable or consistent in a testing environment? If not, are there facilities that can be added to support stability and consistency in tests targeting unrelated functionality?
