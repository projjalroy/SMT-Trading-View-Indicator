# ICT SMT Divergence & CISD

A TradingView **Pine Script v5** indicator that detects **SMT divergence** across six higher timeframes, filters it with ICT-style context (NY midnight open, killzones, liquidity sweeps), and confirms entries with a **CISD (Change in State of Delivery)** on a mapped lower timeframe.

```
HTF SMT divergence  →  context filters  →  PENDING setup  →  LTF CISD confirmation  →  entry marker + alert
```

> **Disclaimer:** This indicator is for educational and informational purposes only. It is not financial advice and does not guarantee results. Always test on a demo account first.

---

## Features

### 1. Multi-timeframe SMT engine
- Scans **Weekly, Daily, H4, H1, M30 and M15** (each can be enabled or disabled; timeframes below the chart timeframe are ignored).
- Compares the chart symbol with **one or two comparison symbols** (default `NQ1!`, optional `YM1!`).
- Supports **inversely correlated** symbols (e.g. DXY-type logic).
- **ANY / ALL** comparison mode when two symbols are used.
- Optional **Reverse SMT** detection.
- Adjustable pivot left/right bars, swing alignment tolerance and maximum swing distance.

### 2. Context filters
An SMT event is only treated as a signal if it passes the enabled filters:

| Filter | Rule |
|---|---|
| **NY midnight open** | Bullish SMT swing low must be below the NY 00:00 open; bearish SMT swing high must be above it. |
| **Killzones (New York time)** | Asia 20:00–00:00, London 02:00–05:00, NY AM 07:00–11:00, NY PM 13:00–16:00. Each can be toggled. |
| **Liquidity sweep** | The SMT swing must sweep a fresh level: PDH/PDL, PWH/PWL, or an Asia / London / NY AM / NY PM session high or low. |
| **MTF alignment** | A level is only valid if it lines up with a swing high/low on Weekly, Daily, H4, H1 or M30. Non-aligned levels (e.g. session levels) are skipped. |

### 3. LTF CISD confirmation
Each HTF SMT is mapped to a lower timeframe (editable):

| SMT timeframe | CISD timeframe |
|---|---|
| Weekly | H4 |
| Daily | H1 |
| H4 | M15 |
| H1 | M5 |
| M30 | M3 |
| M15 | M1 |

A CISD is a candle **close** beyond the open of the delivery series (run of same-colour candles) that produced the leg into the SMT swing. A setup is invalidated if price trades beyond the SMT extreme, or if it takes longer than the configured number of LTF bars.

> **Important:** the CISD is only monitored when the **chart timeframe equals the mapped LTF** (e.g. an H1 SMT is confirmed on the M5 chart).

### 4. Visual overlay
- **SMT trendlines** connecting the two swings that form the divergence, labelled **Bullish SMT** / **Bearish SMT** with the timeframe and swept level.
- Option to also draw rejected (unfiltered) SMT events, faded.
- **Entry markers:** green arrow (long) / red arrow (short) on the exact candle where the CISD confirms.
- NY 00:00 open line, killzone shading and liquidity level lines (valid, invalid and swept levels can be shown separately).

### 5. Status dashboard
A compact table in any screen corner (position, text size, light/dark theme and transparency are configurable) with one row per timeframe pair:

| Column | Values |
|---|---|
| **HTF SMT** | Active ▲ / Active ▼ / Inactive / Off |
| **MTF Liquidity** | Valid Level / Skipped Session / Skipped Level / No Sweep / Filter off |
| **LTF CISD** | Pending / Confirmed / Failed |

A footer shows whether the CISD engine is running on the current chart timeframe.

### 6. Alerts
Four alert conditions:

1. HTF Bullish SMT Formed
2. HTF Bearish SMT Formed
3. Bullish CISD Confirmed (LTF Trigger)
4. Bearish CISD Confirmed (LTF Trigger)

Alerts 1–2 fire on filtered SMT events by default (switchable to raw SMT events). Create alerts with **Once Per Bar Close**.

---

## Non-repainting design
- HTF swings are read from the **previous closed HTF bar** (`request.security`, `lookahead_off`, `[1]`).
- The CISD is evaluated on **closed chart candles only**.
- PDH/PDL/PWH/PWL use the standard `[1]` + `lookahead_on` idiom, so only finished days/weeks are used.
- Drawings are created once on the bar where the event is confirmed and are never moved.
- If a CISD already occurred between the SMT extreme and the moment the SMT was confirmed, it is found retroactively and marked on its real candle.

## Installation
1. Open TradingView and the **Pine Editor**.
2. Paste the contents of `ict_smt_cisd_complete.pine`.
3. Click **Add to chart**.
4. Open the settings and adjust symbols, timeframes, filters and dashboard.

## Recommended setup
- Use a futures symbol such as `ES1!` or `NQ1!` with a correlated comparison symbol.
- For an H1 SMT, open the **M5** chart (see the mapping table above).
- Keep **Evaluate filters only on last N chart bars** at `0` for full history, or set a value if you hit a runtime limit on the free plan.

## Notes and limitations
- The previous swing of a trendline is anchored on the open of its HTF bar (its exact candle is not stored), so lines can appear shifted on Weekly/Daily.
- The dashboard shows the most recent setup direction per pair.
- The SMT column reflects regular SMT only; reverse SMT is not shown in it.
- The script makes many `request.security` calls (18 for swings plus 2 for prior day/week levels). Heavy settings may be slow on lower TradingView plans.

## Contributing
Issues and pull requests are welcome.

## License
Add your preferred license here (e.g. MIT).
