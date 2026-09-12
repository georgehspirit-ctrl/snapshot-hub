# CLAUDE.md

Fork of `snapshot-labs/snapshot-hub`, the GraphQL read API of a self-hosted Snapshot
deployment for a shareholder voting product on Robinhood Chain (RHC, chain id 4663).
The frontend queries this directly. Deploys to Railway from GitHub.

## HARD RULES, NON-NEGOTIABLE

- The user never runs anything on their machine. Never output a command for them to
  run locally. No npm, no git clone, no CLI, no scripts, no downloads. Their machine
  holds wallets. Zero risk.
- All work happens in the cloud session and in Railway. Deploys are
  Railway-from-GitHub only.
- Never ask for, log, or handle private keys or seed phrases. Secrets are Railway
  environment variables the user sets; refer to them by name only.
- Before anything that involves a wallet, funds, secrets, a paid API, or an external
  account, stop and ask. List exactly what you need, why, and where the user enters
  it. Do not proceed until answered.

## One space per whitelisted ticker

Space ids are lowercase ticker symbols — `nvda`, `aapl`. The write path enforces it
(`SPACE_ALLOWLIST` in the sequencer fork); this service must not grow a way around
it.

## Read-only

This service holds no keys and writes nothing a voter can see. Every mutation goes
through the sequencer. Do not add a signer here.

## Environment

| Name | Notes |
| --- | --- |
| `PORT` | 3000 |
| `NETWORK` | `mainnet` |
| `HUB_DATABASE_URL` | MySQL, database `snapshot_hub` |
| `SEQ_DATABASE_URL` | MySQL, database `snapshot_sequencer` |
| `SEQUENCER_URL` | sequencer public URL |
| `SCORE_API_URL` | score-api public URL |
| `RATE_LIMIT_DATABASE_URL` | Redis, optional |

`@snapshot-labs/snapshot.js` is pinned to `^0.17.2` — the first published version
whose `networks.json` carries RHC 4663.

Builds to `dist/`, not `build/`. The sequencer is the other way round; do not copy a
start command between the two.

## Schema

Nothing migrates on boot. `src/helpers/schema.sql` is loaded by hand into the
`snapshot_hub` database through Railway's query console. A hub that boots cleanly but
answers every query empty means this step was skipped.

## Reporting

After each task: what you did, what's blocked, what you need from the user, in that
order. Short.
