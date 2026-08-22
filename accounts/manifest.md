# Registrar accounts

One row per registrar. Created 2026-08-22 on the public LEZ testnet
(`https://testnet.lez.logos.co`) via `tests/tests/adoption.rs` (snapshot in
this repo). The 2-month sustained-activity clock starts at the first
on-chain registration; account creation today is the prerequisite.

Wallet material (mnemonic, storage) lives in `~/osm-adoption/regN/` —
**not recorded here, ever.**

| # | label | wallet dir | AccountId (bech32) | AccountId (hex) | created (UTC) | first-reg block |
|---|-------|------------|--------------------|-----------------|---------------|-----------------|
| 1 | osm-mirror-1 | ~/osm-adoption/reg1 | HePemHzZMigwLvFxFpW49tto4TBekLDLYeYUfyy3YRk3 | f74f17a9d7a6f33267798166978d69c6981a9657634a9236a11d30ecf6338f18 | 2026-08-22 22:32 | — |
| 2 | osm-mirror-2 | ~/osm-adoption/reg2 | 5LDFMwSW5RChKBuKKrkwUimqjX11aBU4hd27cXzE9no1 | 405aa1a928afc03071c59d36094a7360fa31682f28d0ad46955d5ef25a815070 | 2026-08-22 22:32 | — |
| 3 | osm-mirror-3 | ~/osm-adoption/reg3 | 2siFz1EhTYGuaddXmAPLkrcVHqzfxKzoMW3b6DCKjMp3 | 1bd960d9598c61074a77d185a16a7484427942ca9de411daba46ffbe89913e88 | 2026-08-22 22:32 | — |
| 4 | osm-mirror-4 | ~/osm-adoption/reg4 | V1yYtux2HERssGGpjrLMYCNZSoiebXbKJkC6bRf1URL | 072d5d766fd19baa9f481139dd954f221f0086086fd19a0fe288bad85abf85af | 2026-08-22 22:32 | — |

> Fill the AccountId columns from each `adoption_create_account` run's
> `ADOPTION_ACCOUNT_ID` / `ADOPTION_ACCOUNT_HEX` output once they land. The
> hex is what the on-chain registry keys a registrar by.
