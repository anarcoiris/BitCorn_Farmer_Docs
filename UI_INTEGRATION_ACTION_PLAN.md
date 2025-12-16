# Plan de Acción de Integración UI - BitCorn Farmer

**Fecha**: 2025-12-13  
**Versión**: 1.0  
**Estado**: Plan Completo

---

## Resumen Ejecutivo

Este plan prioriza y detalla todas las tareas necesarias para:
1. Migrar integraciones precarias
2. Integrar funcionalidades faltantes
3. Mejorar conectividad UI
4. Eliminar dependencias de código legacy

**Total de Tareas**: 18  
**Tiempo Estimado Total**: 12-16 días  
**Prioridad Alta**: 8 tareas (6-8 días)  
**Prioridad Media**: 6 tareas (4-5 días)  
**Prioridad Baja**: 4 tareas (2-3 días)

---

## Fase 1: Migraciones Críticas de Estructura (3-4 días)

### Tarea 1.1: Migrar `training_options_panel.py` a `ui/components/`

**Prioridad**: ALTA  
**Esfuerzo**: 2-3 horas  
**Dependencias**: Ninguna

**Acciones**:
1. Mover `training_options_panel.py` → `ui/components/training_options_panel.py`
2. Actualizar imports en `ui/ui_main_window.py` (4 lugares):
   - Línea 1361: `from ui.components.training_options_panel import TrainingOptionsPanel`
   - Línea 1399: `from ui.components.training_options_panel import _load_training_config, _get_default_training_config`
   - Línea 1417: `from ui.components.training_options_panel import _get_default_training_config`
   - Línea 1435: `from ui.components.training_options_panel import _save_training_config`
3. Agregar try/except con fallback si módulo no existe
4. Verificar que funcionalidad se mantiene

**Criterios de Éxito**:
- ✅ Panel se abre correctamente desde UI
- ✅ Configuración se carga/guarda correctamente
- ✅ No hay errores de import

---

### Tarea 1.2: Migrar `dashboard_visualizations.py` a `ui/utils/`

**Prioridad**: ALTA  
**Esfuerzo**: 1 hora  
**Dependencias**: Ninguna

**Acciones**:
1. Mover `dashboard_visualizations.py` → `ui/utils/dashboard_visualizations.py`
2. Actualizar import en `ui/components/multihorizon_dashboard.py`:
   - Línea 46: `from ui.utils.dashboard_visualizations import plot_prediction_fan_live_simple`
3. Verificar que visualización funciona

**Criterios de Éxito**:
- ✅ Dashboard muestra gráficos correctamente
- ✅ No hay errores de import

---

### Tarea 1.3: Renombrar y Migrar `feature_registry.py` (root)

**Prioridad**: MEDIA  
**Esfuerzo**: 1-2 horas  
**Dependencias**: Ninguna

**Acciones**:
1. Renombrar `feature_registry.py` (root) → `feature_builder_registry.py`
2. Mover a `ui/utils/feature_builder_registry.py`
3. Actualizar import en `ui/tabs/feature_composer_tab.py`:
   - Línea 32: `from ui.utils.feature_builder_registry import (...)`
4. Actualizar documentación si es necesario

**Criterios de Éxito**:
- ✅ Feature Composer Tab funciona correctamente
- ✅ No hay confusión con `core/feature_registry.py`

---

### Tarea 1.4: Migrar Utilidades de `dashboard_utils.py`

**Prioridad**: ALTA  
**Esfuerzo**: 2-3 horas  
**Dependencias**: Ninguna

**Acciones**:
1. Analizar funciones en `dashboard_utils.py`:
   - `fetch_latest_data()` → Mover a `data_pipeline/data_loader.py` o crear `ui/utils/data_utils.py`
   - `PredictionCache` → Mover a `ui/utils/prediction_cache.py`
   - `AsyncPredictionRunner` → Mover a `ui/workers/prediction_worker.py` o `ui/utils/async_prediction.py`
   - `check_data_freshness()` → Mover a `data_pipeline/data_loader.py`
   - `validate_data_for_prediction()` → Mover a `data_pipeline/data_loader.py` o `ui/utils/validation.py`
2. Migrar cada función a su ubicación apropiada
3. Actualizar imports en archivos que las usan
4. Verificar que funcionalidad se mantiene

