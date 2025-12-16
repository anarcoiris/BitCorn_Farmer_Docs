# BitCorn_Farmer - Documentation Index

**Last Updated**: 2025-12-13  
**Version**: 3.0 (Unified Roadmaps + TensorBoard Integration)  
**Maintained By**: documentation-specialist

Welcome to the BitCorn_Farmer documentation hub. This index provides quick access to all project documentation organized by category and purpose.

> **Note**: Documentación limpiada y consolidada el 2025-12-08. Documentos obsoletos eliminados, información consolidada en documentos principales. Ver [DELETED_DOCUMENTS_LOG.md](DELETED_DOCUMENTS_LOG.md) para detalles de la limpieza.

---

## 📋 Fuentes de Verdad (Source of Truth)

Para evitar confusiones, estos son los documentos oficiales para cada tema:

| Tema | Fuente de Verdad | Última Actualización |
|------|------------------|---------------------|
| **Progreso General del Proyecto** | `PROJECT_STATUS.md` | 2025-12-08 |
| **Roadmap de Desarrollo** | `ui/AGENT_ROADMAP.md` (detailed) | 2025-12-08 |
| **Roadmap Consolidado** | `.agent/workflows/consolidated-roadmap.md` | 2025-12-13 |
| **Estado Técnico de Componentes** | `PROJECT_STATUS.md` | 2025-12-08 |
| **Configuración Central** | `config/master_config.json` | 2025-12-13 |
| **Quick Start** | `docs/guides/QUICK_START.md` | 2025-11-18 |
| **Hyperparameter Strategy** | `ui/HYPERPARAMETER_STRATEGY_PLAN.md` | 2025-12-08 |
| **Feature Engineering** | `PROJECT_STATUS.md` (Section 1.1) | 2025-12-08 |
| **Tareas Pendientes** | `TODO.md` | Variable |
| **Tests Pendientes** | `TESTING_TODO.md` | Variable |

> ⚠️ **Nota**: Si encuentras información contradictoria, consulta la fuente de verdad correspondiente.

---

## Quick Navigation

### 🚀 Getting Started
- [QUICK_START.md](guides/QUICK_START.md) - **Start here!** 5-minute setup and first steps
- [PROJECT_STATUS.md](PROJECT_STATUS.md) - Current implementation status
- [TODO.md](TODO.md) - Actionable task list with priorities

### 📊 Status & Planning
- [PROJECT_STATUS.md](PROJECT_STATUS.md) - **Fuente de Verdad** - Comprehensive status report (all components)
- [ui/AGENT_ROADMAP.md](../ui/AGENT_ROADMAP.md) - **Fuente de Verdad** - Roadmap de desarrollo con tareas por agente
- [TODO.md](TODO.md) - Task list with dependencies and timelines
- [TESTING_TODO.md](TESTING_TODO.md) - Testing tracker with test cases

### 📚 User Guides
- [guides/QUICK_START.md](guides/QUICK_START.md) - **Start here!** Quick start guide (instalación, entrenamiento, predicciones)

### 🔧 Technical Documentation
- [reference/guides/DEVELOPER_GUIDE.md](reference/guides/DEVELOPER_GUIDE.md) - Developer guide completo (incluye sección DevOps)
- [reference/installation/INSTALL_CUDA.md](reference/installation/INSTALL_CUDA.md) - Instalación CUDA
- [reference/api/CSV_UPSERTER_GUIDE.md](reference/api/CSV_UPSERTER_GUIDE.md) - API CSV upserter
- [STANDARDS.md](STANDARDS.md) - Estándares y mejores prácticas del proyecto

### 📝 Análisis y Organización
- [NAVIGATION_GUIDE.md](NAVIGATION_GUIDE.md) - Guía de navegación rápida por rol y tarea
- [DELETED_DOCUMENTS_LOG.md](DELETED_DOCUMENTS_LOG.md) - Registro de documentos eliminados durante la limpieza (50+ documentos eliminados, ~30% reducción)

### 📁 Archive (Old Documentation)
- [archive/sessions/](archive/sessions/) - Session reports antiguos
- [archive/memories/](archive/memories/) - Implementation memories
- [archive/changelogs/](archive/changelogs/) - Changelogs antiguos

---

## Documentation by Purpose

### For New Users
**"I'm new to this project"**

1. Read [QUICK_START.md](guides/QUICK_START.md) - Get overview and setup (5 min)
2. Review [PROJECT_STATUS.md](PROJECT_STATUS.md) - Understand what's ready (10 min)
3. Check [TODO.md](TODO.md) - See what needs to be done (5 min)
4. Follow Quick Start steps to train first model

**Total time**: 20 minutes + training time

---

### For Developers
**"I need to contribute or fix something"**

1. Read [PROJECT_STATUS.md](PROJECT_STATUS.md) - Component status and known bugs
2. Check [TODO.md](TODO.md) - Pick a task (prioritized)
3. Review [TESTING_TODO.md](TESTING_TODO.md) - Write/run tests
4. Read relevant technical docs (see below)

**Key files**:
- `fiboevo.py` - Core feature engineering + LSTM models
- `run_ui.py` - GUI  (refactor of tradeapp.py into ui/ folder)
- `multi_horizon_inference.py` - Prediction engine

---

### For Data Scientists
**"I want to understand the models"**

