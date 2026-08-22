# Operator runner — host + register

The runner lives in the **public** `logos-osm` repo at
`tests/tests/adoption.rs`, but it is git-**excluded** there (`.git/info/exclude`)
so it never ships in the submission. This snapshot
(`../adoption.rs.snapshot`) is the same file, kept here so operators are
self-contained.

All commands run from `~/logos-osm`. Region ids (not paths) are the argument
— `austria`, `belgium`, `czech-republic`, `us/california`, … (see
`../regions/manifest.md`; the id is the first column's short form).

## 0. Prereqs (one-time per VPS)

- `vault-codex` (Logos Storage) up: `docker start vault-codex` (REST on
  `127.0.0.1:8080`). **Open question (BLOCKER #1):** this is loopback-only;
  evaluators must fetch the CID from the *public* Logos Storage network. Until
  a public storage swarm / reachable node is confirmed, registrations are
  on-chain but not yet evaluator-fetchable. See `../docs/ADOPTION_PLAN.md` §
  "Open operational questions".
- Public LEZ testnet reachable: `curl -s https://testnet.lez.logos.co/health`
  (or just watch the sync).

## 1. Create a registrar (once per operator)

```sh
ADOPTION_WALLET_DIR=~/osm-adotion/regN \
ADOPTION_ACCOUNT_LABEL=osm-mirror-N \
cargo test -p osm-integration-tests --test adoption adoption_create_account -- --ignored --nocapture
```
Writes `mnemonic.txt` (0600 — BACK UP), `storage`, `config.json`,
`statistics.json`, and `account.txt` (hex AccountId) into the wallet dir.
~15 min (cold sync of ~19k blocks + the account tx). Refuses to clobber an
existing wallet.

## 2. Host regions (once per region per snapshot version)

```sh
ADOPTION_REGIONS=austria,belgium,czech-republic \
cargo test -p osm-integration-tests --test adoption adoption_host -- --ignored --nocapture
```
Downloads each PBF from Geofabrik, verifies the published MD5, stores the
verified bytes in Logos Storage, and appends a `HostedSnapshot` JSON line
to `~/osm-adoption/snapshots.jsonl` (region, cid, checksum, version, bytes,
path). The CID is content-derived → identical bytes host to one CID; every
registrar's registration of that snapshot points at the same blob.

## 3. Register (once per registrar per snapshot)

```sh
ADOPTION_WALLET_DIR=~/osm-adoption/reg1 \
ADOPTION_ONLY=austria,belgium,czech-republic \
cargo test -p osm-integration-tests --test adoption adoption_register -- --ignored --nocapture
```
Reopens the wallet, cold-syncs, reads `account.txt` for the registrar, and
submits one `RegisterRegion` per matching snapshot line, signed by that
registrar. Prints `REGISTERED region=… registrar=0x… cid=… version=… tx=0x…`
per registration. Repeat with `reg2`, `reg3`, `reg4` for the 3×-mirror bar.

## Cadence

Per `docs/ADOPTION_PLAN.md`: ~3 seed regions × 4 registrars in week 1, then
2–3 new regions/week per registrar (batched), spread over ≥2 months. Freshness
advances ride on top. **Never burst** — the sustained-activity criterion
rejects a single dump.