**Criterios de Éxito**:
- ✅ Todas las funciones migradas
- ✅ Imports actualizados
- ✅ Funcionalidad verificada

---

## Fase 2: Integración de Features Faltantes (4-5 días)

### Tarea 2.1: Integrar `PredictionCache` en `multihorizon_dashboard.py`

**Prioridad**: ALTA  
**Esfuerzo**: 2-3 horas  
**Dependencias**: Tarea 1.4 (migración de `PredictionCache`)

**Acciones**:
1. Importar `PredictionCache` desde nueva ubicación
2. Inicializar cache en `__init__`:
   ```python
   self.cache = PredictionCache(max_age_seconds=300, max_size=10)
   ```
3. Crear función `_get_cache_key()` basada en (horizons, data_length, last_price)
4. En `_poll_predictions()` o `_manual_refresh()`:
   - Verificar cache antes de generar predicciones
   - Guardar en cache después de generar
5. Agregar botón "Clear Cache" opcional

**Criterios de Éxito**:
- ✅ Cache evita trabajo redundante
- ✅ Performance mejora en actualizaciones frecuentes
- ✅ Cache se limpia apropiadamente

---

### Tarea 2.2: Integrar `AsyncPredictionRunner` en `multihorizon_dashboard.py`

**Prioridad**: ALTA  
**Esfuerzo**: 3-4 horas  
**Dependencias**: Tarea 1.4 (migración de `AsyncPredictionRunner`)

**Acciones**:
1. Importar `AsyncPredictionRunner` desde nueva ubicación
2. Inicializar runner en `__init__`:
   ```python
   self.async_runner = AsyncPredictionRunner(callback=self._on_prediction_complete)
   ```
3. Implementar `_on_prediction_complete(success, data)`:
   - Si success: actualizar display con predicciones
   - Si error: mostrar mensaje de error
4. Modificar `_manual_refresh()` para usar runner:
   ```python
   self.async_runner.run_prediction(df, model, meta, scaler, device, horizons)
   ```
5. Agregar indicador de "Generando predicciones..." mientras corre

**Criterios de Éxito**:
- ✅ UI no se bloquea durante generación de predicciones
- ✅ Callback actualiza UI correctamente
- ✅ Manejo de errores robusto

---

### Tarea 2.3: Agregar Persistencia de Configuración a `multihorizon_dashboard.py`

**Prioridad**: ALTA  
**Esfuerzo**: 2-3 horas  
**Dependencias**: Ninguna

**Acciones**:
1. Agregar método `_load_config()`:
   - Usar `ConfigManager` o `MasterConfigManager` si disponible
   - Fallback a JSON directo
   - Cargar: auto_refresh, update_interval, color_scheme, etc.
2. Agregar método `_save_config()`:
   - Guardar configuración actual
   - Integrar con config manager centralizado
3. Cargar config en `__init__` después de `_build_ui()`
4. Agregar botón "Save Config" en UI
5. Auto-guardar al cerrar ventana (opcional)

**Criterios de Éxito**:
- ✅ Configuración se persiste entre sesiones
- ✅ Se carga correctamente al abrir dashboard
- ✅ Integración con config manager funciona

---

### Tarea 2.4: Agregar Soporte para Múltiples Escenarios

**Prioridad**: MEDIA  
**Esfuerzo**: 4-5 horas  
**Dependencias**: Tarea 1.4 (migración de `dashboard_visualizations.py`)

**Acciones**:
1. Agregar checkbox "Enable Multiple Scenarios" en UI
2. Agregar función `_apply_volatility_multiplier(predictions, multiplier)`:
   - Multiplicar intervalos de confianza por multiplier
   - Ajustar predicciones según escenario
3. Modificar `_update_display()` para mostrar múltiples fans:
   - Base: predictions originales
   - Bull: apply_volatility_multiplier(predictions, 0.7)
   - Bear: apply_volatility_multiplier(predictions, 1.5)
4. Usar `plot_prediction_fan_live()` de `dashboard_visualizations.py` que soporta múltiples escenarios
5. Agregar leyenda para distinguir escenarios

**Criterios de Éxito**:
- ✅ Múltiples fans se muestran correctamente
- ✅ Escenarios se distinguen visualmente
- ✅ Toggle funciona correctamente