1. Read [technical/VOLATILITY_SCALING_FIX.md](technical/VOLATILITY_SCALING_FIX.md) - Critical mathematical fix
2. Review Feature Engineering v2 in `fiboevo.py`
3. Check LSTM2Head architecture in `fiboevo.py`
4. Read [guides/SYNTHETIC_DATA_GUIDE.md](guides/SYNTHETIC_DATA_GUIDE.md) - Data quality

**Key concepts**:
- Dual-head LSTM (returns + volatility)
- Heteroscedastic NLL loss
- Multi-horizon forecasting
- Confidence interval scaling

---

### For Testers/QA
**"I need to test and validate"**

1. Start with [TESTING_TODO.md](TESTING_TODO.md) - Complete test tracker
2. Review test cases by priority (Critical → Low)
3. Run existing tests:
   - `test_feature_generation_v2.py`
   - `test_volatility_fix.py`
4. Follow test execution plan (Phase 1-4)

**Success criteria**:
- Test coverage ≥ 80%
- All critical tests passing
- No regressions

---

### For Deployment
**"I need to deploy to production"**

1. Check [PROJECT_STATUS.md](PROJECT_STATUS.md) - Verify production readiness
2. Complete [TODO.md](TODO.md) - Critical priority tasks (TASK-001 to TASK-004)
3. Run [TESTING_TODO.md](TESTING_TODO.md) - Phase 1 tests
4. Review deployment checklist in [QUICK_START.md](guides/QUICK_START.md)

**Prerequisites**:
- At least one model trained ✓
- Inference validated ✓
- Critical GUI bugs fixed ✓
- Oracle tab functional ✓

---

## Documentation by Component

### Core System

#### LSTM Models
- **Architecture**: `fiboevo.py` - LSTM2Head, LSTMMultiHead classes
- **Training**: See TODO.md TASK-001
- **Validation**: `test_feature_generation_v2.py`
- **Status**: [PROJECT_STATUS.md](PROJECT_STATUS.md) - Section 1.1

#### Feature Engineering
- **Implementation**: `fiboevo.py` - `generate_features_v2()`
- **Features**: 14 clean features, no data leakage
- **Testing**: `test_feature_generation_v2.py`
- **Status**: [PROJECT_STATUS.md](PROJECT_STATUS.md) - Section 1.1

#### Multi-Horizon Inference
- **Engine**: `multi_horizon_inference.py`
- **Real-time**: `multi_horizon_fan_inference.py`
- **Example**: `examples/example_multi_horizon.py`
- **Fix**: [technical/VOLATILITY_SCALING_FIX.md](technical/VOLATILITY_SCALING_FIX.md)
- **Status**: [PROJECT_STATUS.md](PROJECT_STATUS.md) - Section 1.3

### Data Management

#### Synthetic Data Pipeline
- **Guide**: [guides/SYNTHETIC_DATA_GUIDE.md](guides/SYNTHETIC_DATA_GUIDE.md) - Comprehensive (1000+ lines)
- **Quick Ref**: [guides/SYNTHETIC_DATA_README.md](guides/SYNTHETIC_DATA_README.md)
- **Generator**: `synthetic_data_generator.py`
- **Validator**: `synthetic_data_validator.py`
- **Status**: [PROJECT_STATUS.md](PROJECT_STATUS.md) - Section 1.2

#### Database
- **Implementation**: UI SQLite integration
- **Schema**: OHLCV + metadata + add (features? experiments? OHCLV_date with date format only?)
- **Status**: Production ready

### Advanced Features

#### Walk-Forward Validation
- **Implementation**: `walk_forward_validation.py`
- **Models**: 19 folds in `artifacts/walk_forward/`
- **GUI**: ui/ folder (check)
- **Status**: [PROJECT_STATUS.md](PROJECT_STATUS.md) - Section 1.4

#### Reinforcement Learning (PPO)
- **Agent**: `rl_agent/ppo_agent.py`
- **Training**: `rl_agent/ppo_trainer.py`
- **Rewards**: `rl_agent/reward_functions.py`
- **State**: `rl_agent/state_encoder.py`
- **Status**: [PROJECT_STATUS.md](PROJECT_STATUS.md) - Section 1.5
- **TODO**: TODO.md TASK-008

#### Model Organization
- **Manager**: `model_manager.py`
- **Configs**: `training_plan.json`
- **Runs**: `artifacts/runs/run_000*/`
- **Status**: [PROJECT_STATUS.md](PROJECT_STATUS.md) - Section 1.6

### GUI (run_ui)

#### Main Application
- **File**: `run_ui.py` (5,974 lines)
- **Tabs**: 6 implemented (Data, Training, Status, Oracle, Walk-Forward, RL)
- **Known Bugs**: See TODO.md TASK-002
- **Status**: [PROJECT_STATUS.md](PROJECT_STATUS.md) - Section 1.7

---

## Documentation by File Type

### Markdown Documentation

#### Root Directory (Active)
- `guides/QUICK_START.md` - Quick start guide
- `PROJECT_STATUS.md` - Comprehensive status
- `TODO.md` - Task list
- `TESTING_TODO.md` - Test tracker
- `SESSION_COMPLETE_2025-11-17.md` - Latest session report

