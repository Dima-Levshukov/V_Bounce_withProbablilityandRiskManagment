# CLAUDE.md

This file provides guidance for AI assistants working with this repository.

## Project Overview

This is a **Pine Script v6** trading indicator for **TradingView** called **V-Bounce Probability & Risk Management Calculator (ProbCalcV1)**. It detects V-shaped bounce patterns in financial price data and provides probability-based signal quality assessment with integrated risk management.

**License:** Mozilla Public License 2.0

## Repository Structure

```
V_Bounce_withProbablilityandRiskManagment/
└── ProbCalcV1.pine    # Main (and only) source file — Pine Script v6 indicator
```

This is a single-file project. There are no build tools, package managers, test frameworks, or configuration files.

## Language & Runtime

- **Language:** Pine Script v6 (`//@version=6`)
- **Runtime:** TradingView charting platform (no local execution possible)
- **No build/test/lint commands** — the script is pasted directly into the TradingView Pine Editor and runs on their servers

## Code Architecture

The file `ProbCalcV1.pine` (233 lines) is organized into 8 clearly delimited sections:

| Section | Lines | Purpose |
|---------|-------|---------|
| **INPUTS** | 12–40 | 20 configurable parameters in 4 groups |
| **CORE CALCULATIONS** | 43–51 | ATR volatility and volume spike detection |
| **V-BOUNCE DETECTION** | 54–87 | Pattern identification (swing analysis, depth/recovery thresholds, confirmation) |
| **PROBABILITY ENGINE** | 90–135 | Historical outcome tracking, win rate, expectancy, profit factor |
| **RISK MANAGEMENT** | 138–151 | Position sizing, ATR-based stops, Kelly Criterion |
| **VISUAL OUTPUT** | 154–169 | Chart plotting (signals, stop/target lines, background highlights) |
| **INFORMATION TABLE** | 172–227 | On-chart 2×12 data table with color-coded metrics |
| **ALERTS** | 230–233 | TradingView alert conditions for signals |

### Input Parameter Groups

- **V-Bounce Detection** — `lookbackPeriod`, `depthPercent`, `recoveryPercent`, `confirmBars`
- **Probability Engine** — `historyLength`, `minSampleSize`, `probThreshold`
- **Risk Management** — `riskPercent`, `rewardRatio`, `accountSize`, `useATRStop`, `atrMultiplier`, `atrLength`
- **Volume Filter** — `useVolume`, `volMultiplier`, `volAvgLength`
- **Display** — `showLabels`, `showStopLevels`, `showInfoTable`, `tablePosition`

### Signal Detection Flow

1. Compute swing high/low over the lookback window
2. Check if the drop from high to low exceeds `depthPercent`
3. Verify current price recovery from the swing low exceeds `recoveryPercent`
4. Confirm the bounce holds for `confirmBars` consecutive bars
5. Optionally validate a volume spike (volume > average × multiplier)
6. Suppress duplicate signals on consecutive bars

### Key Stateful Variables

Pine Script uses the `var` keyword for variables that persist across bars:
- `confirmCount` — bounce confirmation bar counter
- `prevSignal` — deduplication flag
- `totalBounces`, `successBounces`, `sumGain`, `sumLoss` — historical outcome tracking
- `entryPrice`, `stopPrice`, `targetPrice`, `inTrade` — trade lifecycle state

## Coding Conventions

### Naming

- **camelCase** for all variables and identifiers (e.g., `swingLow`, `winRate`, `isHighProbability`)
- Boolean variables prefixed with `is` or `has` (e.g., `isDeepEnough`, `hasSufficientData`)
- Toggle inputs prefixed with `use` or `show` (e.g., `useATRStop`, `showLabels`)
- Descriptive names preferred over abbreviations

### Structure

- Sections separated by comment banners: `// ─────...` + `// SECTION NAME` + `// ─────...`
- Input parameters use `group=` for logical grouping in the TradingView UI
- All inputs include descriptive labels and `minval`/`maxval` constraints
- Guard clauses and ternary expressions used for safe division (`x > 0 ? result : 0.0`)

### Color Coding

- **Green** — high probability / positive metrics (win rate above threshold, positive expectancy)
- **Yellow** — moderate / caution (win rate 50–threshold, profit factor 1.0–1.5)
- **Red** — negative metrics / stop loss levels
- **Gray** — insufficient data
- **Blue** — neutral / entry price / header

## How to Modify This Project

### Adding New Features

1. Identify which section the feature belongs to (detection, probability, risk, display)
2. Add new input parameters in the INPUTS section with appropriate `group=` tags
3. Implement logic in the corresponding section
4. If visual, add plots/table cells in the VISUAL OUTPUT or INFORMATION TABLE sections
5. If alertable, add `alertcondition()` in the ALERTS section

### Pine Script v6 Notes

- Use `ta.*` namespace for built-in technical functions (e.g., `ta.atr()`, `ta.sma()`, `ta.lowest()`)
- Use `math.*` for math operations (`math.abs()`, `math.floor()`, `math.max()`, `math.min()`)
- Use `str.*` for string formatting (`str.tostring()`)
- The `var` keyword makes a variable persist across bars (initialized once)
- `na` represents "not available" — Pine Script's null equivalent
- `barstate.islast` is true only on the most recent bar (used for table rendering)
- Use `input.*` functions for user-configurable parameters

### Testing

There is no automated test suite. Changes are validated by:
1. Pasting the script into TradingView's Pine Editor
2. Adding the indicator to a chart
3. Visually verifying signals, table output, and alert behavior across different instruments and timeframes

## Git Conventions

- **Commit messages:** Imperative mood, describe the change clearly (e.g., "Add ProbCalcV1.pine - V-Bounce probability calculator with risk management")
- **Branch naming:** Session-based branches prefixed with `claude/`
- **Single-file workflow:** All code lives in `ProbCalcV1.pine`
