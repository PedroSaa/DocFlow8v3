# DocFlow v3 - Sistema de Gestión Documental

Rediseño completo del sistema de gestión documental DocFlow. Producto genérico, configurable y adaptable para cualquier organización.

## 🎯 Objetivo

Desarrollar la versión 3 del sistema DocFlow con tecnología moderna, configurable y adaptable para cualquier organización en Chile.

## 📦 Tipo de Producto

**SaaS Multi-tenant** - Sistema genérico que se adapta a cada organización mediante configuración.

---

## ✨ Características del Sistema

### Autenticación (Configurable por Organización)
| Método | Descripción |
|--------|-------------|
| Local | Usuario + Contraseña (base de datos del tenant) |
| ClaveUnica | OIDC con ChileAutentica (RUN) |
| MFA/Doble Factor | TOTP (Google Authenticator, Authy) |
| SSO Corporativo | SAML 2.0 / OIDC externo |

### Tipos de Documentos (Flujo Completo)

| Categoría | Tipos | Flujo |
|-----------|-------|-------|
| **Recibido Externo** | Documentos de afuera | Llega → Partes → Derivar |
| **Generado Interno** | Memo, Despacho, Circular | Crear → Derivar (nunca sale) |
| **Generado Externo** | Oficio, Resolución, Ordinario | Crear → Firmar → Enviar |

### Funcionalidades Principales
- ✅ **OCR**: Extracción de texto de imágenes escaneadas
- ✅ **Theming Dinámico**: Colores, logos, estilo por organización
- ✅ **Módulo de Configuración**: Todo parametrizable
- ✅ **Multi-tenant**: Base de datos aislada por organización
- ✅ **Búsqueda Full-Text**: FTS5

---

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
| 12 | Dashboard Premium | Dashboard premium |
| 13 | Consulta Docs | Búsqueda avanzada (12 filtros) |
| 14 | Consulta Tareas | Gestión de tareas |
| 15 | Archivadores | Gestión física de archivos |
| 16 | Reportes | Reportes con gráficos |

### Presets de Theming
Todos los demos incluyen presets configurables:
- 🔵 **DocFlow** (azul, default)
- 🟢 **Villarrica** (verde)
- 🟣 **Lampa** (violeta)
- 🔴 **Las Condes** (rojo)

*( cualquier organización puede agregar el suyo)*

---

## 📄 Documentación

En `contexto/`:

| Documento | Descripción |
|-----------|-------------|
| `Informe_Ejecutivo_DocFlow_v3.md` | Resumen ejecutivo completo |
| `Plan_Trabajo_DocFlow_v3.md` | Plan de trabajo con 21 tareas |
| `Especificaciones_Tecnicas.md` | Stack tecnológico y arquitectura |
| `Analisis_Screenshots_Legacy.md` | Análisis detallado del sistema actual |
| `Analisis_Reutilizacion_Backend_v2.md` | Componentes reutilizables |
| `Design_System.md` | Sistema de diseño UI |
| `Decisiones_UI.md` | Decisiones de diseño tomadas |
| `Accesibilidad.md` | Normas WCAG 2.1 AA |
| `Micro_interacciones.md` | Animaciones y transiciones |

---

## 🛠️ Stack Tecnológico

| Capa | Tecnología |
|------|-------------|
| Frontend | Angular 17 + Angular Material |
| Backend | .NET 8 + Entity Framework Core |
| Base de datos | SQL Server (multi-tenant) |
| Autenticación | JWT + Identity + MFA + SSO + ClaveUnica |
| Búsqueda | FTS5 (SQL Server Full-Text) |
| OCR | Tesseract / Azure Vision |
| Despliegue | Docker + Kubernetes |

---

## 📈 Estado del Proyecto

- [x] Análisis de screenshots (129 imágenes)
- [x] Decisiones de diseño UI
- [x] 16 prototipos HTML interactivos
- [x] Informe ejecutivo
- [x] Plan de trabajo actualizado
- [ ] Desarrollo backend
- [ ] Demos UI restantes (Administración, Crear Documento, etc.)
- [ ] Documentación técnica completa

---

## 📋 Tareas Totales: 21

| Fase | Descripción | Tareas |
|------|-------------|-------|
| Fase 1 | Preparación | 4 |
| Fase 2 | Backend Core | 7 |
| Fase 3 | Frontend | 12 |
| Fase 4 | Integración y Testing | 5 |
| Fase 5 | Despliegue y Entrega | 5 |

---

## 🎯 Licitaciones Objetivo

| Organización | Valor Estimado | Estado |
|--------------|----------------|--------|
| Villarrica | $75.000.000 CLP | Objetivo |
| Las Condes | $30.000.000 CLP | Objetivo |
| Lampa | $25.000.000 CLP | Objetivo |
| **Total** | **$130.000.000 CLP** | |

---

## 📝 Licencia

Privado - Proyecto en desarrollo

---

*Desarrollado para organizaciones chilenas | 2026*
*Producto genérico, configurable y adaptable*