#### docs/guides/ (User Guides)
- `guides/QUICK_START.md` - **Start here!** Quick start guide

#### docs/technical/ (Technical Docs)
- `VOLATILITY_SCALING_FIX.md` - CI coverage fix

#### docs/archive/ (Historical - Mantener estructura)
- `archive/sessions/` - Session summaries históricos
- `archive/memories/` - Implementation memories
- `archive/changelogs/` - Changelogs históricos

### Python Code (Referencia Rápida)

#### Core Implementation (Raíz)
- `fiboevo.py` - Feature engineering + LSTM models
- `run_ui.py` - Entry point UI modular
- `trading_daemon.py` - Background inference daemon
- `utils.py` - Utilidades generales
- `config_manager.py` - Gestor de configuración
- `data_pipeline/data_loader.py` - Data loading y feature engineering
- `ui/` - UI modular (tabs, workers, components)

#### Scripts Organizados (`scripts/`)
Los scripts Python han sido organizados por función para mejorar la navegabilidad:

**Training** (`scripts/training/`):
- `training_pipeline.py` - Pipeline serial de entrenamiento LSTM (~660 líneas)
- `train_multi_configs.py` - Entrenamiento multi-configuración (~235 líneas)
- `train_all_horizons.py` - Entrenamiento multi-horizonte (~1044 líneas)
- `retrain_clean_features.py` - Re-entrenamiento con features limpias (~514 líneas)

**RL** (`scripts/rl/`):
- `rl_training_pipeline.py` - Pipeline principal RL (~802 líneas)
- `rl_training_pipeline_unified.py` - Versión unificada (~449 líneas, duplicado)
- `rl_training_pipeline_multi_strategy.py` - Multi-estrategia (~340 líneas, duplicado)
- `rl_backtester.py` - Backtesting RL (~746 líneas)

**Inference** (`scripts/inference/`):
- `multi_horizon_inference.py` - Motor principal de inferencia (~1507 líneas)
- `multi_horizon_fan_inference.py` - Fan charts (DEPRECATED, ~11106 líneas)
- `oracle_inference.py` - Sistema Oracle (~66654 líneas)
- `oracle_visualization.py` - Visualización Oracle (~25858 líneas)
- `TheOracle.py` - Oracle completo standalone (~30980 líneas)
- `future_forecast_fan.py` - Forecast fan (~9996 líneas)
- `simple_future_forecast.py` - Forecast simple (~10676 líneas)

**Features** (`scripts/features/`):
- `feature_evolution_deap.py` - Evolución genética de features (~32270 líneas)
- `run_feature_evolution.py` - Runner de evolución (~20116 líneas)
- `feature_builder.py` - Constructor de features (~43655 líneas)
- `analyze_evolution_results.py` - Análisis de resultados (~24021 líneas)

**Diagnostics** (`scripts/diagnostics/`):
- `diagnostics.py` - Diagnósticos generales (~1198 líneas)
- `diagnose_model.py` - Diagnóstico de modelos (~16773 líneas)
- `diagnose_volatility_scale.py` - Diagnóstico avanzado volatilidad (~19335 líneas)
- `diagnose_volatility_simple.py` - Diagnóstico simple volatilidad (~15054 líneas, duplicado)
- `check_env.py` - Verificación de entorno (~5607 líneas)
- `validate_v4_continuity.py` - Validación continuidad v4 (~12974 líneas)

**Test Scripts** (`tests/scripts/`):
- `test_app.py` - Test básico de aplicación
- `test_deap_debug.py` - Debug DEAP
- `test_feature_evolution_comprehensive.py` - Test comprehensivo evolución
- `test_icons.py` - Test de iconos
- `test_large_images.py` - Test imágenes grandes
- `test_timestamp_preservation.py` - Test preservación timestamps
- `test_v3_vs_v4_comparison.py` - Comparación v3 vs v4

**Pendientes de mover**: Hyperparameter (4), Data (7), Presets (4), Visualization (4), Models (3), Utils (8), Tools (3)

Ver `scripts/README.md` para índice completo y descripciones detalladas.

### Configuration Files
- `config/base.json` - Configuración base (defaults)
- `config/training_options.json` - Opciones de entrenamiento
- `config/gui_config.json` - Configuración GUI
- `config/rl_config.json` - Configuración RL
- Ver `docs/reference/guides/DEVELOPER_GUIDE.md` sección "Infrastructure & DevOps" para detalles

---

## Documentation Maintenance

### Adding New Documentation

**For User Guides**:
1. Create file in `docs/guides/`
2. Add entry to this index under "User Guides"
3. Link from QUICK_START.md if relevant
4. Update PROJECT_STATUS.md if component documentation

**For Technical Docs**:
1. Create file in `docs/technical/`
2. Add entry to this index under "Technical Documentation"
3. Link from relevant component section
4. Reference in PROJECT_STATUS.md

**For Session Reports**:
1. Create file in `docs/archive/` (historical) or root (current)
2. Add entry to this index under "Archive"
3. Update PROJECT_STATUS.md with key findings

### Archiving Old Documentation

**When to archive**:
- Session reports older than 1 week
- Implementation notes superseded by updated docs
- Deployment summaries after next deployment

**How to archive**:
1. Move file to `docs/archive/`
2. Update this index (keep link in Archive section)
3. Remove from guides/QUICK_START.md if listed
4. Keep reference in PROJECT_STATUS.md if historically important

