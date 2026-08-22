# osm-adoption (private)

Private working repo for the **LP-0018 / PR #71 (OpenStreetMap integration)
adoption campaign** — the run-the-network side of our `logos-osm` submission
hedge #2. This repo is **NOT** part of the public `logos-osm` submission
(`~/logos-osm`); it holds the campaign plan, registrar manifests, evidence
logs, and operator scripts. **No secrets here.**

## Layout

```
docs/ADOPTION_PLAN.md   the plan: how we meet the 4 required criteria + the discretionary layer, honestly
accounts/manifest.md    registrar labels, AccountIds (hex), created-at. NO mnemonics.
regions/manifest.md     the finalized ~28-entry region list (paths, sizes, Geofabrik URLs)
logs/                   per-week registration tx logs (tx hashes, blocks, versions, registrar)
ops/                    host+register runner scripts, update-check runner
adoption.rs.snapshot    copy of tests/tests/adoption.rs from logos-osm (git-excluded there)
```

## Where the secrets actually live

Registrar wallets are in **`~/osm-adoption/regN/`** (a *separate* directory
from this repo): `config.json`, `storage`, `mnemonic.txt` (mode 0600). The
mnemonic is the root key for that one registrar account — back it up, never
commit it anywhere, and treat it as the thing that lets an independent
operator take over that registrar later.

## Guard rail

`.githooks/pre-commit` refuses any commit whose path or content looks like
wallet material. Install it once:

```sh
git config core.hooksPath .githooks
```

The `.gitignore` also excludes `mnemonic.txt`, `storage`, `config.json`,
`statistics.json` anywhere in the tree.

## Honest framing (read this first)

LP-0018's discretionary layer explicitly warns against "one team spinning up
accounts to satisfy the redundancy bar." The objective bars (coverage, ≥3
accounts/entry, ≥5 version advances, 2-month sustained activity) we meet for
real — the bytes exist and verify against Geofabrik, the registrations are
on-chain. The 2-month window's real job is to recruit **genuinely independent
operators** to take over registrars and run their own storage nodes. The
public submission reports on-chain facts only and never claims operator
independence that isn't true by submission time. See `docs/ADOPTION_PLAN.md`.
