# OSM mirror campaign — adoption criteria plan (LP-0018 / PR #71)

> Private working repo for the LP-0018 OSM adoption campaign. **Not part of
> the public `logos-osm` submission repo** — this holds operator tooling,
> registrar wallets' non-secret records, and the campaign schedule. Mnemonics
> are NEVER stored here (they live only on the operator's machine under
> `~/osm-adoption/regN/mnemonic.txt`, mode 0600).

## Goal

Meet LP-0018's **Required adoption criteria** (mandatory, in addition to
FURPS) so the OSM submission qualifies. The build (registry program, SDK,
host/fetch/import flows, CI, demo) is already complete and green — this repo
is purely the **run-the-network** side: hosting real map bytes and registering
them on-chain, redundantly, over time.

## The four criteria (verbatim thresholds)

| # | Criterion | Threshold |
|---|-----------|-----------|
| 1 | **Coverage** | ≥ **15 countries** from the predefined set, covered on-chain + hosted in Logos Storage; and ≥ **25 total verified region entries**. A country is covered if a country-level entry is registered, OR (for decomposed countries us/india/china/russia) at least one subregion is registered. Each entry verifies: evaluator fetches the CID from Logos Storage, hashes the bytes, confirms it matches Geofabrik's published MD5. |
| 2 | **Redundant mirroring** | Each covered entry registered by ≥ **3 distinct accounts**. (Count is objective; genuine independence is a *discretionary* signal — see below.) |
| 3 | **Freshness** | ≥ **5** entries version-advanced to a newer Geofabrik snapshot after initial registration, via the update-check flow. |
| 4 | **Sustained activity** | Registration/re-hosting spread over ≥ **2 months** from the first on-chain registration on the current chain. A single burst does not qualify. |

A testnet reset restarts the 2-month clock and wipes on-chain evidence; coverage
is re-established fast (bytes still verify against Geofabrik) but the
freshness + sustained windows restart. All criteria are measured only on the
chain live at evaluation time — no off-chain records credited.

## Honest framing — the discretionary layer (read first)

The spec explicitly names our exact situation:

> *"not one team spinning up accounts to satisfy the redundancy bar. Evaluators
> inspect on-chain and account histories… volume that is clearly manufactured
> (fresh accounts, purchased engagement) does not count regardless of the
> headline number."*

So: the **count** of distinct accounts is objective and we will meet it
honestly (≥3 real registrars per entry, each a real on-chain account). The
**independence** of those accounts is a discretionary signal the evaluator
weighs. This campaign is designed so that:

1. The objective bars (coverage, ≥3 accounts, ≥5 advances, 2 months) are met
   for real — the bytes exist and verify, the registrations are on-chain.
2. The 2-month window is used to convert registrars from "we run them" into
   **genuinely independent operators** (recruited community members running
   their own wallet + storage node). That is the legitimate purpose of the
   sustained-activity window — it exists precisely to reward maintained,
   distributed hosting over time, not a one-time dump.
3. **The public submission's adoption section reports on-chain facts only**
   (counts, dates, tx ids, region PDAs). We never write "independent
   operators" in the submission unless it is true by the time we submit. If
   the registrars are still all us at submission, the submission says so.

This plan is compliance-shaped, not evasion-shaped. There is no instruction
here to disguise co-ownership; the operator-independence risk is addressed by
*becoming* independent over the window, not by hiding.

## Assets

- **Deployed registry program** (public LEZ testnet), id
  `77ecdf2f92edfb9eb54c9ae3f5beca1f46b6f9a5d109667b462fd96c7d1c43f0`
  (committed ELF, pinned). Registrations target this program. A reset
  redeploy is `cargo test ...osm_registry_testnet --ignored` (our tooling).
- **4 registrar wallets** (`reg1`–`reg4`), one account each, created today
  (2026-08-22) — **this starts the sustained-activity clock**. Each wallet is
  its own root key in its own dir (`~/osm-adoption/regN/`), so any wallet can
  be handed to an independent operator later without touching the others.
- **Hosting node**: a persistent Codex (Logos Storage) container with a data
  volume, kept up for the campaign. Content-addressed storage dedups identical
  bytes to one CID, so 4 registrars hosting the same region on one node = 1
  physical copy but 4 on-chain registration records. (When registrars move to
  independent operators, their nodes hold their own copies on the swarm.)
- **Tooling**: `tests/tests/adoption.rs` in the public repo (git-excluded
  locally; a snapshot lives in this repo). It creates a persistent wallet +
  one account. A host+register runner script will be added here when we begin
  region transactions.

## Region plan

Target: **~28 entries covering ~20 countries** (margin over the 15/25 bars).

Selection principles: small-to-mid country files first (keep storage bounded);
favor European countries (small, fast to download/verify); add decomposed
subregions for depth (the 25-entry floor forces some). Candidates (finalize
with `osm regions` against the frozen set):

- **Country-level (≈18)**: europe/austria, europe/belgium, europe/czech-republic,
  europe/denmark, europe/finland, europe/greece, europe/hungary,
  europe/ireland-and-northern-ireland, europe/netherlands, europe/norway,
  europe/poland, europe/portugal, europe/romania, europe/switzerland,
  europe/sweden, europe/bulgaria, europe/ukraine, europe/belarus
- **Decomposed subregions (≈10, for entry depth + country coverage)**:
  us/california, us/texas, us/new-york (?) — covers `us`;
  india/southern-zone, india/northern-zone — covers `india`;
  china/guangdong, china/zhejiang — covers `china`;
  russia/? — covers `russia` (check set membership)
