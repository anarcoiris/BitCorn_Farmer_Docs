# Guía de Navegación Rápida - BitCorn Farmer

**Última Actualización**: 2025-12-05  
**Versión**: 1.0  
**Mantenido por**: documentation-specialist

Esta guía te ayuda a encontrar rápidamente la documentación que necesitas según tu rol y la tarea que quieres realizar.

---

## Navegación por Rol

### 👤 Nuevo Usuario / Primera Vez

**Objetivo**: Entender el proyecto y hacer setup inicial

**Ruta Recomendada**:
1. **[QUICK_START.md](guides/QUICK_START.md)** (5 min) - Setup básico y primeros pasos
2. **[PROJECT_STATUS.md](PROJECT_STATUS.md)** (10 min) - Entender qué está listo y qué falta
3. **[DOCUMENTATION_INDEX.md](DOCUMENTATION_INDEX.md)** (5 min) - Ver estructura completa

**Tiempo Total**: ~20 minutos

**Documentos Clave**:
- `guides/QUICK_START.md` - Guía principal de inicio
- `PROJECT_STATUS.md` - Estado actual del proyecto
- `README.md` (raíz) - Overview del proyecto

---

### 👨‍💻 Desarrollador / Contribuidor

**Objetivo**: Contribuir código, arreglar bugs, implementar features

**Ruta Recomendada**:
1. **[PROJECT_STATUS.md](PROJECT_STATUS.md)** - Ver estado de componentes y bugs conocidos
2. **[TODO.md](TODO.md)** - Elegir tarea priorizada
3. **[reference/guides/DEVELOPER_GUIDE.md](reference/guides/DEVELOPER_GUIDE.md)** - Guía de desarrollo
4. **[TESTING_TODO.md](TESTING_TODO.md)** - Ver tests existentes y escribir nuevos

**Documentos Clave**:
- `PROJECT_STATUS.md` - Estado técnico y bugs conocidos
- `TODO.md` - Lista de tareas priorizadas
- `reference/guides/DEVELOPER_GUIDE.md` - Guía de desarrollo
- `ui/AGENT_ROADMAP.md` - Roadmap con tareas por agente
- `TESTING_TODO.md` - Tests y validación

**Archivos de Código Principales**:
- `fiboevo.py` - Feature engineering + LSTM models
- `TradeApp.py` - GUI principal
- `trading_daemon.py` - Daemon de trading
- `ui/` - Estructura modular de UI

---

### 🏗️ Arquitecto / Tech Lead

**Objetivo**: Entender arquitectura, tomar decisiones de diseño, planificar

**Ruta Recomendada**:
1. **[PROJECT_MASTER_PLAN.md](PROJECT_MASTER_PLAN.md)** - Plan maestro consolidado
2. **[ui/AGENT_ROADMAP.md](../ui/AGENT_ROADMAP.md)** - Roadmap detallado con agentes
3. **[PROJECT_STATUS.md](PROJECT_STATUS.md)** - Estado técnico completo
4. **[DOCUMENTATION_ANALYSIS_REPORT.md](DOCUMENTATION_ANALYSIS_REPORT.md)** - Análisis de documentación

**Documentos Clave**:
- `PROJECT_MASTER_PLAN.md` - Plan maestro
- `ui/AGENT_ROADMAP.md` - Roadmap con asignación de agentes
- `ui/HYPERPARAMETER_STRATEGY_PLAN.md` - Plan de optimización de hiperparámetros
- `ui/REFACTORING_PLAN.md` - Plan de refactorización
- `docs/technical/` - Documentación técnica profunda

---

### 🧪 Tester / QA Engineer

**Objetivo**: Testear, validar, asegurar calidad

