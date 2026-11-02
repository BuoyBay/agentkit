---
title: "agentkit typed API"
summary: "Source-derived public signatures, properties, parameters, defaults, and return values."
category: "Reference"
order: 21
types: ["AgentEndpoint", "AgentEvent", "AgentProtocol", "JSONLDecoder"]
dependencies: []
symbols: ["AgentEndpoint", "AgentEvent", "AgentProtocol", "JSONLDecoder"]
related: ["api.md", "getting-started.md", "index.md"]
---

# agentkit typed API

Generated from the package source declarations. Platform guards are shown where present; inherited members are documented on the base type. See the [API contracts](api.md) for behavior and limitations.

## AgentProtocol

`Public Enum AgentProtocol As Integer`

### Values

| Name | Type | Value / Default | Description |
| --- | --- | --- | --- |
| `ACP` | `AgentProtocol` | `1` | — |
| `Codex` | `AgentProtocol` | `2` | — |
| `Claude` | `AgentProtocol` | `3` | — |
| `MCP` | `AgentProtocol` | `4` | — |
| `Custom` | `AgentProtocol` | `5` | — |

## AgentEndpoint

`Public Class AgentEndpoint`

An explicit local endpoint; it never discovers, launches or substitutes an agent.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Name` | `String` | Yes | No | — | — |
| `Protocol` | `AgentProtocol` | Yes | No | — | — |
| `Executable` | `String` | Yes | No | — | — |
| `Arguments` | `Array Of String` | Yes | No | — | — |

### Constructor

`Public Sub Constructor(name As String, protocol As AgentProtocol, executable As String, arguments As Array Of String)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `name` | `String` | `ByVal` | Required |
| `protocol` | `AgentProtocol` | `ByVal` | Required |
| `executable` | `String` | `ByVal` | Required |
| `arguments` | `Array Of String` | `ByVal` | Required |

Returns: no value.

## AgentEvent

`Public Class AgentEvent`

Deliberately compact normalization; RawJSON preserves provider-specific fields.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Kind` | `String` | Yes | No | — | — |
| `Text` | `String` | Yes | No | — | — |
| `SessionId` | `String` | Yes | No | — | — |
| `TurnId` | `String` | Yes | No | — | — |
| `ToolCallId` | `String` | Yes | No | — | — |
| `RequestId` | `JSONItem` | Yes | No | — | — |
| `RawJSON` | `JSONItem` | Yes | No | — | — |

### EncodeJSONLine

`Public Function EncodeJSONLine(message As JSONItem, maxLineBytes As Integer = 8388608) As Bytes`

Encode exactly one UTF-8 JSON object followed by LF. No Content-Length header.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `message` | `JSONItem` | `ByVal` | Required |
| `maxLineBytes` | `Integer` | `ByVal` | `8388608` |

#### Returns

| Type |
| --- |
| `Bytes` |

### AgentRequest

`Public Function AgentRequest(protocol As AgentProtocol, requestId As JSONItem, methodName As String, arguments As JSONItem) As JSONItem`

Generic escape hatch for documented methods; Claude stream-json is not RPC.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `protocol` | `AgentProtocol` | `ByVal` | Required |
| `requestId` | `JSONItem` | `ByVal` | Required |
| `methodName` | `String` | `ByVal` | Required |
| `arguments` | `JSONItem` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### AgentNotification

`Public Function AgentNotification(protocol As AgentProtocol, methodName As String, arguments As JSONItem) As JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `protocol` | `AgentProtocol` | `ByVal` | Required |
| `methodName` | `String` | `ByVal` | Required |
| `arguments` | `JSONItem` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### AgentResponse

`Public Function AgentResponse(protocol As AgentProtocol, requestId As JSONItem, payload As JSONItem) As JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `protocol` | `AgentProtocol` | `ByVal` | Required |
| `requestId` | `JSONItem` | `ByVal` | Required |
| `payload` | `JSONItem` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### ACPInitialize

`Public Function ACPInitialize(requestId As JSONItem, clientName As String = "buoy-studio", version As String = "0.1.0") As JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `requestId` | `JSONItem` | `ByVal` | Required |
| `clientName` | `String` | `ByVal` | `"buoy-studio"` |
| `version` | `String` | `ByVal` | `"0.1.0"` |

#### Returns

| Type |
| --- |
| `JSONItem` |

### ACPNewSession

`Public Function ACPNewSession(requestId As JSONItem, workingDirectory As String, mcpServers As JSONItem) As JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `requestId` | `JSONItem` | `ByVal` | Required |
| `workingDirectory` | `String` | `ByVal` | Required |
| `mcpServers` | `JSONItem` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### ACPPrompt

`Public Function ACPPrompt(requestId As JSONItem, sessionId As String, text As String) As JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `requestId` | `JSONItem` | `ByVal` | Required |
| `sessionId` | `String` | `ByVal` | Required |
| `text` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### ACPCancel

`Public Function ACPCancel(sessionId As String) As JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `sessionId` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### ACPPermission

`Public Function ACPPermission(requestId As JSONItem, selectedOptionId As String) As JSONItem`

Empty selectedOptionId means cancelled; caller must present offered options to the user.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `requestId` | `JSONItem` | `ByVal` | Required |
| `selectedOptionId` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### CodexInitialize

`Public Function CodexInitialize(requestId As JSONItem, clientName As String = "buoy-studio", version As String = "0.1.0") As JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `requestId` | `JSONItem` | `ByVal` | Required |
| `clientName` | `String` | `ByVal` | `"buoy-studio"` |
| `version` | `String` | `ByVal` | `"0.1.0"` |

