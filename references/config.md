# Configuration reference — scrapling-node

File: `config.toml` (copy from `config.example.toml`). Every option can be
overridden by env var `SIP01_<OPTION_UPPER>` (lists comma-separated).
Exception: the indexer key — `SIP01_NSEC` is **env-only**, never read from a file.

## Options

| option | default | meaning |
|---|---|---|
| `seeds` | `[]` | seed URLs to start from (repeatable `--seed URL` on CLI) |
| `relays` | v3 network set | relay fan-out list (see below) |
| `source` | `crawlstr/v3` (indexstr repo: `indexstr/v3`) | source tag; `indexstr/v3` enables kind-16919 heartbeat + intake flags |
| `data_dir` | `./data` | sqlite queue/outbox, adaptive store, generated `indexer.key` |
| `max_depth` | `3` | same-domain link discovery depth (0 = seeds only) |
| `per_domain_delay` | `5.0` | min seconds between requests per domain (robots crawl-delay wins) |
| `max_page_kb` | `512` | page size cap |
| `hourly_bandwidth_mb` | `256.0` | rolling hourly bandwidth budget; crawling pauses when exhausted |
| `adaptive_store` | `false` | persist Scrapling adaptive (selector-relocation) store in data dir |
| `stealth` | `auto` | `off` = pinned HTTP only · `auto` = browser escalation only with SIP01_SAFE_EGRESS_PROXY · `always` = requires proxy and starts at stealth tier |
| `concurrency` | `4` | concurrent fetches across domains (never per domain — that stays 1) |
| `log_level` | `INFO` | logging verbosity |
| `fetch_timeout` | `30.0` | hard per-fetch ceiling (browser tiers included) |
| `publish_timeout` | `15.0` | relay OK-ack timeout |
| `rss_discovery` | `true` | enqueue RSS/Atom/sitemap links found in page heads |

## SSRF and browser egress\n\nThe static HTTP tier resolves every hostname before connecting, rejects any non-global A/AAAA answer, pins the actual libcurl connection with CURLOPT_RESOLVE, and validates each redirect hop manually before connecting to it. Automatic redirects and environment proxy inheritance are disabled in this tier. Do not bypass this path or loosen its checks.\n\nBrowser tiers can make redirects and additional requests for scripts, images, fonts and frames, so checking only the page URL is insufficient. They are disabled unless SIP01_SAFE_EGRESS_PROXY is set to an operator-verified HTTP(S) egress proxy. The proxy must apply destination/IP checks to every request and CONNECT tunnel, block loopback/private/link-local/CGNAT/reserved/multicast/metadata targets, re-check DNS at connection time, and cap redirects. Apply host/container firewall policy so browser traffic cannot bypass the proxy. Configure the URL as an environment secret, not in config.toml; never log it.\n\nWith stealth=auto and no proxy, only the pinned static HTTP tier runs. With stealth=always and no proxy, startup/fetch fails closed.\n\n## Default relay set

```toml
relays = [
  # SIP-01 index relays
  "wss://relay-na1.metanomalist.com",
  "wss://test-sip-relay.sip-01test.workers.dev",
  "wss://sip-relay-2.sip-booster-relay.workers.dev",
  "wss://sip-relay-3.uncaged-sip.workers.dev",
  "wss://sip-relay-4.sip-relay-4.workers.dev",
  # Search relays (NIP-50)
  "wss://relay.nostr.band",
  "wss://relay.ditto.pub",
  "wss://search.nos.today",
  "wss://relay.noswhere.com",
  # Write relays (broad propagation)
  "wss://jskitty.cat/nostr",
  "wss://relay.primal.net",
  "wss://relay.damus.io",
  "wss://nostr.hifish.org",
]
```

Keep at least one SIP-01-aware index relay in the set; add relays freely.

## Minimal production config

```toml
seeds = ["https://bitcoin.org", "https://nostr.com"]
source = "crawlstr/v3"        # or "indexstr/v3"
data_dir = "./data"
stealth = "auto"
```

## CLI flags

```
python -m sip01_node [--config config.toml] [--source crawlstr/v3|indexstr/v3]
    [--seed URL ...] [--once] [--dry-run] [--selftest]
```

`--selftest` runs spec §13 vectors and exits · `--dry-run` builds + validates
events but publishes nothing · `--once` exits when the queue drains.
