---
title:
toc: false
---
<br/>

# Welcome to Wired.IO
## Built for developers

**Wired.IO** is a lightweight, high-performance HTTP 1.x server framework for .NET. Designed from the ground up for **embedding**, **extensibility**, and **raw speed**, it gives you full control over your request pipeline without the weight of traditional web frameworks.

Whether you're building APIs, embedded servers, developer tools, or hybrid applications, Wired.IO provides a focused, zero-friction foundation that runs anywhere your .NET code does — no external hosting required.

<br/>

{{< cards >}}
  {{< card link="basic" title="Basic" subtitle="Core concepts and minimal setup" icon="check" >}}
  {{< card link="advanced" title="Advanced" subtitle="High-performance patterns and internals" icon="star" >}}
{{< /cards >}}

## HTTP 1.1 Support

We provide an out of the box HTTP 1.1 handler which you can see tutorials and examples for in our Basic and Advanced guides. Currently there is no support for HTTP 2 or 3. The typical applications we support benefit a lot more from HTTP 1.1 so there are no plans to implement other official HTTP versions. It is however possible to implement your own custom HTTP protocol for very high performance use cases, that cover the HTTP 2 benefits while keeping a much better performance than the clunky HTTP 2. We will provide in the future a detailed guide to do so.

## Support

We are happy to support you via our discord, also feel more than welcome to create an issue or PR in our github repository.