# BitCorn_Farmer - Quick Start Guide

**Version**: 2.0
**Status**: Near Production-Ready
**Last Updated**: 2025-11-18

---

## What is BitCorn_Farmer?

BitCorn_Farmer is a **sophisticated cryptocurrency trading system** that uses:
- **LSTM Neural Networks** to predict price movements and volatility
- **Multi-Horizon Forecasting** for predictions from 1 to 24 hours ahead
- **Reinforcement Learning** (PPO) for adaptive trading strategies
- **Walk-Forward Validation** for robust out-of-sample testing
- **Comprehensive GUI** (TradeApp) for easy interaction

---

## Current Status: What Works Now

### ✅ Ready to Use
1. **Synthetic Data Pipeline** - Fill gaps in historical data
2. **Feature Engineering v2** - 14 clean features, no data leakage
3. **Walk-Forward Validation** - 19 trained model folds
4. **Multi-Horizon Inference** - Predictions with confidence intervals
5. **TradeApp GUI** - Full trading interface with 6 tabs

### ⚠ Needs Setup First
1. **Multi-Config Models** - Configs ready, need training (30-60 min per model)
2. **RL Strategy** - Implemented, needs training
3. **Production Deployment** - Ready to deploy after model training

---

## Quick Setup (5 Minutes)

### Prerequisites
- Python 3.10
- Windows OS
- 10GB free disk space
- (Optional) NVIDIA GPU with CUDA for faster training

### Installation
1. **Clone or navigate to project**
   ```cmd
   cd C:\Users\aladin\Documents\BitCorn_Farmer(beta)
   ```

2. **Verify data is ready**
   ```cmd
   dir synthetic_data_output\btcusdt_1h_filled.csv
   ```
   Should show: 71,613 rows of BTCUSDT 1h data (2017-2025)

3. **Check training configs**
   ```cmd
   type training_plan.json
   ```
   Should show: 5 model configurations ready

4. **Launch GUI** (optional, to explore)
   ```cmd
   py -3.10 TradeApp.py
   ```

---

## Next 3 Steps (Get to Production)

### Step 1: Train Models (4-6 hours)
**Why**: You need trained models to make predictions

```cmd
REM Train baseline model (h=1 hour forecast)
py -3.10 train_multi_configs.py --config run_0001

REM Train multi-horizon models
py -3.10 train_multi_configs.py --config run_0002  REM h=6
py -3.10 train_multi_configs.py --config run_0003  REM h=12
```

**What to expect**:
- Training takes 30-60 minutes per model
- Loss decreases (good sign)
- Models saved to `artifacts/runs/run_000*/models/`

**Success check**:
```cmd
dir artifacts\runs\run_0001\models\best_model.pt
```

### Step 2: Validate Predictions (30 minutes)
**Why**: Ensure predictions are accurate and confidence intervals are correct

```cmd
py -3.10 examples\example_multi_horizon.py
```

**What to expect**:
- Generates 500+ predictions
- Creates plots: `predictions_plot.png`
- Shows metrics: MAE, RMSE, directional accuracy
- CI coverage should be ~95% (after volatility fix)

**Success check**:
- Directional accuracy > 50%
- CI coverage 94-96%
- No errors or NaN values

### Step 3: Test in GUI (10 minutes)
**Why**: Verify everything works in the user interface

```cmd
py -3.10 TradeApp.py
```

**Actions**:
1. Go to **Oracle Tab**
2. Click "Load Model" → Select `artifacts/runs/run_0001/models/best_model.pt`
3. Click "Generate Predictions"
4. View multi-horizon fan chart

**Success check**:
- Fan chart displays 5 horizons
- Confidence bands render
- No feature mismatch errors

---

## How to Use the System

### Make Predictions
```python
from multi_horizon_inference import MultiHorizonInference

# Load trained model
mhi = MultiHorizonInference(
    model_path='artifacts/runs/run_0001/models/best_model.pt',
    data_path='synthetic_data_output/btcusdt_1h_filled.csv'
)

# Generate predictions
results = mhi.predict(mode='jump', num_predictions=100)
```

### View Results in GUI
1. Launch: `py -3.10 TradeApp.py`
2. Navigate to **Oracle Tab** or **Status Tab**
3. Load model and generate predictions
4. View live updates

### Run Backtesting
1. In TradeApp, go to **Backtesting Tab**
2. Select model
3. Configure strategy (RL or rule-based)
4. Run backtest
5. View performance metrics

---

## Project Structure