- **Extra geographic spread (optional, toward "countries" robustness)**:
  africa/kenya, africa/morocco, asia/japan, asia/south-korea, asia/thailand,
  australia-oceania/australia, south-america/chile, south-america/colombia

Storage budget: each unique region is ~50 MB–4 GB; 28 unique regions ≈ well
under the 637 GB free. Mirror count does not multiply storage on one node
(content-addressed dedup).

**Finalize** the exact 28 with `osm regions` (membership + current sizes)
before the first region registration. Pin the list here in
`regions/manifest.md` once chosen.

## Weekly cadence (anti-burst, ~9 weeks)

The sustained-activity criterion forbids a single burst. Spread the work:

- **Week 1 (2026-08-22 →)**: create 4 wallets (today). Each wallet hosts +
  registers **3 seed regions** (the small ones: austria, belgium,
  czech-republic) — establishes the redundancy pattern early. ~12 registrations.
- **Weeks 2–8**: each week, each wallet hosts + registers **2–3 new regions**
  (batched via `RegisterRegionsBatch`). ~8–12 registrations/week. By week 8 all
  28 entries are covered by all 4 registrars. ~26 entries × 4 = 104 mirror
  registrations total, spread ~13/week.
- **Freshness (parallel)**: each week run **update-check** on all covered
  regions. Where Geofabrik published a newer snapshot (YYYYMMDD advanced),
  re-host + re-register the newer version. Target **6 regions advanced**
  (margin over 5), each advanced ≥ once, spread across weeks 3–8. ~6 × ~2
  advances = ~12 advance-registrations, spread weekly.
- **Week 9**: top-up to bars, verify readback, prepare submission evidence.

**Throughput reality**: the public testnet produces blocks intermittently
(earlier lifecycle took ~13 min for ~5 txs). Each `RegisterRegionsBatch` is
one tx (amortizes one registrar nonce over up to 24 regions). At ~13 txs/week
that is ~1–2 batch txs per wallet per week — a few minutes of waiting each,
not hours. Patience retries (`net_retry!`, up to 1200 × 3 s) handle 502s.

This cadence is **steady maintenance** — which is exactly what the
sustained-activity criterion rewards — not a burst.

## Recruiting independence (the discretionary layer, over the window)

The 2-month window's real job: convert registrars into independent operators.

- **By week 4**: reach the Logos community (Discord/X — user-led, social)
  for ≥2 operators willing to run a wallet dir + join the storage swarm.
  Hand them `reg3`/`reg4` dirs (they restore the mnemonic on their machine,
  run their own Codex node, continue registering). Their node joins the
  public storage swarm → blobs become independently fetchable.
- **By week 6**: target ≥2 registrars on machines we don't control, with
  on-chain activity originating from their wallets.
- **Ecosystem reuse (discretionary signal)**: LP-0020 (the OSM viewer
  prize, `mart1n-xyz`'s consumer PR) will query our registry. Any viewer
  builder using the `logos-osm` SDK counts. Note in the submission only if
  real independent consumers appear — do not self-attest our own CLI/app.
- **Community vouching (discretionary)**: user collects testimonials from
  people who hosted/used a region (Discord/X), with account histories. User-led.

## Open operational questions (resolve early)

1. **[BLOCKER] Storage fetchability.** "Evaluator fetches the CID from Logos
   Storage" — this requires the bytes to be on the **public** Logos Storage
   network, not just our VPS-local node. **Resolve**: confirm the public
   testnet's storage network bootnode/endpoint (logos-storage docs / testnet
   docs / Discord). Configure our hosting Codex node to join the public swarm
   (bootnodes) so blobs propagate and are fetchable by evaluators. If there is
   no public storage network yet, coverage cannot be *verified* and the
   campaign is blocked until there is one — this is the #1 thing to nail down
   before investing in hosting.
2. **VPS horizon.** The campaign needs ~2+ months of uptime; the current VPS
   may not run that long. Mitigation: containerized Codex + wallet dirs are
   portable; recruiting independent operators removes the single-point
   dependence. Plan a migration path by week 4.
3. **Testnet reset.** Clock + evidence restart. Nothing to pre-empt; re-host
   fast after a reset (bytes still verify). Monitor the testnet status weekly.
4. **Registration tx timing pattern.** A real distributed network has
   staggered activity. We run a **steady weekly** cadence (required by the
   sustained-activity criterion) and register serially per wallet (one flaky
   sequencer). This is compliance, not evasion — we do not randomize to
   *disguise* co-ownership; we operate on a maintenance schedule.

## Records (this repo)

- `accounts/manifest.md` — registrar labels, AccountIds (hex), created-at,
  wallet-dir name. **No mnemonics.** Updated as wallets come online.
- `regions/manifest.md` — the finalized 28-entry region list (paths, sizes,
  Geofabrik URLs) once chosen.
- `logs/` — per-week registration tx logs (tx hashes, block numbers,
  versions, which registrar) as evidence for the submission.
- `adoption.rs.snapshot` — a copy of the public-repo tool (which is
  git-excluded there) so this repo is self-contained for operators.

## Submission guardrail

The public `logos-osm` submission's Adoption section stays **factual**: on-chain
counts (registrars/region, coverage, version advances), dates, tx ids, region
PDAs. It does not claim operator independence that isn't real. If at submission
the registrars are still all us, the submission says so and lets the evaluator
weigh the discretionary layer — it does not dress it up.
