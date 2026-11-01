---
title: "AgentKit API"
summary: "Protocol descriptors, JSONL framing, request builders, and normalized event records."
category: "Reference"
order: 20
keywords: ["protocol", "request", "response", "approval", "events"]
symbols: ["AgentProtocol", "AgentEndpoint", "AgentEvent", "EncodeJSONLine", "JSONLDecoder", "JSONLDecoder.Feed", "JSONLDecoder.Finish", "JSONLDecoder.Reset", "AgentRequest", "AgentNotification", "AgentResponse", "ACPInitialize", "ACPNewSession", "ACPPrompt", "ACPCancel", "ACPPermission", "CodexInitialize", "CodexInitialized", "CodexStartThread", "CodexStartTurn", "CodexInterrupt", "CodexApproval", "ClaudeUserMessage", "ClaudeControlRequest", "ClaudePermission", "MCPInitialize", "MCPInitialized", "MCPToolsList", "MCPCallTool", "MCPResourcesList", "MCPReadResource", "NormalizeEvent"]
related: ["index.md", "getting-started.md", "typed-api.md"]
---

# API reference

## Core types and framing

| Symbol | Contract |
| --- | --- |
| `AgentProtocol` | Enum members ACP, Codex, Claude, MCP, and Custom |
| `AgentEndpoint(name, protocol, executable, arguments)` | Read-only `Name`, `Protocol`, `Executable`, and defensive-copy `Arguments`; no launch |
| `EncodeJSONLine(message, maxLineBytes = 8388608)` | Object to UTF-8 bytes plus LF; rejects unsupported/oversized data |
| `JSONLDecoder(maxLineBytes = 8388608, maxFeedBytes = 67108864, maxMessagesPerFeed = 256)` | Stateful bounded newline decoder |
| `JSONLDecoder.Feed(chunk)` | Returns `Array Of JSONItem`; retains incomplete bytes. A bad line throws for the whole call, so no earlier messages from that call are delivered; pending bytes clear and `Reset()` is required before reuse. |
| `Finish()` / `Reset()` | Reject an incomplete final frame; clear failed/pending state |
| `IsFailed` / `HasPendingData` | Read-only decoder status |
| `AgentEvent` | Immutable normalized `Kind`, `Text`, `SessionId`, `TurnId`, `ToolCallId`, `RequestId`, `RawJSON` |
| `NormalizeEvent(protocol, message)` | Adapt one provider object while retaining its raw fields |

## Requests and provider builders

| Family | Public functions |
| --- | --- |
| Generic | `AgentRequest(protocol, requestId, methodName, arguments)`, `AgentNotification(protocol, methodName, arguments)`, `AgentResponse(protocol, requestId, payload)` |
| ACP | `ACPInitialize`, `ACPNewSession`, `ACPPrompt`, `ACPCancel`, `ACPPermission` |
| Codex | `CodexInitialize`, `CodexInitialized`, `CodexStartThread`, `CodexStartTurn`, `CodexInterrupt`, `CodexApproval` |
| Claude | `ClaudeUserMessage(text, sessionId = "")`, `ClaudeControlRequest(requestId, subtype, arguments)`, `ClaudePermission(requestId, allow, reviewedInput, reason = "Denied by client")` |
| MCP | `MCPInitialize`, `MCPInitialized`, `MCPToolsList`, `MCPCallTool`, `MCPResourcesList`, `MCPReadResource` |

RPC builders validate required IDs, methods, and object parameters, but do not implement complete provider schema validation. IDs must be strings or exactly represented integer JSON numbers within ±(2^53−1). ACP/MCP/Custom use JSON-RPC 2.0. Codex omits `jsonrpc`. Generic RPC builders reject Claude.

`ACPNewSession` accepts `Nil` for an empty MCP-server list. `ACPPermission` requires the actual offered option ID; an empty selection returns the cancelled outcome. `CodexApproval` accepts `accept`, `acceptForSession`, `decline`, or `cancel` for its simple approval shape. The caller owns validation against the corresponding request. Advanced permission-grant shapes require explicit generic responses. `ClaudePermission` includes only reviewed input on allow, or a reason on deny; it grants no persistent permission.

`MCPInitialize` defaults the protocol version to `2025-11-25`. Tool/resource list builders accept opaque cursors. `MCPCallTool` requires the actual discovered tool name and object arguments. The application chooses pagination and dispatch policy.

## Event and validation behavior

Common normalized kinds include text/thought deltas, complete messages, tool calls/updates/input deltas, approval, plan, session/turn lifecycle, response/request/notification, error, and unknown. Only an explicit completed/success state becomes `turn_complete`. Full multi-block messages remain intact in raw JSON. Claude tool-input deltas require correlation using the raw content-block index.

Wire discriminators and approval decisions are case-sensitive. Raw or escaped JSON NUL rejects before Buoy parsing; a literal escaped backslash followed by `u0000` remains text. Invalid envelopes and decoder input throw catchable exceptions. Returned request IDs and raw JSON are detached copies.

Follow the [getting-started guide](getting-started.md) for a compiled example or return to the [overview](index.md).


[Full typed API: properties, parameters, defaults, and returns](typed-api.md)