#### Returns

| Type |
| --- |
| `JSONItem` |

### CodexInitialized

`Public Function CodexInitialized() As JSONItem`

No parameters.

#### Returns

| Type |
| --- |
| `JSONItem` |

### CodexStartThread

`Public Function CodexStartThread(requestId As JSONItem, workingDirectory As String) As JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `requestId` | `JSONItem` | `ByVal` | Required |
| `workingDirectory` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### CodexStartTurn

`Public Function CodexStartTurn(requestId As JSONItem, threadId As String, text As String) As JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `requestId` | `JSONItem` | `ByVal` | Required |
| `threadId` | `String` | `ByVal` | Required |
| `text` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### CodexInterrupt

`Public Function CodexInterrupt(requestId As JSONItem, threadId As String, turnId As String) As JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `requestId` | `JSONItem` | `ByVal` | Required |
| `threadId` | `String` | `ByVal` | Required |
| `turnId` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### CodexApproval

`Public Function CodexApproval(requestId As JSONItem, decision As String) As JSONItem`

For command/file-change approvals; permission-grant requests have a different result schema.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `requestId` | `JSONItem` | `ByVal` | Required |
| `decision` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### ClaudeUserMessage

`Public Function ClaudeUserMessage(text As String, sessionId As String = "") As JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `text` | `String` | `ByVal` | Required |
| `sessionId` | `String` | `ByVal` | `""` |

#### Returns

| Type |
| --- |
| `JSONItem` |

### MCPInitialize

`Public Function MCPInitialize(requestId As JSONItem, clientName As String = "buoy-studio", version As String = "0.1.0", protocolVersion As String = "2025-11-25") As JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `requestId` | `JSONItem` | `ByVal` | Required |
| `clientName` | `String` | `ByVal` | `"buoy-studio"` |
| `version` | `String` | `ByVal` | `"0.1.0"` |
| `protocolVersion` | `String` | `ByVal` | `"2025-11-25"` |

#### Returns

| Type |
| --- |
| `JSONItem` |

### MCPInitialized

`Public Function MCPInitialized() As JSONItem`

No parameters.

#### Returns

| Type |
| --- |
| `JSONItem` |

### MCPToolsList

`Public Function MCPToolsList(requestId As JSONItem, cursor As String = "") As JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `requestId` | `JSONItem` | `ByVal` | Required |
| `cursor` | `String` | `ByVal` | `""` |

#### Returns

| Type |
| --- |
| `JSONItem` |

### MCPCallTool

`Public Function MCPCallTool(requestId As JSONItem, toolName As String, toolArguments As JSONItem) As JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `requestId` | `JSONItem` | `ByVal` | Required |
| `toolName` | `String` | `ByVal` | Required |
| `toolArguments` | `JSONItem` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### MCPResourcesList

`Public Function MCPResourcesList(requestId As JSONItem, cursor As String = "") As JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `requestId` | `JSONItem` | `ByVal` | Required |
| `cursor` | `String` | `ByVal` | `""` |

#### Returns

| Type |
| --- |
| `JSONItem` |

### MCPReadResource

`Public Function MCPReadResource(requestId As JSONItem, uri As String) As JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `requestId` | `JSONItem` | `ByVal` | Required |
| `uri` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### NormalizeEvent

`Public Function NormalizeEvent(protocol As AgentProtocol, message As JSONItem) As AgentEvent`

Normalize a single event without executing any request or granting permissions.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `protocol` | `AgentProtocol` | `ByVal` | Required |
| `message` | `JSONItem` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `AgentEvent` |

### ClaudeControlRequest

`Public Function ClaudeControlRequest(requestId As String, subtype As String, arguments As JSONItem) As JSONItem`

SDK control requests for Claude stream-json; caller supplies a unique string id.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `requestId` | `String` | `ByVal` | Required |
| `subtype` | `String` | `ByVal` | Required |
| `arguments` | `JSONItem` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### ClaudePermission

`Public Function ClaudePermission(requestId As String, allow As Boolean, reviewedInput As JSONItem, reason As String = "Denied by client") As JSONItem`

Allow only the reviewed input, or deny with a reason; no policy changes are inferred.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `requestId` | `String` | `ByVal` | Required |
| `allow` | `Boolean` | `ByVal` | Required |
| `reviewedInput` | `JSONItem` | `ByVal` | Required |
| `reason` | `String` | `ByVal` | `"Denied by client"` |

#### Returns

| Type |
| --- |
| `JSONItem` |

## JSONLDecoder

`Public Class JSONLDecoder`

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `IsFailed` | `Boolean` | Yes | No | — | — |
| `HasPendingData` | `Boolean` | Yes | No | — | — |

### Constructor

`Public Sub Constructor(maxLineBytes As Integer = 8388608, maxFeedBytes As Integer = 67108864, maxMessagesPerFeed As Integer = 256)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `maxLineBytes` | `Integer` | `ByVal` | `8388608` |
| `maxFeedBytes` | `Integer` | `ByVal` | `67108864` |
| `maxMessagesPerFeed` | `Integer` | `ByVal` | `256` |

Returns: no value.

### Reset

`Public Sub Reset()`

No parameters.

Returns: no value.

### Feed

`Public Function Feed(chunk As Bytes) As Array Of JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `chunk` | `Bytes` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Array Of JSONItem` |

### Finish

`Public Sub Finish()`

No parameters.

Returns: no value.
