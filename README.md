# ZER0DTE

**Real-time options intelligence for AI agents.**

16 MCP tools that give your AI assistant live 0DTE options data: dealer exposure, key levels, regime classification, expected moves, and smart-filtered chains.

No installation. No dependencies. One URL. **Free to try — 10 calls/day, no registration.**

```
https://mcp.zer0dte.trade/sse
```

---

## How it works

ZER0DTE is an [MCP server](https://modelcontextprotocol.io) — a standardized way for AI models to access real-time data. Add one URL to your AI tool's MCP configuration, and you get 16 specialized tools for 0DTE options analysis.

The data pipeline polls live options data every 60 seconds during market hours, computes dealer exposure metrics (GEX, DEX, VEX, CHEX), and serves structured results through the MCP protocol. All computation happens server-side.

```
Your AI Client ←── MCP Protocol ──→ ZER0DTE Server ←── Compute Pipeline ──→ Live Options Data
```

---

## Quick Start

No API key needed. Add the URL and start using it.

### Claude Code

```bash
claude mcp add --transport http zer0dte https://mcp.zer0dte.trade/sse
```

Or in `.mcp.json`:

```json
{
  "mcpServers": {
    "zer0dte": {
      "type": "http",
      "url": "https://mcp.zer0dte.trade/sse"
    }
  }
}
```

> **Free tier:** 10 calls/day, 50/week. For unlimited access, add an API key — [get one here](https://zer0dte.trade).

### Claude Desktop

In `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "zer0dte": {
      "url": "https://mcp.zer0dte.trade/sse",
      "headers": {
        "Authorization": "Bearer YOUR_API_KEY"
      }
    }
  }
}
```

### Cursor / Windsurf / Any MCP Client

ZER0DTE uses standard MCP over HTTP transport. Any MCP-compatible client can connect with the URL and an API key.

---

## Tools

### Situational Awareness

| Tool | Description |
|------|-------------|
| [`zer0dte_snapshot`](#zer0dte_snapshot) | Full 0DTE situational awareness in one call |
| [`zer0dte_pulse`](#zer0dte_pulse) | Lightweight monitoring — call every 5-10 minutes |
| [`zer0dte_alert`](#zer0dte_alert) | Regime flips, level breaks, VIX spikes |
| [`zer0dte_calendar`](#zer0dte_calendar) | Economic events, FOMC, early close status |

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

## What makes this different

- **Computed, not raw** — GEX, DEX, VEX, CHEX derived from Black-Scholes closed-form, not regurgitated chain data
- **Real-time** — refreshed every 60 seconds during market hours
- **AI-native** — built for MCP from day one. Structured data optimized for LLM consumption
- **Accuracy tracking** — historical record of how often computed levels held
- **Zero friction** — one URL, no SDK, no local installation

---

## Pricing

| Plan | Limit | Price |
|------|-------|-------|
| **Free** | 10 calls/day, 50/week | $0 — no registration |
| **Pro** | Unlimited | Coming soon — API key based |

The free tier is live now. No API key, no signup. Just add the URL.

Pro tier with unlimited access via API key is in development. Join the waitlist at [zer0dte.trade](https://zer0dte.trade) to get notified.

---

## Links

- [zer0dte.trade](https://zer0dte.trade)
- [@zer0dte_ai](https://twitter.com/zer0dte_ai)
- info@zer0dte.trade

---

<sub>ZER0DTE provides options market data and statistical metrics for informational and educational purposes only. Nothing provided by this service constitutes financial advice, investment recommendations, or solicitation to trade. All trading decisions are your own responsibility.</sub>
