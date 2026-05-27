# 🌡️ Sentiment Zone Oscillator (SZO) Alert — `Sentiment_Zone_Oscillator__SZO__001.mq4`

> **MQL4 Script for MetaTrader 4**  
> Calculates the Sentiment Zone Oscillator using a normalized bull/bear price movement ratio and fires real-time alerts when market sentiment crosses into extreme bullish or bearish zones.

---

## Overview

The Sentiment Zone Oscillator (SZO) is a momentum-based indicator that measures the **relative balance of bullish vs bearish price movement** over a rolling period, normalizing the result to a range between -1.0 and +1.0.

Unlike RSI (which uses average gains vs losses) or MACD (which compares moving averages), the SZO directly measures the proportion of upward price movement versus total movement in the lookback window — giving an intuitive reading of market sentiment:

- **SZO near +1.0** — Overwhelmingly bullish sentiment; nearly all price movement has been upward
- **SZO near -1.0** — Overwhelmingly bearish sentiment; nearly all price movement has been downward
- **SZO near 0.0** — Balanced; upward and downward movement roughly equal

This script monitors SZO crossings into configurable extreme threshold zones and fires alerts the moment sentiment shifts into bullish or bearish extremes.

---

## How It Works

**SZO Formula:**

For each bar in the lookback window:
```
change = Close[i] - Close[i+1]
if change > 0: sumPos += change
if change < 0: sumNeg += |change|

SZO = (sumPos - sumNeg) / (sumPos + sumNeg)
```

Result ranges from -1.0 (pure bearish) to +1.0 (pure bullish).

**Alert Logic (polled every 60 seconds):**

```
SZO >= BullishThreshold AND previous SZO < BullishThreshold
→ "Bullish Sentiment Extreme Detected" (new bullish extreme — crossing up)

SZO <= BearishThreshold AND previous SZO > BearishThreshold
→ "Bearish Sentiment Extreme Detected" (new bearish extreme — crossing down)
```

The edge-trigger design (crossing *into* the zone, not just being in it) prevents repeated alerts while SZO remains in the zone.

---

## Input Parameters

| Parameter | Default | Type | Description |
|---|---|---|---|
| `TradeSymbol` | `"EURUSD"` | string | Symbol to monitor |
| `Timeframe` | `PERIOD_H1` | ENUM_TIMEFRAMES | Timeframe for SZO calculation |
| `SZOPeriod` | `14` | int | Lookback period for SZO calculation |
| `BullishThreshold` | `0.7` | double | SZO level to define bullish extreme (0.0 to 1.0) |
| `BearishThreshold` | `-0.7` | double | SZO level to define bearish extreme (-1.0 to 0.0) |
| `EnableAlerts` | `true` | bool | Trigger MT4 sound alerts |
| `EnableEmail` | `false` | bool | Send email notifications |
| `EnablePush` | `false` | bool | Send push notifications to mobile |

---

## Alert Signals

```
Bullish Sentiment Extreme Detected on EURUSD (Timeframe: PERIOD_H1)
SZO Value: 0.742
```
```
Bearish Sentiment Extreme Detected on EURUSD (Timeframe: PERIOD_H1)
SZO Value: -0.815
```

All events are logged to the MT4 **Experts journal**.

---

## SZO Zone Reference

```
SZO Scale:
 -1.0 ──── -0.7 ──────────── 0.0 ──────────── +0.7 ──── +1.0
            │                  │                  │
      [BEARISH EXTREME]   [NEUTRAL ZONE]   [BULLISH EXTREME]
```

| SZO Range | Market Sentiment | Implication |
|---|---|---|
| +0.7 to +1.0 | Extreme bullish | Overbought sentiment; potential exhaustion |
| +0.3 to +0.7 | Moderately bullish | Uptrend bias |
| -0.3 to +0.3 | Neutral | No clear sentiment edge |
| -0.7 to -0.3 | Moderately bearish | Downtrend bias |
| -1.0 to -0.7 | Extreme bearish | Oversold sentiment; potential exhaustion |

---

## Threshold Configuration Guide

| Market Type | `BullishThreshold` | `BearishThreshold` | Effect |
|---|---|---|---|
| Trending | `0.8` | `-0.8` | Fewer, higher-conviction alerts |
| Ranging | `0.6` | `-0.6` | More frequent alerts at moderate extremes |
| Scalping | `0.55` | `-0.55` | Early warning, high frequency |
| Conservative | `0.85` | `-0.85` | Only extreme market conditions |

---

## SZO vs RSI — Comparison

| Feature | SZO | RSI |
|---|---|---|
| Output scale | -1.0 to +1.0 | 0 to 100 |
| Calculation basis | Raw price change sum | Average gain/loss ratio |
| Interpretation | Proportion of bull vs bear moves | Speed/magnitude of price change |
| Overbought extreme | > +0.7 | > 70 |
| Edge trigger alerts | ✅ Yes (this script) | ❌ (would need custom code) |

The SZO's edge-trigger design makes it particularly useful for identifying the **precise moment** when sentiment shifts into extreme territory, rather than alerting repeatedly while already in the zone.

---

## Installation

1. Copy `Sentiment_Zone_Oscillator__SZO__001.mq4` to:
   ```
   MetaTrader 4/MQL4/Scripts/
   ```
2. Restart MT4 or right-click **Navigator** → **Refresh**
3. Drag onto a chart
4. Configure thresholds and click **OK**

---

## Requirements

- MetaTrader 4 (Build 600+)
- `#property strict` compliance (enforced)
- At least `SZOPeriod + 1` bars of history loaded
- MT4 must remain running for continuous monitoring

---

## Disclaimer

This script is provided for **educational and informational purposes only**. Sentiment extremes in strongly trending markets can persist for extended periods. The SZO measures historical price movement ratios, not actual market participant positioning data. Always validate signals with price structure analysis. Test on a demo account before live use.

---

## License

MIT License — free to use, modify, and distribute with attribution.
