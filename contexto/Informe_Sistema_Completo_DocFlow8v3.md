# INFORME DETALLADO DEL SISTEMA DOCFLOW 8 INFINITY

**Versión:** 1.0  
**Fecha:** 9 de Abril 2026  
**Proyecto:** DocFlow 8 Infinity - Sistema de Gestión Documental

---

## 1. RESUMEN EJECUTIVO

DocFlow 8 Infinity es un sistema de gestión documental para el sector público chileno, diseñado para digitalizar y automatizar los flujos de documentos heredados del sistema legacy (DocFlow 6). El sistema soporta los 3 flujos principales de documentos: **LLEGAN** (exterior → interior), **CIRCULAN** (interno → interno), y **SALEN** (interior → exterior).

---

## 2. ARQUITECTURA GENERAL

### 2.1 Stack Tecnológico

| Capa | Tecnología |
|------|------------|
| **Frontend** | Angular 17+ (SPA) |
| **Backend** | .NET 8 / ASP.NET Core |
| **Base de Datos** | SQL Server / PostgreSQL |
| **Auth** | JWT + Identity + MFA |
| **Archivo** | Blob Storage (Azure/AWS) |

### 2.2 Estructura de Proyecto

```
DocFlow8v3/
├── src/
│   ├── DocFlow.Core/          # Entidades, interfaces, enums
│   ├── DocFlow.Application/    # Use cases, DTOs, Services
│   ├── DocFlow.Infrastructure/ # EF Core, repositories
│   └── DocFlow.Api/           # Controllers, middleware
└── contexto/
    ├── demos/                 # 21 demos HTML
    ├── Especificaciones_Tecnicas.md
    └── presentaciones/       # Presentaciones comerciales
```

---

## 3. MÓDULOS DEL SISTEMA

### 3.1 Bandeja de Entrada

**Ubicación:** `demos/08-bandeja.html`, `demos/20-bandeja-completa.html`

**Funcionalidades:**
- Documentos pendientes por recibir/derivar
- Filtros por fecha, tipo, número, materia, correlativo
- Acciones: Recibir, Derivar, Reenviar, Devolver, Archivar, Anular, Destacar
- Indicadores visuales: T (timbre), I (imagen), A (adjuntos), R (referencias), O (observaciones)
- Sub-bandejas:
  - DocDigital del Gob. (documentos del gobierno)
  - Email (documentos recibidos por email)
  - Facturas (DTE del SII)
  - Órdenes de Compra

**Estados de documento:**
- Pendiente
- Recibido
- En proceso
- Firmado
- Archivrado

### 3.2 Registro de Documentos

**Demos:** 
- `17-registrar-documento-recibido.html` (LLEGAN)
- `18-crear-documento-interno.html` (CIRCULAN)
- `19-crear-documento-externo.html` (SALEN)

#### 3.2.1 Tipos de Documentos por Flujo

| Código | Tipo | Flujo | Requiere Firma |
|--------|------|-------|----------------|
| REC | Recibido | LLEGAN | ❌ |
| PAR | Partes | LLEGAN | ❌ |
| MEM | Memo | CIRCULAN | ❌ |
| DES | Despacho | CIRCULAN | ✅ |
| CIR | Circular | CIRCULAN | ❌ |
| INF | Informe | CIRCULAN | ✅ |
| OFI | Oficio | SALEN | ✅ |
| RES | Resolución | SALEN | ✅ |
| ORD | Ordinario | SALEN | ✅ |
| CAR | Carta | SALEN | ✅ |

#### 3.2.2 Campos del Formulario

- Tipo de Documento
- Número Documento (auto-generado)
- Fecha Documento
- Remitente (Autor)
- Destinatario
- Código Cite
- Fecha Cite
- Materia
- Fecha Vencimiento (opcional)
- Adjuntos
- Observaciones

### 3.3 Expedientes

**Demo:** `demos/10-expedientes.html`

**Funcionalidades:**
- Agrupar documentos por proyecto/hito
- Tree view de documentos
- 8 tabs: Resumen, Documentos, Tareas, Derivaciones, Bitácora, Historial, Archivadores, Adjuntos
- Compartir expediente con otros usuarios
- Estados: Pendiente, Proceso, Cerrado

### 3.4 Tareas

**Demo:** `demos/14-consulta-tareas.html`

**Funcionalidades:**
- Asignar tareas a usuarios
- Nivel responsable (quien asigna)
- Usuario ejecutor (quien realiza)
- Glosa (descripción)
- Fecha vencimiento
- Responder con documento
- Estados: Pendiente, Cumplida, Semicumplida

