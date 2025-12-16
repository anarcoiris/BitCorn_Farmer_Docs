# Data Pipeline Integration Guide

**Version**: 1.0  
**Date**: 2025-12-03  
**Status**: Production Ready

---

## Overview

The unified data pipeline (`data_pipeline` module) centralizes all data loading, feature preparation, and sequence building operations across the BitCorn_Farmer system. This guide explains how to use it and manage its intelligent caching system.

---

## Architecture

```
data_pipeline/
├── __init__.py
├── data_loader.py       # Core data loading and feature prep
└── training_service.py  # Unified LSTM training wrapper

cache/
└── data_pipeline/
    ├── index.json           # Cache metadata index
    ├── features/            # Cached feature datasets (parquet)
    └── data_quality/        # Quality reports (json)
```

---

## Core Functions

### 1. Loading Market Data

```python
from data_pipeline.data_loader import load_market_data

result = load_market_data(
    db_path="data/btcusdt_1h.db",
    symbol="BTCUSDT",
    timeframe="1h",
    limit=None,  # Optional row limit
    log_fn=print  # Optional logging callback
)

df = result.dataframe
quality_report = result.quality_report

print(f"Loaded {len(df)} rows")
print(f"Quality: {quality_report}")
```

**Features**:
- Multi-strategy query fallback (handles various DB schemas)
- Automatic timestamp normalization (`ts` → `timestamp`)
- Built-in quality diagnostics (gaps, duplicates, missing values)

---

### 2. Preparing Features

```python
from data_pipeline.data_loader import prepare_features

feature_result = prepare_features(
    df=df,
    feature_system="v4",  # or "v2"
    use_cache=True,       # Enable on-disk caching
    db_path="data/btcusdt_1h.db",  # For cache validation
    log_fn=print
)

features_df = feature_result.dataframe
feature_cols = feature_result.feature_cols
cache_key = feature_result.cache_key

print(f"Features: {feature_cols}")
print(f"Cache key: {cache_key}")
print(f"Cache hit: {feature_result.quality_report.get('cache_hit', False)}")
```

**Features**:
- Automatic feature engineering via `fiboevo`
- On-disk Parquet caching (~30-50x speedup on cache hit)
- Cache invalidation based on DB modification
- Quality reports persisted alongside features

---

### 3. Building Sequences

```python
from data_pipeline.data_loader import build_sequences

seq_result = build_sequences(
    df=features_df,
    feature_cols=feature_cols,
    seq_len=64,
    horizon=1,
    fill_gaps_strategy="forward",  # Optional gap filling
    max_gap_size=10,
    fit_scaler=True,
    log_fn=print
)

X = seq_result.X           # (N, seq_len, n_features)
y_ret = seq_result.y_ret   # (N,)
y_vol = seq_result.y_vol   # (N,)
scaler = seq_result.scaler
metadata = seq_result.metadata
```

**Features**:
- Delegates to `fiboevo.create_sequences_from_df`
- Optional StandardScaler fitting
- Gap detection and filling
- Returns numpy arrays ready for PyTorch

---

### 4. Training LSTM Models

```python
from data_pipeline.training_service import (
    train_multihorizon_model,
    MultiHorizonTrainingRequest
)

request = MultiHorizonTrainingRequest(
    df=features_df,
    feature_cols=feature_cols,
    horizons=[1, 4, 12],
    seq_len=64,
    hidden_size=256,
    num_layers=2,
    dropout=0.2,
    batch_size=64,
    epochs=50,
    learning_rate=1e-3,
    val_frac=0.2,
    symbol="BTCUSDT",
    timeframe="1h"
)

result = train_multihorizon_model(
    request=request,
    artifacts_dir="models/run_001",
    log_fn=print
)

model = result.model
scaler = result.scaler
metadata = result.metadata
```

**Features**:
- Wraps `fiboevo.train_multihorizon_lstm`
- Automatic artifact persistence (model.pt, scaler.pkl, metadata.json)
- Unified logging and error handling

---

## Cache Management

### Understanding the Cache

The pipeline uses a two-tier caching system:

1. **Feature Cache** (`cache/data_pipeline/features/`)
   - Stores precomputed feature DataFrames as Parquet files
   - Named by content hash: `<hash>.parquet` + `<hash>.meta.json`
   - Speeds up repeated loads by ~30-50x

2. **Cache Index** (`cache/data_pipeline/index.json`)
   - Tracks all cache entries with metadata
   - Stores DB hash for automatic invalidation
   - Records timestamp ranges and feature systems

### Cache Validation

Cache entries are automatically validated on each load:

```python
# Cache is checked before feature computation
feature_result = prepare_features(
    df=df,
    feature_system="v4",
    use_cache=True,
    db_path="data/btcusdt_1h.db"  # Required for validation
)

# If DB has changed since cache creation:
# - Cache is marked stale and removed
# - Features are recomputed
# - New cache entry is created
```

**Validation Logic**:
- Computes hash of DB file (modification time + size)
- Compares against stored hash in cache index
- Automatically invalidates on mismatch

### Manual Cache Management

```python
from data_pipeline.data_loader import invalidate_cache

# Clear ALL cache
count = invalidate_cache()
print(f"Cleared {count} cache entries")

# Clear cache for specific database
count = invalidate_cache(db_path="data/btcusdt_1h.db")
print(f"Cleared {count} entries for btcusdt_1h.db")
```

**When to Invalidate**:
- After updating data in the database
- After modifying feature engineering code
- When switching between feature systems (v2 ↔ v4)
- To free disk space (cache files can be large)

### Cache Location

```
cache/data_pipeline/
├── index.json                    # 2-10 KB
├── features/
│   ├── a1b2c3d4e5f6g7h8.parquet # ~500 KB - 5 MB per entry
│   ├── a1b2c3d4e5f6g7h8.meta.json
│   ├── x9y8z7w6v5u4t3s2.parquet
│   └── x9y8z7w6v5u4t3s2.meta.json
└── data_quality/
    ├── a1b2c3d4e5f6g7h8.json    # ~1-5 KB per report
    └── x9y8z7w6v5u4t3s2.json
```

**Disk Usage**:
- Typical feature cache: 500 KB - 5 MB per dataset
- Quality reports: 1-5 KB each
- Index: ~2-10 KB

---

## Integration Examples

### Example 1: Feature Evolution Workflow

```python
from data_pipeline.data_loader import (
    load_market_data,
    prepare_features
)

# Load once, reuse for all generations
market_result = load_market_data(
    db_path="data/btcusdt_1h.db",
    symbol="BTCUSDT",
    timeframe="1h"
)

# Prepare features (cached for subsequent runs)
feature_result = prepare_features(
    df=market_result.dataframe,
    feature_system="v4",
    use_cache=True,
    db_path="data/btcusdt_1h.db",
    log_fn=print
)

# Run evolution with precomputed features
# (no recomputation needed between generations)
```

### Example 2: Hyperparameter Optimization

```python
# First trial: computes features and caches
trial_1_features = prepare_features(
    df=df,
    feature_system="v2",
    use_cache=True,
    db_path="data/btcusdt_1h.db"
)

# Subsequent trials: instant load from cache
trial_2_features = prepare_features(
    df=df,
    feature_system="v2",
    use_cache=True,
    db_path="data/btcusdt_1h.db"
)
# ✓ Cache hit! Loaded in <100ms instead of 5-10s
```

### Example 3: Walk-Forward Validation

```python
# Load and prepare once
market_result = load_market_data(...)
feature_result = prepare_features(
    df=market_result.dataframe,
    feature_system="v2",
    use_cache=True,
    db_path="..."
)

# Split into folds without recomputing features
for fold in folds:
    train_df = feature_result.dataframe.iloc[fold.train_indices]
    val_df = feature_result.dataframe.iloc[fold.val_indices]
    
    # Build sequences for this fold
    train_seq = build_sequences(train_df, ...)
    val_seq = build_sequences(val_df, ...)
    
    # Train model
    result = train_multihorizon_model(...)
```

---

## Performance Benchmarks

Typical performance improvements with caching enabled:

| Operation | Without Cache | With Cache | Speedup |
|-----------|--------------|------------|---------|
| Feature prep (v2, 70K rows) | 5-8s | 150-300ms | ~30x |
| Feature prep (v4, 70K rows) | 8-12s | 200-400ms | ~35x |
| Sequence building | 2-4s | 2-4s | 1x (not cached) |
| Full pipeline | 10-15s | 2-5s | ~4x |

**Note**: Sequence building is not cached because it's relatively fast and depends on dynamic parameters (seq_len, horizon, etc.).

---

## Troubleshooting

### Cache Not Working

**Symptom**: Features are recomputed every time

**Solutions**:
1. Ensure `use_cache=True` in `prepare_features()`
2. Provide `db_path` parameter for validation
3. Check `cache/data_pipeline/` directory exists and is writable
4. Verify disk space available

### Stale Cache Issues

**Symptom**: Using outdated features after DB update

**Solutions**:
1. Automatic: Cache should auto-invalidate on DB change
2. Manual: Call `invalidate_cache(db_path="...")`
3. Nuclear: Delete entire `cache/data_pipeline/` directory

