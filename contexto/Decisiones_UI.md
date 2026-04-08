# DECISIONES DE DISENO UI — DocFlow8v3

**Fecha inicio**: Abril 2026  
**Estado**: **COMPLETADO** (5 pantallas diseñadas)  
**Enfoque**: Rediseno completo (no evolucion del legacy)

---

## 1. DECISION PRINCIPAL: ESTILO VISUAL

### Opciones evaluadas

Se crearon 5 demos HTML interactivos para evaluar estilos:

| Demo | Estilo | Archivo | Referencia |
|------|--------|---------|-----------|
| 01 | **Corporate Clean** | `demos/01-corporate-clean.html` | Microsoft 365, SAP Fiori |
| 02 | Modern SaaS | `demos/02-modern-saas.html` | Notion, Linear |
| 03 | Government Formal | `demos/03-government-formal.html` | Portales gov.cl |
| 04 | Material Design 3 | `demos/04-material-design.html` | Google Workspace |
| 05 | Theming Corporativo | `demos/05-corporate-theming.html` | — |

### Elegido: Corporate Clean

**Razon**: Profesional, transmite confianza institucional, familiar para usuarios de gobierno acostumbrados a Microsoft Office.

Caracteristicas clave:
- **Sidebar** colapsable, modo dark por defecto
- **Densidad compacta** — mas informacion visible sin scroll
- **Tipografia**: Inter o similar sans-serif, pesos 400/500/600/700
- **Iconografia**: Outlined, linea fina, estilo Lucide/Heroicons
- **Layout**: Sidebar izquierda + area de contenido con header breadcrumb

---

## 2. DECISION: THEMING DINAMICO POR ORGANIZACION

### Requerimiento
Cada municipalidad (organizacion/tenant) debe poder configurar su propia identidad visual sin tocar codigo.

### Implementacion definida

| Aspecto | Configurable | Detalle |
|---------|:------------:|---------|
| **Color primario** | SI | Color picker, se generan automaticamente: hover, active, light, dark |
| **Logo** | SI | Upload de imagen, se muestra en sidebar y login |
| **Nombre del sistema** | SI | Reemplaza "DocFlow" en header y login |
| **Modo sidebar** | SI | Dark (default), Light, Color (usa el primario como fondo) |
| **Favicon** | SI | Upload o generacion automatica desde logo |

### Presets de ejemplo (implementados en todos los demos)

| Preset | Color primario | Uso |
|--------|---------------|-----|
| DocFlow (default) | Azul `#1a56db` | Default del producto |
| Villarrica | Verde `#166534` | Municipalidad de Villarrica |
| Lampa | Violeta `#7c3aed` | Municipalidad de Lampa |
| Las Condes | Rojo `#b91c1c` | Municipalidad de Las Condes |

### Formato de configuracion
La configuracion se exporta como JSON:
```json
{
  "organizationName": "Municipalidad de Villarrica",
  "systemName": "DocFlow Municipal",
  "primaryColor": "#166534",
  "sidebarMode": "dark",
  "logo": "data:image/png;base64,..."
}
```

---

## 3. ANALISIS DEL SISTEMA LEGACY (DocFlow 7)

### Metodologia
Se analizaron **103 screenshots** del video legacy del sistema DocFlow 7, organizados en lotes de 5 para evitar limites de contexto.

### Screenshots analizados

| Lote | Screenshots | Contenido identificado |
|------|-------------|----------------------|
| 1 | 001-005 | Login, seleccion cargo, dashboard |
| 2 | 006-010 | Expedientes, carpetas |
| 3 | 011-015 | Detalle documento (9 tabs) |
| 4 | 016-020 | Tareas, asociar docs |
| 5 | 021-025 | Lista de control (checklist) |
| 6 | 026-030 | Vencimiento, documentos en expediente |
| 7 | 031-035 | Observaciones, sidebar completa |
| 8-9 | 036-045 | Referencias, bandeja recibidos |
| 10 | 046-050 | Atender/Derivar documentos |
| 11 | 051-055 | Panel Atender, Derivar modal |
| 12 | 056-060 | Crear Tarea desde documento |
| 13 | 061-065 | Bandeja Doc. Recibidos con badges |
| 14 | 066-070 | DocDigital (documentos escaneados) |
| 15 | 071-075 | Registro doc recibido, crear externo/interno |
| 16 | 076-080 | Crear Doc Interno, creacion exitosa |
| 17 | 081-085 | Consulta global de documentos |
| 18 | 086-090 | Detalle documento desde busqueda |
| 19 | 091-095 | Historial documento |
| 20 | 096-100 | Consulta tareas |
| 21 | 101-103 | Ejecutar tarea, fin video |

