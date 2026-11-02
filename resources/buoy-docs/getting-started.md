---
title: "AgentKit getting started"
summary: "Round-trip one UTF-8 JSONL message and connect the codec to an owned transport."
category: "Guides"
order: 10
keywords: ["jsonl", "utf8", "decoder", "framing"]
related: ["index.md", "api.md", "typed-api.md"]
---

# Getting started

Copy `agentkit-0.1.0.buipkg` into the consumer project's `packages/` directory, then `Import agentkit`. The package has no transport or process dependency.

## Round-trip a message locally

The complete program below serializes one object with its terminating LF and decodes it. It performs no network request and launches no agent.

```basic
Import agentkit

Sub Main()
    Dim message As JSONItem = JSONItem.NewObject()
    message.Value("text") = "Hello from the editor"
    Dim wire As Bytes = EncodeJSONLine(message)
    Dim decoder As New JSONLDecoder()
    For Each decoded As JSONItem In decoder.Feed(wire)
        Print decoded.Value("text").AsString().Or("")
    Next
    decoder.Finish()
End Sub
```

A real host passes arbitrary stdout byte chunks to the same decoder. A feed can return no complete frames or several complete objects. UTF-8 code points and JSON lines may span feeds. At EOF call `Finish()` so a truncated frame becomes an error instead of disappearing.

## Connect a protocol

Create an `AgentEndpoint` with the actual protocol, executable, and arguments. This descriptor is immutable and does not launch anything. Use a matching convenience builder, write `EncodeJSONLine(builderResult)` to your transport, and correlate replies by their original JSON request IDs. Generic builders accept ACP, MCP, Custom, and Codex's distinct envelope; Claude uses its own stream-json/control builders.

Pass each decoded provider message to `NormalizeEvent(endpoint.Protocol, message)`. Keep raw JSON for blocks or extension fields that the one-record normalized event cannot represent. An `approval` kind means the host has a request to present, not permission to proceed.

## Handle failures deliberately

Blank lines, non-object messages, malformed UTF-8/JSON, oversized feeds, and excessive nesting reject. After decoder failure, pending bytes are discarded and `Reset()` is required before reuse. Default bounds are 8 MiB per line, 64 MiB per feed, 256 messages per feed, and 128 nesting levels. Reduce bounds for endpoints you do not fully control.

See [API reference](api.md) for builders and limits, or return to [AgentKit](index.md).
