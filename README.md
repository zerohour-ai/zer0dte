# ZER0DTE

![Beta](https://img.shields.io/badge/status-beta-blue) ![MCP](https://img.shields.io/badge/protocol-MCP-green) ![Tools](https://img.shields.io/badge/tools-21-orange) ![License](https://img.shields.io/badge/data-free_tier-brightgreen)

**Real-time options intelligence for AI agents.**

21 MCP tools that give your AI assistant live 0DTE options data: dealer exposure, key levels, regime classification, expected moves, and smart-filtered chains.

No installation. No dependencies. One URL. **Free beta — 3 sessions/day, no registration.**

```
https://mcp.zer0dte.trade/sse
```

---

## How it works

ZER0DTE is an [MCP server](https://modelcontextprotocol.io) — a standardized way for AI models to access real-time data. Add one URL to your AI tool's MCP configuration, and you get 21 specialized tools for 0DTE options analysis.

The data pipeline polls live options data every 60 seconds during market hours, computes dealer exposure metrics (GEX, DEX, VEX, CHEX), and serves structured results through the MCP protocol. All computation happens server-side.

```
Your AI Client ←── MCP Protocol ──→ ZER0DTE Server ←── Compute Pipeline ──→ Live Options Data
```

---

## Quick Start

No API key needed. Add the URL and start using it. Data is live during US market hours (9:30–16:00 ET, Mon–Fri).

### Claude Code

```bash
claude mcp add --transport sse zer0dte https://mcp.zer0dte.trade/sse \
  --header "Authorization: Bearer free-tier"
```

> **Free beta:** 3 sessions/day, 5/week. Limits are temporary — unlimited API keys coming soon.

### Other MCP Clients (Claude Desktop, Cursor, Windsurf)

Add the SSE endpoint with a Bearer token header:

```
URL: https://mcp.zer0dte.trade/sse
Header: Authorization: Bearer free-tier
```

### First question to ask

Once connected, you have two ways to start:

**Minimal:** just say `Hi` — the AI will offer you the current snapshot.

**Or go direct:**

> **What's the 0DTE setup on SPX right now?**

This calls `zer0dte_snapshot` and returns regime, key levels, expected
move, VIX, and flows in one response. From there, drill into any dimension:

**Market state**
- *"Where are the call and put walls?"* → `zer0dte_levels`
- *"Show me GEX distribution by strike"* → `zer0dte_exposure`
- *"What regime are we in?"* → `zer0dte_regime`
- *"What are vanna and charm flows doing right now?"* → `zer0dte_flows`
- *"How much of the expected move has been consumed?"* → `zer0dte_expected_move`
- *"Has anything flipped or broken since my last check?"* → `zer0dte_alert`

**Options data**
- *"Show me the 0DTE chain around ATM"* → `zer0dte_chain`
- *"How tight are spreads on the 5300 put?"* → `zer0dte_liquidity`

**Historical intelligence**
- *"When did the regime last flip?"* → `zer0dte_regime_history`
- *"What happened the last time we were in positive gamma?"* → `zer0dte_pattern`
- *"Find sessions similar to today"* → `zer0dte_compare`
- *"How did levels and regime evolve last Friday?"* → `zer0dte_history`
- *"How often does the call wall actually hold?"* → `zer0dte_accuracy`
- *"Did we break out of the opening range?"* → `zer0dte_opening_range`

**Trade setup**
- *"Suggest 10-delta strikes for an iron condor"* → `zer0dte_strikes`
- *"Should I enter now?"* → `zer0dte_entry_score`
- *"Is there a FOMC event today?"* → `zer0dte_calendar`

**Monitoring & pre-market**
- *"Quick update — where's price vs key levels?"* → `zer0dte_pulse`
- *"What happened overnight? Futures, VIX, gap?"* → `zer0dte_overnight`

All 21 tools documented below.

---

## Tools

### Situational Awareness

| Tool | Description |
|------|-------------|
| [`zer0dte_snapshot`](#zer0dte_snapshot) | Full 0DTE situational awareness in one call |
| [`zer0dte_pulse`](#zer0dte_pulse) | Lightweight monitoring — call every 5-10 minutes |
| [`zer0dte_alert`](#zer0dte_alert) | Regime flips, level breaks, VIX spikes |
| [`zer0dte_calendar`](#zer0dte_calendar) | Economic events, FOMC, early close status |
| [`zer0dte_entry_score`](#zer0dte_entry_score) | Research-backed entry scoring (0-100) |
| [`zer0dte_overnight`](#zer0dte_overnight) | Pre-market: ES/NQ/RTY futures, VIX, gap analysis |

### Dealer Positioning

| Tool | Description |
|------|-------------|
| [`zer0dte_levels`](#zer0dte_levels) | Key price levels from options positioning |
| [`zer0dte_exposure`](#zer0dte_exposure) | GEX/DEX/VEX/CHEX exposure by strike |
| [`zer0dte_regime`](#zer0dte_regime) | GEX regime classification with context |
| [`zer0dte_flows`](#zer0dte_flows) | Vanna and charm flows with drift direction |

### Options Data

| Tool | Description |
|------|-------------|
| [`zer0dte_chain`](#zer0dte_chain) | Smart-filtered 0DTE chain with Greeks |
| [`zer0dte_strikes`](#zer0dte_strikes) | Delta-targeted strike analysis |
| [`zer0dte_liquidity`](#zer0dte_liquidity) | Bid-ask spread, volume, fill probability |
| [`zer0dte_expected_move`](#zer0dte_expected_move) | Expected price range from IV + straddle |

### Historical

| Tool | Description |
|------|-------------|
| [`zer0dte_accuracy`](#zer0dte_accuracy) | Historical accuracy of predicted levels |
| [`zer0dte_history`](#zer0dte_history) | Session snapshots for a specific date |
| [`zer0dte_compare`](#zer0dte_compare) | Find past sessions similar to today |
| [`zer0dte_opening_range`](#zer0dte_opening_range) | Opening range and breakout status |
| [`zer0dte_regime_history`](#zer0dte_regime_history) | Regime transition history with context |
| [`zer0dte_pattern`](#zer0dte_pattern) | Pattern matching: similar past setups and outcomes |
| [`zer0dte_session_log`](#zer0dte_session_log) | Today's reasoning log: regime flips, wall breaks, entry scores |

---

## Tool Reference

### `zer0dte_snapshot`

Full 0DTE situational awareness in one call. Returns regime classification, key levels (gamma flip, call/put walls, max pain), expected move, volatility context, exposure totals, and flow summary.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `symbol` | string | `SPX` | Underlying symbol |

**Returns:** regime, levels, expected move, ATM IV, put/call ratio, total GEX/DEX, flow sentiment.

---

### `zer0dte_levels`

Key price levels derived from options positioning. Strikes where dealer hedging creates support, resistance, and magnetic effects.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `symbol` | string | `SPX` | Underlying symbol |

**Returns:** gamma flip point, call wall, put wall, max pain, highest OI strike, top 5 GEX strikes, support/resistance levels.

---

### `zer0dte_exposure`

GEX/DEX/VEX/CHEX exposure distributed across strikes. Shows where dealer hedging activity is concentrated.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `symbol` | string | `SPX` | Underlying symbol |
| `strike_range` | integer | `20` | Strikes above and below ATM |
| `exposure_type` | string | `all` | `gex`, `dex`, `vex`, `chex`, or `all` |

**Returns:** per-strike exposure values with call/put OI breakdown, aggregate totals.

---

### `zer0dte_chain`

Smart-filtered 0DTE options chain. Returns only the relevant ATM slice with Greeks and liquidity scores.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `symbol` | string | `SPX` | Underlying symbol |
| `strike_range` | integer | `10` | Strikes above and below ATM |
| `option_type` | string | `both` | `call`, `put`, or `both` |
| `min_volume` | integer | `0` | Minimum volume filter |
| `min_oi` | integer | `0` | Minimum open interest filter |

**Returns:** contracts with bid/ask/mid, Greeks (delta, gamma, theta, vega, IV), volume, OI, liquidity score.

---

### `zer0dte_regime`

GEX regime classification with statistical context. Whether dealers are long or short gamma, and what that historically implies for price behavior.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `symbol` | string | `SPX` | Underlying symbol |

**Returns:** regime type, GEX magnitude, dealer behavior description, historical context, confidence level, IV, put/call ratios.

---

### `zer0dte_expected_move`

Expected price range derived from implied volatility and ATM straddle price. Shows how much of the expected move has been consumed.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `symbol` | string | `SPX` | Underlying symbol |

**Returns:** full-day and remaining expected move (% and $), expected range, consumed percentage.

---

### `zer0dte_flows`

Aggregated vanna and charm exposure. Second-order Greeks that create systematic buying/selling pressure — charm from time decay, vanna from volatility changes.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `symbol` | string | `SPX` | Underlying symbol |

**Returns:** VEX/CHEX totals, charm direction, vanna description, net drift assessment.

---

### `zer0dte_accuracy`

Historical accuracy of computed levels. Shows how often gamma flip, call wall, put wall, and expected move held over the lookback period.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `symbol` | string | `SPX` | Underlying symbol |
| `lookback_days` | integer | `30` | Days to analyze |

**Returns:** per-level accuracy percentages with held/tested counts.

---

### `zer0dte_history`

All regime snapshots for a specific date. Shows how levels, regime, and price evolved throughout the trading session.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `symbol` | string | `SPX` | Underlying symbol |
| `date` | string | today | Date in `YYYY-MM-DD` format |

**Returns:** array of timestamped snapshots with full regime and level data.

---

### `zer0dte_compare`

Find past sessions most similar to today. Compares current regime, GEX magnitude, and IV level against historical sessions.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `symbol` | string | `SPX` | Underlying symbol |
| `top_n` | integer | `5` | Number of similar sessions |

**Returns:** matched sessions with distance metric, regime, return, range.

---

### `zer0dte_calendar`

Economic calendar check. FOMC, CPI, NFP events, early close status, blackout windows.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `symbol` | string | `SPX` | Underlying symbol |
| `check_date` | string | today | Date in `YYYY-MM-DD` format |

**Returns:** events, early close flag, blackout windows, risk level, trading assessment.

---

### `zer0dte_opening_range`

Opening range: first 60-minute high/low after market open. Breakouts from the opening range historically correlate with trend days.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `symbol` | string | `SPX` | Underlying symbol |

**Returns:** OR high/low, range, breakout status, expected move consumed by OR.

---

### `zer0dte_strikes`

Strike analysis based on delta targeting and volatility context. Identifies strikes at specified delta levels for common 0DTE structures (iron condors, credit spreads).

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `symbol` | string | `SPX` | Underlying symbol |
| `strategy` | string | `iron_condor` | `iron_condor`, `put_spread`, `call_spread` |
| `target_delta` | float | `0.10` | Target delta for short strikes |

**Returns:** short/long strikes at target delta, estimated credit, distance from price, historical context.

---

### `zer0dte_liquidity`

Liquidity assessment: bid-ask spread, volume, and fill probability for strikes near ATM or at specified levels.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `symbol` | string | `SPX` | Underlying symbol |
| `strikes` | float[] | near ATM | Specific strikes to check |

**Returns:** per-contract liquidity score, spread analysis, fill probability rating.

---

### `zer0dte_pulse`

Lightweight monitoring. Returns price vs key levels, regime status, and risk level. Designed to be called every 5-10 minutes during a session.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `symbol` | string | `SPX` | Underlying symbol |

**Returns:** price, regime status, distances to key levels, remaining move, status.

---

### `zer0dte_alert`

Monitors for significant changes: regime flips, level breaks, VIX spikes, proximity to key levels. Returns severity-graded alerts.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `symbol` | string | `SPX` | Underlying symbol |

**Returns:** alert count, severity breakdown, individual alerts with descriptions.

---

### `zer0dte_entry_score`

Should you enter a 0DTE trade right now? Returns a score from 0 to 100 based on multiple market factors evaluated against academic research. The model considers regime dynamics, intraday timing patterns, volatility context, and positioning data.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `symbol` | string | `SPX` | Underlying symbol |

**Returns:** score (0-100), verdict (STRONG/DECENT/WEAK/NO TRADE), suggested action.

---

### `zer0dte_regime_history`

Regime transition history: what happened the last N times the GEX regime flipped? Returns each transition with from/to regime, duration, GEX at flip, price action, and trigger event.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `symbol` | string | `SPX` | Underlying symbol |
| `lookback_days` | integer | `30` | How many days back to search |

**Returns:** transitions with from/to regime, duration, GEX, price, trigger, regime distribution.

---

### `zer0dte_pattern`

Pattern matching: has this setup happened before? Given a regime and/or proximity to a key level, finds historical matches and reports what happened next.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `symbol` | string | `SPX` | Underlying symbol |
| `regime` | string | auto | `positive_gamma`, `negative_gamma`, or `neutral`. Auto-detects if omitted. |
| `near_level` | string | none | `put_wall`, `call_wall`, or `gamma_flip` |
| `lookback_days` | integer | `90` | How many days back to search |

**Returns:** matched setups with dates, price moves, duration, bullish percentage, average outcome.

---

### `zer0dte_overnight`

Pre-market context: overnight futures (ES, NQ, RTY), VIX/VIX1D/VVIX, gap analysis, and risk sentiment. Designed for the pre-market briefing before the session starts.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `symbol` | string | `SPX` | Underlying symbol |

**Returns:** futures prices and changes, VIX complex, gap size and direction, risk sentiment assessment.

---

### `zer0dte_session_log`

Today's reasoning log: regime flips, wall breaks, entry score transitions, and confidence shifts captured throughout the trading session. Useful for end-of-day review or post-mortem on a specific date.

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `symbol` | string | `SPX` | Underlying symbol |
| `date` | string | today | Date in `YYYY-MM-DD` format |

**Returns:** chronological event log with timestamps, event types (regime_flip, wall_break, score_change, alert), and contextual details (from/to values, GEX magnitude, price).

---

## What makes this different

- **MCP-native from day one** — one URL. No dashboard to scrape, no SDK to integrate. Claude, Cursor, Windsurf, Claude Desktop all work out of the box.
- **Computed, not raw** — GEX, DEX, VEX, CHEX derived server-side from Black-Scholes closed-form. The AI gets structured metrics, not re-chewed chain data.
- **Market memory** — every regime transition and key level snapshot is stored and queryable. Ask *"what happened the last time we were in positive gamma near the put wall?"* and get historical matches with actual outcomes.
- **Disambiguated tools** — 21 tools with explicit WHEN-TO-USE and WHEN-NOT-TO-USE descriptions so the AI picks the right one on the first try. No more "the AI called the wrong tool" failures.
- **Educational framing** — language is always statistical and educational. *"Historically, X% of sessions with this setup showed Y"* — never *"you should buy"*.
- **Accuracy tracking** — historical record of how often computed levels actually held. Trust earned with data, not claims.
- **Entry scoring** — research-backed 0-100 score (Option Alpha 25K trades, CAIA Papagelis 2025, Kelly-VIX framework). Not a black box.
- **Open source** — server code on GitHub. Audit the math, read the tool implementations, fork if you want.

---

## Pricing

| Plan | Limit | Price |
|------|-------|-------|
| **Free beta** | 3 sessions/day, 5/week | $0 — no registration |
| **Pro** | Unlimited | Coming soon — API key based |

Free beta is live now. No API key, no signup. Just add the URL. Current limits are generous and will be reduced — get in early.

Pro tier with unlimited access via API key is in development. Contact info@zer0dte.trade for updates.

---

## Links

- [zer0dte.trade](https://zer0dte.trade)
- [@zer0dte_ai](https://twitter.com/zer0dte_ai)
- info@zer0dte.trade

---

<sub>ZER0DTE provides options market data and statistical metrics for informational and educational purposes only. Nothing provided by this service constitutes financial advice, investment recommendations, or solicitation to trade. All trading decisions are your own responsibility.</sub>
