# RL System Architecture & Standardization

## Overview
This document outlines the architecture of the Reinforcement Learning (RL) system within the BitCorn Farmer project. It details the file structure, component relationships, and the "Backend Election" mechanism that allows flexible selection of training pipelines.

## Directory Structure

### Core Components (`rl_agent/`)
The `rl_agent` directory contains the core logic for the RL agents. It is designed to be a library used by scripts and the UI.
-   **`envs/`**: Gym environments (`trading_env.py`, `portfolio_env.py`).
-   **`strategies/`**: Base strategy implementations.
-   **`utils/`**: Shared utilities (e.g., `logger.py`).
-   **`ppo_agent.py`**: The PPO Agent implementation (`TradingActorCritic`).
-   **`ppo_trainer.py`**: The training loop logic.
-   **`reward_functions.py`**: Modular reward function definitions (`SharpeReward`, `RobustCompositeReward`, etc.).
-   **`strategy_manager.py`**: **Pipeline Registry** for managing available training backends.

### Execution Scripts (`scripts/rl/`)
Scripts that act as entry points for training and evaluation.
-   **`rl_training_pipeline_unified.py`**: The recommended, modern pipeline supporting both Single Agent and Multi-Strategy modes.
-   **`rl_training_pipeline.py`**: Legacy pipeline (Deprecated).
-   **`rl_training_pipeline_multi_strategy.py`**: Legacy multi-strategy pipeline (Deprecated).
-   **`utils.py`**: Shared utilities for data loading and config management.

### UI Integration (`ui/`)
-   **`tabs/rl_strategy_tab.py`**: The main GUI tab for RL. Features a "Backend" selector to choose the training pipeline.
-   **`workers/rl_worker.py`**: The background worker process. It uses `rl_agent.strategy_manager.PipelineRegistry` to dynamically load and execute the selected pipeline.

## Backend Election Mechanism

To address code scatter and ensure conflict-free execution, the system implements a **Backend Election** pattern.

1.  **Registry**: `rl_agent.strategy_manager.PipelineRegistry` maintains a list of available `RLPipeline` definitions.
2.  **Selection**: The UI (`RLStrategyTab`) queries this registry to populate the "Backend" dropdown.
3.  **Execution**: When training starts, the selected backend name is passed to `rl_worker.py`.
4.  **Dynamic Loading**: `rl_worker.py` uses the registry to look up the module path and function name for the selected backend, then dynamically imports and executes it.

### Adding a New Pipeline
To add a new experimental pipeline:
1.  Create the script in `scripts/rl/`.
2.  Register it in `rl_agent/strategy_manager.py` within `PipelineRegistry.get_defaults()`.
3.  The UI will automatically pick it up.

## Configuration & Logging
-   **Configuration**: All pipelines should respect `config/rl_config.json` (or `MasterConfigManager`).
-   **Logging**:
    -   **Training Logs**: Saved to `artifacts/runs/<run_name>/training.log` (Unified) or `artifacts/logs/` (Legacy).
    -   **Episode Logs**: Handled by `rl_agent.utils.logger.RLEpisodeLogger` (formerly in root).

## Best Practices
-   **Do not** import scripts from `scripts/rl/` into other library code. Use them only as entry points.
-   **Do** put reusable logic in `rl_agent/` or `core/`.
-   **Do** use `PipelineRegistry` to expose new capabilities to the UI.
