# Synthetic Timeframes System - Complete Guide

## Overview

The Synthetic Timeframes System allows you to generate higher timeframe data (2h, 4h, 8h, 12h, 16h, 1D, 2D, etc.) from your existing 1h base data.

**Key Benefits:**
- ✅ Generate unlimited timeframes from single 1h dataset
- ✅ Consistent OHLCV aggregation
- ✅ Automatic feature recalculation
- ✅ Seamless integration with preset system
- ✅ Storage in SQLite database with metadata tracking

---

## Supported Synthetic Timeframes

From 1h base data, you can synthesize:

### Standard Timeframes
- `2h` - 2 hours
- `4h` - 4 hours
- `6h` - 6 hours
- `8h` - 8 hours
- `12h` - 12 hours
- `1D` / `24h` - 1 day
- `2D` / `48h` - 2 days
- `3D` / `72h` - 3 days

### Custom Timeframes
- `16h` - 16 hours
- `32h` - 32 hours
- Any multiple of hours: `{N}h` where N ≥ 1
- Any multiple of days: `{N}D` where N ≥ 1

---

## Quick Start

### 1. Synthesize Timeframes

```bash
# Synthesize standard timeframes for BTCUSDT
python timeframe_synthesizer.py \
    --symbol BTCUSDT \
    --timeframes 2h 4h 8h 12h 1D \
    --db data_manager/exports/marketdata_base.db
```

### 2. List Available Timeframes

```bash
python timeframe_synthesizer.py \
    --symbol BTCUSDT \
    --db data_manager/exports/marketdata_base.db \
    --list
```

**Output:**
```
Available synthetic timeframes for BTCUSDT:
================================================================================
Timeframe  Base       Features   Rows       Updated
--------------------------------------------------------------------------------
2h         1h         v2         8760       2025-11-23 10:30:00
4h         1h         v2         4380       2025-11-23 10:35:00
8h         1h         v2         2190       2025-11-23 10:40:00
1D         1h         v2         365        2025-11-23 10:45:00
================================================================================
```

### 3. Generate Presets for Synthetic Timeframes

```bash
# Generate presets for all synthetic timeframes (quick mode)
python generate_synthetic_presets.py \
    --symbol BTCUSDT \
    --timeframes 2h 4h 8h 1D \
    --quick
```

### 4. Use Synthetic Timeframe Preset

```bash
# List presets
python manage_presets.py list --symbol BTCUSDT

# Apply preset for 4h timeframe
python manage_presets.py apply BTCUSDT_4h_v2_LSTM2Head_multi_h1-3-5-10

# Train with it
python TheOracle.py --config config/BTCUSDT_4h_v2_LSTM2Head_multi_h1-3-5-10.json
```

---

## Architecture

### Components

#### 1. `timeframe_synthesizer.py`

**Classes:**

##### `TimeframeConfig`
- Parses timeframe strings (e.g., "2h", "4h", "1D")
- Validates synthesis feasibility
- Generates pandas resampling rules

##### `TimeframeSynthesizer`
- Resamples OHLCV data
  - Open: first value
  - High: maximum value
  - Low: minimum value
  - Close: last value
  - Volume: sum
- Adds technical features (using fiboevo)
- Complete synthesis workflow

##### `SyntheticTimeframeDatabase`
- Saves synthetic data to SQLite
- Tracks metadata (source, dates, feature system)
- Loads synthetic data
- Lists available timeframes

---

## How It Works

### OHLCV Resampling

```python
# Example: Resampling 1h to 4h

# Input (1h bars):
timestamp            open    high    low     close   volume
2025-01-01 00:00    100.0   101.0   99.0    100.5   1000
2025-01-01 01:00    100.5   102.0   100.0   101.5   1100
2025-01-01 02:00    101.5   103.0   101.0   102.0   1200
2025-01-01 03:00    102.0   102.5   101.5   102.0   1050

# Output (4h bar):
timestamp            open    high    low     close   volume
2025-01-01 00:00    100.0   103.0   99.0    102.0   4350
                    ↑       ↑       ↑       ↑       ↑
                    first   max     min     last    sum
```

### Feature Recalculation

After resampling OHLCV, technical features are recalculated for the new timeframe:

