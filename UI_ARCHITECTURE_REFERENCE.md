# BitCorn Farmer UI Architecture Reference

**Version**: 2.0 (Post-Refactor)  
**Date**: 2025-12-11  
**Status**: CURRENT

---

## ⚠️ CRITICAL: Legacy Code Warning

### TradeApp.py is LEGACY CODE
- **File**: `TradeApp.py` (5,974 lines)
- **Status**: **DEPRECATED** - Reference only
- **DO NOT USE** for new features or bug fixes
- All functionality has been migrated to modular UI structure

### Why Did We Refactor?
- Monolithic 6,000-line file was unmaintainable
- Testing individual components was impossible
- Threading/worker patterns were inconsistent
- No separation of concerns
- Difficult to onboard new developers

---

## Current UI Architecture (Modular)

### Entry Points
```
run_ui.py           # Main entry point (launches UI)
  └─> ui/ui_main_window.py  # TradingAppExtended class (800 lines)
```

### Directory Structure
```
ui/
├── ui_main_window.py      # Main window coordinator
├── tabs/                  # Individual tab implementations
│   ├── preview_tab.py
│   ├── training_container_tab.py
│   ├── backtest_tab.py    # Strategy Lab with sub-tabs
│   ├── status_tab.py
│   ├── audit_tab.py
│   ├── rl_strategy_tab.py
│   ├── feature_evolution_tab.py
│   ├── feature_composer_tab.py
│   └── walkforward_tab.py
├── workers/               # Background task executors
│   ├── training_worker.py
│   ├── backtest_worker.py
│   ├── optimization_worker.py
│   ├── rl_worker.py
│   ├── oracle_worker.py
│   └── feature_evolution_worker.py
├── components/            # Reusable UI components
│   ├── oracle_window.py
│   ├── forecast_window.py
│   ├── multihorizon_dashboard.py
│   ├── data_preview_window.py
│   ├── websocket_panel.py
│   └── logging_panel.py
├── utils/                 # UI utilities
│   ├── config_manager.py
│   ├── toast.py
│   ├── error_manager.py
│   └── validation.py
└── styles/                # Theming and styling
    ├── theme_manager.py
    └── colors.py
```

---

## File Mapping: Legacy → Modern

When reading old documentation that references TradeApp.py:

| Legacy Reference | Modern Location | Notes |
|-----------------|----------------|-------|
| `TradeApp.py` (main) | `ui/ui_main_window.py` | Main window class |
| `TradeApp._build_backtest_tab()` | `ui/tabs/backtest_tab.py` | Strategy Lab tab |
| `TradeApp._backtest_worker()` | `ui/workers/backtest_worker.py` | Backtest execution |
| `TradeApp._optimization_worker()` | `ui/workers/optimization_worker.py` | Hyperparameter optimization |
| `TradeApp._build_training_tab()` | `ui/tabs/training_container_tab.py` | Training container |
| `TradeApp._training_worker()` | `ui/workers/training_worker.py` | Model training |
| `TradeApp._open_oracle_window()` | `ui/components/oracle_window.py` | Oracle simulation |
| `TradeApp._open_forecast_window()` | `ui/components/forecast_window.py` | Forecast window |
| `TradeApp._poll_predictions()` | `ui/tabs/status_tab.py` | Prediction polling |
| `TradeApp._build_status_tab()` | `ui/tabs/status_tab.py` | Status/monitoring |
| `TradeApp._build_rl_strategy_tab()` | `ui/tabs/rl_strategy_tab.py` | RL strategy interface |
| `TradeApp._rl_training_worker()` | `ui/workers/rl_worker.py` | RL training |
| `TradeApp._build_feature_evolution_tab()` | `ui/tabs/feature_evolution_tab.py` | Feature evolution |
| `TradeApp._feature_evolution_worker()` | `ui/workers/feature_evolution_worker.py` | Evolution execution |
| `TradeApp._build_feature_composer_tab()` | `ui/tabs/feature_composer_tab.py` | Feature composer |
| `TradeApp._open_ws_panel()` | `ui/components/websocket_panel.py` | WebSocket panel |

---

## Key Architecture Patterns

