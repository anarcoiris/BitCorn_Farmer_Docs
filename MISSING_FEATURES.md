# Reporte de Funcionalidades Faltantes - BitCorn Farmer UI

**Fecha**: 2025-12-13  
**Versión**: 1.0  
**Estado**: Análisis Completo

---

## Resumen Ejecutivo

Este reporte compara funcionalidades de scripts huérfanos con las implementaciones actuales en el sistema UI modular, identificando features avanzadas que faltan y utilidades reutilizables no integradas.

**Total de Features Faltantes Identificadas**: 9  
**Features Críticas**: 4  
**Features Importantes**: 3  
**Features Opcionales**: 2

---

## 1. Comparación: `prediction_dashboard_tab.py` vs `multihorizon_dashboard.py`

### 1.1 Features Faltantes en `multihorizon_dashboard.py`

#### ❌ AsyncPredictionRunner (CRÍTICA)

**Estado**: NO IMPLEMENTADO

**Descripción**:
- Clase en `dashboard_utils.py` que ejecuta predicciones en background thread
- Evita bloquear el UI thread durante generación de predicciones
- Proporciona callbacks para actualización asíncrona

**Código en `prediction_dashboard_tab.py`**:
```python
from dashboard_utils import AsyncPredictionRunner

self.async_runner = AsyncPredictionRunner(callback=self._on_prediction_complete)
self.async_runner.run_prediction(df, model, meta, scaler, device, horizons)
```

**Implementación Actual en `multihorizon_dashboard.py`**:
- Usa polling directo de `daemon.predictions_queue`
- No genera predicciones propias de forma asíncrona
- Depende completamente del daemon

**Impacto**:
- **Performance**: ALTO - Bloquea UI si genera predicciones propias
- **Funcionalidad**: ALTO - No puede generar predicciones independientes del daemon
- **UX**: MEDIO - Usuario debe esperar si genera predicciones manualmente

**Recomendación**: **INTEGRAR** `AsyncPredictionRunner` en `multihorizon_dashboard.py`

**Prioridad**: ALTA

#### ❌ PredictionCache (CRÍTICA)

**Estado**: NO IMPLEMENTADO

**Descripción**:
- Caché inteligente que evita recomputar predicciones cuando los datos no han cambiado
- Basado en key tuple: (horizons, data_length, last_price)
- TTL configurable (default: 300 segundos)

**Código en `prediction_dashboard_tab.py`**:
```python
from dashboard_utils import PredictionCache

self.cache = PredictionCache(max_age_seconds=300, max_size=10)
cached = self.cache.get(cache_key)
if cached:
    return cached
# ... generate prediction ...
self.cache.set(cache_key, predictions)
```

**Implementación Actual en `multihorizon_dashboard.py`**:
- No tiene caché
- Regenera predicciones en cada actualización
- Puede causar trabajo redundante

**Impacto**:
- **Performance**: ALTO - Trabajo redundante
- **Recursos**: MEDIO - CPU/GPU innecesario
- **UX**: BAJO - Usuario no nota diferencia directamente

**Recomendación**: **INTEGRAR** `PredictionCache` en `multihorizon_dashboard.py`

**Prioridad**: ALTA

#### ❌ Multiple Scenarios (bull/base/bear) (IMPORTANTE)

**Estado**: NO IMPLEMENTADO

**Descripción**:
- Soporte para múltiples escenarios de volatilidad:
  - Base: Volatilidad estándar (1.0x)
  - Bull: Volatilidad reducida (0.7x) - optimista
  - Bear: Volatilidad aumentada (1.5x) - pesimista
- Permite visualizar diferentes casos de uso

**Código en `prediction_dashboard_tab.py`**:
```python
self.scenarios_enabled_var = BooleanVar(value=False)
self.active_scenarios = ["base"]  # Always include base

# In prediction generation:
if self.scenarios_enabled_var.get():
    scenarios = {
        "base": predictions,
        "bull": apply_volatility_multiplier(predictions, 0.7),
        "bear": apply_volatility_multiplier(predictions, 1.5)
    }
```

