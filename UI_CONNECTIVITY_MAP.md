# Mapa de Conectividad UI - BitCorn Farmer

**Fecha**: 2025-12-13  
**Versión**: 1.0  
**Estado**: Análisis Completo

---

## Resumen Ejecutivo

Este documento mapea el flujo completo de datos y conexiones en el sistema UI modular, identificando workers, tabs, componentes, y el flujo de estado compartido.

**Tabs Implementados**: 9  
**Workers Implementados**: 7  
**Componentes Implementados**: 12  
**Conexiones Identificadas**: 15  
**Gaps Identificados**: 3

---

## 1. Arquitectura General

```
run_ui.py
  └─> ui/ui_main_window.py (TradingAppExtended)
       ├── Tabs (9)
       ├── Workers (7)
       ├── Components (12)
       └── Shared State
```

---

## 2. Flujo de Datos Principal

### 2.1 Trading Daemon → UI Components

```
trading_daemon.py
  ├── predictions_queue (Queue)
  │     │
  │     ├─> ui/ui_main_window.py::_poll_predictions_queue()
  │     │     └─> ui/tabs/status_tab.py::_update_prediction_fan_display()
  │     │
  │     └─> ui/components/multihorizon_dashboard.py::_poll_predictions()
  │           └─> _update_display()
  │
  ├── log_queue (Queue)
  │     └─> ui/ui_main_window.py::_poll_log_queue()
  │           └─> ui/components/logging_panel.py
  │
  └── model, model_meta, model_scaler
        └─> Shared via app.daemon (accesible por todos los componentes)
```

**Estado**: ✅ FUNCIONAL

**Problemas Identificados**:
- `multihorizon_dashboard.py` y `status_tab.py` ambos hacen polling de la misma queue
- No hay mecanismo de suscripción/notificación (solo polling)
- Posible pérdida de mensajes si queue se llena (maxsize=10)

**Recomendación**: Implementar patrón Observer para notificaciones en lugar de polling múltiple

---

## 3. Mapa de Tabs y Sus Conexiones

### 3.1 PreviewTab

**Ubicación**: `ui/tabs/preview_tab.py`

**Conexiones**:
- **Data**: `app.df_loaded` (shared state)
- **Workers**: Ninguno (solo visualización)
- **Components**: `ui/components/data_preview_window.py`

**Estado**: ✅ CONECTADO

**Gaps**: Ninguno

---

### 3.2 TrainingContainerTab / TrainingTab

**Ubicación**: `ui/tabs/training_container_tab.py`, `ui/tabs/training_tab.py`

**Conexiones**:
- **Workers**: 
  - `ui/workers/training_worker.py::prepare_data_worker()`
  - `ui/workers/training_worker.py::train_model_worker()`
  - `ui/workers/training_worker.py::prepare_and_train_worker()`
  - `ui/workers/retraining_worker.py::retrain_lstm_worker()`
- **Shared State**: 
  - `app.model`, `app.model_meta`, `app.model_scaler` (después de entrenamiento)
  - `app.df_loaded`, `app.df_features` (datos preparados)
- **Components**: `training_options_panel.py` (desde root, precario)

**Estado**: ✅ CONECTADO

**Gaps**:
- `training_options_panel.py` está en root, debería estar en `ui/components/`

---

### 3.3 BacktestTab

**Ubicación**: `ui/tabs/backtest_tab.py`

**Conexiones**:
- **Workers**: 
  - `ui/workers/backtest_worker.py::backtest_worker()`
  - `ui/workers/backtest_worker.py::optimization_worker()`
- **Shared State**: 
  - `app.model`, `app.model_meta` (para backtesting)
  - `app.df_loaded` (datos históricos)
- **External**: `hyperparameter_phases.py::ExperimentTracker`

**Estado**: ✅ CONECTADO

**Gaps**: Ninguno crítico

---

### 3.4 StatusTab

**Ubicación**: `ui/tabs/status_tab.py`

**Conexiones**:
- **Daemon**: 
  - `app.daemon` (control start/stop)
  - `app.daemon.predictions_queue` (polling)
- **Shared State**: 
  - `app.daemon` (estado del daemon)
  - Recibe predicciones vía `_poll_predictions_queue()` en `ui_main_window.py`
- **Components**: 
  - `ui/components/multihorizon_dashboard.py` (se puede abrir desde tab)
  - `ui/components/websocket_panel.py` (integración WebSocket)

