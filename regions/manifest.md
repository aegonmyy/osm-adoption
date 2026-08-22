# Region manifest — 28 entries, 26 covered countries

Final selection (verified against the frozen 72-region set in
`logos-osm/methods/osm-core/src/set.rs` on 2026-08-22 — every path below is a
member). Bars: ≥15 countries AND ≥25 total verified entries → this gives
**26 countries / 28 entries** (3-entry margin). Every entry gets hosted in
Logos Storage + registered on-chain by **all 4 registrars** over the
sustained-activity window.

Country-coverage rule (spec): a country counts via its country-level entry,
OR (us/india/china/russia only) via ≥1 registered subregion.

URL pattern: `https://download.geofabrik.de/<path>-latest.osm.pbf`;
MD5: same URL + `.md5`. `size` recorded at first host.

> **Runner argument = the region id (the `path` minus the `…/` prefix),
> NOT the path.** `adoption_host`/`adoption_register` reject `europe/austria`
> ("not in the predefined region set"); pass `austria`. Subregions keep their
> slash: `us/california`, `india/southern-zone`. The id column below is the
> value to pass; the path is the Geofabrik URL segment.

## Country-level (22 countries)

| # | path | country | size (B) | note |
|---|------|---------|----------|------|
| 1 | europe/austria | Austria | — | |
| 2 | europe/belgium | Belgium | ~660 MB | measured 2026-08-22 |
| 3 | europe/czech-republic | Czechia | — | set id `czechia` |
| 4 | europe/denmark | Denmark | ~470 MB | measured |
| 5 | europe/finland | Finland | — | |
| 6 | europe/greece | Greece | ~323 MB | measured; smallest sampled |
| 7 | europe/hungary | Hungary | — | |
| 8 | europe/ireland-and-northern-ireland | Ireland | — | |
| 9 | europe/netherlands | Netherlands | — | |
| 10 | europe/norway | Norway | — | |
| 11 | europe/poland | Poland | — | |
| 12 | europe/portugal | Portugal | — | |
| 13 | europe/romania | Romania | — | |
| 14 | europe/switzerland | Switzerland | — | |
| 15 | europe/sweden | Sweden | — | |
| 16 | europe/bulgaria | Bulgaria | — | |
| 17 | europe/ukraine | Ukraine | — | |
| 18 | europe/belarus | Belarus | — | |
| 19 | africa/kenya | Kenya | ~333 MB | measured |
| 20 | africa/morocco | Morocco | — | |
| 21 | asia/south-korea | South Korea | — | |
| 22 | south-america/chile | Chile | — | |

## Decomposed subregions (entry depth + us/in/china/russia coverage) (6)

| # | path | covers | size (B) | note |
|---|------|--------|----------|------|
| 23 | us/california | us | — | |
| 24 | us/new-york | us | — | confirmed in set (set.rs:251) |
| 25 | india/southern-zone | india | — | |
| 26 | china/guangdong | china | — | |
| 27 | china/zhejiang | china | — | |
| 28 | russia/central-fed-district | russia | — | Geofabrik serves russia from top level, not europe/ |

## Order of operations

Week 1 seeds: 1–3 (austria, belgium, czech-republic) by all 4 registrars —
smallest first, establishes the ≥3-registrar pattern. Weeks 2–8 add 4–28 in
table order (2–3 per wallet per week, batched via `RegisterRegionsBatch`,
MAX_BATCH=24). Freshness advances ride on top (target 6 regions advanced,
weeks 3–8).