**Implementación Actual en `multihorizon_dashboard.py`**:
- Solo muestra escenario base
- No tiene controles para múltiples escenarios
- No tiene visualización de múltiples fans

**Impacto**:
- **Funcionalidad**: ALTO - Feature avanzada útil para análisis
- **UX**: MEDIO - Mejora capacidad de análisis
- **Competitividad**: MEDIO - Feature diferenciadora

**Recomendación**: **INTEGRAR** soporte para múltiples escenarios

**Prioridad**: MEDIA

#### ❌ Probability Density Layers (IMPORTANTE)

**Estado**: NO IMPLEMENTADO

**Descripción**:
- Visualización de densidad de probabilidad en puntos clave del futuro
- Usa KDE (Kernel Density Estimation) para mostrar distribución de precios
- Añade capa visual de incertidumbre

**Código en `prediction_dashboard_tab.py`**:
```python
from dashboard_visualizations import plot_probability_density_layers

if self.show_probability_var.get():
    plot_probability_density_layers(ax, predictions, key_horizons=[5, 10, 20])
```

**Implementación Actual en `multihorizon_dashboard.py`**:
- No tiene visualización de densidad de probabilidad
- Solo muestra intervalos de confianza básicos

**Impacto**:
- **Visualización**: ALTO - Mejora comprensión de incertidumbre
- **Análisis**: MEDIO - Útil para análisis avanzado
- **UX**: MEDIO - Mejora experiencia visual

**Recomendación**: **INTEGRAR** probability density layers

**Prioridad**: MEDIA

#### ❌ Auto-refresh Configurable (IMPORTANTE)

**Estado**: PARCIALMENTE IMPLEMENTADO

**Descripción**:
- Auto-refresh con intervalo configurable (1-60 minutos)
- Toggle para habilitar/deshabilitar
- Persistencia de configuración

**Código en `prediction_dashboard_tab.py`**:
```python
self.auto_refresh_var = BooleanVar(value=True)
self.update_interval_var = IntVar(value=5)  # minutes

if self.auto_refresh_var.get():
    self._schedule_next_update()
```

**Implementación Actual en `multihorizon_dashboard.py`**:
- Tiene polling automático pero no es configurable por usuario
- Intervalo fijo (5 segundos)
- No tiene toggle para deshabilitar

**Impacto**:
- **UX**: MEDIO - Usuario no puede controlar frecuencia
- **Performance**: BAJO - Polling constante puede ser innecesario
- **Funcionalidad**: MEDIO - Feature básica de configuración

**Recomendación**: **MEJORAR** para hacer configurable

**Prioridad**: MEDIA

#### ❌ Horizon Selection Checkboxes (OPCIONAL)

**Estado**: NO IMPLEMENTADO

**Descripción**:
- Checkboxes para seleccionar qué horizontes mostrar
- Botones "Select All" / "Clear All"
- Persistencia de selección

**Código en `prediction_dashboard_tab.py`**:
```python
self.horizon_vars: Dict[int, BooleanVar] = {}
for h in [1, 3, 5, 10, 15, 20, 30]:
    self.horizon_vars[h] = BooleanVar(value=(h in default_horizons))
    Checkbutton(horizon_frame, text=f"{h}h", variable=self.horizon_vars[h])
```

**Implementación Actual en `multihorizon_dashboard.py`**:
- Muestra todos los horizontes disponibles del modelo
- No tiene controles para seleccionar/deseleccionar

**Impacto**:
- **UX**: BAJO - Mejora control del usuario
- **Visualización**: BAJO - Reduce clutter si hay muchos horizontes
- **Funcionalidad**: BAJO - Feature de conveniencia

**Recomendación**: **AGREGAR** si hay muchos horizontes (opcional)

**Prioridad**: BAJA

#### ❌ Color Scheme Selection (OPCIONAL)

**Estado**: NO IMPLEMENTADO

