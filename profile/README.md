<div align="center">
  <img src="https://raw.githubusercontent.com/usewrit/writ/main/assets/banner.svg" alt="writ — record a web task once, replay it forever, on hardware you own" width="100%">
</div>

## Record a web task once. Replay it forever.

**Writ** is self-hosted browser automation that runs on hardware you own.

Record a repeatable web task once, in a real browser — logins, forms, navigation, extraction. Then replay it forever as a workflow, a scheduled job, a REST endpoint, or a tool your AI assistant can call. Recording uses a model once; **replay never does**, so a task that runs every hour costs nothing per run and doesn't get slower, more expensive or less predictable as you scale it.

Everything stays on your machine: one SQLite file, no cloud dependency, no telemetry, credentials encrypted at rest.

```bash
git clone https://github.com/usewrit/writ.git && cd writ
./scripts/gen-env.sh
docker compose up -d --build
```

## Repositories

| | |
|---|---|
| **[writ](https://github.com/usewrit/writ)** | The self-hosted coordinator — API, web UI, scheduler, document and OCR extraction. `docker compose up`. |
| **[writ-agent](https://github.com/usewrit/writ-agent)** | The Rust fleet agent that drives the browsers. Dials out over WebSocket; needs no inbound ports. |
| **[writ-sdks](https://github.com/usewrit/writ-sdks)** | Client SDKs for TypeScript, Python, Go and Rust — four languages, one contract. |
| **[writ-mcp](https://github.com/usewrit/writ-mcp)** | The MCP connector. Your saved workflows become tools an AI assistant can call. Zero dependencies. |

## How it fits together

The coordinator is Python and FastAPI on SQLite, and it **never launches a browser itself**. Every browsing job goes to a separate Rust agent that you run wherever the work should happen — your laptop, a spare box, a VM.

That split is the design, not an implementation detail: the component holding your data has no browser, and the component driving browsers holds no data. Agents dial out, so nothing needs an inbound port or a public address.

Some of what that has meant in practice:

- The agent refuses loopback, RFC1918 and cloud-metadata addresses outright, and normalises IPv4-mapped IPv6 before checking — SSRF protection with no environment opt-out.
- Releases are gated end to end across repositories: bring the stack up, create the owner, install the *published* agent release through the same one-liner the UI prints, verify its checksum, record a workflow on a real browser, replay it, and call it over both REST and MCP. A tag is not cut until that passes — and you can run the same gate yourself with `./scripts/release-e2e.sh`.
- AGPL-3.0, with a live `GET /api/about` source offer, because §13 asks for one and network services are where that obligation actually bites.

## Start here

- **[Quickstart and documentation](https://github.com/usewrit/writ/wiki)** — setup, deployment, operations
- **[usewrit.app](https://usewrit.app)** — the project site
- **[Security policy](https://github.com/usewrit/writ/blob/main/SECURITY.md)** — how to report an issue

Maintained by **[Benjamin Garcia](https://github.com/benjamingarciaMTL)**. Issues and pull requests are welcome on any repository above.