---

### Tarea 2.5: Integrar Probability Density Layers

**Prioridad**: MEDIA  
**Esfuerzo**: 3-4 horas  
**Dependencias**: Tarea 1.4 (migración de `dashboard_visualizations.py`)

**Acciones**:
1. Importar `plot_probability_density_layers` desde `ui/utils/dashboard_visualizations.py`
2. Agregar checkbox "Show Probability Layers" en UI
3. En `_update_display()`, después de plot principal:
   ```python
   if self.show_probability_var.get():
       plot_probability_density_layers(self.ax, predictions, key_horizons=[5, 10, 20])
   ```
4. Verificar que KDE funciona correctamente
5. Ajustar transparencia y colores para legibilidad

**Criterios de Éxito**:
- ✅ Probability layers se muestran correctamente
- ✅ No afecta performance significativamente
- ✅ Visualización es clara y útil

---

### Tarea 2.6: Hacer Auto-refresh Configurable

**Prioridad**: MEDIA  
**Esfuerzo**: 1-2 horas  
**Dependencias**: Tarea 2.3 (persistencia de configuración)

**Acciones**:
1. Agregar checkbox "Auto-Refresh" en UI
2. Agregar slider "Interval (minutes): 1-60" en UI
3. Modificar `_poll_predictions()` para respetar intervalo configurado
4. Agregar método `_toggle_auto_refresh()`:
   - Si se habilita: iniciar polling
   - Si se deshabilita: detener polling
5. Integrar con persistencia de configuración

**Criterios de Éxito**:
- ✅ Auto-refresh se puede habilitar/deshabilitar
- ✅ Intervalo es configurable
- ✅ Configuración se persiste

---

## Fase 3: Migración de Wrappers (2-3 días)

### Tarea 3.1: Migrar Funcionalidad Completa de `walk_forward_visualization_tab.py`

**Prioridad**: ALTA  
**Esfuerzo**: 6-8 horas  
**Dependencias**: Ninguna

**Acciones**:
1. Analizar `walk_forward_visualization_tab.py` (1058 líneas):
   - Identificar funcionalidad core vs UI
   - Separar lógica de visualización de lógica de negocio
2. Migrar funcionalidad a `ui/tabs/walkforward_tab.py`:
   - Controles de reproducción (Play/Pause, Step Prev/Next, etc.)
   - Control de velocidad
   - Navegación por slider
   - Visualización interactiva de folds
3. Adaptar interfaz:
   - Cambiar de `(parent, root, log_callback)` a `(parent, app)`
   - Usar `app.log()` en lugar de `log_callback`
   - Usar `app.df_loaded` en lugar de cargar datos internamente
4. Eliminar wrapper `ui/tabs/wf_visualization_tab.py`
5. Actualizar referencias en `ui_main_window.py` si es necesario

**Criterios de Éxito**:
- ✅ `walkforward_tab.py` tiene toda la funcionalidad del legacy
- ✅ Wrapper eliminado
- ✅ No hay dependencias de código legacy
- ✅ Funcionalidad verificada

---

## Fase 4: Mejoras de Conectividad (2-3 días)

### Tarea 4.1: Centralizar Polling de Predictions Queue

**Prioridad**: ALTA  
**Esfuerzo**: 3-4 horas  
**Dependencias**: Ninguna

**Acciones**:
1. Modificar `ui_main_window.py::_poll_predictions_queue()`:
   - Mantener polling centralizado
   - Agregar lista de suscriptores: `self.prediction_subscribers = []`
2. Agregar método `subscribe_to_predictions(callback)`:
   ```python
   def subscribe_to_predictions(self, callback):
       self.prediction_subscribers.append(callback)
   ```
3. En `_poll_predictions_queue()`, después de obtener predicciones:
   ```python
   for callback in self.prediction_subscribers:
       callback(predictions)
   ```
4. Modificar `multihorizon_dashboard.py`:
   - Eliminar `_poll_predictions()` propio
   - Suscribirse en `__init__`: `app.subscribe_to_predictions(self._on_predictions_update)`
   - Implementar `_on_predictions_update(predictions)`
5. Modificar `status_tab.py` si también hace polling directo

