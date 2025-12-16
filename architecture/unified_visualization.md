# Unified Visualization Architecture

## Overview

The Unified Visualization Component (`UnifiedChartCanvas`) is a modular, high-performance charting system designed to replace ad-hoc Matplotlib/Tkinter implementations across the application. It ensures consistent styling, robust threading, and centralized configuration persistence.

## Core Architecture

### Component Structure

```
ui/components/
├── unified_chart.py          # Core visualization engine (Frame + Matplotlib)
└── charts/layers/            # Modular indicator layers
    ├── base_layer.py         # Abstract Base Class
    ├── candlestick_layer.py  # OHLC rendering with dynamic width
    ├── volume_layer.py       # Volume bars (shared/twin axis)
    ├── indicator_layer.py    # Line-based indicators (SMA, BB, RSI)
    └── signal_layer.py       # Trading signals (TD Seq, Buy/Sell)
```

### Key Design Principles

1.  **Threading Safety**:
    -   All heavy data processing (indicators, signals) happens *outside* the layer code, in background threads (Workers).
    -   The UI thread only handles rendering pre-computed data.
    -   `UnifiedChartCanvas` manages a thread lock for internal state updates.

2.  **Modular Layers**:
    -   Each visualization type is a `ChartLayer` subclass.
    -   Layers implement a simple `render(ax, df, config)` interface.
    -   Layers are independent; failure in one does not crash the chart (per-layer try/except).

3.  **Config Persistence**:
    -   Configuration (toggles, colors) is passed as a standard dictionary.
    -   Integrates with `ConfigManager` hierarchical loading (flat `app.*` + nested tab config).

## Layer System

### 1. `CandlestickLayer`
-   **Problem**: Standard matplotlib `plot()` doesn't handle OHLC bars. `mplfinance` is heavy.
-   **Solution**: Custom rendering using `ax.bar()` with `matplotlib.dates.date2num` for precise width calculation.
-   **Features**: Dynamic width based on time delta, separate Up/Down colors and wicks.

### 2. `VolumeLayer`
-   **Features**: Overlaid volume bars, support for separate subplot or twin axis (push-down scaling).

### 3. `IndicatorLayer`
-   **Supported**: SMA, EMA, Bollinger Bands, RSI.
-   **Rendering**: Optimized line plotting with `fill_between` support for bands.

### 4. `SignalLayer`
-   **features**: TD Sequential (9/13), Custom Buy/Sell markers.
-   **Assets**: Uses optimized scatter plots for markers.

## Integration Guide

### Basic Usage

```python
from ui.charts import UnifiedChartCanvas, CandlestickLayer, VolumeLayer, IndicatorLayer

# 1. Prepare Layers
layers = [
    CandlestickLayer(),
    VolumeLayer(),
    IndicatorLayer(indicators=[
        {'column': 'sma_20', 'label': 'SMA(20)', 'color': 'blue'}
    ])
]

# 2. Create Canvas
chart = UnifiedChartCanvas(parent_frame, layers=layers)
chart.pack(fill='both', expand=True)

# 3. Update Data
config = {'show_candlestick': True, 'show_volume': True}
chart.update_data(df, config)
```

### Advanced Features

-   **DataPreviewWindow Integration**: Serves as the experimental testbed for the system.
-   **PreviewTab Migration**: Planned migration to replace legacy code.

## Future Advanced Layers (Phase 3)

1.  **`PredictionLayer`**: For Oracle Monte Carlo paths (Fan Charts).
2.  **`RegionLayer`**: For Walk-Forward Fold visualization (Train/Test splits).
3.  **`InferenceLayer`**: Real-time ticker updates.
