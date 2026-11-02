# JSONL codecs for agent protocols, ACP/Codex/Claude events, MCP requests

Protocol codecs and event adapters for an agent-aware editor. This package
owns **no transport, agent process, authentication or execution policy**. Feed
stdout bytes from `processkit`, and send encoded bytes to its stdin; route
stderr separately. MCP HTTP transports need their own HTTP/SSE implementation.

Copy `packages/agentkit-0.1.0.buipkg` into a consumer's `packages/` directory and
`Import agentkit`. See [the console example](examples/agentkit_demo.bui).

## Install

From a Buoy consumer project with BPM installed:

```sh
bpm add agentkit --git https://github.com/BuoyBay/agentkit.git --rev refs/heads/main
```

BPM records the resolved commit in its lockfile. Keep that lockfile with the
consumer project for reproducible installs.

## Example

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

## Build from source

With the native `build-packages` driver from [devtools](https://github.com/BuoyBay/devtools)
on your `PATH`, run from this checkout:

```sh
build-packages --root "$PWD" --compile-consumer agentkit
```

The driver checks source targets and compiles an import consumer. See the
[API guide](docs/API.md) for behavior, limits and any host prerequisites.

## Local Buoy setup

On your own machine, install Buoy and run:

```sh
buoy ai init --both
```


## Contributing

[Contributing](CONTRIBUTING.md) · [License](LICENSE)
