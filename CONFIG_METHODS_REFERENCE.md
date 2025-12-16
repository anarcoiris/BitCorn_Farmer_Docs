# Referencia de Métodos de Configuración - BitCorn Farmer

**Última actualización**: 2025-12-10  
**Estado**: Actualizado con progreso de migración a `master_config.json`  
**Fuente**: Consolidado desde `docs/config_audit/phase*/` y `docs/config_migration_status_report.md`

---

## Resumen Ejecutivo

### Métricas Actuales (2025-12-10)

- **Total de métodos**: 131
- **Métodos migrados a `master_config.json`**: 22 (17%)
- **Métodos críticos migrados**: 7/7 (100%) ✅
- **Métodos importantes migrados**: 15/18 (83%)
- **Métodos legacy**: 86 (66%)
- **Métodos con acceso directo**: 19 (15%)

### Progreso de Migración

| Categoría | Total | Migrados | Pendientes | % Completado |
|-----------|-------|----------|------------|--------------|
| Métodos críticos | 7 | 7 | 0 | 100% ✅ |
| Métodos importantes | 18 | 15 | 3 | 83% |
| Métodos legacy | 86 | 0 | 86 | 0% |
| Acceso directo | 19 | 0 | 19 | 0% |
| **TOTAL** | **131** | **22** | **109** | **17%** |

---

## 1. Inventario de Métodos por Sistema

### 1.1 MasterConfigManager ✅ (Migrado - 22 métodos)

**Estado**: ✅ Funcional y en uso  
**Archivos principales**:
- `core/config_manager.py` - Implementación completa
- `training_utils.py` - Integrado
- `scripts/train_multihorizon_model.py` - Integrado
- `scripts/train_multiscale_model.py` - Integrado
- `scripts/rl/rl_training_pipeline.py` - Integrado
- `ui/utils/unified_config_manager.py` - Usa como backend

#### Métodos de Lectura (20)

- `get_training_config()` - `core/config_manager.py:352`
- `get_model_config()` - `core/config_manager.py:372`
- `get_data_config()` - `core/config_manager.py:381`
- `get_features_config()` - `core/config_manager.py:390`
- `get_global_config()` - `core/config_manager.py:512`
- `get_section_config()` - `core/config_manager.py:527`
- `load_influx_config()` - `data_manager/data_sources/influx_to_sql.py:305`
- `load_run_config()` - `scripts/create_run_config.py:97`
- `load_legacy_config()` - `scripts/migrate_configs.py:36`
- `load_config()` - `scripts/rl/rl_training_pipeline.py:43`
- `load_config()` - `scripts/train_multihorizon_model.py:71`
- `load_multiscale_config()` - `scripts/train_multiscale_model.py:59`
- `load_training_config()` - `training_utils.py:63`
- `get_default_config()` - `training_utils.py:177`
- `get_loss_function()` - `training_utils.py:348`
- `get_optimizer()` - `training_utils.py:452`
- `get_scheduler()` - `training_utils.py:502`
- `get_scaler()` - `training_utils.py:548`
- `get_instance()` - `ui/utils/unified_config_manager.py:137`
- `get_unified_config_manager()` - `ui/utils/unified_config_manager.py:431`

#### Métodos de Escritura (2)

- `save()` - `core/config_manager.py:417`
- `save_training_config()` - `training_utils.py:142`

### 1.2 UnifiedConfigManager ✅ (Parcial - 2 métodos)

**Estado**: ✅ Funcional, pero necesita mejoras  
**Archivos principales**:
- `ui/utils/unified_config_manager.py` - Implementación
- `ui/utils/config_manager.py` - Integrado

#### Métodos

- `load_config()` - `ui/utils/config_manager.py:43` (Lectura)
- `save_config()` - `ui/utils/config_manager.py:124` (Escritura)

**Problemas conocidos**:
- `set()` no guarda en `master_config.json` (crítico)
- Mapeo legacy incompleto

### 1.3 ConfigManager_UI (1 método)

- `save_config()` - `ui/ui_main_window.py:1732` (Escritura)

### 1.4 config_manager_legacy (1 método)

- `save_config()` - `trading_daemon.py:529` (Escritura)

### 1.5 Acceso Directo a JSON ❌ (19 métodos - Necesitan migración)

**Estado**: ❌ Necesita migración

#### Métodos de Lectura (11)

- `load_config()` - `config_manager.py:93`
- `load_training_config()` - `config_manager.py:258`
- `load_gui_config()` - `config_manager.py:308`
- `load_rl_config()` - `config_manager.py:341`
- `load_dashboard_config()` - `config_manager.py:377`
- `load_influx_config()` - `config_manager.py:414`
- `get_default_training_config()` - `config_manager.py:555`
- `get_default_gui_config()` - `config_manager.py:642`
- `load_base_config()` - `scripts/training_config.py:69`
- `test_config()` - `tests/test_rl_integration.py:149`
- `train_model_worker()` - `ui/workers/training_worker.py:483`