### Hallazgos clave del analisis

#### Workflow de documentos
```
1. Documento recibido (externo o interno)
       ↓
2. Atender (derivar, firmar, crear tarea, responder)
       ↓
3. Derivar → Crear derivacion (badge verde)
       ↓
4. Tarea creada en consulta tareas
       ↓
5. Ejecutar tarea → Estado: Ejecutada
```

#### Tipos de documentos identificados

| Tipo | Descripcion |
|------|-------------|
| Interno | Documentos creados dentro del municipio |
| Externo | Documentos enviados fuera del municipio |
| DocDigital | Documentos escaneados/digitalizados |
| Recibido | Documentos fisicos recibidos |

#### Modulo Expedientes
- Numeracion: `Exp-XXXXXXXXXX`
- 7 tabs: Carpetas, Lista de control, Vencimiento, Documento, Observaciones, Referencias, Compartir
- Lista de control: barra de progreso %, checklist con CRUD
- Carpetas: asociar documentos, ordenamiento

#### Documento (9 tabs legacy)
- Detalle, Documento, Tareas, Historial, Imagen Doc., Adjuntos Doc., Observaciones, Referencias, Referencias Zonales

#### Sidebar completa
```
📁 Expedientes
   ├── Mis Expedientes
   └── Compartido conmigo

📥 Bandeja entrada

📄 Registro de documentos
   ├── Registrar Doc. Recibido
   ├── Bandeja de DocDigital
   ├── Bandeja de Doc. Recibidos
   ├── Crear Doc. Externo
   └── Crear Doc. Interno

🔍 Consulta documentos
🔍 Consulta tareas

📚 Archivadores
📊 Reportes

👤 Mi Perfil
🚪 Cerrar Sesion
```

### Conclusiones del analisis legacy
- El workflow document → atender → derivar → tarea → ejecutar es的核心
- Los 9 tabs del documento deben simplificarse en el rediseño
- La seleccion de cargo es flujo critico para usuarios multi-rol
- Las bandejas (Recibidos, Derivaciones, DocDigital) son puntos de entrada principales

---

## 4. PANTALLAS DISENADAS

### Estado: COMPLETADO

| # | Pantalla | Archivo demo | Descripcion |
|---|----------|--------------|-------------|
| 1 | **Login + Seleccion de Cargo** | `06-login-cargo.html` | Formulario login + modal seleccion cargo multi-rol |
| 2 | **Dashboard principal** | `07-dashboard.html` | 4 KPIs + docs recientes + acciones rapidas |
| 3 | **Bandeja Entrada/Salida** | `08-bandeja.html` | 4 tabs (Todos/Recibidos/Derivaciones/DocDigital) + filtros + panel atender |
| 4 | **Documento (crear/ver/editar)** | `09-documento.html` | 3 modos + formulario completo + tabs de detalle |
| 5 | **Expedientes** | `10-expedientes.html` | Vista maestro-detalle + 7 tabs + checklist con progreso |

---

### Pantalla 1: Login + Seleccion de Cargo

**Archivo**: `demos/06-login-cargo.html`  
**Estado**: Aprobado

Elementos implementados:
- Logo dinamico por theming
- Nombre del sistema configurable
- Campos usuario/contrasena
- Checkbox "Recordar sesion"
- Link "Olvide mi contrasena"
- Boton "Ingresar con ClaveUnica" (requisito licitaciones)
- Pantalla de carga/transicion
- Modal seleccion de cargo con:
  - Avatar + nombre usuario
  - Lista de cargos disponibles
  - Badge "Ultimo usado"
  - Metadatos: ultima sesion, pendientes por cargo