```python
synthesizer = TimeframeSynthesizer(base_timeframe='1h')

# Resample
df_4h = synthesizer.resample_ohlcv(df_1h, target_timeframe='4h')

# Add features (SMA, RSI, etc.)
df_4h = synthesizer.add_technical_features(df_4h, feature_system='v2')
```

Features are computed on the resampled data, not aggregated from 1h features.

---

## Database Storage

### Tables Created

For each synthetic timeframe, a table is created:

```
{symbol_lower}_{timeframe}

Example:
- btcusdt_2h
- btcusdt_4h
- btcusdt_1d
```

### Metadata Table

```sql
CREATE TABLE synthetic_timeframes_metadata (
    symbol TEXT,
    timeframe TEXT,
    base_timeframe TEXT,
    feature_system TEXT,
    n_rows INTEGER,
    start_date TEXT,
    end_date TEXT,
    created_at TEXT,
    updated_at TEXT,
    PRIMARY KEY (symbol, timeframe)
);
```

This tracks which timeframes are synthetic and their properties.

---

## Usage Examples

### Example 1: Generate Standard Timeframes

```bash
python timeframe_synthesizer.py \
    --symbol BTCUSDT \
    --timeframes 2h 4h 8h 12h 1D \
    --db data_manager/exports/marketdata_base.db \
    --features v2
```

### Example 2: Generate Custom Timeframes

```bash
# Generate 16h and 32h timeframes
python timeframe_synthesizer.py \
    --symbol BTCUSDT \
    --timeframes 16h 32h \
    --db data_manager/exports/marketdata_base.db
```

### Example 3: Batch Synthesis for Multiple Symbols

```bash
# Bash script for multiple symbols
for symbol in BTCUSDT ETHUSDT BNBUSDT; do
    python timeframe_synthesizer.py \
        --symbol $symbol \
        --timeframes 2h 4h 8h 1D \
        --db data_manager/exports/marketdata_base.db
done
```

### Example 4: Complete Preset Generation Workflow

```bash
# 1. Synthesize timeframes
python timeframe_synthesizer.py \
    --symbol BTCUSDT \
    --timeframes 2h 4h 8h 1D \
    --db data_manager/exports/marketdata_base.db

# 2. Generate presets (overnight job)
python generate_synthetic_presets.py \
    --symbol BTCUSDT \
    --timeframes 2h 4h 8h 1D \
    --features v2 \
    --phases 4

# 3. List generated presets
python manage_presets.py list --symbol BTCUSDT

# 4. Apply best preset
python manage_presets.py apply BTCUSDT_4h_v2_LSTM2Head_multi_h1-3-5-10

# 5. Train
python TheOracle.py --config config/BTCUSDT_4h_v2_LSTM2Head_multi_h1-3-5-10.json
```

---

## Programmatic API

### Synthesize Timeframe

```python
from timeframe_synthesizer import TimeframeSynthesizer
import pandas as pd

# Load 1h data
df_1h = pd.read_csv('btcusdt_1h.csv')

# Create synthesizer
synthesizer = TimeframeSynthesizer(base_timeframe='1h')

# Synthesize 4h data with features
df_4h = synthesizer.synthesize_timeframe(
    df_1h=df_1h,
    target_timeframe='4h',
    feature_system='v2'
)

print(f"Original 1h data: {len(df_1h)} rows")
print(f"Synthetic 4h data: {len(df_4h)} rows")
```

### Save to Database

```python
from timeframe_synthesizer import SyntheticTimeframeDatabase

db = SyntheticTimeframeDatabase("data_manager/exports/marketdata_base.db")

# Save synthetic timeframe
db.save_synthetic_timeframe(
    df=df_4h,
    symbol="BTCUSDT",
    timeframe="4h",
    base_timeframe="1h",
    feature_system="v2"
)

# Load it back
df_loaded = db.load_synthetic_timeframe(
    symbol="BTCUSDT",
    timeframe="4h"
)
```

### Batch Synthesis

```python
db = SyntheticTimeframeDatabase("data_manager/exports/marketdata_base.db")

# Synthesize and save multiple timeframes
db.synthesize_and_save(
    symbol="BTCUSDT",
    target_timeframes=['2h', '4h', '8h', '12h', '1D'],
    feature_system='v2'
)

# List available
timeframes = db.get_available_timeframes("BTCUSDT")
for tf in timeframes:
    print(f"{tf['timeframe']}: {tf['n_rows']} rows")
```

