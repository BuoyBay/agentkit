# AgentKit

Protocol codecs and event adapters for an agent-aware Buoy editor. This package
owns **no transport, agent process, authentication or execution policy**. Feed
stdout bytes from `processkit`, and send encoded bytes to its stdin; route
stderr separately. MCP HTTP transports need their own HTTP/SSE implementation.

Copy `packages/agentkit-0.1.0.buipkg` into a consumer's `packages/` directory and
`Import agentkit`. See [the console example](../examples/agentkit_demo.bui).

## Supported interfaces

| Interface | Implemented surface | Application responsibilities |
| --- | --- | --- |
| ACP v1 | initialize, session creation/prompt/cancel, permission response, text/tool/plan/session event normalization | Negotiate capabilities/authentication; retain session/request state; implement only advertised filesystem/terminal operations |
| Codex app-server | initialize/initialized, thread/start, turn/start/interrupt, simple command/file approval responses, streamed event normalization | Launch `codex app-server --listen stdio://`; retain threads; handle auth, request correlation and configured sandbox/approval policies |
| Claude Code stream-json | user input, generic SDK control requests, reviewed tool allow/deny responses, streamed text/tool/result normalization | Launch with print/input/output stream-json, verbose and include-partial-messages options; drive control initialization and session lifecycle |
| MCP (including a configured T3 Code MCP server) | initialize/initialized, tools/list/call, resources/list/read | Configure the actual endpoint, authentication and tool names discovered from it; negotiate protocol version and dispatch replies |
| Custom | JSONL objects plus generic JSON-RPC builders and unchanged raw events | Supply the provider-specific contract explicitly |

T3 Code does not have a fabricated native wire adapter here: a T3 MCP endpoint
can be connected through the standard MCP interface when the host exposes one.
Grok's HTTP tool-calling API is a separate provider integration, not ACP or
Codex app-server. Muse has no assumed protocol until the intended product is
identified; an actual ACP/MCP-compatible endpoint can use those adapters.

These are codec adapters, not full SDKs. No provider is silently substituted.
Initializers advertise empty client capabilities instead of claiming filesystem,
terminal or sampling support the application has not implemented. ACP is pinned
to stable v1; the v2 draft is not implied. MCP's default advertised version is
`2025-11-25` and is configurable through `MCPInitialize`.

## API

- `AgentEndpoint(name, protocol, executable, arguments)` is an immutable local
  launch descriptor. `Arguments` returns a defensive copy. Construction does
  not discover or start a program.
- `EncodeJSONLine(message, maxLineBytes)` serializes one JSON object as UTF-8
  followed by LF. This is **not** LSP's `Content-Length` framing.
- `JSONLDecoder.Feed(Bytes)` returns zero or more complete JSON objects. Handles
  arbitrary byte fragmentation, including inside Unicode codepoints, multiple
  frames per feed, and CRLF. `Finish()` rejects truncated final frames;
  `Reset()` recovers from a failed decoder. Blank lines and non-object messages
  reject, so diagnostic logs must use stderr. Treat each `Feed()` call as one
  batch: if any line fails, the call throws without returning earlier messages
  from that batch, clears pending bytes, and requires `Reset()` before reuse.
- `AgentRequest`, `AgentNotification` and `AgentResponse` support documented
  methods beyond the convenience builders. ACP/MCP/Custom use JSON-RPC 2.0;
  Codex uses its documented envelope without a `jsonrpc` field. Claude uses
  its own stream-json/control messages, so these RPC builders reject it.
- `ACPInitialize`, `ACPNewSession`, `ACPPrompt`, `ACPCancel`, `ACPPermission`.
  `ACPNewSession` accepts explicit `Nil` for an empty MCP-server list.
  `ACPPermission` takes the selected offered option ID; an empty string returns
  the cancelled outcome. The caller must validate a choice against the request.
- `CodexInitialize`, `CodexInitialized`, `CodexStartThread`, `CodexStartTurn`,
  `CodexInterrupt`, `CodexApproval`. Simple approval decisions are `accept`,
  `acceptForSession`, `decline`, `cancel`. Permission-grant requests and advanced
  policy amendments require their documented schemas via `AgentResponse`.
- `ClaudeUserMessage`, `ClaudeControlRequest`, `ClaudePermission`.
  The permission helper returns only the reviewed input on allow, or a reason
  on deny. It does not infer persistent permission changes.
- `MCPInitialize`, `MCPInitialized`, `MCPToolsList`, `MCPCallTool`,
  `MCPResourcesList`, `MCPReadResource`. List builders accept opaque pagination
  cursors; the application decides how many pages to request.
- `NormalizeEvent(protocol, message)` returns an immutable `AgentEvent` with
  `Kind`, `Text`, `SessionId`, `TurnId`, `ToolCallId`, `RequestId`, `RawJSON`.
  Raw JSON and request IDs are copied on construction and access.

Normalized kinds include `text_delta`, `thought_delta`, `assistant_message`,
`user_message`, `tool_call`, `tool_update`, `tool_input_delta`, `approval`,
`plan`, `session_started`, `session_status`, `turn_started`, `turn_complete`,
`turn_failed`, `turn_interrupted`, `turn_status`, `response`, `request`,
`notification`, `error`, `unknown`. Unknown provider fields always remain in
`RawJSON`. A full assistant message can contain multiple blocks; this single
record adapter preserves them in `RawJSON` instead of pretending to dispatch
all blocks. Claude streamed tool-input deltas need correlation using the raw
content-block index. An approval event never grants permission or executes a
tool. Only an explicit completed/success status becomes `turn_complete`.

## Limits

Decoder defaults: 8 MiB per JSON line, 64 MiB per input chunk, 256 messages per
feed, and 128 JSON nesting levels. Wire discriminators and approval decisions are compared
case-sensitively, regardless of Buoy's default string comparisons. Real JSON
`\u0000` escapes reject before parsing because Buoy 0.72 otherwise drops NUL
and could alias request IDs or methods; escaped literal `\\u0000` text remains
valid. Limits fail closed: the decoder discards
pending data and requires `Reset()`. LF termination is required at EOF. The
line byte cap excludes LF but includes a possible CR. Configure smaller limits
for less trusted endpoints. Envelope builders validate required IDs, methods
and object parameters, but are not complete provider schema validators.
Numeric RPC IDs are restricted to exactly represented integers within ±(2^53−1).

References: [ACP v1](https://agentclientprotocol.com/protocol/v1/initialization),
[Codex app-server](https://learn.chatgpt.com/docs/app-server),
[Claude Code CLI](https://code.claude.com/docs/en/cli-reference),
[Claude Agent SDK control source](https://github.com/anthropics/claude-agent-sdk-python/blob/main/src/claude_agent_sdk/_internal/query.py),
[MCP lifecycle](https://modelcontextprotocol.io/specification/2025-11-25/basic/lifecycle).
