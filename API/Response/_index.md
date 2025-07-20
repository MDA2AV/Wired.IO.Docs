---
title: Response
weight: 3
type: docs
sidebar:
  open: false
---

The Response component in Wired.IO defines how the server replies to the client. It encapsulates the status code, headers, body content, and metadata like content length, encoding, and expiration. The IResponse interface is designed for composability and streaming: you can plug in different encoders, dynamically adjust headers, and stream body data efficiently via PipeWriter. Wired.IO also supports chunked encoding and static file responses natively, making it well-suited for both dynamic APIs and static resource delivery.