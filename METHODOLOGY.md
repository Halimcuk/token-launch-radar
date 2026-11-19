# Methodology — Token Radar, model radar-2

## The question

For every protocol in the snapshot that records no token, what would a token launch be worth
if the protocol's record stayed as it is? The answer is a model, not a prediction: it says
nothing about whether or when a token is issued.

## The snapshot

- Source: DefiLlama protocol index, fetched 2026-11-14.
- 758 protocols at or above $5,000,000 of TVL.
- Watchlist: 40 protocols at or above the $50,000,000 floor that record no token and are not
  a wrapped, staked or bridged product of a token that already trades.
- Pre-token protocols (no market cap, no CoinGecko id, no symbol): 342, of which 198 are
  rankable: the 40 watchlist protocols plus 158 more below the floor that are not a wrapped,
  staked or bridged product of a token that already trades (not a Bridge, Canonical Bridge,
  Liquid Staking, Liquid Restaking, Restaked BTC or CEX category; no whole word wrapped,
  staked or staking in the name; no exchange-name prefix; no version marker). The board
  ranks all 198. The snapshot carries no parentProtocol field, so the early rows are held to
  the rule's observable parts — category and name — while the watchlist itself was built
  with the full rule.

## The multiple: the market prices launches

A flat multiple (say, 0.25x TVL for every protocol) ignores that the market prices launches
differently by category. The snapshot itself carries the evidence: its live-token protocols
record both a market cap and a TVL.

- **Comparables.** A live-token protocol counts when it records a market cap above zero and a
  TVL above zero, is not an exchange, and its name carries no version marker. 118 protocols
  qualify.
- **Category multiple.** For a category with at least 3 comparables, the multiple is the
  median of `mcap_usd / tvl_usd` over that category's comparables. Median, not mean, so one
  thin protocol cannot drag the number.
- **Fallback.** A category with fewer than 3 comparables, and a protocol with no category,
  use the snapshot-wide median over all 118 comparables: 0.2410.
- **Clamp.** Every multiple is clamped to [0.05, 1.00], so one thin comparable set cannot
  dominate the board.

Category multiples used (13 categories meet the 3-comparable bar):

| Category | Comparables | Multiple |
| --- | --- | --- |
| Cross Chain Bridge | 4 | 1.0000 (clamped from 1.2205) |
| Privacy | 3 | 0.9691 |
| Canonical Bridge | 8 | 0.7464 |
| Basis Trading | 7 | 0.6544 |
| Bridge | 6 | 0.5567 |
| Dexs | 11 | 0.3424 |
| RWA | 9 | 0.2748 |
| Yield | 9 | 0.1914 |
| CDP | 3 | 0.1687 |
| Yield Aggregator | 4 | 0.1510 |
| Indexes | 3 | 0.1477 |
| Lending | 14 | 0.1306 |
| Liquid Staking | 9 | 0.0500 (clamped from 0.0406) |
| Every other category | — | 0.2410 (snapshot-wide median) |

## The other three factors

- **Growth.** Seven-day TVL change, clamped to [-60, +120] per cent so one wild week cannot
  dominate, weighted by 0.5: `growthMult = 1 + growth / 100 x 0.5`. The snapshot's largest
  seven-day move is +4,448%, which the clamp holds at +120%.
- **Maturity.** How long the protocol has run with no token: `0.6 + min(age_days, 1095) /
  1095 x 0.4`, so a protocol tracked for three years scores 1.0 and a new one 0.6. Age is the
  days from `listed_at` to the snapshot date; when `listed_at` is absent, 365 days are used.
  Median age in the snapshot is 677 days.
- **Breadth.** `1 + 0.02 x min(chains, 5)`: a protocol live on five or more chains scores
  1.10, a single-chain one 1.02. Distribution is the reach of the protocol, not a claim about
  any one chain.

## The formula

```
implied = max(1, round( tvl_usd x multiple x growthMult x maturityMult x breadthMult ))
```

No protocol name is special-cased. The same function scores every row, and the dashboard
recomputes each row from the same figures and says so when the numbers agree.

## The farm plan

Every row carries seven steps, each with a cost and a time, derived from the protocol's own
record — category, chains, TVL, age — never from a hand-written list of names:

1. Fresh wallet hygiene (under $3 of gas, 5 minutes).
2. The category's one action with a capital tier from TVL ($250 at $5B+, $150 at $1B+,
   $100 at $100M+, $50 below), on the protocol's first chain (15 minutes to set up).
3. A check: read whether the protocol's own site runs a points or rewards program, and what
   it counts (nothing to check, 10 minutes). This is a check, not a claim that one exists.
4. One qualifying action a week (about $2 of gas a week, 10 minutes a week).
5. Hold at least 30–60 days by age, so the activity lands in more than one snapshot.
6. Claim only from the link the protocol itself publishes; never pay to claim.
7. Keep a farm log (nothing but the log, 5 minutes).

## What the model does not do

- It does not predict whether or when a token is issued. A protocol can sit on the board
  forever without launching.
- It does not use anything outside the snapshot: no price feed, no social signal, no
  hand-written list of "likely" launches.
- "Records no token" is DefiLlama's record, not proof: a protocol on the board may still
  have a token its record does not carry.
- The multiples are medians of a thin set in some categories (3–4 comparables), and the
  snapshot's seven-day window was a down week (median change -8.22%). Both are stated on
  the dashboard.