**Descripción**:
- Selector de esquema de colores (viridis, plasma, coolwarm, etc.)
- Aplica colormap de matplotlib
- Persistencia de preferencia

**Código en `prediction_dashboard_tab.py`**:
```python
self.color_scheme_var = StringVar(value="viridis")
color_schemes = ["viridis", "plasma", "coolwarm", "RdYlGn", "Blues"]
OptionMenu(viz_options_frame, self.color_scheme_var, *color_schemes)
```

**Implementación Actual en `multihorizon_dashboard.py`**:
- Usa esquema de colores fijo
- No tiene selector

**Impacto**:
- **UX**: BAJO - Personalización cosmética
- **Funcionalidad**: BAJO - No afecta funcionalidad core

**Recomendación**: **AGREGAR** como feature de personalización (opcional)

**Prioridad**: BAJA

#### ❌ Config Persistence (CRÍTICA)

**Estado**: NO IMPLEMENTADO

**Descripción**:
- Guarda configuración del dashboard en JSON
- Carga configuración al iniciar
- Integración con config manager centralizado

**Código en `prediction_dashboard_tab.py`**:
```python
def _load_config(self) -> Dict[str, Any]:
    # Uses config_manager if available
    if _HAS_CONFIG_MANAGER:
        return _cm.load_dashboard_config(self.config_path)
    # Fallback to direct loading
    ...

def _save_config(self):
    # Saves current settings
    dashboard_config = {
        "auto_refresh": self.auto_refresh_var.get(),
        "update_interval_minutes": self.update_interval_var.get(),
        "color_scheme": self.color_scheme_var.get(),
        ...
    }
```

**Implementación Actual en `multihorizon_dashboard.py`**:
- No persiste configuración
- Usa valores por defecto siempre
- Usuario debe reconfigurar cada vez

**Impacto**:
- **UX**: ALTO - Usuario pierde configuración al cerrar
- **Funcionalidad**: MEDIO - Feature básica de persistencia
- **Profesionalismo**: MEDIO - Feature esperada en aplicaciones profesionales

**Recomendación**: **INTEGRAR** persistencia de configuración

**Prioridad**: ALTA

#### ❌ fetch_latest_data Utility (CRÍTICA)

**Estado**: NO IMPLEMENTADO

**Descripción**:
- Función en `dashboard_utils.py` para obtener datos recientes de SQLite
- Incluye feature engineering opcional
- Validación de datos

**Código en `prediction_dashboard_tab.py`**:
```python
from dashboard_utils import fetch_latest_data

df = fetch_latest_data(
    sqlite_path=self.app.sqlite_path.get(),
    min_rows=1000,
    add_features=True
)
```

**Implementación Actual en `multihorizon_dashboard.py`**:
- Usa `app.daemon._load_recent_rows()` directamente
- No tiene función utility reutilizable
- Depende de que daemon esté corriendo

**Impacto**:
- **Reutilización**: ALTO - Función útil para otros componentes
- **Independencia**: MEDIO - Depende menos del daemon
- **Mantenibilidad**: MEDIO - Código centralizado

**Recomendación**: **INTEGRAR** `fetch_latest_data` o crear equivalente en `data_pipeline/`

**Prioridad**: ALTA

---

## 2. Comparación: `walk_forward_visualization_tab.py` vs `walkforward_tab.py`

### 2.1 Features Faltantes en `walkforward_tab.py`

#### ❌ Interactive Playback Controls (IMPORTANTE)

**Estado**: NO IMPLEMENTADO

**Descripción en `walk_forward_visualization_tab.py`**:
- Controles de reproducción: Play/Pause, Step Prev/Next, Rewind, Fast Forward
- Control de velocidad (1x-10x)
- Navegación por slider de folds

**Código**:
```python
self.btn_play = Button(text="▶ Play", command=self._toggle_playback)
self.btn_prev = Button(text="⏮ Prev", command=self._step_prev)
self.btn_next = Button(text="⏭ Next", command=self._step_next)
speed_scale = Scale(from_=1.0, to=10.0, variable=self.playback_speed_var)
```

