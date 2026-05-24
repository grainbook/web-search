# web-search-plugin

A grain WASM plugin that adds two tools to the agent:

| Tool | What it does |
|--------------|--------------------------------------------------------|
| `web_search` | Search the live web via [Exa](https://exa.ai), [Tavily](https://tavily.com), [SearXNG](https://docs.searxng.org), or AnySearch |
| `web_fetch` | HTTP GET an arbitrary URL (body truncated to 16 KiB) |

Inspired by [pi-web-access](https://github.com/nicobailon/pi-web-access).

## Prerequisites

```sh
# Install cargo-component
cargo install cargo-component

# Add the WASM target
rustup target add wasm32-wasip2
```

## Build

```sh
cargo component build --release
```

Output: `target/wasm32-wasip2/release/web_search_plugin.wasm`

## Install

Copy the built WASM and the plugin manifest into your grain workspace:

```sh
mkdir -p .grain/plugins/web-search
cp target/wasm32-wasip2/release/web_search_plugin.wasm \
   .grain/plugins/web-search/plugin.wasm
cp plugin.toml .grain/plugins/web-search/plugin.toml
```

Or use `lazy_install` from within grain:

```
lazy_install("web-search", "https://github.com/grain-ai/web-search")
```

### Setting API keys

Three options (highest priority first):

1. **`plugin-spec.toml`** — flat key under `[[plugin]]`:
   ```toml
   # .grain/plugin-spec.toml
   [[plugin]]
   name = "web-search"
   src = "..."
   EXA_API_KEY = "your-key"
   ```

2. **`plugin.toml`** — `[wasm.env]` section:
   ```toml
   # .grain/plugins/web-search/plugin.toml
   [wasm.env]
   EXA_API_KEY = "your-key"
   ```

3. **Shell environment** (fallback):
   ```sh
   export EXA_API_KEY="your-exa-key"
   ```

## Providers

| Provider | Environment variable | Notes |
|----------|---------------------|-------|
| Exa | `EXA_API_KEY` | Free tier available |
| Tavily | `TAVILY_API_KEY` | Cloud API |
| SearXNG | `SEARXNG_BASE_URL` | Self-hosted |
| AnySearch | `ANYSEARCH_API_KEY` | Cloud, optional key |

## License

MIT OR Apache-2.0