#### Métodos de Escritura (8)

- `save_config()` - `config_manager.py:121`
- `save_training_config()` - `config_manager.py:293`
- `save_gui_config()` - `config_manager.py:326`
- `save_rl_config()` - `config_manager.py:362`
- `save_dashboard_config()` - `config_manager.py:397`
- `save_influx_config()` - `config_manager.py:433`
- `save_config()` - `scripts/train_multiscale_model.py:305` (acceso directo)
- Otros scripts menores

---

## 2. Clasificación por Prioridad

### 2.1 Métodos Críticos 🔴 (7 métodos - 100% migrados ✅)

Estos métodos son esenciales para el funcionamiento del sistema. **Todos migrados a MasterConfigManager**.

| Método | Archivo | Impacto | Estado |
|--------|---------|---------|--------|
| `load_training_config()` | `training_utils.py` | 10/10 | ✅ Migrado |
| `get_default_config()` | `training_utils.py` | 10/10 | ✅ Migrado |
| `get_loss_function()` | `training_utils.py` | 10/10 | ✅ Migrado |
| `get_optimizer()` | `training_utils.py` | 10/10 | ✅ Migrado |
| `get_scheduler()` | `training_utils.py` | 10/10 | ✅ Migrado |
| `get_scaler()` | `training_utils.py` | 10/10 | ✅ Migrado |
| `save_training_config()` | `training_utils.py` | 10/10 | ✅ Migrado |

### 2.2 Métodos Importantes 🟡 (18 métodos - 83% migrados)

Estos métodos afectan features específicas pero no son críticos para el core.

| Método | Archivo | Impacto | Estado |
|--------|---------|---------|--------|
| `load_training_config()` | `training_options_panel.py` | 8/10 | ⚠️ Legacy |
| `load_config()` | `scripts/train_multihorizon_model.py` | 8/10 | ✅ Migrado |
| `load_config()` | `scripts/rl/rl_training_pipeline.py` | 8/10 | ✅ Migrado |
| `save()` | `core/config_manager.py` | 8/10 | ✅ Migrado |
| `load_influx_config()` | `data_manager/data_sources/influx_to_sql.py` | 7/10 | ✅ Migrado |
| `load_run_config()` | `scripts/create_run_config.py` | 7/10 | ✅ Migrado |
| `get_default_config()` | `training_options_panel.py` | 7/10 | ⚠️ Legacy |
| `get_training_config()` | `core/config_manager.py` | 7/10 | ✅ Migrado |
| `get_instance()` | `ui/utils/unified_config_manager.py` | 7/10 | ✅ Migrado |
| `get_unified_config_manager()` | `ui/utils/unified_config_manager.py` | 7/10 | ✅ Migrado |
| `save_training_config()` | `training_options_panel.py` | 7/10 | ⚠️ Legacy |
| `load_legacy_config()` | `scripts/migrate_configs.py` | 6/10 | ✅ Migrado |
| `load_multiscale_config()` | `scripts/train_multiscale_model.py` | 6/10 | ✅ Migrado |
| `get_model_config()` | `core/config_manager.py` | 6/10 | ✅ Migrado |
| `get_data_config()` | `core/config_manager.py` | 6/10 | ✅ Migrado |
| `get_features_config()` | `core/config_manager.py` | 6/10 | ✅ Migrado |
| `get_global_config()` | `core/config_manager.py` | 6/10 | ✅ Migrado |
| `get_section_config()` | `core/config_manager.py` | 6/10 | ✅ Migrado |

### 2.3 Métodos Legacy 🟠 (86 métodos - 0% migrados)

Métodos deprecated pero aún en uso. Deben migrarse gradualmente.

**Principales archivos legacy**:
- `config_manager.py` (legacy) - En proceso de deprecación
- Múltiples tabs UI - Algunos usan legacy, otros no implementados

**Estado**: ⚠️ En transición, algunos aún en uso

### 2.4 Métodos con Acceso Directo ❌ (19 métodos - 0% migrados)

**Problemas**:
- Algunos scripts acceden directamente a JSON
- Algunos fallbacks usan acceso directo (aceptable)
- Paths hardcodeados en algunos lugares

**Estado**: ❌ Necesita migración

---

## 3. Estado de Migración a master_config.json

### 3.1 Métodos Migrados ✅

**Total**: 22 métodos (17%)

**Sistemas migrados**:
- ✅ MasterConfigManager: 22 métodos
- ✅ UnifiedConfigManager: 2 métodos (parcial)

**Archivos completamente migrados**:
- ✅ `core/config_manager.py`
- ✅ `training_utils.py`
- ✅ `scripts/train_multihorizon_model.py`
- ✅ `scripts/train_multiscale_model.py`
- ✅ `scripts/rl/rl_training_pipeline.py`
- ✅ `data_manager/data_sources/influx_to_sql.py`

