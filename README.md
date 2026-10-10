# trading-strategy-sell-put-gdx

Systematic quantitative cash-secured short put selling strategy on **`US.GDX`** (VanEck Gold Miners ETF) with a **`GLD`** (SPDR Gold Shares) macro 200-day moving average trend filter, engineered for small accounts ($10,000–$25,000).

## Highlights
- **Cross-Asset Macro Trend Filter:** Uses `GLD` (physical gold ETF) 200-day SMA (`GLD Spot > SMA200`) as the directional gatekeeper. Anchoring to macro gold prices filters out gold mining company idiosyncratic noise, operational leverage spikes, and general stock market sell-offs, avoiding false trend breakdowns.
- **Conservative Delta Selection (`delta_target: 0.16`):** Sells out-of-the-money puts at approximately 1 standard deviation OTM (~0.16 Delta), providing a large buffer against mining stock volatility.
- **Tastytrade Mechanics:** Sells ~0.16 Delta Puts targeting 45 DTE on Fridays, systematically takes profit at 50% max credit, and rolls or closes trades at 21 DTE.
- **Defensive Adjustments:** Automated `ROLL_DOWN_AND_OUT` when tested ($\text{Spot} \le \text{Strike}$) or at 21 DTE to extend duration and improve breakevens.
- **Position Sizing:** Fixed 2 contracts per entry ($5,000–$7,000 notional on $10,000 capital) keeping cash margin utilization within the optimal 50%–70% range.

## Specification
The canonical machine-readable specification is authored in [`STRATEGY.md`](./STRATEGY.md) with YAML frontmatter.

## Historical Performance Benchmark
Simulated across real historical ThetaData options data for `US.GDX` and underlying `GLD` from **2020-01-02** to **2026-09-30** (6.8 years):

| Metric | Result (Real ThetaData) |
| :--- | :--- |
| **Period** | `2020-01-02` to `2026-09-30` (6.8 Years) |
| **Initial Capital** | $10,000.00 |
| **Final NAV** | $13,745.71 |
| **Total Return** | **+37.46%** ($+3,745.71) |
| **Annualized CAGR** | **+4.83%** |
| **Max Drawdown** | **-5.74%** (-$578.86) |
| **Sharpe Ratio** | 0.10 |
| **Sortino Ratio** | 0.13 |
| **Win Rate** | **80.6%** (116W / 28L, 144 positions) |
| **Profit Factor** | 1.47 |
| **Avg Win / Avg Loss** | $45.23 / -$127.89 |
| **Avg Days Held** | 11.7 days |
| **Total Trades Executed** | 288 |
| **Closed Positions** | 144 |
| **Total Commissions** | $374.40 |
| **Total Slippage** | $142.00 |
| **Cash Sweep Interest** | $2,080.11 |
| **Total Rolls** | 29 |
| **Data Source** | ThetaData v3 Real EOD Options & Parquet Lake |

_IV/Greeks: American (Bjerksund-Stensland 2002) with historical FRED Treasury rates and trailing dividends._

## Development & Testing
```bash
pytest --cov=src/trading_strategy_sell_put_gdx --cov-branch --cov-fail-under=100
```