### Documentation Quality Standards

**All documentation should**:
- Include "Last Updated" date
- Have clear section headings
- Use consistent formatting
- Include code examples where relevant
- Link to related documentation
- Be tested (commands/examples work)

---

## Quick Reference: Key Metrics

### Project Statistics
- **Total Documentation**: 111 markdown files (inventario completo 2025-12-09)
- **Documentación Activa**: ~50 archivos principales
- **Documentación Archivada**: ~40 archivos en archive/
- **Workflows/Agents**: ~15 archivos en .agent/workflows/
- **Total Code**: ~50 Python files
- **Main GUI**: 5,974 lines (run_ui.py legacy), UI modular en `ui/`
- **Core Modules**: ~10,000 lines
- **Test Coverage**: 50% (target: 80%)

### Calidad de Documentación (Post-Limpieza 2025-12-08)
- **Completitud**: ~85% (mejorada tras consolidación)
- **Consistencia**: ~80% (conflictos resueltos, referencias actualizadas)
- **Actualidad**: ~85% (documentos obsoletos eliminados)
- **Navegabilidad**: ~80% (índice actualizado, referencias cruzadas validadas)
- **Reducción**: 50+ documentos eliminados (~30% reducción)

### Component Status

**Según PROJECT_STATUS.md (Fuente de Verdad - Actualizado 2025-12-10)**:
- **Core LSTM**: 100% complete ✓
- **Data Pipeline**: 100% complete ✓
- **Multi-Horizon**: 100% implementation ✓
- **Walk-Forward**: 100% complete ✓
- **RL Strategy**: 98% complete ✓ (training pipelines complete, profitability tuning ongoing)
- **GUI**: 97% complete ✓ (TensorBoard integration added)
- **Testing**: 55% complete ⚠ (new tests added)
- **Documentation**: 90% complete ✓ (roadmaps unified)
- **Configuration**: 100% complete ✓ (MasterConfigManager fully integrated)

**Según consolidated-roadmap.md**:
- **Track 1 (Hyperparameter)**: 100% ✓
- **Track 2 (Feature Evolution)**: 100% ✓
- **Track 3 (Scripts)**: 85% 🔄
- **Track 4 (RL Strategy)**: 75% 🔄
- **Track 5 (TensorBoard)**: 100% ✓

### Critical Dates
- **2025-11-16**: Feature mismatch fix, synthetic data integration
- **2025-11-17**: Volatility scaling fix (CRITICAL), deployment summary
- **2025-11-18**: Documentation consolidation (this index)
- **2025-12-02**: Project structure cleanup, outputs consolidation
- **2025-12-03**: Data pipeline implementation with caching (5-35x speedup)
- **2025-12-04**: UI refactoring milestones (Backtest, Status, WebSocket tabs)
- **2025-12-05**: SQL validation and timestamp fixes (29/29 tests passing)
- **2025-12-06**: Feature Evolution Tab, Hyperparameter Strategy backend
- **2025-12-07**: Oracle Window, Multi-Horizon Dashboard, UI Test Suite
- **2025-12-08**: Model training execution (TASK-001 completed), documentation cleanup
- **2025-12-09**: Complete documentation inventory and consolidation
- **2025-12-10**: Configuration unification (MasterConfigManager), RL config standardization
- **2025-12-11**: Unified visualization component, PreviewTab fixes
- **2025-12-12**: RL training pipeline updates, strategy inheritance system
- **2025-12-13**: TensorBoard integration for evolution/optimization, CLI master pipeline

---

## Frequently Accessed Documentation

### Daily Reference
1. [TODO.md](TODO.md) - Check daily tasks
2. [TESTING_TODO.md](TESTING_TODO.md) - Check test status
3. [PROJECT_STATUS.md](PROJECT_STATUS.md) - Review component status

### Weekly Review
1. [PROJECT_STATUS.md](PROJECT_STATUS.md) - Update status
2. [TODO.md](TODO.md) - Update task progress
3. This index - Add new documentation

### Before Deployment
1. [PROJECT_STATUS.md](PROJECT_STATUS.md) - Verify readiness
2. [TODO.md](TODO.md) - Complete critical tasks
3. [TESTING_TODO.md](TESTING_TODO.md) - Run Phase 1 tests
4. [QUICK_START.md](guides/QUICK_START.md) - Verify success checklist

---

## Automatización de Documentación

### Scripts Disponibles
- `tools/validate_documentation.py` - Valida links rotos, referencias cruzadas, y duplicados

### Uso
```bash
python tools/validate_documentation.py
```

### Funcionalidades
- ✅ Validación de links rotos en documentación
- ✅ Análisis de referencias cruzadas
- ✅ Detección de títulos duplicados
- ✅ Estadísticas de documentación

### Futuras Mejoras
- Generación automática de índice
- Sincronización de referencias cruzadas
- Validación de consistencia entre documentos
- Actualización automática de fechas "Last Updated"

---

## Contact & Support

### For Questions About
- **Architecture**: Read technical/ docs
- **Usage**: Read guides/ docs
- **Status**: Check [PROJECT_STATUS.md](PROJECT_STATUS.md)
- **Tasks**: Check [TODO.md](TODO.md)
- **Tests**: Check [TESTING_TODO.md](TESTING_TODO.md)