---

## Integration with Preset System

### Automatic Generation

The `generate_synthetic_presets.py` script automatically:

1. Synthesizes timeframes from 1h data
2. Runs hyperparameter optimization for each
3. Saves best configuration as preset

```bash
python generate_synthetic_presets.py \
    --symbol BTCUSDT \
    --timeframes 2h 4h 8h 1D \
    --features v2 \
    --quick
```

### Preset Naming

Synthetic timeframe presets follow the standard naming convention:

```
BTCUSDT_2h_v2_LSTM2Head_multi_h1-3-5-10
BTCUSDT_4h_v2_LSTM2Head_multi_h1-3-5-10
BTCUSDT_8h_v2_LSTM2Head_multi_h1-3-5-10
BTCUSDT_1D_v2_LSTM2Head_multi_h1-3-5-10
```

### Loading in Training

The training pipeline automatically detects synthetic timeframes:

```python
from training_preset_manager import PresetDatabase

db = PresetDatabase()
preset = db.load_preset(preset_name="BTCUSDT_4h_v2_LSTM2Head_multi_h1-3-5-10")

# Metadata indicates if timeframe is synthetic
print(f"Timeframe: {preset.metadata.timeframe}")
print(f"Data source: {preset.metadata.data_source}")

# Data loading handles synthetic timeframes automatically
# (loads from btcusdt_4h table)
```

---

## Production Workflow

### Step 1: Synthesize All Required Timeframes

Create a synthesis script:

```bash
#!/bin/bash
# synthesize_all_timeframes.sh

DB="data_manager/exports/marketdata_base.db"

for symbol in BTCUSDT ETHUSDT; do
    echo "Synthesizing timeframes for $symbol..."

    python timeframe_synthesizer.py \
        --symbol $symbol \
        --timeframes 2h 4h 8h 12h 16h 1D 2D \
        --db $DB \
        --features v2

    echo "✓ $symbol complete"
done

echo "All timeframes synthesized!"
```

Run it:
```bash
chmod +x synthesize_all_timeframes.sh
./synthesize_all_timeframes.sh
```

### Step 2: Generate Presets (Overnight)

```bash
# Create preset generation plan
cat > synthetic_presets_plan.json <<EOF
{
  "configurations": [
    {"symbol": "BTCUSDT", "timeframe": "2h", "feature_system": "v2", "horizon_values": [1,3,5,10]},
    {"symbol": "BTCUSDT", "timeframe": "4h", "feature_system": "v2", "horizon_values": [1,3,5,10]},
    {"symbol": "BTCUSDT", "timeframe": "8h", "feature_system": "v2", "horizon_values": [1,3,5,10]},
    {"symbol": "BTCUSDT", "timeframe": "1D", "feature_system": "v2", "horizon_values": [1,3,5,10]},
    {"symbol": "ETHUSDT", "timeframe": "2h", "feature_system": "v2", "horizon_values": [1,3,5,10]},
    {"symbol": "ETHUSDT", "timeframe": "4h", "feature_system": "v2", "horizon_values": [1,3,5,10]}
  ]
}
EOF

# Generate presets
python generate_training_presets.py \
    --plan synthetic_presets_plan.json \
    --phases 4
```

### Step 3: Deploy Best Presets

```bash
# Review generated presets
python manage_presets.py list

# Apply best presets for each timeframe
python manage_presets.py apply BTCUSDT_2h_v2_LSTM2Head_multi_h1-3-5-10
python manage_presets.py apply BTCUSDT_4h_v2_LSTM2Head_multi_h1-3-5-10
python manage_presets.py apply BTCUSDT_8h_v2_LSTM2Head_multi_h1-3-5-10
python manage_presets.py apply BTCUSDT_1D_v2_LSTM2Head_multi_h1-3-5-10

# Train production models
python TheOracle.py --config config/BTCUSDT_2h_v2_LSTM2Head_multi_h1-3-5-10.json &
python TheOracle.py --config config/BTCUSDT_4h_v2_LSTM2Head_multi_h1-3-5-10.json &
python TheOracle.py --config config/BTCUSDT_8h_v2_LSTM2Head_multi_h1-3-5-10.json &
python TheOracle.py --config config/BTCUSDT_1D_v2_LSTM2Head_multi_h1-3-5-10.json &
```

---

## Performance Considerations

