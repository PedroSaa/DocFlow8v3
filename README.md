# DocFlow v3 - Sistema de Gestión Documental

Rediseño completo del sistema de gestión documental DocFlow para competir en licitaciones municipales chilenas.

## 🎯 Objetivo

Desarrollar la versión 3 del sistema DocFlow con tecnología moderna para participar en licitaciones municipales en Chile.

### Mercado Objetivo

| Municipalidad | Valor Estimado | Estado |
|---------------|----------------|--------|
| Villarrica | $75.000.000 CLP | Objetivo |
| Las Condes | $30.000.000 CLP | Objetivo |
| Lampa | $25.000.000 CLP | Objetivo |

## 📊 Resumen del Proyecto

- **Análisis**: 129 screenshots del sistema actual (v2) analizados
- **Backend reutilizable**: ~70% del código de v2 puede ser reutilizado
- **Frontend nuevo**: 100% nuevo con diseño moderno
- **Demos UI**: 16 prototipos HTML interactivos creados

## 🖥️ Demos UI

Los demos se encuentran en `contexto/demos/`:

| # | Archivo | Descripción |
|---|---------|-------------|
| 01-05 | Estilos base | Layout, tipografía, colores, spacing, theming |
| 06 | Login + Cargo | Autenticación y selección de rol |
| 07 | Dashboard | Panel principal básico |
| 08 | Bandeja | Bandeja con tabs y acordeón expandible |
| 09 | Documento | Crear/ver/editar documento |
| 10 | Expedientes | Gestión de expedientes con tabs |
| 11 | Dashboard KPIs | Dashboard con 4 indicadores clave |
| 12 | Dashboard Premium | Dashboard con más información |
| 13 | Consulta Docs | Búsqueda avanzada (12 filtros) |
| 14 | Consulta Tareas | Gestión de tareas |
| 15 | Archivadores | Gestión física de archivos |
| 16 | Reportes | Reportes con gráficos |

### Presets de Theming

Todos los demos incluyen 4 presets de municipalidades:
- 🔵 **DocFlow** (azul, default)
- 🟢 **Villarrica** (verde)
- 🟣 **Lampa** (violeta)
- 🔴 **Las Condes** (rojo)

## 📄 Documentación

En `contexto/`:

- `Informe_Ejecutivo_DocFlow_v3.md` - Resumen ejecutivo completo
- `Especificaciones_Tecnicas.md` - Stack tecnológico propuesto
- `Analisis_Screenshots_Legacy.md` - Análisis detallado del sistema actual
- `Analisis_Reutilizacion_Backend_v2.md` - Componentes reutilizables
- `Design_System.md` - Sistema de diseño UI
- `Decisiones_UI.md` - Decisiones de diseño tomadas

## 🛠️ Stack Tecnológico Propuesto

| Capa | Tecnología |
|------|-------------|
| Frontend | Angular 17 + Angular Material |
| Backend | .NET 8 + Entity Framework Core |
| Base de datos | SQL Server (multi-tenant) |
| Autenticación | ClaveUnica OIDC |
| Búsqueda | FTS5 (SQL Server Full-Text) |
| Despliegue | Docker + Kubernetes |

## 📈 Estado del Proyecto

- [x] Análisis de screenshots (129 imágenes)
- [x] Decisiones de diseño UI
- [x] 16 prototipos HTML interactivos
- [x] Informe ejecutivo
- [ ] Desarrollo backend
- [ ] Demos UI restantes (Administración, Crear Documento, etc.)
- [ ] Documentación técnica completa

## 📝 Licencia

Privado - Proyecto en desarrollo

---

*Desarrollado para licitaciones municipales chilenas | 2026*