### For Issues
- Check [TODO.md](TODO.md) for known issues
- Review [TESTING_TODO.md](TESTING_TODO.md) for test status
- Check [PROJECT_STATUS.md](PROJECT_STATUS.md) Section 8 (Known Limitations)

---

## Complete Documentation Inventory

### Root Directory (Active Documentation)
| Document | Last Modified | Created | Purpose |
|----------|--------------|---------|---------|
| `README.md` | 2025-12-09 | 2025-11-04 | Project overview, quick start |
| `CHANGELOG.md` | 2025-12-10 | 2025-12-01 | Consolidated changelog |
| `AGENT_CONTEXT.md` | 2025-12-08 | 2025-12-08 | Agent context and configuration |
| `FEATURE_ENGINEERING_V4.md` | 2025-12-06 | 2025-11-24 | Feature engineering v4 documentation |
| `BETA_FIX_COMPLETE.md` | 2025-12-04 | 2025-12-04 | Beta fix completion report |
| `beta_fix_plan.md` | 2025-12-04 | 2025-12-04 | Beta fix plan |
| `COMMIT_PREPARATION.md` | 2025-12-04 | 2025-12-04 | Commit preparation notes |
| `REPORTE_REVISION_RL_FIX.md` | 2025-12-03 | 2025-12-03 | RL fix revision report |
| `CLAUDE.md` | 2025-11-25 | 2025-11-04 | Claude AI configuration |
| `FEATURE_EVOLUTION_RESULTS.md` | 2025-11-24 | 2025-11-24 | Feature evolution results |
| `FEATURE_GRADIENT_ANALYSIS.md` | 2025-11-24 | 2025-11-24 | Feature gradient analysis |
| `FEATURE_V4_IMPLEMENTATION_CHECKLIST.md` | 2025-11-24 | 2025-11-24 | Feature v4 checklist |
| `FEATURE_V4_VISUAL_SUMMARY.md` | 2025-11-24 | 2025-11-24 | Feature v4 visual summary |

### docs/ Directory (Organized Documentation)

#### Core Documentation
| Document | Last Modified | Created | Purpose |
|----------|--------------|---------|---------|
| `docs/DOCUMENTATION_INDEX.md` | 2025-12-13 | 2025-11-18 | **This file** - Complete documentation index |
| `docs/PROJECT_STATUS.md` | 2025-12-10 | 2025-12-02 | **Source of Truth** - Comprehensive project status |
| `docs/PROJECT_MASTER_PLAN.md` | 2025-12-08 | 2025-12-08 | Master plan consolidated |
| `docs/TODO.md` | 2025-12-03 | 2025-12-02 | Actionable task list with priorities |
| `docs/TESTING_TODO.md` | 2025-12-02 | 2025-12-02 | Testing tracker with test cases |
| `docs/PROJECT_POLICIES.md` | 2025-12-02 | 2025-12-02 | Project policies and standards |
| `docs/NAVIGATION_GUIDE.md` | 2025-12-06 | 2025-12-05 | Navigation guide by role and task |
| `docs/VALIDATION_CHECKLIST.md` | 2025-12-02 | 2025-12-02 | Validation checklist |

#### Guides (User Documentation)
| Document | Last Modified | Created | Purpose |
|----------|--------------|---------|---------|
| `docs/guides/QUICK_START.md` | 2025-12-02 | 2025-12-02 | **Start here!** Quick start guide |
| `docs/guides/HYPERPARAMETER_STRATEGY_GUIDE.md` | 2025-12-08 | 2025-12-08 | Hyperparameter strategy guide |
| `docs/guides/SYNTHETIC_TIMEFRAMES_GUIDE.md` | 2025-11-23 | 2025-12-01 | Synthetic timeframes guide |
| `docs/guides/PRESET_SYSTEM_SUMMARY.md` | 2025-12-01 | 2025-12-01 | Preset system summary |

#### Reference Documentation
| Document | Last Modified | Created | Purpose |
|----------|--------------|---------|---------|
| `docs/reference/guides/DEVELOPER_GUIDE.md` | 2025-12-08 | 2025-12-02 | Complete developer guide (includes DevOps) |
| `docs/reference/installation/INSTALL_CUDA.md` | 2025-12-02 | 2025-12-02 | CUDA installation guide |
| `docs/reference/api/CSV_UPSERTER_GUIDE.md` | 2025-12-02 | 2025-12-02 | CSV upserter API guide |
| `docs/reference/api/MULTI_HORIZON_INFERENCE.md` | 2025-12-02 | 2025-12-02 | Multi-horizon inference API |
| `docs/STANDARDS.md` | 2025-12-10 | 2025-12-10 | **Standards** - Coding, DB, and Config protocols |
| `docs/CONFIG_METHODS_REFERENCE.md` | 2025-12-10 | 2025-12-10 | **Config Methods Reference** - Inventario, prioridades, estado de migración |
| `docs/CONFIG_AUDIT_SUMMARY.md` | 2025-12-10 | 2025-12-10 | **Config Audit Summary** - Resumen ejecutivo consolidado |