### 3.5 Archivadores

**Demo:** `demos/15-archivadores.html`

**Funcionalidades:**
- Gestión física de documentos
- Organización por: Año, Mes, Caja
- Descripción del contenido
- Estado: Archivrado, En préstamo

### 3.6 Reportes

**Demo:** `demos/16-reportes.html`

**Tipos de Reportes:**
- Por correlativos
- Por rango de fechas, tipo y correlativos
- Despacho interno por departamento
- Reporte diario/mensual por departamento y formato
- Documentos no recibidos

---

## 4. FLUJO COMPLETO DE DOCUMENTO DE SALIDA

El sistema soporta el flujo completo heredado del legacy (6 pasos):

```
1. CREAR → 2. DERIVAR → 3. RECIBIR → 4. FIRMAR → 5. OFICINA DE PARTES → 6. DESPACHO
```

**Descripción de cada paso:**
1. **Crear**: Redactar documento en editor
2. **Derivar**: Enviar a trámite interno (revisión/aprobación)
3. **Recibir**: Aceptar en bandeja del receptor
4. **Firmar**: Firma electrónica → PDF certificado
5. **Oficina de Partes**: Único punto autorizado para despacho exterior
6. **Despacho**: Envío físico o electrónico al destinatario

---

## 5. FLUJOS ESPECIALIZADOS (CONFIGURABLES)

El sistema soporta 9 flujos especializados configurables:

| # | Flujo | Descripción |
|---|-------|-------------|
| 1 | Decreto Sección 1 y 2 Pagos | Decretos de pago |
| 2 | Decreto Subsidios | Decretos de subsidios |
| 3 | Pago Facturas | Workflow de aprobación de facturas |
| 4 | Decretos Personal | Decretos de personal |
| 5 | Visación Bases Licitación | Revisión de bases |
| 6 | Transparencia Activa | Publicación proactiva |
| 7 | Transparencia Pasiva/SAI | Respuesta a solicitudes |
| 8 | Tramitación Libre | Trámites no categorizados |
| 9 | Oficios DocDigital | Desde bandeja DocDigital |

---

## 6. INTEGRACIONES GUBERNAMENTALES

### 6.1 ClaveUnica (OIDC)

- **Protocolo:** OpenID Connect
- **Endpoint:** `https://accounts.claveunica.gob.cl`
- **Scopes:** `openid run run identifiers`
- **Estado:** ✅ Implementado en demo login

### 6.2 FirmaGob

**Roles:**
- Operador (administrador)
- Ministro de Fe (validador)
- Funcionario/Autoridad (firmante)

**Tipos de Certificados:**
- Propósito General (QR)
- Propósito Desatendido (API)
- Autocertificación

**API Endpoints:**
- `POST /api/v1/signatures/sign`
- `POST /api/v1/signatures/batch`
- `GET /api/v1/certificates/status`
- `POST /api/v1/certificates/revoke`

### 6.3 DocDigital del Gobierno

**Funciones v3:**
1. Firma y distribución de documentos
2. Comunicaciones internas
3. Comunicaciones oficiales

**API:** `https://api.doc.digital.gob.cl`

### 6.4 DTE - Facturas Electrónicas (SII)

- Recepción automática de facturas
- Validación de timbre
- Workflow de aprobación

### 6.5 OIRS / Transparencia

- DECRETO 968
- Plazo: 20 días hábiles
- Portal ciudadano

### 6.6 Módulo de Email (IMAP)

**Protocolos:**
- IMAP (993) - lectura
- POP3 (995) - descarga
- SMTP (587) - notificaciones

**Funcionalidades:**
- Polling automático (configurable)
- Reglas de clasificación (remitente, asunto, adjunto)
- Conversión a documento DocFlow

---

## 7. AUTENTICACIÓN

### 7.1 Métodos Soportados

| Método | Descripción | Estado |
|--------|-------------|--------|
| **Local** | Usuario/password | ✅ |
| **ClaveUnica** | OIDC gobierno | ✅ |
| **MFA** | Segundo factor | ✅ |
| **SSO** | Single Sign-On | ✅ |

### 7.2 Roles y Permisos

- Administrador
- Usuario
- Operador
- Ministro de Fe
- Receptor
- Firmante

---

## 8. LISTA COMPLETA DE DEMOS