### Disk Space Concerns

**Symptom**: Cache directory growing too large

**Solutions**:
```python
# Option 1: Clear specific DB cache
invalidate_cache(db_path="data/old_data.db")

# Option 2: Clear all cache
invalidate_cache()

# Option 3: Manual cleanup
import shutil
shutil.rmtree("cache/data_pipeline")
```

### Import Errors

**Symptom**: `ModuleNotFoundError: No module named 'data_pipeline'`

**Solution**:
- Ensure `data_pipeline/__init__.py` exists
- Check working directory is project root
- Verify Python path includes project directory

---

## Best Practices

1. **Always provide `db_path`**: Enables automatic cache validation
   ```python
   prepare_features(df, db_path="data/btcusdt_1h.db", ...)
   ```

2. **Use caching for repeated workflows**: Feature evolution, hyperparameter optimization
   ```python
   prepare_features(df, use_cache=True, ...)
   ```

3. **Invalidate after DB updates**: Prevent stale features
   ```python
   # After updating database
   invalidate_cache(db_path="data/btcusdt_1h.db")
   ```

4. **Monitor cache size**: Clean up periodically if disk space is limited
   ```bash
   du -sh cache/data_pipeline/
   ```

5. **Log quality reports**: Always check data quality diagnostics
   ```python
   result = load_market_data(...)
   print(result.quality_report)
   ```

---

## Migration Guide

### Migrating Existing Code

**Before** (manual data loading):
```python
import sqlite3
import pandas as pd
from fiboevo import add_technical_features_v2

conn = sqlite3.connect("data/btcusdt_1h.db")
df = pd.read_sql_query("SELECT * FROM ohlcv", conn)
conn.close()

features_df = add_technical_features_v2(
    close=df['close'].values,
    high=df['high'].values,
    low=df['low'].values,
    volume=df['volume'].values,
    dropna_after=True
)
```

**After** (unified pipeline):
```python
from data_pipeline.data_loader import load_market_data, prepare_features

# Load with fallback strategies and quality checks
market_result = load_market_data(
    db_path="data/btcusdt_1h.db",
    symbol="BTCUSDT",
    timeframe="1h"
)

# Prepare features with caching
feature_result = prepare_features(
    df=market_result.dataframe,
    feature_system="v2",
    use_cache=True,
    db_path="data/btcusdt_1h.db"
)

features_df = feature_result.dataframe
```

**Benefits**:
- Automatic caching (30-50x faster on subsequent runs)
- Quality diagnostics included
- Multi-strategy query fallback
- Cache invalidation on DB changes

---

## API Reference

### `load_market_data()`

```python
def load_market_data(
    db_path: str,
    symbol: str,
    timeframe: str,
    *,
    limit: Optional[int] = None,
    log_fn: Optional[Callable[[str], None]] = None,
) -> MarketDataResult
```

### `prepare_features()`

```python
def prepare_features(
    df: pd.DataFrame,
    feature_system: str = "v2",
    *,
    cache_dir: Path = FEATURE_CACHE_DIR,
    use_cache: bool = True,
    feature_registry: Optional[Any] = None,
    db_path: Optional[str] = None,
    log_fn: Optional[Callable[[str], None]] = None,
) -> FeaturePrepResult
```

### `build_sequences()`

```python
def build_sequences(
    df: pd.DataFrame,
    feature_cols: List[str],
    *,
    seq_len: int,
    horizon: int,
    fill_gaps_strategy: Optional[str] = None,
    max_gap_size: Optional[int] = None,
    validate_chronology: bool = True,
    dtype: Any = np.float32,
    fit_scaler: bool = True,
    scaler: Optional[StandardScaler] = None,
    log_fn: Optional[Callable[[str], None]] = None,
) -> SequenceBuildResult
```

### `invalidate_cache()`

```python
def invalidate_cache(db_path: Optional[str] = None) -> int
```

### `train_multihorizon_model()`

```python
def train_multihorizon_model(
    request: MultiHorizonTrainingRequest,
    *,
    artifacts_dir: Optional[Path] = None,
    log_fn: Optional[Callable[[str], None]] = None,
) -> TrainingResult
```

---

## Support

For issues or questions:
1. Check this guide first
2. Review `data_pipeline/data_loader.py` source code
3. Check `docs/PROJECT_STATUS.md` for system overview
4. File an issue with reproduction steps

---

**Last Updated**: 2025-12-03  
**Maintainer**: BitCorn_Farmer Development Team  
**Version**: 1.0