**Criterios de Éxito**:
- ✅ Solo un polling activo
- ✅ Múltiples componentes reciben notificaciones
- ✅ No hay pérdida de mensajes
- ✅ Performance mejorada

---

### Tarea 4.2: Completar Integración RL → Trading Daemon

**Prioridad**: MEDIA  
**Esfuerzo**: 2-3 horas  
**Dependencias**: Verificar que `trading_daemon.py` soporta RL

**Acciones**:
1. Verificar métodos disponibles en `trading_daemon.py`:
   - `load_agent(path)` - ¿Existe?
   - `set_rl_agent(agent)` - ¿Existe?
   - `use_rl_agent` - ¿Existe?
2. Implementar `_integrate_rl_agent_with_daemon()` en `ui_main_window.py`:
   ```python
   def _integrate_rl_agent_with_daemon(self):
       if not self.daemon:
           return
       if self.rl_agent_path.get():
           self.daemon.load_agent(self.rl_agent_path.get())
           self.daemon.use_rl_agent = True
   ```
3. Llamar desde `RLStrategyTab` después de entrenar/cargar agente
4. Agregar controles en `RLStrategyTab` para activar/desactivar RL trading
5. Verificar que daemon usa agente correctamente

**Criterios de Éxito**:
- ✅ Agente RL se carga en daemon
- ✅ Daemon usa agente para decisiones de trading
- ✅ Controles en UI funcionan correctamente

---

### Tarea 4.3: Implementar Sistema de Notificaciones/Eventos

**Prioridad**: MEDIA  
**Esfuerzo**: 4-5 horas  
**Dependencias**: Ninguna

**Acciones**:
1. Crear `ui/utils/event_bus.py`:
   ```python
   class EventBus:
       def __init__(self):
           self.subscribers = {}
       
       def subscribe(self, event_type, callback):
           if event_type not in self.subscribers:
               self.subscribers[event_type] = []
           self.subscribers[event_type].append(callback)
       
       def emit(self, event_type, data):
           for callback in self.subscribers.get(event_type, []):
               callback(data)
   ```
2. Agregar `self.event_bus = EventBus()` en `ui_main_window.py`
3. Emitir eventos cuando estado cambia:
   - `model_loaded` - Cuando se carga modelo
   - `data_loaded` - Cuando se cargan datos
   - `training_complete` - Cuando termina entrenamiento
4. Tabs se suscriben a eventos relevantes:
   - `BacktestTab` se suscribe a `model_loaded`
   - `AuditTab` se suscribe a `model_loaded`, `data_loaded`
   - etc.
5. Tabs actualizan UI cuando reciben eventos

**Criterios de Éxito**:
- ✅ Tabs se actualizan automáticamente cuando estado cambia
- ✅ No hay polling innecesario
- ✅ Sistema es extensible

---

## Fase 5: Features Opcionales (1-2 días)

### Tarea 5.1: Agregar Horizon Selection Checkboxes

**Prioridad**: BAJA  
**Esfuerzo**: 1-2 horas  
**Dependencias**: Tarea 2.3 (persistencia de configuración)

**Acciones**:
1. Agregar frame "Horizon Selection" en UI
2. Crear checkboxes para cada horizonte disponible
3. Filtrar predicciones mostradas según selección
4. Agregar botones "Select All" / "Clear All"
5. Persistir selección en configuración

**Criterios de Éxito**:
- ✅ Usuario puede seleccionar/deseleccionar horizontes
- ✅ Solo se muestran horizontes seleccionados
- ✅ Selección se persiste

---

### Tarea 5.2: Agregar Color Scheme Selection

**Prioridad**: BAJA  
**Esfuerzo**: 1 hora  
**Dependencias**: Tarea 2.3 (persistencia de configuración)

**Acciones**:
1. Agregar dropdown "Color Scheme" en UI
2. Opciones: viridis, plasma, coolwarm, RdYlGn, Blues
3. Aplicar colormap a visualización
4. Persistir preferencia

**Criterios de Éxito**:
- ✅ Usuario puede cambiar esquema de colores
- ✅ Cambio se aplica inmediatamente
- ✅ Preferencia se persiste

---

### Tarea 5.3: Agregar Exportación de Predicciones