---

### Pantalla 2: Dashboard

**Archivo**: `demos/07-dashboard.html`

Elementos implementados:
- **Sidebar dark colapsable** con navegacion completa
- **4 Cards KPIs** (del legacy):
  - Docs pendientes de atencion (rojo)
  - Tareas por controlar (verde)
  - Tareas por ejecutar (teal)
  - Docs no leidos (amarillo)
- **Indicadores de tendencia** (+/- porcentaje)
- **Documentos Recientes**: lista con iconos por tipo (doc, expediente, derivacion), badges de estado (Urgente, En curso, Leido)
- **Acciones Rapidas**: Crear Doc Interno, Registrar Doc Recibido, Nuevo Expediente, Buscar Documento
- **Header** con hamburger toggle, titulo, notificaciones

---

### Pantalla 3: Bandeja de Entrada/Salida

**Archivo**: `demos/08-bandeja.html`

Elementos implementados:
- **Tabs izquierda**: Todos, Recibidos, Derivaciones, DocDigital (con contadores)
- **Filtros**: Tipo documento, Desde, Hasta, Buscar (N°, asunto, remitente)
- **Tabla de documentos** con columnas:
  - N° Doc, Remitente, Asunto, Fecha, Estado, Acciones
- **Estados**: badge "Derivacion" (verde), "Recibido" (cyan), "Leido" (gris)
- **Panel flotante "Atender"** con tabs:
  - Derivar: dropdown destinatario + instrucciones
  - Firmar
  - Crear Tarea
  - Responder
- **Toast notifications** para confirmaciones
- **Paginacion**

---

### Pantalla 4: Documento (crear/ver/editar)

**Archivo**: `demos/09-documento.html`

Elementos implementados:
- **Mode toggle**: Crear / Ver / Editar
- **Modo Crear**: formulario completo
  - Tipo documento (select)
  - N° documento (auto, readonly)
  - Fecha documento
  - Remitente / Destinatario
  - Categoria + Subcategoria (jerarquia)
  - Materia (asunto)
  - Cuerpo del documento (textarea)
  - Adjuntar archivo (drag & drop)
- **Modo Ver**: detalles en formato lista
  - 6 tabs: Documento, Tareas, Historial, Adjuntos, Observaciones, Referencias
- **Modo Editar**: mismo formulario que crear (prellenado)
- **Toasts** para confirmaciones

---

### Pantalla 5: Expedientes

**Archivo**: `demos/10-expedientes.html`

Elementos implementados:
- **Vista maestro-detalle**: lista izquierda + detalle derecha
- **Lista de expedientes** con:
  - N° Expediente
  - Estado (Cerrado, Por Revisar, Activo)
  - Nombre/Materia
  - Fecha creacion, autor
  - Vencimiento (badge verde/rojo)
- **Detalle del expediente** con header + acciones (Modificar, Compartir, Eliminar)
- **7 Tabs** (del legacy):
  - Carpetas: lista + boton crear carpeta
  - Lista de Control: progress bar % + checklist con checkboxes + CRUD items
  - Vencimiento: datepicker con badges vigente/vencido
  - Documento: lista de docs asociados + boton "Asociar Documento"
  - Observaciones: comentarios cronologicos con avatar, autor, fecha, texto + editar/eliminar
  - Referencias: (vacio en demo)
  - Compartir: (vacio en demo)
- **Checklist dinamico**: recalcula progreso al marcar/desmarcar

---

## 5. ELEMENTOS UI COMUNES (En todos los demos)

### Sidebar
- Fondo dark (`#1f2937`)
- Logo + nombre organizacion (dinamico)
- Tarjeta de usuario (avatar, nombre, dept)
- Navegacion con secciones: Principal, Expedientes, Documentos, Sistema
- Nav items con badges de contadores
- Nav anidado (submenus) expandible
- Footer: colapsar menu, cerrar sesion

