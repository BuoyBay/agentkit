---
title: "AgentKit"
summary: "Encode agent protocol messages and normalize events without owning a transport."
category: "Overview"
order: 0
keywords: ["agent", "jsonl", "acp", "codex", "claude", "mcp"]
related: ["getting-started.md", "api.md", "typed-api.md"]
---

# AgentKit

AgentKit supplies JSONL framing, request builders, and normalized events for an agent-aware editor. Use it when your application already owns the subprocess or HTTP transport and needs predictable protocol data at that boundary.

## Supported protocols

| Protocol | Package surface | Application responsibility |
| --- | --- | --- |
| ACP v1 | Initialization, session prompts/cancel, offered permission outcomes, event normalization | Capabilities, transport, request correlation, filesystem/terminal policy |
| Codex app-server | Initialization, threads, turns, interruption, simple approval messages | Exact executable and arguments, authentication, sandbox configuration |
| Claude stream-json | User input, control requests, reviewed tool allow/deny replies | CLI flags, SDK/control lifecycle, tool policy |
| MCP | Initialization, tools and resources requests, pagination cursors | Endpoint, authentication, discovery, reply dispatch |
| Custom | Generic JSON-RPC objects and raw normalized events | An explicit provider contract |

These are codecs and adapters rather than full provider SDKs. An `AgentEndpoint` describes a launch but starts nothing. `NormalizeEvent` produces data and never executes a tool or grants approval. No provider, executable, model, or endpoint is substituted automatically.

## Choose the right layer

Feed child stdout into `JSONLDecoder`, route stderr separately, and serialize outgoing objects with `EncodeJSONLine`. JSONL uses newline framing, not LSP Content-Length headers. Use AgentSession instead when you want the repository's process-backed session driver.

The initializers advertise empty client capabilities; a host must implement and deliberately advertise any filesystem, terminal, or sampling support. ACP targets stable v1. MCP's advertised version is configurable. Unknown provider fields remain available in `AgentEvent.RawJSON`.

Continue with [Getting started](getting-started.md) or browse the [API reference](api.md).