**Estado**: ✅ CONECTADO

**Gaps**:
- Polling duplicado: `ui_main_window.py` y `multihorizon_dashboard.py` ambos hacen polling
- No hay sincronización entre múltiples consumidores de `predictions_queue`

---

### 3.5 AuditTab (Oracle)

**Ubicación**: `ui/tabs/audit_tab.py`

**Conexiones**:
- **Workers**: 
  - `ui/workers/oracle_worker.py` (Monte Carlo simulations)
- **Components**: 
  - `ui/components/oracle_window.py` (ventana principal)
- **Shared State**: 
  - `app.model`, `app.model_meta`, `app.model_scaler`
  - `app.df_loaded` (datos históricos)

**Estado**: ✅ CONECTADO

**Gaps**: Ninguno crítico

---

### 3.6 WalkForwardTab

**Ubicación**: `ui/tabs/walkforward_tab.py`

**Conexiones**:
- **Workers**: Thread propio (no usa worker dedicado)
- **External**: `walk_forward_validation.py::run_walk_forward()`
- **Shared State**: `app.df_loaded`

**Estado**: ✅ CONECTADO

**Gaps**:
- No usa patrón worker estándar
- Ejecuta validación directamente en thread

---

### 3.7 WalkForwardVisualizationTab (Wrapper)

**Ubicación**: `ui/tabs/wf_visualization_tab.py`

**Conexiones**:
- **Legacy**: `walk_forward_visualization_tab.py` (root) - WRAPPER PRECARIO
- **Shared State**: `app.df_loaded`, `app.root` (para log_callback)

**Estado**: ⚠️ PRECARIO - Depende de código legacy

**Gaps**:
- Es un wrapper, no migración completa
- Dependencia de código legacy en root

---

### 3.8 RLStrategyTab

**Ubicación**: `ui/tabs/rl_strategy_tab.py`

**Conexiones**:
- **Workers**: 
  - `ui/workers/rl_worker.py::rl_training_worker()`
- **Shared State**: 
  - `app.daemon` (para integración con trading)
  - `app.rl_agent_path` (ruta al agente RL)
- **External**: 
  - `scripts/rl/rl_training_pipeline.py`
  - `rl_agent/ppo_trainer.py`

**Estado**: ✅ CONECTADO

**Gaps**:
- Integración con `trading_daemon.py` para RL live trading está marcada como TODO
- Ver `ui/ui_main_window.py:1746-1781` para detalles

---

### 3.9 FeatureEvolutionTab

**Ubicación**: `ui/tabs/feature_evolution_tab.py`

**Conexiones**:
- **Workers**: 
  - `ui/workers/feature_evolution_worker.py::feature_evolution_worker()`
- **External**: 
  - `scripts/features/feature_evolution_deap.py`
  - `hyperparameter_phases.py::ExperimentTracker`

**Estado**: ✅ CONECTADO

**Gaps**: Ninguno crítico

---

### 3.10 FeatureComposerTab

**Ubicación**: `ui/tabs/feature_composer_tab.py`

**Conexiones**:
- **External**: `feature_registry.py` (root) - PRECARIO
- **Shared State**: Ninguno (tab independiente)

**Estado**: ⚠️ PRECARIO - Import desde root

**Gaps**:
- Importa `feature_registry.py` desde root
- Debería usar `ui/utils/feature_builder_registry.py`

---

## 4. Mapa de Workers

### 4.1 Training Workers

**Archivos**:
- `ui/workers/training_worker.py`
  - `prepare_data_worker()`
  - `train_model_worker()`
  - `prepare_and_train_worker()`
- `ui/workers/retraining_worker.py`
  - `retrain_lstm_worker()`

**Conexiones**:
- **Tabs**: `TrainingTab`, `TrainingContainerTab`
- **Shared State**: 
  - Escribe: `app.model`, `app.model_meta`, `app.model_scaler`
  - Lee: `app.df_loaded`
- **External**: `fiboevo.py`, `data_pipeline/data_loader.py`

**Estado**: ✅ CONECTADO

---

### 4.2 Backtest Worker

**Archivo**: `ui/workers/backtest_worker.py`
- `backtest_worker()`
- `optimization_worker()`

**Conexiones**:
- **Tabs**: `BacktestTab`
- **Shared State**: 
  - Lee: `app.model`, `app.model_meta`, `app.df_loaded`