**Implementación Actual en `walkforward_tab.py`**:
- Solo tiene botón "RUN VALIDATION"
- No tiene controles de reproducción
- No tiene visualización interactiva de folds

**Impacto**:
- **UX**: ALTO - Mejora significativa en experiencia de usuario
- **Funcionalidad**: ALTO - Feature principal del tab legacy
- **Análisis**: MEDIO - Facilita análisis de resultados

**Recomendación**: **MIGRAR** controles de reproducción

**Prioridad**: ALTA

#### ❌ Fibonacci Levels Overlay (OPCIONAL)

**Estado**: NO IMPLEMENTADO

**Descripción**:
- Overlay de niveles de Fibonacci en visualización
- Identificación de top candidates
- Colores específicos para niveles

**Código en `walk_forward_visualization_tab.py`**:
```python
from fibonacci_utils import (
    calculate_fibonacci_levels,
    identify_top_candidates,
    get_fibonacci_colors
)

if self.show_fibonacci_var.get():
    fib_levels = calculate_fibonacci_levels(df, train_indices, val_indices)
    # Plot Fibonacci levels
```

**Implementación Actual en `walkforward_tab.py`**:
- No tiene overlay de Fibonacci
- Visualización básica de train/val windows

**Impacto**:
- **Análisis**: MEDIO - Útil para análisis técnico
- **Funcionalidad**: BAJO - Feature avanzada opcional

**Recomendación**: **AGREGAR** como feature opcional

**Prioridad**: BAJA

#### ❌ Side Panels with Fold Summary (OPCIONAL)

**Estado**: NO IMPLEMENTADO

**Descripción**:
- Panel lateral con tabla de resumen de folds
- Métricas por fold (train loss, val loss, MSE)
- Navegación rápida desde tabla

**Implementación Actual en `walkforward_tab.py`**:
- Tiene tabla de resultados pero básica
- No tiene panel lateral dedicado
- No tiene navegación desde tabla

**Impacto**:
- **UX**: MEDIO - Mejora navegación
- **Funcionalidad**: BAJO - Feature de conveniencia

**Recomendación**: **MEJORAR** tabla existente

**Prioridad**: BAJA

---

## 3. Utilidades de `dashboard_utils.py` No Integradas

### 3.1 `fetch_latest_data()`

**Estado**: NO INTEGRADA

**Descripción**: Función para obtener datos recientes de SQLite con feature engineering opcional

**Ubicación Actual**: `dashboard_utils.py` (root)

**Uso Potencial**:
- `multihorizon_dashboard.py` - Para obtener datos independientes del daemon
- `oracle_window.py` - Para cargar datos históricos
- Otros componentes que necesiten datos recientes

**Recomendación**: **MIGRAR** a `data_pipeline/data_loader.py` o `ui/utils/data_utils.py`

**Prioridad**: ALTA

### 3.2 `PredictionCache`

**Estado**: NO INTEGRADA

**Descripción**: Caché inteligente para predicciones

**Ubicación Actual**: `dashboard_utils.py` (root)

**Uso Potencial**:
- `multihorizon_dashboard.py` - Para evitar trabajo redundante
- Cualquier componente que genere predicciones repetidamente

**Recomendación**: **MIGRAR** a `ui/utils/prediction_cache.py`

**Prioridad**: ALTA

### 3.3 `AsyncPredictionRunner`

**Estado**: NO INTEGRADA

**Descripción**: Runner asíncrono para predicciones no bloqueantes

**Ubicación Actual**: `dashboard_utils.py` (root)

**Uso Potencial**:
- `multihorizon_dashboard.py` - Para predicciones manuales
- `oracle_window.py` - Para predicciones en background
- Cualquier worker que genere predicciones

**Recomendación**: **MIGRAR** a `ui/workers/prediction_worker.py` o `ui/utils/async_prediction.py`

**Prioridad**: ALTA

### 3.4 `check_data_freshness()`

