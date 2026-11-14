# Token Launch Radar

A token launch radar for the make.fun `token-radar-01` bounty. It ranks
pre-token DeFi protocols by an implied value, so the protocols most likely
to launch a token — and most worth farming — show up first.

## What it scores

Snapshot: DefiLlama protocol index, 2026-11-14, 758 protocols. The
watchlist is 40 protocols with at least $50M of TVL and no token on
record; the radar adds 186 pre-token protocols below that floor, for
226 rows in total. 24 live-token protocols are set aside, not ranked.

## The model (radar-2)

One function scores every protocol, from snapshot figures only — no
hardcoded names:

```
implied = TVL x category multiple x growth multiplier
              x maturity multiplier x chain multiplier
```

- **Category multiple:** 0.12–0.30 by category (Liquid Staking 0.12,
  Dex / Lending / Perpetual 0.30, Yield / Curators 0.25, CDP / RWA 0.22,
  Chain 0.18, default 0.22).
- **Growth multiplier:** `1 + clamp(7-day change, -60, +120) / 100 x 0.5`.
- **Maturity multiplier:** `0.6 + min(age in days, 1095) / 1095 x 0.4`.
- **Chain multiplier:** `1 + 0.015 x min(chains - 1, 8)`, up to 1.12.

Each row also carries a farm plan: 7 concrete steps with cost and time
(fresh wallet, the main deposit, a weekly repeat action, a hold period,
claiming only from the protocol's own page, a test exit, and a farm log).

## Run it

Requires Node.js 22.18 or later.

```sh
MAKEFUN_OUTPUT_DIR=out node main.ts --input ../kit/input/snapshot.json
```

That writes `out/board.json`, `out/data.js`, `out/dashboard/` and
`out/dossier.json`. The dashboard in `dashboard/` is static and renders
with no network: open `out/dashboard/index.html` after a run, or
`dashboard/index.html` with `data.js` beside it.

## Layout

- `main.ts` — entrypoint: reads the snapshot, scores, writes the outputs.
- `lib/model.ts` — the radar-2 model and farm-plan builder.
- `dashboard/` — static dashboard (sortable, filterable, detail view).
- `vendor/makefun-board.ts` — board writer from the bounty kit.
- `screenshots/` — board, protocol detail, and filtered view.
