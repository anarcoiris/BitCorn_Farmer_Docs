# Automatic Signal Flagging & Guided Rewards

## Overview
The Automatic Signal Flagging system (`AutomaticSignalFlagger`) and Guided Reward system (`GuidedReward`) work together to bridge the gap between traditional technical analysis and Reinforcement Learning.

### Concept
Instead of hoping the RL agent randomly discovers that "Golden Cross = Buy", we explicitly **flag** these events in the observation space and optionally **reward** the agent for acting in accordance with them.

- **Signal Flagger**: Scans technical indicators (RSI, Moving Averages, etc.) and outputs `buy_flag` and `sell_flag` along with their strengths.
- **Guided Reward**: Adds a bonus to the normal financial reward (e.g., Sharpe Ratio) if the agent's action aligns with the flag, and a penalty if it opposes it.

## Configuration
These features are configured in `config/rl_config.json` (or via the UI).

```json
{
  "environment": {
    "signal_detectors": {
      "enabled": true,
      "weights": {
        "rsi_momentum": 1.0,
        "ma_cross": 0.8
      },
      "detectors": {
        "rsi_momentum": { "enabled": true },
        "ma_cross": { "enabled": true }
      }
    }
  },
  "reward": {
    "guidance": {
      "enabled": true,
      "min_strength": 0.3,
      "weights": {
        "buy_signal_reward": 0.1,
        "sell_signal_reward": 0.1,
        "against_signal_penalty": 0.2,
        "conflict_penalty": 0.05
      }
    }
  }
}
```

## Supported Detectors

### 1. RSI Momentum
- **Logic**: Detects overbought/oversold reversals.
- **Buy**: RSI < 30 and starts rising.
- **Sell**: RSI > 70 and starts falling.

### 2. MA Cross (Moving Average Crossover)
- **Logic**: Traditional Golden/Death Cross.
- **Buy**: Fast MA (e.g., 50) crosses above Slow MA (e.g., 200).
- **Sell**: Fast MA crosses below Slow MA.

### 3. Volatility Breakout
- **Logic**: Price breaking out of Bollinger Bands.
- **Buy**: Close > Upper Band.
- **Sell**: Close < Lower Band.

## Architecture

### AutomaticSignalFlagger (`rl_agent/signal_flagging.py`)
- Standardizes outputs from multiple `SignalDetector` classes.
- Resolves conflicts (e.g., RSI says Buy but MA says Sell) using a weighted voting system.
- Injects flags into the `TradingEnvironment`'s `info` dictionary.

### GuidedReward (`rl_agent/reward_functions.py`)
- Wraps the primary reward function (e.g., `SharpeReward`).
- Intercepts the `info` dictionary from the step result.
- Calculates bonus/penalty based on `info['flags']`.
- **Formula**: `FinalReward = BaseReward + (SignalStrength * Weight * DirectionMultiplier)`

## UI Integration
- **Training Controls**: dedicated "Signal Detectors" panel in `RLStrategyTab`.
- **Strategy Editor**: "Guided Reward Configuration" section to tune weights.
- **Dynamic Patching**: UI settings are injected into the training process via temporary config files on launch.

## Best Practices
1. **Start Strong, Fade Later**: Give high guidance weights early in training to jump-start learning, then reduce them (Curriculum Learning) if possible (currently requires manual intervention or multiple runs).
2. **Don't Over-Reward**: If the guided reward is too high compared to the financial reward (Sharpe), the agent will just blindly follow signals and ignore risk/drawdown. Keep guidance weights small (0.1 - 0.5 range).
3. **Verify Detectors**: Ensure the technical indicators used by detectors are actually reasonable for your timeframe.