- **External**: `hyperparameter_phases.py`, `hyperparameter_integration.py`

**Estado**: ✅ CONECTADO

---

### 4.3 Optimization Worker

**Archivo**: `ui/workers/optimization_worker.py`
- `optimization_worker()`

**Conexiones**:
- **Tabs**: `BacktestTab` (sub-tab de optimización)
- **External**: `hyperparameter_phases.py`, `hyperparameter_strategy.py`

**Estado**: ✅ CONECTADO

---

### 4.4 Feature Evolution Worker

**Archivo**: `ui/workers/feature_evolution_worker.py`
- `feature_evolution_worker()`

**Conexiones**:
- **Tabs**: `FeatureEvolutionTab`
- **External**: `scripts/features/feature_evolution_deap.py`

**Estado**: ✅ CONECTADO

---

### 4.5 RL Worker

**Archivo**: `ui/workers/rl_worker.py`
- `rl_training_worker()`

**Conexiones**:
- **Tabs**: `RLStrategyTab`
- **External**: `scripts/rl/rl_training_pipeline.py`

**Estado**: ✅ CONECTADO

---

### 4.6 Oracle Worker

**Archivo**: `ui/workers/oracle_worker.py`
- `oracle_worker()`

**Conexiones**:
- **Tabs**: `AuditTab`
- **Components**: `ui/components/oracle_window.py`
- **External**: `scripts/inference/oracle_inference.py`

**Estado**: ✅ CONECTADO

---

## 5. Estado Compartido en `ui_main_window.py`

### 5.1 Estado de Modelo

```python
self.model = None
self.model_meta = None
self.model_scaler = None
```

**Usado por**:
- `TrainingTab` - Escribe después de entrenamiento
- `BacktestTab` - Lee para backtesting
- `AuditTab` - Lee para predicciones Oracle
- `StatusTab` - Lee para daemon
- `multihorizon_dashboard.py` - Lee para validación

**Estado**: ✅ BIEN COMPARTIDO

---

### 5.2 Estado de Datos

```python
self.df_loaded = None
self.df_features = None
self.df_scaled = None
self.X_full = None
self.y_full = None
self.feature_cols_used = None
self.scaler_used = None
```

**Usado por**:
- `PreviewTab` - Lee `df_loaded`
- `TrainingTab` - Lee/Escribe durante preparación
- `BacktestTab` - Lee para backtesting
- `WalkForwardTab` - Lee para validación
- `AuditTab` - Lee para predicciones

**Estado**: ✅ BIEN COMPARTIDO

---

### 5.3 Estado de Daemon

```python
self.daemon = None
```

**Usado por**:
- `StatusTab` - Control start/stop, estado
- `multihorizon_dashboard.py` - Polling de predicciones
- `ui_main_window.py` - Polling centralizado
- `RLStrategyTab` - Integración RL (parcial)

**Estado**: ⚠️ POLLING DUPLICADO

**Problema**: Múltiples componentes hacen polling de `daemon.predictions_queue`

**Recomendación**: Centralizar polling en `ui_main_window.py` y notificar a componentes

---

## 6. Gaps de Conectividad Identificados

### 6.1 Polling Duplicado de Predictions Queue

**Problema**:
- `ui_main_window.py::_poll_predictions_queue()` hace polling
- `multihorizon_dashboard.py::_poll_predictions()` hace polling independiente
- Ambos consumen de la misma queue

**Impacto**:
- Posible pérdida de mensajes
- Trabajo redundante
- Inconsistencias si un componente consume y el otro no

**Solución Propuesta**:
- Centralizar polling en `ui_main_window.py`
- Implementar patrón Observer para notificar a componentes
- O usar queue separada para cada consumidor

**Prioridad**: ALTA

---

### 6.2 Falta Conexión RL → Trading Daemon

**Problema**:
- `RLStrategyTab` puede entrenar agentes
- `trading_daemon.py` tiene soporte para RL (`use_rl_agent`, `agent`)
- Pero no hay conexión automática desde UI

**Código en `ui_main_window.py:1746-1781`**:
```python
# TODO: Complete implementation (requires trading_daemon.py modifications)
# This requires coordination with trading-execution-engineer
```

**Impacto**:
- Usuario no puede activar RL trading desde UI
- Feature implementada pero no conectada