**Estado**: NO INTEGRADA

**Descripción**: Verifica si los datos en la base de datos están actualizados

**Uso Potencial**:
- Validación antes de generar predicciones
- Alertas de datos obsoletos

**Recomendación**: **MIGRAR** a `data_pipeline/data_loader.py`

**Prioridad**: MEDIA

### 3.5 `validate_data_for_prediction()`

**Estado**: NO INTEGRADA

**Descripción**: Valida que DataFrame tenga todas las features requeridas

**Uso Potencial**:
- Validación antes de inferencia
- Detección temprana de problemas de features

**Recomendación**: **MIGRAR** a `data_pipeline/data_loader.py` o `ui/utils/validation.py`

**Prioridad**: MEDIA

---

## 4. Features Avanzadas de `prediction_dashboard_tab.py` No Disponibles

### 4.1 Exportación de Predicciones

**Estado**: NO IMPLEMENTADO en `multihorizon_dashboard.py`

**Descripción**:
- Exportar predicciones a CSV/JSON
- Incluye timestamps, horizontes, precios, intervalos de confianza

**Código en `prediction_dashboard_tab.py`**:
```python
def _export_predictions(self):
    if not self.current_predictions:
        messagebox.showwarning("No Data", "No predictions to export")
        return
    
    filename = filedialog.asksaveasfilename(
        defaultextension=".json",
        filetypes=[("JSON", "*.json"), ("CSV", "*.csv")]
    )
    # Export logic
```

**Recomendación**: **AGREGAR** funcionalidad de exportación

**Prioridad**: MEDIA

### 4.2 Métricas Comprehensivas

**Estado**: PARCIALMENTE IMPLEMENTADO

**Descripción**:
- Tabla de métricas por horizonte
- Incluye: precio predicho, cambio $, cambio %, CI, señal

**Implementación Actual**:
- `multihorizon_dashboard.py` tiene tabla básica
- `prediction_dashboard_tab.py` tiene tabla más completa con más columnas

**Recomendación**: **MEJORAR** tabla de métricas

**Prioridad**: BAJA

---

## 5. Priorización de Integraciones

### Críticas (Alta Prioridad)

1. **AsyncPredictionRunner** - Evita bloqueo de UI
2. **PredictionCache** - Mejora performance significativamente
3. **Config Persistence** - Feature básica esperada
4. **fetch_latest_data** - Utilidad reutilizable importante

### Importantes (Media Prioridad)

5. **Multiple Scenarios** - Feature avanzada útil
6. **Probability Density Layers** - Mejora visualización
7. **Auto-refresh Configurable** - Mejora UX
8. **Interactive Playback Controls** - Feature principal de WF visualization

### Opcionales (Baja Prioridad)

9. **Horizon Selection Checkboxes** - Conveniencia
10. **Color Scheme Selection** - Personalización
11. **Fibonacci Overlay** - Feature avanzada opcional

---

## 6. Plan de Integración Sugerido

### Fase 1: Utilidades Críticas (2-3 días)

1. Migrar `PredictionCache` a `ui/utils/prediction_cache.py`
2. Migrar `AsyncPredictionRunner` a `ui/workers/prediction_worker.py`
3. Migrar `fetch_latest_data` a `data_pipeline/data_loader.py`
4. Integrar en `multihorizon_dashboard.py`

### Fase 2: Features Avanzadas (2-3 días)

5. Agregar soporte para múltiples escenarios
6. Integrar probability density layers
7. Agregar persistencia de configuración
8. Hacer auto-refresh configurable

### Fase 3: Mejoras de UX (1-2 días)

9. Agregar controles de reproducción a `walkforward_tab.py`
10. Agregar exportación de predicciones
11. Mejorar tabla de métricas

### Fase 4: Features Opcionales (1 día)

12. Agregar horizon selection checkboxes
13. Agregar color scheme selector
14. Agregar Fibonacci overlay (opcional)

---

**Última actualización**: 2025-12-13  
**Próxima revisión**: Después de integraciones

