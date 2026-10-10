---
name: trading-strategy-sell-put-gdx
version: 1.0.1
enabled: true
description: Tastytrade mechanics for selling out-of-the-money puts on GDX on Fridays with GLD macro 200-day trend filter, defensive rolling, and 0.16 Delta
strategy_type: SHORT_PUT
underlying: US.GDX
market: US
currency: USD

entry_rules:
  max_active_positions: 2
  default_contracts: 2
  max_entries_per_day: 1

  # Schedule & Holiday Handling
  schedule_days: ["FRIDAY"]
  holiday_behavior: PRIOR_TRADING_DAY

  # Cross-Asset Macro Moving Average Trend Filter (GLD 200 SMA)
  trend_filter:
    enabled: true
    underlying: GLD
    ma_type: SMA
    period: 200
    timeframe: DAY
    condition: ABOVE

  min_ivr: 0.0
  dte_target: 45
  dte_min: 30
  dte_max: 60
  delta_target: 0.16
  delta_min: 0.10
  delta_max: 0.20
  min_open_interest: 50
  min_volume: 5
  max_bid_ask_spread_pct: 0.35
  min_premium_usd: 0.20
  escalation_timeout_sec: 25.0

exit_rules:
  profit_target_pct: 0.50
  time_stop_dte: 21
  roll_min_ivr: 0.0
  close_on_low_ivr: true
  stop_loss_multiplier: null
  urgent_exit_dte: 7

# Defensive Adjustment Rules (Roll Out, Roll Down, Price Triggers)
adjustment_rules:
  - name: "tested_strike_defense"
    trigger_type: UNDERLYING_PRICE_TOUCH
    trigger_threshold: 1.0
    action: ROLL_DOWN_AND_OUT
    target_dte: 45
    target_delta: 0.16
    min_ivr: 0.0

  - name: "21_dte_roll"
    trigger_type: TIME_DTE
    trigger_threshold: 21
    action: ROLL_DOWN_AND_OUT
    target_dte: 45
    target_delta: 0.16
    min_ivr: 0.0
---

# Trading Strategy: Sell Put on GDX (Tastytrade Friday Ladder with GLD Macro Trend Filter)

## Core Philosophy
Selling out-of-the-money puts on GDX (VanEck Gold Miners ETF) every Friday (or preceding trading day if Friday is a holiday) when:
1. The strategy is ENABLED.
2. The macro commodity trend is bullish (GLD spot price > 200-day SMA). Anchoring to GLD (physical gold ETF) instead of GDX filters out mining company operational volatility and stock market drawdowns, avoiding whipsaws during commodity bull runs.
3. Conservative Delta selection: targeting ~0.16 Delta (approximately 1 standard deviation out-of-the-money) to buffer against gold miner equity drawdowns.

## Position Sizing & Capacity
- **Default Contracts**: `2` contracts per entry (allocating ~$5,000–$7,000 notional on $10,000 capital for 50%–70% margin safety).
- **Max Simultaneous Positions**: `2` positions.
- **Roll Invariant**: Rolling an existing position preserves position count and never consumes an additional ladder slot.

## Weekend & Holiday Protection
- **Weekend Guard**: On Saturdays and Sundays, no orders are placed and the monitor remains idle.
- **Holiday Observation**: If Friday is an official exchange holiday (e.g. Good Friday), the bot automatically evaluates entry on **Thursday**, ensuring the weekly ladder remains uninterrupted.

## Defensive Adjustments (Roll Out & Roll Down)
- **Tested Strike Defense**: If GDX spot touches the strike price (Spot ≤ Strike), the position is considered tested. The strategy initiates a **ROLL_DOWN_AND_OUT** to the next ~45 DTE cycle at the 0.16 delta strike.
- **21 DTE Roll**: At 21 DTE, the position is systematically rolled out to the next ~45 DTE cycle at the target ~0.16 delta strike to maintain continuous time decay exposure.