### 3.2 Métodos Pendientes de Migración

#### Prioridad Alta

1. **UnifiedConfigManager.set()** - No guarda en `master_config.json`
2. **ConfigManager.save_config()** - No guarda en `master_config.json`
3. **MasterConfigManager.load()** - Falta fallback a legacy (✅ Resuelto según roadmap)

#### Prioridad Media

4. **Tabs UI sin get_config/set_config**:
   - `backtest_tab.py`
   - `training_tab.py` (set_config no implementado)
   - `feature_evolution_tab.py`
   - `walkforward_tab.py`
   - `audit_tab.py`
   - `status_tab.py`

5. **Scripts con acceso directo**:
   - `scripts/train_multiscale_model.py` (línea 305)
   - Otros scripts menores

#### Prioridad Baja

6. **Paths hardcodeados**:
   - Reemplazar con PROJECT_ROOT
   - Usar paths relativos

---

## 4. Problemas Identificados y Estado

### 4.1 Problemas Críticos (Resueltos ✅)

1. ✅ **MasterConfigManager.load()** - Fallback a legacy implementado
2. ✅ **UnifiedConfigManager.set()** - Guardado en MasterConfigManager implementado
3. ✅ **ConfigManager.save_config()** - Guardado en MasterConfigManager implementado
4. ✅ **Validación antes de escribir** - Implementada

### 4.2 Problemas Pendientes

1. **Mapeo legacy incompleto** - UnifiedConfigManager solo mapea 17 claves
2. **Tabs UI sin implementación** - 6 tabs sin get_config/set_config
3. **Acceso directo a JSON** - 19 métodos aún acceden directamente
4. **Paths hardcodeados** - Varios lugares con paths absolutos

---

## 5. Guía de Uso

### 5.1 Para Nuevos Desarrolladores

**Usar MasterConfigManager** para todas las operaciones de configuración:

```python
from core.path_init import ensure_project_root
ensure_project_root()
from core.config_manager import MasterConfigManager

config_mgr = MasterConfigManager("config/master_config.json")
config = config_mgr.load()

# Acceder a valores
training_config = config.get("global", {}).get("training", {})
```

### 5.2 Para Migración de Código Legacy

1. Identificar método legacy a migrar
2. Reemplazar con `MasterConfigManager`
3. Actualizar referencias
4. Probar funcionalidad
5. Marcar como migrado en este documento

### 5.3 Para UI Components

**Usar UnifiedConfigManager** con mapeo a MasterConfigManager:

```python
from ui.utils.unified_config_manager import UnifiedConfigManager

config_mgr = UnifiedConfigManager.get_instance()
value = config_mgr.get("training.epochs")
config_mgr.set("training.epochs", 100)
```

---

## 6. Referencias

### Documentación Histórica

- `docs/config_audit/phase1/config_methods_inventory.md` - Inventario completo original
- `docs/config_audit/phase2/config_methods_priority.md` - Clasificación por prioridad original
- `docs/config_audit/phase1/config_methods_matrix.md` - Matriz de dependencias
- `docs/config_audit/phase5/config_issues_report_final.md` - Problemas identificados
- `docs/config_audit/phase5/config_audit_executive_summary.md` - Resumen ejecutivo original

### Documentación Actual

- `docs/config_migration_status_report.md` - Estado actual de migración
- `docs/STANDARDS.md` - Estándares de configuración
- `docs/CONFIG_AUDIT_SUMMARY.md` - Resumen ejecutivo consolidado

### Código

- `core/config_manager.py` - MasterConfigManager (sistema unificado)
- `ui/utils/unified_config_manager.py` - UnifiedConfigManager (UI)
- `config/master_config.json` - Archivo de configuración maestro

---

## 7. Roadmap de Migración

### Corto Plazo (Completado ✅)

1. ✅ Implementar fallback en MasterConfigManager.load()
2. ✅ Implementar guardado en MasterConfigManager en UnifiedConfigManager.set()
3. ✅ Implementar guardado en MasterConfigManager en ConfigManager.save_config()
4. ✅ Agregar validación antes de escribir

### Medio Plazo (1 mes)

1. Implementar get_config/set_config en tabs faltantes
2. Expandir mapeo legacy en UnifiedConfigManager
3. Migrar scripts con acceso directo
4. Reemplazar paths hardcodeados

### Largo Plazo (2-3 meses)

1. Completar migración de todos los métodos
2. Deprecar archivos legacy
3. Eliminar fallbacks a legacy
4. Consolidar en `master_config.json` únicamente

---

**Nota**: Este documento se actualiza periódicamente. Para información histórica detallada, consultar `docs/config_audit/phase*/`.

