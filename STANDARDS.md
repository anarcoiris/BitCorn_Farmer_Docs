# Project Standards & Best Practices

**Date:** 2025-12-10

This document outlines the architectural standards and best practices for the BitCorn_Farmer project. All new development should adhere to these guidelines to ensure consistency and maintainability.

---

## 1. Database Access

**Standard:** DO NOT write raw SQL queries in UI or business logic components.
**Module:** `data_source/db_access.py`
**Class:** `StandardizedDBAccess`

### Why?
Legacy databases found in the wild often use Unix Timestamps in Seconds, while the system standard is Milliseconds. `StandardizedDBAccess` handles this detection and conversion transparently.

### Usage
```python
from data_source.db_access import StandardizedDBAccess

df = StandardizedDBAccess.get_ohlcv_data(
    db_path="path/to/db.sqlite",
    symbol="BTCUSDT",
    timeframe="1h",
    start_date="2023-01-01",
    end_date="2023-02-01"
)
# Returns DataFrame with 'timestamp' column in Milliseconds (int).
```

---

## 2. Configuration Management

**Standard:** Use `master_config.json` via `MasterConfigManager`. Avoid creating new scattered `.json` config files.
**Module:** `core/config_manager.py`
**File:** `config/master_config.json`

### Usage
```python
from core.path_init import ensure_project_root
ensure_project_root()
from core.config_manager import MasterConfigManager

config_mgr = MasterConfigManager("config/master_config.json")
config = config_mgr.load()

# Access values
features = config.get("global", {}).get("features", {})
```

---

## 3. Project Path Resolution

**Standard:** Use `core.path_init` to resolve import paths, especially for scripts running from subdirectories.
**Module:** `core/path_init.py`

### Usage
```python
# At the very top of your script
import sys
from pathlib import Path

# Bootstrap path if necessary
root_dir = Path(__file__).resolve().parent.parent # adjust depth as needed
if str(root_dir) not in sys.path:
    sys.path.insert(0, str(root_dir))

from core.path_init import ensure_project_root
ensure_project_root()
```

---

## 4. Logging

**Standard:** Use Python's built-in `logging` module. Do not use `print()` for status or errors in production code.
**Format:** `%(asctime)s | %(levelname)s | %(message)s`

---
