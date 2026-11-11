# SIP-01 Node Skill

Portable Agent Skill for installing, verifying and operating the SIP-01 Scrapling crawler node.

## Install

Clone the skill repository into a directory named sip01-node so that the directory matches the skill frontmatter:

~~~bash
git clone https://github.com/SIP-01/sip01-node <skills-root>/sip01-node
~~~

## Runtime support

The actual crawler implementation is maintained in the scrapling-node directories of:
- https://github.com/SIP-01/Crawlstr-v3
- https://github.com/SIP-01/indexstr-v3

Python 3.11+ is required because the runtime imports standard-library tomllib. Python 3.10 is not supported by the current code.

The node creates a per-node indexer identity in indexer.key with mode 0600 by default. It does not support SIP01_NSEC_FILE. The external-key environment override is SIP01_NSEC only; inject it from a protected secret source and never place it in command arguments or a committed file.

Run the protocol selftest and dry-run before enabling publishing. Public observations require explicit authorization.