#### Data Pipeline Documentation
| Document | Last Modified | Created | Purpose |
|----------|--------------|---------|---------|
| `docs/DATA_PIPELINE_GUIDE.md` | 2025-12-03 | 2025-12-03 | Data pipeline comprehensive guide |
| `docs/DATA_PIPELINE_CHANGELOG.md` | 2025-12-03 | 2025-12-03 | Data pipeline changelog |
| `data_pipeline/README.md` | 2025-12-03 | 2025-12-03 | Data pipeline module README |
| `data_source/README.md` | 2025-12-01 | 2025-12-01 | Data source directory README |
| `data_source/DATA_PIPELINE_INTEGRATION.md` | 2025-12-01 | 2025-12-01 | Data pipeline integration guide |
| `data_manager/README.md` | 2025-10-18 | 2025-11-04 | Data manager README |

#### Config Audit Documentation
| Document | Last Modified | Created | Purpose |
|----------|--------------|---------|---------|
| `docs/CONFIG_AUDIT_SUMMARY.md` | 2025-12-10 | 2025-12-10 | **Config Audit Summary** - Resumen ejecutivo consolidado |
| `docs/CONFIG_METHODS_REFERENCE.md` | 2025-12-10 | 2025-12-10 | **Config Methods Reference** - Referencia completa de métodos |
| `docs/config_migration_status_report.md` | 2025-12-10 | 2025-12-10 | Estado de migración a master_config.json |
| `docs/config_audit/README.md` | 2025-12-10 | 2025-12-08 | Índice de documentación de auditoría |
| `docs/config_audit/phase*/` | Varios | Varios | Documentación histórica por fase (no modificar) |

#### Summaries
| Document | Last Modified | Created | Purpose |
|----------|--------------|---------|---------|
| `docs/audits/CONFIG_UNIFICATION_AUDIT_2025_12_11.md` | 2025-12-11 | 2025-12-11 | Audit of Config Unification implementation |
| `docs/summaries/EXECUTIVE_SUMMARY_V4.md` | 2025-12-02 | 2025-12-02 | Executive summary v4 |
| `docs/summaries/FIXES_SUMMARY.md` | 2025-12-02 | 2025-12-02 | Fixes summary |

| `docs/summaries/IMPLEMENTATION_SUMMARY_V4.md` | 2025-12-02 | 2025-12-02 | Implementation summary v4 |
| `docs/summaries/PHASE2_IMPLEMENTATION_SUMMARY.md` | 2025-12-02 | 2025-12-02 | Phase 2 implementation summary |
| `docs/summaries/PRESET_SYSTEM_SUMMARY.md` | 2025-12-02 | 2025-12-02 | Preset system summary |
| `docs/summaries/FASE5_IMPLEMENTATION_REPORT.md` | 2025-12-01 | 2025-12-01 | Phase 5 implementation report |
| `docs/summaries/RL_INTEGRATION_SUMMARY.md` | 2025-12-01 | 2025-12-01 | RL integration summary |
| `docs/summaries/RL_STRATEGY_TAB_REPORT.md` | 2025-12-01 | 2025-12-01 | RL strategy tab report |


#### Archive (Historical Documentation)
| Directory | Count | Purpose |
|-----------|-------|---------|
| `docs/archive/sessions/` | 9 files | Historical session summaries |
| `docs/archive/memories/` | 8 files | Implementation memories |
| `docs/archive/changelogs/` | 2 files | Historical changelogs |

### ui/ Directory (UI Refactoring Documentation)
| Document | Last Modified | Created | Purpose |
|----------|--------------|---------|---------|
| `ui/AGENT_ROADMAP.md` | 2025-12-08 | 2025-12-04 | **Source of Truth** - Development roadmap (95% complete) |
| `ui/HYPERPARAMETER_STRATEGY_PLAN.md` | 2025-12-08 | 2025-12-04 | Hyperparameter strategy plan |
| `ui/REFACTORING_STATUS.md` | 2025-12-08 | 2025-12-04 | UI refactoring status |
| `ui/SPECIALIST_REQUIREMENTS.md` | 2025-12-08 | 2025-12-04 | Specialist requirements |
| `ui/HANDOFF_NOTES.md` | 2025-12-08 | 2025-12-04 | Handoff notes between agents |
| `ui/HANDOFF_TRADING_EXECUTION_ENGINEER.md` | 2025-12-08 | 2025-12-04 | Trading execution engineer handoff |
| `ui/assets/README.md` | 2025-12-07 | 2025-12-07 | UI assets README |

#### Validation Reports
| Document | Last Modified | Created | Status |
|----------|--------------|---------|--------|
| `ui/BACKTEST_TAB_VALIDATION_REPORT.md` | 2025-12-06 | 2025-12-04 | ✅ APPROVED |
| `ui/STATUS_TAB_VALIDATION_REPORT.md` | 2025-12-06 | 2025-12-04 | ✅ APPROVED |
| `ui/WEBSOCKET_PANEL_VALIDATION_REPORT.md` | 2025-12-06 | 2025-12-04 | ✅ APPROVED |
| `ui/RL_STRATEGY_TAB_VALIDATION_REPORT.md` | 2025-12-06 | 2025-12-04 | ✅ APPROVED |
| `ui/TIMESTAMPS_SQL_VALIDATION_REPORT.md` | 2025-12-06 | 2025-12-05 | ✅ APPROVED (39/39 tests) |