**Prioridad**: MEDIA  
**Esfuerzo**: 2 horas  
**Dependencias**: Ninguna

**Acciones**:
1. Agregar botón "Export Predictions..." en UI
2. Implementar `_export_predictions()`:
   - Dialog para elegir formato (JSON/CSV)
   - Serializar predicciones actuales
   - Incluir timestamps, horizontes, precios, CIs
3. Guardar archivo

**Criterios de Éxito**:
- ✅ Usuario puede exportar predicciones
- ✅ Formato JSON y CSV funcionan
- ✅ Datos exportados son completos

---

### Tarea 5.4: Agregar Fibonacci Overlay a Walk-Forward Tab

**Prioridad**: BAJA  
**Esfuerzo**: 2-3 horas  
**Dependencias**: Tarea 3.1 (migración completa)

**Acciones**:
1. Verificar que `fibonacci_utils.py` tiene funciones necesarias
2. Agregar checkbox "Show Fibonacci Levels" en UI
3. Implementar `_plot_fibonacci_levels()`:
   - Calcular niveles de Fibonacci
   - Plotear en visualización
4. Integrar con controles de reproducción

**Criterios de Éxito**:
- ✅ Fibonacci levels se muestran correctamente
- ✅ Toggle funciona
- ✅ Visualización es clara

---

## Resumen de Priorización

### Sprint 1 (Semana 1): Estructura y Utilidades Críticas

**Días 1-2**: Migraciones de estructura
- Tarea 1.1: Migrar `training_options_panel.py`
- Tarea 1.2: Migrar `dashboard_visualizations.py`
- Tarea 1.4: Migrar utilidades de `dashboard_utils.py`

**Días 3-4**: Integración de utilidades críticas
- Tarea 2.1: Integrar `PredictionCache`
- Tarea 2.2: Integrar `AsyncPredictionRunner`
- Tarea 2.3: Agregar persistencia de configuración

**Día 5**: Conectividad
- Tarea 4.1: Centralizar polling

### Sprint 2 (Semana 2): Features Avanzadas y Migración

**Días 1-2**: Features avanzadas
- Tarea 2.4: Múltiples escenarios
- Tarea 2.5: Probability density layers
- Tarea 2.6: Auto-refresh configurable

**Días 3-4**: Migración de wrapper
- Tarea 3.1: Migrar `walk_forward_visualization_tab.py`

**Día 5**: Mejoras de conectividad
- Tarea 4.2: Integración RL → Daemon
- Tarea 4.3: Sistema de eventos (inicio)

### Sprint 3 (Semana 3): Finalización

**Días 1-2**: Sistema de eventos y features opcionales
- Tarea 4.3: Sistema de eventos (completar)
- Tarea 5.3: Exportación de predicciones

**Días 3-4**: Features opcionales
- Tarea 5.1: Horizon selection
- Tarea 5.2: Color scheme
- Tarea 5.4: Fibonacci overlay

**Día 5**: Testing y documentación

---

## Métricas de Éxito

### Estructura
- ✅ 0 módulos en root que deberían estar en `ui/`
- ✅ 0 imports directos precarios desde root
- ✅ 0 wrappers de código legacy

### Funcionalidad
- ✅ Todas las features críticas de `prediction_dashboard_tab.py` integradas
- ✅ Todas las utilidades reutilizables migradas
- ✅ Funcionalidad de `walk_forward_visualization_tab.py` migrada completamente

### Conectividad
- ✅ 0 polling duplicado
- ✅ Sistema de notificaciones implementado
- ✅ Todas las integraciones completas (RL → Daemon)

---

## Riesgos y Mitigaciones

### Riesgo 1: Breaking Changes en Migraciones

**Mitigación**:
- Hacer migraciones una a la vez
- Verificar funcionalidad después de cada migración
- Mantener backups de archivos originales

### Riesgo 2: Performance Degradation

**Mitigación**:
- Profiling antes y después de cambios
- Optimizar código crítico
- Usar caching apropiadamente

### Riesgo 3: Regresiones en Funcionalidad Existente

**Mitigación**:
- Testing exhaustivo después de cada cambio
- Verificar todos los tabs y componentes afectados
- Testing de integración end-to-end

---

**Última actualización**: 2025-12-13  
**Próxima revisión**: Después de completar Sprint 1