| # | Archivo | Descripción |
|---|---------|-------------|
| 01 | `01-corporate-clean.html` | UI Corporate Clean |
| 02 | `02-modern-saas.html` | UI Modern SaaS |
| 03 | `03-government-formal.html` | UI Government Formal |
| 04 | `04-material-design.html` | UI Material Design |
| 05 | `05-corporate-theming.html` | Theming dinámico |
| 06 | `06-login-cargo.html` | Login con 4 auth methods |
| 07 | `07-dashboard.html` | Dashboard básico |
| 08 | `08-bandeja.html` | Bandeja de entrada |
| 09 | `09-documento.html` | Ver/editar documento (9 tabs) |
| 10 | `10-expedientes.html` | Expedientes |
| 11 | `11-dashboard-kpis.html` | Dashboard con KPIs |
| 12 | `12-dashboard-premium.html` | Dashboard premium |
| 13 | `13-consulta-documentos.html` | Búsqueda documentos |
| 14 | `14-consulta-tareas.html` | Consulta tareas |
| 15 | `15-archivadores.html` | Archivadores |
| 16 | `16-reportes.html` | Reportes |
| 17 | `17-registrar-documento-recibido.html` | Registrar (LLEGAN) |
| 18 | `18-crear-documento-interno.html` | Crear (CIRCULAN) |
| 19 | `19-crear-documento-externo.html` | Crear (SALEN) |
| 20 | `20-bandeja-completa.html` | Bandeja + sub-bandejas |
| 21 | `21-administracion.html` | Administración del sistema |

---

## 9. DOCUMENTACIÓN EXISTENTE

### 9.1 Informes

| Documento | Descripción |
|-----------|-------------|
| `Especificaciones_Tecnicas.md` | Especificaciones técnicas completas |
| `Informe_Ejecutivo_DocFlow_v3.md` | Resumen ejecutivo |
| `Informe_Detallado_Sistema.md` | Análisis detallado |
| `Informe_Consolidado_DocFlow_v3.md` | Consolidado completo |
| `Plan_Trabajo_DocFlow_v3.md` | Plan de trabajo |
| `Plan_Desarrollo_Premium_DocFlow8v2.md` | Plan de desarrollo premium |

### 9.2 Análisis

| Documento | Descripción |
|-----------|-------------|
| `Analisis_Licitaciones_Municipal.md` | Análisis para licitaciones |
| `Analisis_Reutilizacion_Backend_v2.md` | Reutilización de código |
| `Analisis_Screenshots_Legacy.md` | Análisis del sistema legacy |

### 9.3 Diseño

| Documento | Descripción |
|-----------|-------------|
| `Design_System.md` | Sistema de diseño UI |
| `Decisiones_UI.md` | Decisiones de interfaz |
| `Accesibilidad.md` | Estándares WCAG |

### 9.4 Auditoría

| Documento | Descripción |
|-----------|-------------|
| `Auditoria_DocFlow8v2.md` | Control de cambios y auditoría |

---

## 10. FUNCIONALIDADES PENDIENTES O POR MEJORAR

### 10.1 Pendientes de Implementación

- [ ] Motor de workflows visual (BPMN-like)
- [ ] Designer visual para workflows
- [ ] Portal Ciudadano (SPA externo completo)
- [ ] App móvil React Native
- [ ] Integración completa API DocDigital
- [ ] Integración completa API FirmaGob

### 10.2 Mejoras Sugeridas

1. **Editor de documentos**: Mejorar el editor WYSIWYG
2. **Firma digital**: Agregar más opciones de证书
3. **Reportes**: Agregar más tipos de reportes
4. **Búsqueda**: Mejorar búsqueda con FTS
5. **Notificaciones**: Agregar notifications push

---

## 11. TABLAS DE BASE DE DATOS (PROPUESTAS)

### Documentos
- Documents
- DocumentVersions
- DocumentAttachments
- DocumentReferences

### Expedientes
- Expedients
- ExpedientDocuments
- ExpedientHitos

### Tareas
- Tasks
- TaskComments
- TaskAttachments

### Usuarios
- Users
- Roles
- UserRoles
- UserSessions

### Configuración
- Tenants
- DocumentTypes
- Departments
- Archivadores

### Integraciones
- EmailAccounts
- EmailRules
- IntegrationLogs

---

## 12. PRÓXIMOS PASOS

Para continuar el desarrollo, se recomienda:

1. **Revisar** este informe y validar completitud
2. **Priorizar** funcionalidades pendientes
3. **Asignar** recursos para implementación
4. **Planificar** sprints de desarrollo
5. **Definir** timeline de entrega

---

*Documento generado el 9 de Abril de 2026*