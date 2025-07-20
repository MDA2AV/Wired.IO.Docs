---
title: Create an Endpoint
weight: 4
type: docs
sidebar:
  open: false
---

To create endpoints there are three different options.

#### Fast Endpoints
Ideal to quickly wiring up and endpoint with minimal footprint. Great for low complexity endpoints.

#### Hybrid Endpoints
Best of both worlds, create a fast endpoint but delegate logic to a different class (IRequestHandler), at the cost of a little extra boilerplate.

#### Mediator Endpoints
Delegate endpoint logic to a class, great for constructor injection and complex endpoints.
