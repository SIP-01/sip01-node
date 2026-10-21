# sip01-node — agent skill

Teach any AI agent to install, verify, and operate a **SIP-01 Scrapling node** —
the headless Python crawler that publishes [SIP-01](https://github.com/NostrDanish/SIP-01)
kind 39697 web index observations to the decentralized search index on Nostr.

## Use it

Point your agent at this repo:

> *"Read `SKILL.md` from github.com/SIP-01/sip01-node and set up a SIP-01 node on my server."*

The skill covers: Docker + pip install paths, the mandatory verification ritual
(`scripts/verify_install.sh`), full config reference, key hygiene
(`SIP01_NSEC` env-only), crawlstr vs indexstr source modes, updates, key
rotation, and troubleshooting.

## Layout

```
SKILL.md                     — the skill (agent entry point)
references/config.md         — every config option + env overrides + relay set
references/operations.md     — run modes, updating, key management, troubleshooting
scripts/verify_install.sh    — deterministic post-install verification ritual
```

## The node it operates

- [`SIP-01/Crawlstr-v3`](https://github.com/SIP-01/Crawlstr-v3/tree/main/scrapling-node) → `scrapling-node/` (`source=crawlstr/v3`)
- [`SIP-01/indexstr-v3`](https://github.com/SIP-01/indexstr-v3/tree/main/scrapling-node) → `scrapling-node/` (`source=indexstr/v3`)

Also mirrored into both repos at `.agents/skills/sip01-node/`.

## License

MIT