### .agent/workflows/ (Agent Workflows)
| Document | Last Modified | Created | Purpose |
|----------|--------------|---------|---------|
| `.agent/workflows/documentation-specialist.md` | 2025-12-06 | 2025-12-05 | Documentation specialist workflow |
| `.agent/workflows/trading-execution-engineer.md` | 2025-12-06 | 2025-12-04 | Trading execution engineer workflow |
| `.agent/workflows/rl-strategy-developer.md` | 2025-12-07 | 2025-12-07 | RL strategy developer workflow |
| `.agent/workflows/test-quality-engineer.md` | 2025-12-07 | 2025-12-07 | Test quality engineer workflow |
| `.agent/workflows/gui-ux-developer.md` | 2025-12-07 | 2025-12-07 | GUI/UX developer workflow |
| `.agent/workflows/data-pipeline-architect.md` | 2025-12-06 | 2025-12-04 | Data pipeline architect workflow |
| `.agent/workflows/quant-research-architect.md` | 2025-12-06 | 2025-12-04 | Quant research architect workflow |
| `.agent/workflows/hyperparameter-strategy-specialist.md` | 2025-12-06 | 2025-12-05 | Hyperparameter strategy specialist workflow |
| `.agent/workflows/devops-infrastructure.md` | 2025-12-07 | 2025-12-07 | DevOps infrastructure workflow |
| `.agent/workflows/agent-meeting.md` | 2025-12-07 | 2025-12-04 | Agent meeting protocol |
| `.agent/workflows/AGENT_COMMUNICATION_PROTOCOL.md` | 2025-12-07 | 2025-12-07 | Agent communication protocol |
| `.agent/workflows/MULTI_AGENT_WORKFLOW.md` | 2025-12-08 | 2025-12-08 | Multi-agent workflow |
| `.agent/workflows/AGENT_PROMPTS.md` | 2025-12-08 | 2025-12-08 | Agent prompts |
| `.agent/Standards.md` | 2025-12-06 | 2025-12-06 | Agent standards |
| `.agent/workflows/specialists-rules.md` | 2025-12-06 | 2025-12-06 | Specialists rules |

### tests/ Directory
| Document | Last Modified | Created | Purpose |
|----------|--------------|---------|---------|
| `tests/REGRESSION_REPORT_2025-12-04.md` | 2025-12-06 | 2025-12-04 | Regression test report |
| `tests/README_TESTS.md` | 2025-11-15 | 2025-11-15 | Tests README |

### artifacts/ Directory
| Document | Last Modified | Created | Purpose |
|----------|--------------|---------|---------|
| `artifacts/training_report_2025.md` | 2025-12-08 | 2025-12-08 | Training report 2025 |
| `artifacts/model_comparison_report.md` | 2025-12-08 | 2025-12-08 | Model comparison report |

## Resumen del Trabajo Reciente (Últimas 2 Semanas)

### Semana 1 (2025-12-02 a 2025-12-05)

#### ✅ Completado
1. **Data Pipeline Implementation (2025-12-03)**
   - Sistema de caché de features implementado (5-35x speedup)
   - Validación cronológica centralizada
   - Orden determinístico de features garantizado
   - 22 archivos modificados (+1,118 / -965 líneas)

2. **UI Refactoring Milestones (2025-12-04)**
   - Backtest Tab: Worker completo con optimización de hiperparámetros
   - Status Tab: Integración con daemon completada
   - WebSocket Panel: Visualización en tiempo real operativa

3. **SQL Validation & Timestamps (2025-12-05)**
   - Helper de validación SQL implementado (`ui/utils/sql_utils.py`)
   - Corrección de queries SQL (ORDER BY ASC estandarizado)
   - 29/29 tests pasando ✅
   - Reporte de validación generado

#### 🔄 En Progreso
- Hyperparameter Strategy: Sprint iniciado (prioridad ALTA)
- RL Strategy Tab: Validación en progreso

### Semana 2 (2025-12-06 a 2025-12-09)

#### ✅ Completado
1. **Feature Evolution & Hyperparameter Strategy (2025-12-06)**
   - Feature Evolution Tab implementado
   - Hyperparameter Strategy backend completado
   - UI Asset Integration completada

2. **Oracle Window & Multi-Horizon Dashboard (2025-12-07)**
   - Oracle Window implementado
   - Multi-Horizon Dashboard funcional
   - UI Test Suite completado

3. **Model Training Execution (2025-12-08)**
   - TASK-001 completado: 5 modelos entrenados (run_0001 a run_0005)
   - Training report generado
   - Model comparison report creado

4. **Documentation Cleanup (2025-12-08-09)**
   - 50+ documentos obsoletos eliminados
   - Consolidación de información en documentos principales
   - Inventario completo de documentación (111 archivos catalogados)

#### 📊 Progreso General
- **UI Refactoring**: 95% completado (según AGENT_ROADMAP.md)
- **Core System**: 100% completo ✓
- **Testing Coverage**: 50% (target: 80%)
- **Documentation**: 85% completo ✓

### Tareas Pendientes Consolidadas

#### 🔴 Críticas (Bloquean Deployment)
1. **TASK-002**: Fix Critical GUI Bugs (Backtest hardcoding, data loading duplication)
2. **TASK-003**: Validate Multi-Horizon Inference Pipeline
3. **TASK-004**: Test Oracle Tab with v2 Features