```
BitCorn_Farmer(beta)/
├── fiboevo.py                    # Core: Feature engineering + LSTM models
├── TradeApp.py                   # GUI application (5,974 lines)
├── multi_horizon_inference.py    # Multi-horizon predictions
├── synthetic_data_generator.py   # Gap filling pipeline
├── model_manager.py              # Model organization
├── walk_forward_validation.py    # Walk-forward validation
│
├── rl_agent/                     # Reinforcement learning
│   ├── ppo_agent.py
│   ├── ppo_trainer.py
│   ├── reward_functions.py
│   └── state_encoder.py
│
├── examples/
│   └── example_multi_horizon.py  # Inference example
│
├── artifacts/
│   ├── runs/                     # Multi-config training outputs
│   │   ├── run_0001/            # Baseline h=1
│   │   ├── run_0002/            # Multi-horizon h=6
│   │   └── run_0003/            # Multi-horizon h=12
│   └── walk_forward/            # 19 walk-forward models
│
├── synthetic_data_output/
│   └── btcusdt_1h_filled.csv    # Main dataset (71,613 rows)
│
└── docs/                        # Documentation
    ├── PROJECT_STATUS.md        # Comprehensive status report
    ├── TODO.md                  # Actionable task list
    ├── TESTING_TODO.md          # Testing tracker
    └── guides/                  # User guides
```

---

## Key Features Explained

### 1. Multi-Horizon Forecasting
- Predict **multiple time horizons** simultaneously (1h, 3h, 6h, 12h, 24h)
- Get **confidence intervals** based on predicted volatility
- Three modes:
  - **Jump**: Best for testing (uses historical data)
  - **Autoregressive**: Experimental generative mode
  - **Native**: For LSTMMultiHead architecture

### 2. Volatility-Aware Predictions
- Model predicts **returns AND volatility**
- Confidence intervals properly scaled by `sqrt(horizon)`
- **Critical fix (2025-11-17)**: CI coverage improved from 43.7% to 95.3%

### 3. Feature Engineering v2
- **14 clean features**, no data leakage
- Technical indicators: SMA, EMA, RSI, ATR, Bollinger Bands
- Fibonacci retracements: 23.6%, 38.2%, 50%, 61.8%
- Returns: 1-step and 5-step
- Automatic feature system detection

### 4. Synthetic Data Pipeline
- Fills **gaps in historical data** using GARCH-GBM
- Maintains statistical properties
- Only 1.03% synthetic (740/71,613 rows)
- 193 gaps filled in BTCUSDT dataset

### 5. Walk-Forward Validation
- **19 model folds** for robust out-of-sample testing
- Rolling window: 30 days train, 7 days test
- Prevents overfitting and data leakage

### 6. Reinforcement Learning (PPO)
- Learns **adaptive trading strategies**
- Multi-objective reward: PnL + Sharpe + Drawdown penalty
- State includes: Market data + LSTM predictions
- Backtesting and paper trading support

---

## Common Tasks

### Train a New Model
```cmd
REM Edit training_plan.json to add new config
REM Then run:
py -3.10 train_multi_configs.py --config run_0006
```

### Compare Model Performance
```python
from model_manager import ModelRunManager

mgr = ModelRunManager()
runs = mgr.list_runs()

for run in runs:
    meta = mgr.load_metadata(run['run_id'])
    print(f"{run['run_id']}: Val Loss = {meta['val_loss']:.4f}")
```

### Select Best Model
```python
from model_manager import select_best_model

best = select_best_model(
    runs_dir='artifacts/runs/',
    metric='val_loss',
    minimize=True
)

print(f"Best model: {best['run_id']}")
print(f"Val loss: {best['metric_value']:.4f}")
```

### Generate Synthetic Data
```cmd
py -3.10 synthetic_data_generator.py ^
  --input data/btcusdt_1h.csv ^
  --output synthetic_data_output/btcusdt_1h_filled.csv ^
  --method garch-gbm
```

---

## Troubleshooting

### Issue: "ModuleNotFoundError: No module named 'torch'"
**Solution**: Install PyTorch
```cmd
py -3.10 -m pip install torch torchvision
```

### Issue: "CUDA out of memory"
**Solution**: Reduce batch size in training config
```json
{
  "batch_size": 32  // Try 16 or 8
}
```

### Issue: "Feature mismatch error"
**Solution**: This is automatically handled now! Feature system v2 auto-detects from metadata.

If you still get errors:
```python
# Check model metadata
import json
with open('artifacts/runs/run_0001/meta.json') as f:
    meta = json.load(f)
print(meta['feature_cols'])  # Should match your data
```

### Issue: Training is very slow
**Solutions**:
1. Use GPU (CUDA): 10-20x faster
2. Reduce sequence length in config
3. Reduce hidden units
4. Use fewer layers

### Issue: Predictions are all NaN
**Check**:
1. Data has no NaN values: `df.isna().sum()`
2. Model loaded correctly
3. Feature generation successful
4. Scaler parameters exist in metadata

---

## Performance Benchmarks

### Training Times (CPU)
- Baseline (h=1, 192 hidden): ~45 minutes
- Multi-horizon (h=6, 256 hidden): ~60 minutes
- Multi-horizon (h=12, 256 hidden): ~75 minutes

### Training Times (GPU - CUDA)
- Baseline: ~5 minutes
- Multi-horizon (h=12): ~10 minutes

### Inference Speed
- Current: 99 predictions in ~4 seconds (40ms each)
- Target: <5ms per prediction (after optimization)

