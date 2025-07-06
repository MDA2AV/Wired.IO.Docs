---
title: 2.2 Mediator
type: docs
sidebar:
  open: false
---

Mediator feature allows a better code structuring for complex endpoint implementations, as well as a more streamlined dependency resolution (see 3. Dependency Injection chapter).

While it can be more convenient than fast endpoints, keep in mind that it's performance is slightly worse due to the IRequestHandler/IContextHandler resolution overhead.

Just like mediator like patterns, it is posdsible to set up IPipelineBehavior behaviors, see 4. Pipeline Behaviors chapter.