### Data Volume

From 1 year of 1h data (8760 rows):
- 2h: ~4380 rows
- 4h: ~2190 rows
- 8h: ~1095 rows
- 12h: ~730 rows
- 1D: ~365 rows
- 2D: ~182 rows

### Synthesis Time

**Per timeframe:**
- OHLCV resampling: <1 second
- Feature calculation: 5-30 seconds
- Database save: <1 second

**Total: ~30-60 seconds per timeframe**

### Disk Usage

- 1h data: ~2 MB per year
- Each synthetic timeframe: ~0.5-2 MB per year
- Metadata table: <10 KB

---

## Best Practices

### 1. Synthesize Once, Use Many Times

Generate synthetic timeframes once and reuse:

```bash
# Generate all timeframes upfront
python timeframe_synthesizer.py \
    --symbol BTCUSDT \
    --timeframes 2h 4h 8h 12h 16h 1D 2D \
    --db data_manager/exports/marketdata_base.db
```

### 2. Consistent Feature Systems

Use the same feature system for all timeframes:

```bash
# Always use v2 for consistency
python timeframe_synthesizer.py \
    --symbol BTCUSDT \
    --timeframes 2h 4h 8h \
    --features v2
```

### 3. Regular Updates

Re-synthesize when new 1h data is added:

```bash
# After updating 1h data
python timeframe_synthesizer.py \
    --symbol BTCUSDT \
    --timeframes 2h 4h 8h 1D \
    --db data_manager/exports/marketdata_base.db
```

### 4. Validation

Compare synthetic data with real data (if available):

```python
# Load synthetic 4h
df_synthetic = db.load_synthetic_timeframe("BTCUSDT", "4h")

# Load real 4h (if you have it)
df_real = load_real_4h_data()

# Compare OHLCV
assert (df_synthetic['close'] - df_real['close']).abs().max() < 0.01
```

---

## Limitations

### 1. Forward-Looking Bias

Synthetic timeframes are created from complete periods. Ensure alignment with your trading logic.

### 2. Volume Aggregation

Volume is summed across periods. For volume-weighted indicators, results may differ slightly from exchange data.

### 3. Incomplete Periods

The last incomplete period is dropped. For 4h synthesis from 1h, if you have 15 1h bars, only 3 complete 4h bars are created (12 hours), and the last 3 1h bars are discarded.

### 4. Timestamp Alignment

Timestamps represent the start of each period. Ensure your trading logic accounts for this.

---

## Troubleshooting

### Issue: "No 1h data found"

**Solution:**
- Verify 1h data exists in database
- Check table name: `marketdata`, `btcusdt_1h`, or `ohlcv`
- Run: `sqlite3 <db> "SELECT COUNT(*) FROM <table> WHERE symbol='BTCUSDT' AND timeframe='1h'"`

### Issue: Synthetic data has fewer rows than expected

**Solution:**
- Incomplete periods are dropped
- Check for gaps in source 1h data
- Verify data completeness: `python timeframe_synthesizer.py --symbol BTCUSDT --db <db> --list`

### Issue: Features not calculated

**Solution:**
- Ensure fiboevo module is available
- Check feature system: `--features v2`
- Verify no errors in logs

---

## Files Created

- **`timeframe_synthesizer.py`** (500 lines) - Core synthesis engine
- **`generate_synthetic_presets.py`** (300 lines) - Preset generation integration
- **`SYNTHETIC_TIMEFRAMES_GUIDE.md`** (this file) - Complete documentation

---

## Summary

The Synthetic Timeframes System enables you to:

✅ Generate unlimited timeframes from 1h base data
✅ Maintain consistency across all timeframes
✅ Automatically optimize hyperparameters for each timeframe
✅ Store everything in a unified database
✅ Seamlessly integrate with the preset system

**Next Steps:**

1. Synthesize your first timeframes:
   ```bash
   python timeframe_synthesizer.py --symbol BTCUSDT --timeframes 2h 4h 8h 1D --db <your_db>
   ```

2. Generate presets for them:
   ```bash
   python generate_synthetic_presets.py --symbol BTCUSDT --timeframes 2h 4h 8h 1D --quick
   ```

3. Train models with optimized configurations! 🚀

---

**Author:** BitCorn_Farmer
**Date:** 2025-11-23
**Version:** 1.0.0
