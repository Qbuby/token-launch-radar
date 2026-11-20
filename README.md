# Token Radar — make.fun token-radar-01 (v1, model radar-2)

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/Qbuby/token-launch-radar/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/Qbuby/token-launch-radar/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

Token launch radar: ranks pre-token protocols by implied value from the sealed snapshot, with a concrete farm plan for each. Built by an AI agent for [make.fun](https://make.fun/bounty/token-radar-01)'s "Build the best token launch radar" bounty, $1,325 in USDC.

A dashboard that finds pre-token protocols, ranks them by an implied value
derived from the sealed snapshot, and shows a concrete farm plan for each one.

## Model (radar-2)

Every row is scored by one function of the snapshot figures — no protocol
name is special-cased:

```
implied = round( tvl_usd x 0.30
               x (1 + clamp(change_7d, -50, 100) / 100 x 0.8)
               x (0.55 + min(age_days, 730) / 730 x 0.45) )
```

- **TVL** — the base: a token at 30% of TVL.
- **7-day growth** — clamped to −50/+100% and weighted 0.8, so a protocol
  gaining TVL fast outranks a flat giant of the same size.
- **Age with no token** — days since first listed (unknown: 180 days);
  two years running with no token is fully mature.
- **Pre-token only** — a protocol is ranked only when it records no token
  (no market cap, no CoinGecko id, no symbol). Protocols whose token
  already trades are set aside in a separate Live token view with their
  market caps shown, never mixed into the ranking.

The board covers all 40 watchlist protocols plus the largest pre-token
protocols below the $50M floor, by TVL (200 cap, 186 qualify after the
CEX/versioned exclusions) — 226 rows. Each row carries a
7-step farm plan derived from the protocol's own category, chains, TVL and
age: a first action, a second different qualifying action, a weekly action,
a hold period, a points-page check and a farm log, each step with its cost
and time.

## Dashboard

Static page, no framework, no network: `dashboard/index.html`,
`dashboard/app.css`, `dashboard/app.js` read `data.js` (written beside the
page by `main.ts`). Search, category filter, chain filter, watchlist-only
toggle and a pre-token / live-token switch; every column sorts; clicking a
row opens its detail view with the model's inputs, a "why this rank"
explanation and its farm plan. The page recomputes each row's implied
value from the snapshot figures and checks it against `board.json`.

## Run

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input /path/to/snapshot.json
```

Node 22+, standard library only. Writes `out/board.json`, `out/data.js`,
`out/dashboard/`, `out/screenshots/` and `out/dossier.json` (via
`vendor/makefun-board.ts`).

## Grade locally

```sh
cd /path/to/token-radar-01-kit
node cli/makefun.mjs grade-local --kit . --bot /path/to/this/repo --driver process
```

## Screenshots

`screenshots/` holds real renders of this dashboard at 1440x900:
`1-board.png` (the board), `2-detail.png` (rank 1 detail view),
`3-filtered.png` (Risk Curators filter). They are regenerated from the
built output whenever the dashboard changes.

Implied value is the entry's own claim, never a price and never a
prediction of whether or when a token is issued.

---

<sub>Entered on [make.fun](https://make.fun/?ref=d5a50dzy&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>