**Ruta Recomendada**:
1. **[TESTING_TODO.md](TESTING_TODO.md)** - Lista completa de tests
2. **[PROJECT_STATUS.md](PROJECT_STATUS.md)** - Ver qué componentes necesitan testing
3. **[tests/REGRESSION_REPORT_2025-12-04.md](../tests/REGRESSION_REPORT_2025-12-04.md)** - Reportes de regresión
4. **[ui/*_VALIDATION_REPORT.md](../ui/)** - Reportes de validación de componentes

**Documentos Clave**:
- `TESTING_TODO.md` - Tracker de tests
- `tests/REGRESSION_REPORT_*.md` - Reportes de regresión
- `ui/RL_STRATEGY_TAB_VALIDATION_REPORT.md` - Validación RL Strategy
- `ui/STATUS_TAB_VALIDATION_REPORT.md` - Validación Status Tab
- `ui/BACKTEST_TAB_VALIDATION_REPORT.md` - Validación Backtest Tab
- `ui/WEBSOCKET_PANEL_VALIDATION_REPORT.md` - Validación WebSocket Panel

---

### 📊 Data Scientist / ML Engineer

**Objetivo**: Entender modelos, features, optimización

**Ruta Recomendada**:
1. **[PROJECT_STATUS.md](PROJECT_STATUS.md)** Sección 1.1 - Core LSTM System
2. **[technical/VOLATILITY_SCALING_FIX.md](technical/VOLATILITY_SCALING_FIX.md)** - Fix crítico matemático
3. **[guides/SYNTHETIC_DATA_GUIDE.md](guides/SYNTHETIC_DATA_GUIDE.md)** - Pipeline de datos
4. **[ui/HYPERPARAMETER_STRATEGY_PLAN.md](../ui/HYPERPARAMETER_STRATEGY_PLAN.md)** - Estrategia de optimización

**Documentos Clave**:
- `PROJECT_STATUS.md` - Estado de modelos y features
- `technical/VOLATILITY_SCALING_FIX.md` - Fix matemático crítico
- `guides/SYNTHETIC_DATA_GUIDE.md` - Data quality y gap filling
- `ui/HYPERPARAMETER_STRATEGY_PLAN.md` - Optimización sistemática
- `FEATURE_ENGINEERING_V4.md` (raíz) - Feature engineering v4

**Conceptos Clave**:
- LSTM2Head: Dual-output (returns + volatility)
- Feature Engineering: v1 (deprecated), v2 (producción), v3/v4 (mejoras)
- Multi-Horizon Forecasting: 1h a 24h
- Walk-Forward Validation: 19 folds

---

## Navegación por Tarea

### 🚀 Setup Inicial

**"Quiero instalar y configurar el proyecto"**

1. **[QUICK_START.md](guides/QUICK_START.md)** - Setup básico (5 min)
2. **[reference/installation/INSTALL_CUDA.md](reference/installation/INSTALL_CUDA.md)** - Si necesitas CUDA
3. Verificar datos en `data_manager/exports/marketdata_base.db`

**Checklist**:
- [ ] Python 3.10+ instalado
- [ ] Dependencias instaladas
- [ ] Base de datos con datos
- [ ] (Opcional) CUDA configurado

---

### 🎓 Entrenar Primer Modelo

**"Quiero entrenar mi primer modelo LSTM"**

1. **[QUICK_START.md](guides/QUICK_START.md)** Sección "Train Your First Model"
2. **[PROJECT_STATUS.md](PROJECT_STATUS.md)** Sección 1.1 - LSTM Training Pipeline
3. **[TODO.md](TODO.md)** TASK-001 - Multi-Config Model Training

**Documentos Relacionados**:
- `guides/TRAINING_PRESETS_GUIDE.md` - Presets de entrenamiento
- `config/training_options.json` - Opciones de configuración

**Nota**: `docs/guides/MODEL_TRAINING_GUIDE.md` está planificado pero no creado aún.

---

### 🐛 Debugging / Troubleshooting

**"Algo no funciona, necesito debuggear"**

1. **[PROJECT_STATUS.md](PROJECT_STATUS.md)** Sección 8 - Problemas Identificados
2. **[TODO.md](TODO.md)** - Ver bugs conocidos y fixes
3. **[tests/REGRESSION_REPORT_*.md](../tests/)** - Ver reportes de regresión
4. **[DOCUMENTATION_ANALYSIS_REPORT.md](DOCUMENTATION_ANALYSIS_REPORT.md)** - Ver conflictos conocidos

**Áreas Comunes de Problemas**:
- **Data Leakage**: Ver `PROJECT_STATUS.md` Sección 1.1 (Feature Engineering)
- **Chronological Ordering**: Ver `REVISION_ESTADO_DESARROLLO_2025-12-04.md` Sección 1
- **GUI Bugs**: Ver `TODO.md` TASK-002
- **Model Loading**: Ver `PROJECT_STATUS.md` Sección 1.6

---

### 🔧 Implementar Nueva Feature

**"Quiero agregar una nueva funcionalidad"**

1. **[ui/AGENT_ROADMAP.md](../ui/AGENT_ROADMAP.md)** - Ver roadmap y asignación de agentes
2. **[ui/SPECIALIST_REQUIREMENTS.md](../ui/SPECIALIST_REQUIREMENTS.md)** - Ver requisitos por especialista
3. **[reference/guides/DEVELOPER_GUIDE.md](reference/guides/DEVELOPER_GUIDE.md)** - Guía de desarrollo
4. **[.agent/workflows/](../.agent/workflows/)** - Ver workflows de agentes especializados

**Proceso Recomendado**:
1. Verificar si está en roadmap
2. Asignar a agente especializado apropiado
3. Seguir protocolo de handoff (ver `AGENT_COMMUNICATION_PROTOCOL.md`)
4. Actualizar documentación al completar

---

### 📈 Optimizar Hiperparámetros

**"Quiero optimizar hiperparámetros de mi modelo"**

1. **[ui/HYPERPARAMETER_STRATEGY_PLAN.md](../ui/HYPERPARAMETER_STRATEGY_PLAN.md)** - Plan sistemático
2. **[ui/AGENT_MEETING_2025-12-04.md](../ui/AGENT_MEETING_2025-12-04.md)** - Decisiones de reunión
3. **[guides/HYPERPARAMETER_PHASES_GUIDE.md](guides/HYPERPARAMETER_PHASES_GUIDE.md)** - Sistema de fases

**Estado Actual**:
- ⚠️ Plan existe pero implementación pendiente
- UI básica en `ui/tabs/backtest_tab.py`
- Worker en `ui/workers/backtest_worker.py` (con TODO)

**Nota**: `docs/guides/HYPERPARAMETER_STRATEGY_GUIDE.md` está vacío y necesita completarse.

---

### 🧪 Escribir Tests

**"Necesito escribir tests para mi código"**

1. **[TESTING_TODO.md](TESTING_TODO.md)** - Ver tests existentes y pendientes
2. **[tests/](../tests/)** - Ver estructura de tests existentes
3. **[reference/guides/DEVELOPER_GUIDE.md](reference/guides/DEVELOPER_GUIDE.md)** - Guía de desarrollo

**Tests Existentes**:
- `test_feature_generation_v2.py` - Feature engineering
- `test_volatility_fix.py` - Volatility scaling
- `tests/integration/test_ui_initialization_flow.py` - UI integration

**Cobertura Objetivo**: 80% (actual: 40%)

---

## Navegación por Feature

### 🤖 LSTM Models

**Documentación Principal**:
- `PROJECT_STATUS.md` Sección 1.1 - Core LSTM System
- `fiboevo.py` - Implementación
- `technical/VOLATILITY_SCALING_FIX.md` - Fix crítico

**Estado**: 100% completo, production ready

---

### 🎯 Feature Engineering

**Documentación Principal**:
- `PROJECT_STATUS.md` Sección 1.1 - Feature Engineering Systems
- `FEATURE_ENGINEERING_V4.md` (raíz) - v4 con fuzzy logic
- `fiboevo.py` - Implementación

**Versiones**:
- **v1**: Deprecated (data leakage)
- **v2**: Producción actual (14 features)
- **v3/v4**: Mejoras (14-17 features)

**Nota**: Falta guía consolidada de todas las versiones.

---

### 🔮 Multi-Horizon Forecasting

**Documentación Principal**:
- `PROJECT_STATUS.md` Sección 1.3 - Multi-Horizon Inference
- `reference/guides/MULTI_HORIZON_DASHBOARD.md` - Dashboard guide
- `multi_horizon_inference.py` - Implementación

**Estado**: 100% implementado, 60% testing

---

### 🎲 Reinforcement Learning (RL)

**Documentación Principal**:
- `PROJECT_STATUS.md` Sección 1.5 - RL Strategy
- `guides/RL_IMPLEMENTATION_GUIDE.md` - Guía de implementación
- `ui/RL_STRATEGY_TAB_VALIDATION_REPORT.md` - Validación
- `rl_training_pipeline.py` - Implementación

**Estado**: 95% completo, 30% testing

---

### ⚙️ Hyperparameter Strategy

**Documentación Principal**:
- `ui/HYPERPARAMETER_STRATEGY_PLAN.md` - Plan sistemático
- `ui/AGENT_MEETING_2025-12-04.md` - Decisiones
- `guides/HYPERPARAMETER_PHASES_GUIDE.md` - Sistema de fases

**Estado**: ⚠️ **PENDIENTE** - Plan listo, implementación pendiente

**Nota**: `docs/guides/HYPERPARAMETER_STRATEGY_GUIDE.md` está vacío.

---

### 🖥️ GUI (TradeApp)

**Documentación Principal**:
- `PROJECT_STATUS.md` Sección 1.7 - TradeApp GUI
- `ui/AGENT_ROADMAP.md` - Roadmap de refactorización
- `ui/*_VALIDATION_REPORT.md` - Reportes de validación por tab

**Tabs Implementados**:
- ✅ Preview Tab
- ✅ Training Tab
- ✅ WalkForward Tab
- ✅ Audit Tab
- ✅ Backtest Tab
- ✅ Status Tab
- ✅ WebSocket Panel
- ✅ RL Strategy Tab

**Tabs Pendientes**:
- 🔴 Hyperparameter Strategy (Prioridad ALTA)
- 📋 Oracle Window
- 📋 Multi-Horizon Dashboard
- 📋 Feature Evolution Tab
- 📋 Feature Composer Tab

---

## Búsqueda Rápida

### Por Problema Común

**"Tengo un error de data leakage"**
→ `PROJECT_STATUS.md` Sección 1.1 (Feature Engineering)  
→ `tests/` - Buscar tests de data leakage

**"Los datos no están en orden cronológico"**
→ `REVISION_ESTADO_DESARROLLO_2025-12-04.md` Sección 1  
→ `trading_daemon.py:793-812` - Validación cronológica

**"El modelo no carga correctamente"**
→ `PROJECT_STATUS.md` Sección 1.6 (Model Organization)  
→ `TODO.md` TASK-007 - Best Model Selection

**"La GUI se congela"**
→ `TODO.md` TASK-002 - Critical GUI Bugs  
→ `ui/*_VALIDATION_REPORT.md` - Ver reportes de validación

---

### Por Archivo de Código

**"Quiero entender `fiboevo.py`"**
→ `PROJECT_STATUS.md` Sección 1.1  
→ `FEATURE_ENGINEERING_V4.md` (raíz)

**"Quiero entender `trading_daemon.py`"**
→ `PROJECT_STATUS.md` Sección 1.7  
→ `ui/STATUS_TAB_VALIDATION_REPORT.md`

**"Quiero entender `TradeApp.py`"**
→ `PROJECT_STATUS.md` Sección 1.7  
→ `ui/AGENT_ROADMAP.md` - Refactorización UI

---

## Referencias Rápidas

### Documentos Más Consultados

1. **[PROJECT_STATUS.md](PROJECT_STATUS.md)** - Estado técnico completo
2. **[TODO.md](TODO.md)** - Tareas pendientes
3. **[QUICK_START.md](guides/QUICK_START.md)** - Setup inicial
4. **[ui/AGENT_ROADMAP.md](../ui/AGENT_ROADMAP.md)** - Roadmap de desarrollo
5. **[TESTING_TODO.md](TESTING_TODO.md)** - Tests pendientes

### Índices y Guías

- **[DOCUMENTATION_INDEX.md](DOCUMENTATION_INDEX.md)** - Índice completo de documentación
- **[NAVIGATION_GUIDE.md](NAVIGATION_GUIDE.md)** - Esta guía
- **[DOCUMENTATION_ANALYSIS_REPORT.md](DOCUMENTATION_ANALYSIS_REPORT.md)** - Análisis de documentación

---

## Tips de Navegación

1. **Siempre consulta la Fuente de Verdad** (ver `DOCUMENTATION_INDEX.md` Sección "Fuentes de Verdad")
2. **Si encuentras información contradictoria**, consulta `DOCUMENTATION_ANALYSIS_REPORT.md` Sección 2
3. **Para features pendientes**, verifica `ui/AGENT_ROADMAP.md` y `ui/SPECIALIST_REQUIREMENTS.md`
4. **Para bugs conocidos**, consulta `TODO.md` y `PROJECT_STATUS.md` Sección 8
5. **Para entender decisiones**, revisa `ui/AGENT_MEETING_*.md` más reciente

---

**Última Actualización**: 2025-12-05  
**Próxima Revisión**: Semanal o después de cambios mayores

