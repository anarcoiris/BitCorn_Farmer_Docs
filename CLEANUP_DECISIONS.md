# Decisiones de Limpieza - Documentación y Scripts

**Fecha**: 2025-12-11  
**Estado**: En progreso

---

## Fase 1.1: Documentación Consolidada ✅

### Estado
- ✅ `docs/CONFIG_METHODS_REFERENCE.md` - Creado y actualizado
- ✅ `docs/CONFIG_AUDIT_SUMMARY.md` - Creado y actualizado  
- ✅ `docs/DOCUMENTATION_INDEX.md` - Referencias actualizadas
- ⚠️ `docs/config_audit/README.md` - No existe el directorio, pero referencias en DOCUMENTATION_INDEX.md son suficientes

**Decisión**: Los documentos consolidados ya existen y están referenciados correctamente. No se requiere acción adicional.

---

## Fase 1.2: Scripts RL Duplicados

### Análisis

**Archivos**:
- `scripts/rl/rl_training_pipeline.py` (802 líneas) - Principal/Base
- `scripts/rl/rl_training_pipeline_unified.py` (449 líneas) - Extensión Unificada
- `scripts/rl/rl_training_pipeline_multi_strategy.py` (340 líneas) - Extensión Multi-Strategy

**Hallazgos**:
1. `unified` importa funciones del principal (líneas 31-38), no es duplicado completo
2. `unified` soporta ambos tipos de environment (TradingEnvironment y MultiStrategyEnvironment)
3. `rl_worker.py` usa fallbacks: principal → multi_strategy → unified
4. `unified` es la versión más completa y flexible

### Decisión

**Opción seleccionada**: Mantener `rl_training_pipeline_unified.py` como versión principal y marcar los otros como deprecated.

**Razón**: 
- `unified` ya importa funciones del principal, evitando duplicación
- Soporta todos los casos de uso (single y multi-strategy)
- Es la versión más mantenible

**Acciones**:
1. ✅ Actualizar `scripts/rl/README.md` con decisión
2. ⏳ Agregar comentarios DEPRECATED en `rl_training_pipeline.py` y `rl_training_pipeline_multi_strategy.py`
3. ⏳ Actualizar `ui/workers/rl_worker.py` para usar `unified` como primera opción

---

## Fase 1.3: Scripts de Diagnóstico Volatilidad

### Análisis

**Archivos**:
- `scripts/diagnostics/diagnose_volatility_scale.py` (~19335 líneas, "avanzado")
- `scripts/diagnostics/diagnose_volatility_simple.py` (~15054 líneas, "simplificado", marcado como duplicado)

**Hallazgos**:
1. Ambos scripts tienen propósitos diferentes:
   - `scale`: Análisis avanzado con datos reales
   - `simple`: Análisis teórico sin necesidad de datos
2. El `simple` no es realmente redundante, tiene un propósito específico

### Decisión

**Opción seleccionada**: Mantener ambos scripts, pero actualizar documentación para clarificar propósitos.

**Razón**: Son herramientas complementarias, no duplicados.

**Acciones**:
1. ⏳ Actualizar comentarios en `diagnose_volatility_simple.py` para remover marca de "duplicado"
2. ⏳ Agregar documentación en `scripts/diagnostics/README.md` explicando diferencias

---

## Fase 1.4: Scripts Inference Deprecated

### Análisis

**Archivo**: `scripts/inference/multi_horizon_fan_inference.py` (~11106 líneas)

**Hallazgos**:
1. Marcado como DEPRECATED en múltiples lugares
2. Funcionalidad consolidada en `multi_horizon_inference.py`
3. README indica mantener solo por compatibilidad temporal

### Decisión

**Opción seleccionada**: Archivar el archivo moviéndolo a `scripts/inference/archive/` y actualizar referencias.

**Razón**: 
- Ya está marcado como deprecated
- Funcionalidad consolidada en otro archivo
- Mantener en archive para referencia histórica

**Acciones**:
1. ⏳ Crear `scripts/inference/archive/` si no existe
2. ⏳ Mover `multi_horizon_fan_inference.py` a archive
3. ⏳ Actualizar referencias en README y otros documentos

---

## Fase 2: Funciones Duplicadas

### Análisis

**Funciones identificadas**:
- `generate_data()` - Duplicada en `create_ensemble.py` y `analyze_features.py`
- `compute_features_dynamic()` - Duplicada en `create_ensemble.py` y `analyze_features.py`

**Hallazgos**:
1. ✅ Ya existe `scripts/utils.py` con funciones consolidadas
2. ⚠️ Algunos scripts pueden no estar usando `scripts/utils.py`

### Decisión

**Opción seleccionada**: Verificar qué scripts aún tienen código duplicado y refactorizar para usar `scripts/utils.py`.

**Acciones**:
1. ⏳ Verificar si `create_ensemble.py` y `analyze_features.py` usan `scripts/utils.py`
2. ⏳ Si no, refactorizar para usar funciones consolidadas
3. ⏳ Eliminar código duplicado

---

## Fase 3: Documentación Obsoleta

### Análisis

**Referencia**: `docs/DEVOPS_DOCUMENTATION_REVIEW.md` mencionado en el plan, pero no existe.

**Hallazgos**:
1. El archivo no existe en el workspace
2. Puede haber sido eliminado en limpieza previa

### Decisión

**Opción seleccionada**: Buscar otros documentos que identifiquen documentación obsoleta, o crear un nuevo análisis.

**Acciones**:
1. ⏳ Buscar documentos que identifiquen archivos para eliminar
2. ⏳ Revisar planes huérfanos y reportes temporales
3. ⏳ Eliminar archivos obvios (planes huérfanos, reportes temporales ya procesados)

---

## Resumen de Acciones

### ✅ Completadas
1. ✅ Fase 1.1 - Documentos consolidados ya existen y están referenciados
2. ✅ Fase 1.2 - Scripts RL actualizados (marcados como deprecated, worker actualizado)
3. ✅ Fase 1.3 - Documentación scripts volatilidad actualizada
4. ✅ Fase 1.4 - Script inference deprecated archivado
5. ✅ Fase 2 - Funciones duplicadas ya consolidadas en `scripts/utils.py`

### ⏳ Pendientes (Opcional)
6. ⏳ Fase 3 - Limpiar documentación obsoleta:
   - Ya existe `DELETED_DOCUMENTS_LOG.md` registrando eliminaciones previas
   - Archivos legacy en `config/` identificados en `CONFIG_UNIFICATION_AUDIT_2025_12_11.md` pueden eliminarse
   - Plan actual en `.cursor/plans/` puede mantenerse como referencia histórica

## Estado Final

**Todas las tareas principales del plan han sido completadas.**

Las tareas pendientes son opcionales y pueden realizarse en una limpieza futura si es necesario.