### Theming
- CSS variables para colores primarios
- 4 presets: DocFlow (azul), Villarrica (verde), Lampa (violeta), Las Condes (rojo)
- Funcion JS `applyPreset(name, chip)` para cambiar temas en tiempo real
- Controles flotantes en esquina superior derecha

### Componentes
- **Botones**: primary (solid), secondary (outline), danger
- **Inputs**: text, select, date, textarea
- **Tablas**: headers con mayusculas, rows con hover
- **Cards**: con shadow y border radius
- **Tabs**: estilo pill con indicador activo
- **Badges**: colores por contexto (success, warning, danger, info)
- **Toasts**: posicion inferior centro, animacion slide-up

---

## 6. DESIGN SYSTEM — Tokens definidos

### Colores

| Token | Valor default | Uso |
|-------|---------------|-----|
| `--primary` | `#1a56db` | Botones principales, links, acentos |
| `--primary-light` | `#e8effc` | Fondos de highlight |
| `--primary-dark` | `#1240a8` | Hover states |
| `--success` | `#0d9f6e` | Estados exitosos |
| `--warning` | `#e3a008` | Warnings, pendientes |
| `--danger` | `#e02424` | Errores, urgencia |
| `--info` | `#3f83f8` | Informacion |
| `--gray-50` a `--gray-900` | escala | Fondos, textos, bordes |

### Espaciado

| Token | Valor |
|-------|-------|
| `--sidebar-width` | `260px` |
| `--sidebar-collapsed` | `72px` |
| `--header-height` | `64px` |

### Tipografia
- **Familia**: Inter, -apple-system, BlinkMacSystemFont, Segoe UI, Roboto
- **Tamaños**: 12px (small), 13px (body small), 14px (body), 15px (card title), 16px (section title), 18px (page title), 24px (h1)
- **Pesos**: 400 (regular), 500 (medium), 600 (semibold), 700 (bold)

### Border radius
- Botones/inputs: `6px` - `8px`
- Cards: `12px`
- Badges: `4px` - `6px` - `10px`
- Avatars: `50%` (circulo) o `10px` (rounded square)

---

## 7. PROXIMOS PASOS (Post UI Design)

1. **Especificaciones tecnicas**: Definir implementacion (Angular 17, .NET 8, etc.)
2. **Componentes reutilizables**: Crear library de componentes basados en este design system
3. **Responsive**: Definir breakpoints y comportamiento mobile/tablet
4. **Accesibilidad**: WCAG 2.1 nivel AA
5. **Micro-interacciones**: Animaciones, transiciones, feedback visual

---

## 8. ARCHIVOS GENERADOS

### Documentos
- `Plan_Desarrollo_Premium_DocFlow8v2.md` — Plan 8 etapas
- `Carta_Gantt_DocFlow8v2.md` — Carta Gantt
- `Auditoria_DocFlow8v2.md` — Estado real del codigo
- `Analisis_Licitaciones_Municipales.md` — Gap analysis 3 municipalidades
- `Decisiones_UI.md` — Este documento
- `Analisis_Screenshots_Legacy.md` — Analisis completo de 103 screenshots

### Demos HTML
- `demos/01-corporate-clean.html` — Estilo Corporate Clean
- `demos/02-modern-saas.html` — Estilo Modern SaaS
- `demos/03-government-formal.html` — Estilo Government Formal
- `demos/04-material-design.html` — Estilo Material Design 3
- `demos/05-corporate-theming.html` — Theming dinamico
- `demos/06-login-cargo.html` — **Pantalla 1: Login + Cargo**
- `demos/07-dashboard.html` — **Pantalla 2: Dashboard**
- `demos/08-bandeja.html` — **Pantalla 3: Bandeja**
- `demos/09-documento.html` — **Pantalla 4: Documento**
- `demos/10-expedientes.html` — **Pantalla 5: Expedientes**

---

*Documento consolidado — Abril 2026*  
*Proyecto: DocFlow8v3ClaudeCode*