#### 🟡 Altas (Importantes)
1. **TASK-005**: Compare Model Performance (5 modelos entrenados)
2. **TASK-006**: Centralize Data Loading Logic
3. **TASK-007**: Create Best Model Selection Automation
4. **TASK-008**: Train and Validate RL Strategy
5. **TASK-009**: Walk-Forward Validation with New Models

#### 🟢 Medias
1. **TASK-010**: Implement and Train LSTMMultiHead
2. **TASK-011**: Performance Optimization (Feature caching ✅, batch inference pendiente)
3. **TASK-013**: Create Model Training Guide
4. **TASK-014**: Implement CI Coverage Calibration
5. **TASK-019**: Integrate run_ui.py (and childrens) with Data Pipeline
6. **TASK-020**: Integrate trading_daemon.py with Data Pipeline

#### 🔵 Bajas
1. **TASK-015**: Setup CI/CD Pipeline
2. **TASK-016**: Enhance Autoregressive Mode
3. **TASK-017**: Create Deployment Guide
4. **TASK-018**: Implement Production Monitoring Dashboard
5. **TASK-021**: Integrate Inference Scripts with Data Pipeline
6. **TASK-022**: Refactor prepare_dataset.py to Use Data Pipeline

### Ideas y Tareas Descartadas (Duplicados/Obsolletos)
- Planes huérfanos obsoletos eliminados (PLAN_HUERFANO_*.md)
- Reportes temporales consolidados en CHANGELOG.md
- Documentación duplicada eliminada (QUICKSTART_V4.md, etc.)
- Análisis temporales consolidados en PROJECT_STATUS.md

## Document History

### v3.0 (2025-12-13)
- **MAJOR**: Unified roadmaps into single `consolidated-roadmap.md`
- Added TensorBoard integration documentation
- New CLI Pipelines section added (master pipeline, RL batch training)
- Updated component status to reflect current state
- Added critical dates through 2025-12-13
- Archived `REVISION_ESTADO_DESARROLLO_2025-12-11.md` to sessions/
- Cleaned stale references and updated source of truth table

### v2.7 (2025-12-10)
- Sincronización con remoto: cambios de RL training infrastructure y standardization refactor
- Consolidación de documentación: información de scripts integrada en DOCUMENTATION_INDEX.md
- Actualización con nuevos estándares (`docs/STANDARDS.md`)
- Eliminación de archivos duplicados para mantener estructura limpia

### v2.6 (2025-12-09)
- Organización de scripts Python: tests y diagnostics movidos a subdirectorios
- README.md creados en cada subdirectorio de scripts
- Script de análisis de estructura creado (`tools/analyze_script_structure.py`)
- Documentación actualizada con nueva estructura de scripts

### v2.5 (2025-12-09)
- Inventario completo de documentación (111 archivos .md catalogados)
- Lista completa de documentos con fechas de creación y modificación
- Resumen del trabajo de las últimas 2 semanas añadido
- Consolidación de TODOs y tareas pendientes

### v2.4 (2025-12-08)
- Limpieza masiva de documentación (50+ archivos eliminados)
- Consolidación de información en documentos existentes
- Sección DevOps añadida a DEVELOPER_GUIDE.md
- Script de validación de documentación creado (`tools/validate_documentation.py`)
- Referencias actualizadas en README.md y DOCUMENTATION_INDEX.md
- Reducción del 30% en cantidad de archivos .md

### v2.3 (2025-12-08)
- Actualización de referencias post-limpieza
- Eliminación de documentos duplicados

### v2.2 (2025-12-05)
- Análisis de documentación completado

### v2.1 (2025-12-02)
- Major project cleanup
- Consolidated outputs/ folder structure
- Archived notes/ to backup
- Moved examples to examples/ folder
- Updated all status documents
- Removed duplicate documentation

### v1.0 (2025-11-18)
- Initial documentation index created
- Organized existing documentation
- Created docs/ folder structure
- Archived old session summaries

---

## CLI Pipelines (New in v3.0)

| Script | Purpose | Created |
|--------|---------|--------|
| `scripts/run_feature_evolution_cli.py` | CLI feature evolution with TensorBoard + --save-to-config | 2025-12-13 |
| `scripts/run_optimization_pipeline.py` | Full LSTM optimization + walk-forward training | 2025-12-13 |
| `scripts/run_full_master_pipeline.py` | Master orchestrator (evo → opt → RL) | 2025-12-13 |
| `scripts/run_rl_pipeline_all.py` | Train all RL strategies from config/ | 2025-12-13 |

### TensorBoard Integration
Evolution and optimization experiments now log to TensorBoard:
- **Evolution**: `artifacts/runs/evolution/` - Best/Avg/Std fitness per generation
- **Optimization**: `artifacts/runs/optimization/` - Trial validation loss + hyperparams
- **RL Training**: `artifacts/runs/ppo/` - Reward curves, policy/value loss

Launch with: `tensorboard --logdir artifacts/runs`

---

**Documentation Index Version**: 3.0
**Last Updated**: 2025-12-13
**Maintained By**: documentation-specialist
**Next Review**: Weekly or after major changes
**Validation**: Ejecutar `python tools/validate_documentation.py` antes de commits
