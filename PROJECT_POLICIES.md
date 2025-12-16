# Políticas del Proyecto - BitCorn Farmer

**Fecha**: 2025-01-08  
**Versión**: 1.0  
**Estado**: Activo

---

## 1. Política de Shell y Comandos

### 1.1 Shell Principal: PowerShell

**Política**: Este proyecto utiliza **PowerShell** como shell principal para todos los comandos y scripts.

**Razón**: 
- El proyecto se desarrolla en **Windows 10**
- PowerShell es el shell nativo de Windows
- Mejor integración con el ecosistema Windows
- Compatibilidad con herramientas de desarrollo en Windows

**Aplicación**:
- Todos los scripts de automatización deben usar PowerShell (`.ps1`)
- Todos los comandos en documentación deben ser compatibles con PowerShell
- Los comandos en scripts de build/deploy deben usar sintaxis PowerShell
- Los ejemplos en documentación deben mostrar comandos PowerShell

**Excepciones**:
- Scripts Python pueden usar `subprocess` con PowerShell
- Scripts de CI/CD pueden usar sintaxis multiplataforma cuando sea necesario

---

## 2. Política de Estructura de Directorios

### 2.1 Organización de Documentación

**Estructura Estándar**:
```
docs/
├── guides/              # Guías de usuario y desarrollo
├── summaries/           # Resúmenes de implementación
├── archive/             # Documentación histórica
│   ├── sessions/       # Resúmenes de sesiones
│   └── technical/      # Documentación técnica archivada
└── technical/          # Documentación técnica activa
```

**Reglas**:
- Documentos principales en raíz: Solo `README.md`, `PROJECT_MASTER_PLAN.md`, `TODO.md`, `TESTING_TODO.md`, `PROJECT_STATUS.md`, `QUICK_START.md`
- Guías de usuario: `docs/guides/`
- Resúmenes de implementación: `docs/summaries/`
- Documentación histórica: `docs/archive/`

### 2.2 Organización de Tests

**Estructura Estándar**:
```
tests/
├── unit/               # Tests unitarios
├── integration/        # Tests de integración
└── README_TESTS.md    # Documentación de tests
```

**Reglas**:
- Todos los tests deben estar en `tests/`
- Tests unitarios: `tests/unit/`
- Tests de integración: `tests/integration/`
- No dejar tests en la raíz del proyecto

---

## 3. Política de Nomenclatura

### 3.1 Archivos de Documentación

**Convenciones**:
- Nombres en UPPER_SNAKE_CASE para documentos principales: `PROJECT_MASTER_PLAN.md`
- Nombres en lowercase con guiones para guías: `getting-started.md`
- Nombres descriptivos y claros

### 3.2 Archivos de Tests

**Convenciones**:
- Prefijo `test_` para todos los tests: `test_feature_generation_v2.py`
- Nombres descriptivos que indiquen qué se está probando
- Agrupar por categoría en subdirectorios

---

## 4. Política de Versionado

### 4.1 Documentos Principales

**Regla**: Los documentos principales deben incluir:
- Fecha de última actualización
- Versión del documento
- Estado (Activo, Archivado, Deprecado)

### 4.2 Historial de Cambios

**Regla**: Documentos importantes deben mantener un historial de cambios o referencia a cambios significativos.

---

## 5. Política de Archivo

### 5.1 Cuándo Archivar

**Criterios**:
- Resúmenes de sesión después de 1 semana
- Resúmenes de implementación después de completar la siguiente fase
- Documentación técnica superada por versiones actualizadas
- Fix summaries después de que el fix está en producción

### 5.2 Dónde Archivar

**Estructura**:
- Sesiones: `docs/archive/sessions/`
- Técnico: `docs/archive/technical/`
- Planificación: `docs/archive/planning/`

---

## 6. Política de Referencias

### 6.1 Enlaces entre Documentos

**Regla**: Usar rutas relativas desde la raíz del proyecto:
- `docs/guides/getting-started.md`
- `notes/PLAN_GLOBAL.md`
- `PROJECT_MASTER_PLAN.md`

### 6.2 Actualización de Referencias

**Regla**: Cuando se mueve un documento, actualizar todas las referencias en otros documentos.

---

## 7. Política de Testing

### 7.1 Ubicación de Tests

**Regla**: Todos los tests deben estar en `tests/` o subdirectorios.

### 7.2 Organización de Tests

**Regla**: Organizar tests por tipo:
- Unit tests: `tests/unit/`
- Integration tests: `tests/integration/`
- End-to-end tests: `tests/integration/` (marcados con prefijo `test_e2e_`)

---

## 8. Publicación y Acceso a la Documentación

### 8.1 Servir Documentación vía HTTP Local

**Regla**: La forma recomendada de visualizar la documentación es sirviéndola como HTTP local usando PowerShell.

**Script estándar**:
- Ubicación: `tools/serve_docs.ps1`
- Uso básico (desde PowerShell):
  - `.\tools\serve_docs.ps1` → sirve `docs/` en `http://localhost:8000/`
  - `.\tools\serve_docs.ps1 -Port 9000` → cambia el puerto
  - `.\tools\serve_docs.ps1 -Root notes` → sirve la carpeta `notes/`

**Requisitos**:
- Python disponible en el `PATH` (usa `python -m http.server`)

### 8.2 Exposición Externa con ngrok (Opcional)

**Regla**: Para compartir la documentación con terceros, se puede exponer el servidor local mediante `ngrok`.

**Ejemplo**:
- Iniciar servidor local: `.\tools\serve_docs.ps1 -Port 8000`
- En otra consola: `ngrok http 8000`

**Notas**:
- La instalación y configuración de `ngrok` es responsabilidad del desarrollador.
- No exponer entornos de desarrollo con datos sensibles.

---

## 9. Cumplimiento

### 9.1 Revisión

**Proceso**: Estas políticas deben revisarse:
- Al inicio de cada sprint
- Cuando se identifiquen inconsistencias
- Cuando cambien las necesidades del proyecto

### 9.2 Actualización

**Proceso**: Las políticas pueden actualizarse mediante:
- Propuesta del equipo
- Aprobación del Technical Project Manager
- Actualización de este documento
- Comunicación al equipo

---

**Última actualización**: 2025-01-08  
**Mantenido por**: Technical Project Manager  
**Próxima revisión**: 2025-02-08