**Solución Propuesta**:
- Completar implementación en `ui_main_window.py`
- Agregar método `_integrate_rl_agent_with_daemon()`
- Conectar desde `RLStrategyTab`

**Prioridad**: MEDIA

---

### 6.3 Falta Sincronización de Estado entre Tabs

**Problema**:
- Cuando se carga un modelo en un tab, otros tabs no se actualizan automáticamente
- Cuando se preparan datos, otros tabs no saben que hay nuevos datos disponibles

**Impacto**:
- Usuario debe refrescar manualmente
- Estado inconsistente entre tabs

**Solución Propuesta**:
- Implementar sistema de notificaciones/eventos
- Tabs se suscriben a eventos de interés
- `ui_main_window.py` emite eventos cuando estado cambia

**Prioridad**: MEDIA

---

## 7. Diagrama de Flujo de Datos

```
┌─────────────────┐
│ trading_daemon  │
│   .py           │
└────────┬────────┘
         │
         ├── predictions_queue ──┐
         │                       │
         ├── log_queue ──────────┤
         │                       │
         └── model/state ────────┤
                                 │
                    ┌────────────┴────────────┐
                    │                         │
         ┌──────────▼──────────┐   ┌──────────▼──────────┐
         │ ui_main_window.py  │   │ multihorizon_       │
         │ _poll_predictions()  │   │ dashboard.py        │
         └──────────┬──────────┘   │ _poll_predictions() │
                    │              └────────────────────┘
                    │
         ┌──────────▼──────────┐
         │ status_tab.py        │
         │ _update_fan_chart() │
         └────────────────────┘
```

**Problema**: Polling duplicado identificado

---

## 8. Mapa de Conexiones Workers → Tabs

| Worker | Tab | Método de Conexión | Estado |
|--------|-----|-------------------|--------|
| `training_worker.py` | `TrainingTab` | Thread + callback | ✅ |
| `retraining_worker.py` | `TrainingTab` | Thread + callback | ✅ |
| `backtest_worker.py` | `BacktestTab` | Thread + callback | ✅ |
| `optimization_worker.py` | `BacktestTab` | Thread + callback | ✅ |
| `feature_evolution_worker.py` | `FeatureEvolutionTab` | Thread + callback | ✅ |
| `rl_worker.py` | `RLStrategyTab` | Thread + callback | ✅ |
| `oracle_worker.py` | `AuditTab` | Thread + callback | ✅ |

**Estado General**: ✅ TODOS CONECTADOS

**Patrón**: Todos usan threading + callbacks vía `app.root.after()`

---

## 9. Estado Compartido Faltante

### 9.1 Estado de Configuración de Dashboard

**Falta**:
- Configuración de `multihorizon_dashboard.py` no se persiste
- No hay estado compartido para preferencias de visualización

**Recomendación**: Agregar a `ui_main_window.py` o usar `ConfigManager`

---

### 9.2 Estado de Validación Walk-Forward

**Falta**:
- Resultados de walk-forward no se almacenan en estado compartido
- `WalkForwardTab` y `wf_visualization_tab.py` no comparten resultados

**Recomendación**: Agregar `app.wf_results` a estado compartido

---

### 9.3 Estado de Feature Evolution

**Falta**:
- Resultados de evolución no se almacenan en estado compartido
- No hay forma de acceder a resultados desde otros tabs

**Recomendación**: Agregar `app.feature_evolution_results` a estado compartido

---

## 10. Recomendaciones de Mejora

### Alta Prioridad

1. **Centralizar Polling de Predictions Queue**
   - Mover todo el polling a `ui_main_window.py`
   - Implementar notificaciones a componentes interesados
   - Eliminar polling duplicado

2. **Completar Integración RL → Daemon**
   - Implementar `_integrate_rl_agent_with_daemon()` en `ui_main_window.py`
   - Conectar desde `RLStrategyTab`

### Media Prioridad

3. **Sistema de Notificaciones/Eventos**
   - Implementar patrón Observer
   - Tabs se suscriben a eventos de interés
   - Sincronización automática de estado

4. **Estado Compartido para Resultados**
   - Agregar `app.wf_results`
   - Agregar `app.feature_evolution_results`
   - Agregar `app.dashboard_config`

---

**Última actualización**: 2025-12-13  
**Próxima revisión**: Después de mejoras de conectividad