### Accuracy Benchmarks
- Directional accuracy target: >52% (vs 50% random)
- MAPE target: <3%
- Sharpe ratio (RL backtest): >1.0

---

## What's Next

### Immediate (Today)
1. **Train first model**: `py -3.10 train_multi_configs.py --config run_0001`
2. **Validate predictions**: `py -3.10 examples\example_multi_horizon.py`
3. **Test Oracle tab**: Launch TradeApp, test multi-horizon predictions

### This Week
1. Train all 5 model configs
2. Compare performance, select best
3. Fix critical GUI bugs (documented in TODO.md)
4. Run walk-forward validation with new models

### This Month
1. Train and validate RL strategy
2. Complete comprehensive testing (see TESTING_TODO.md)
3. Performance optimization (10x speedup)
4. Deploy to production

---

## Documentation Map

**Quick Reference** (You are here):
- `QUICK_START.md` - This file

**Comprehensive Status**:
- `PROJECT_STATUS.md` - Full project status, component by component
- `TODO.md` - Actionable task list with priorities and timelines
- `TESTING_TODO.md` - Testing tracker with test cases

**Technical Guides**:
- `SYNTHETIC_DATA_GUIDE.md` - Synthetic data pipeline (1000+ lines)
- `VOLATILITY_SCALING_FIX.md` - Critical volatility fix explanation
- `MULTI_HORIZON_INFERENCE.md` - Multi-horizon prediction guide

**Session Reports**:
- `DEPLOYMENT_SUMMARY_2025-11-17.md` - Latest deployment status
- `SESSION_COMPLETE_2025-11-17.md` - Complete session report
- Older reports in `docs/archive/` (to be created)

---

## Getting Help

### Common Questions

**Q: Which model should I use?**
A: Start with run_0001 (baseline h=1). After training all configs, use `select_best_model()` to choose optimal.

**Q: How much historical data do I need?**
A: Minimum 6 months for training. Currently using 8 years (2017-2025).

**Q: Can I use this for other cryptocurrencies?**
A: Yes! Just provide OHLCV data in the same format as BTCUSDT.

**Q: Should I use RL or rule-based strategy?**
A: RL is more adaptive but requires training. Start with rule-based for simplicity.

**Q: How do I know if predictions are good?**
A: Check:
- Directional accuracy > 52%
- CI coverage ~95%
- MAPE < 3%
- No systematic bias

### Documentation
- Read `PROJECT_STATUS.md` for detailed component status
- Read `TODO.md` for task priorities
- Read `TESTING_TODO.md` for test cases

### Issues
- Check `TODO.md` for known issues and fixes
- Review `TESTING_TODO.md` for test status
- Read technical docs in `docs/` folder

---

## Key Takeaways

### What You Get
1. **Sophisticated LSTM System** - Dual-head architecture (returns + volatility)
2. **Multi-Horizon Predictions** - 1 to 24 hours ahead with confidence intervals
3. **Production-Ready Data Pipeline** - Synthetic gap filling, robust validation
4. **Comprehensive GUI** - Easy-to-use interface for all operations
5. **RL Integration** - Adaptive trading strategies (needs training)

### Critical Recent Fixes
1. **Volatility Scaling (2025-11-17)** - CI coverage now 95.3% (was 43.7%)
2. **Feature Auto-Detection (2025-11-16)** - Backward compatible v1/v2/v3
3. **Synthetic Data Validation** - 193 gaps filled, quality validated

### Main Bottleneck
- **Models prepared but not trained** - Ready to execute training plan

### Time to Production
- **Training**: 4-6 hours
- **Validation**: 2-3 hours
- **Bug Fixes**: 1 hour
- **Total**: ~1 day to production-ready system

---

## Success Checklist

Before considering the system "production-ready", verify:

- [ ] At least one model trained successfully
- [ ] Inference validated (CI coverage 94-96%)
- [ ] Oracle tab functional in GUI
- [ ] Critical GUI bugs fixed
- [ ] Backtesting works
- [ ] Walk-forward validation completed
- [ ] Documentation reviewed
- [ ] Performance acceptable (<5s per prediction)
- [ ] Error handling robust
- [ ] Monitoring in place (optional)

---

## Quick Command Reference

```cmd
REM Train baseline model
py -3.10 train_multi_configs.py --config run_0001

REM Validate predictions
py -3.10 examples\example_multi_horizon.py

REM Launch GUI
py -3.10 TradeApp.py

REM Generate synthetic data
py -3.10 synthetic_data_generator.py --input data.csv --output filled.csv

REM Run tests
py -3.10 test_feature_generation_v2.py
py -3.10 test_volatility_fix.py

REM Select best model
py -3.10 -c "from model_manager import select_best_model; print(select_best_model())"

REM Walk-forward validation
py -3.10 walk_forward_validation.py --model best_model.pt --data data.csv
```

---

**Quick Start Version**: 1.0
**Last Updated**: 2025-11-18
**For detailed information, see**: `PROJECT_STATUS.md`

**Ready to start? Run**: `py -3.10 train_multi_configs.py --config run_0001`
