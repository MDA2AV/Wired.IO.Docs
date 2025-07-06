---
title: 4. Pipeline Behaviors
type: docs
sidebar:
  open: false
---

While not the most useful feature as it does not provide anything extra apart from maybe slight better code structure, pipeline behaviors can be attached to the mediator pipeline.

When using hybrid or mediator endpoints it is possible to add IPipelineBehavior behaviors that will execute before or after the endpoint, similar to ASP.NET Core filters.

IPipelineBehaviors can be configured to run for all endpoints (hybrid or mediator mutually exclusive) or a specific set of endpoints, which is an advantage in comparison with middleware which executes for all endpoints.

Another important difference is that pipeline behaviors execute after all middlewares next() call, and before middleware return path. We could say behaviors wrap the endpoint and middleware pipeline wraps the behaviors.