### 1. Tab Pattern
Each tab is a self-contained class:
```python
# ui/tabs/example_tab.py
from ui.tabs.base_tab import BaseTab

class ExampleTab(BaseTab):
    def __init__(self, parent, app):
        super().__init__(parent, app)
        self._build_ui()
    
    def _build_ui(self):
        # Build tab UI
        pass
```

### 2. Worker Pattern
Background tasks run in separate threads:
```python
# ui/workers/example_worker.py
def example_worker(app, **config):
    """Worker function for background execution."""
    try:
        # Do work
        app.log("Task complete")
    except Exception as e:
        app.log(f"Error: {e}", logging.ERROR)
```

Called from tab:
```python
# In tab class
thread = threading.Thread(
    target=example_worker,
    args=(self.app,),
    kwargs=config,
    daemon=True
)
thread.start()
```

### 3. Component Pattern
Reusable popup windows:
```python
# ui/components/example_window.py
class ExampleWindow:
    def __init__(self, parent, app):
        self.window = tk.Toplevel(parent)
        self.app = app
        self._build_ui()
```

---

## Common Pitfalls When Updating Old Code

### ❌ DON'T: Reference TradeApp.py
```python
# OLD (wrong)
# Modify TradeApp.py:5866
model = fibo.LSTM2Head(...)
```

### ✅ DO: Use Modern Structure
```python
# NEW (correct)
# Modify ui/workers/backtest_worker.py
model = fibo.load_model(model_path, device=device)
```

### ❌ DON'T: Add Features to TradeApp.py
Never add new features to the legacy file.

### ✅ DO: Create New Tab/Worker/Component
Follow the modular pattern in `ui/` directory.

---

## Testing Current UI

### Manual Testing
```bash
# Run modular UI (current)
python run_ui.py

# DO NOT run legacy UI
# python TradeApp.py  # DEPRECATED
```

### Automated Testing
```bash
# Test UI components
python -m pytest tests/ui/

# Test specific tab
python -m pytest tests/ui/test_backtest_tab.py
```

---

## When Reading Old Documentation

If you encounter documentation referencing `TradeApp.py`:

1. **Check date**: Is it archived (pre-2025-12)?
2. **Translate reference**: Use mapping table above
3. **Verify modern location**: Files in `ui/` directory
4. **Update docs** if you're the one finding errors

### Example Translation

**Old doc says:**
> "Fix the backtest bug in TradeApp.py line 5866"

**Translate to:**
> "Fix the backtest bug in ui/workers/backtest_worker.py"
> Note: Check if this has been migrated and already fixed

---

## Migration Status

### ✅ Fully Migrated (Safe to Use)
- All tabs (preview, training, backtest, status, audit, RL strategy)
- All workers (training, backtest, optimization, RL, oracle, evolution)
- All components (windows, panels, dialogs)
- Logging system
- Configuration management
- Theme management
- Error handling

### ⚠️ Legacy References Remaining
- Archived documentation (appropriate - kept for history)
- Some old planning documents (should be updated on review)

### 🚫 Never Migrate
- TradeApp.py stays as reference
- Archive maintains history

---

## For New Developers

### Starting Point
1. Read this document first
2. Explore `ui/ui_main_window.py` (main coordinator)
3. Look at `ui/tabs/preview_tab.py` (simple example)
4. Review worker pattern in `ui/workers/training_worker.py`

### Adding New Features
1. **New Tab**: Copy `ui/ui_tab_template.py` as starting point
2. **New Worker**: Follow pattern in `ui/workers/`
3. **New Component**: Follow pattern in `ui/components/`
4. **Never** edit TradeApp.py

### File Locations Reference
- **Entry**: `run_ui.py`
- **Main Class**: `ui/ui_main_window.py::TradingAppExtended`
- **Tab Template**: `ui/ui_tab_template.py`
- **Base Tab**: `ui/tabs/base_tab.py`

---

## Security Note

**This document exists to prevent accidental use of deprecated code.**

If you're updating features:
1. ✅ Use files in `ui/` directory
2. ✅ Follow modular patterns
3. ✅ Reference this guide
4. ❌ Do not modify TradeApp.py
5. ❌ Do not follow pre-2025-12 docs without translation

---

**Last Updated**: 2025-12-11  
**Maintained By**: Project Lead  
**Review Frequency**: On major UI changes
