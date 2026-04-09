# INFORME CONSOLIDADO — DocFlow v3
## Sistema de Gestión Documental Multi-tenant Genérico

**Versión**: 3.0  
**Fecha**: Abril 2026  
**Estado**: En Desarrollo  
**Tipo**: Producto SaaS Multi-tenant Genérico  
**Objetivo**: Competir en licitaciones públicas municipales chilenas

---

## RESUMEN EJECUTIVO

DocFlow v3 es la evolución del sistema de gestión documental DocFlow, transformado en un **producto genérico y configurable** que puede adaptarse a cualquier organización en Chile.

### Oportunidad Comercial

| Organización | Valor Estimado | Probabilidad |
|--------------|----------------|--------------|
| Villarrica | $75.000.000 CLP | Alta |
| Las Condes | $30.000.000 CLP | Media-Alta |
| Lampa | $25.000.000 CLP | Media |
| **Total Potencial** | **$130.000.000 CLP** | |

### Diferenciadores Clave

1. **Multi-tenant nativo**: Cada organización tiene su propia base de datos aislada
2. **Theming dinámico**: White-label completo sin tocar código
3. **ClaveUnica**: Integración OIDC con ChileAutentica (requisito licitaciones públicas)
4. **100% configurable**: Tipos de documentos, flujos, estados, unidades
5. **OCR integrado**: Extracción de texto de imágenes escaneadas
6. **FTS5**: Búsqueda full-text avanzada

---

## 1. ANÁLISIS DEL SISTEMA ACTUAL (DocFlow v2)

### 1.1 Fortalezas Identificadas

El sistema actual (v2) cuenta con un workflow robusto y funcional:

- **Workflow completo**: documento → atender → derivar → tarea → ejecutar
- **Módulos funcionales**: documentos internos/externos, DocDigital (escaneados), expedientes
- **Bandeja de recibidos y derivaciones** con seguimiento
- **Expedientes** con carpetas, checklist y progreso %
- **9 tabs por documento**: Detalle, Documento, Tareas, Historial, Imagen Doc., Adjuntos Doc., Observaciones, Referencias, Referencias Zonales
- **Sidebar completa** con navegación completa
- **Administración** de usuarios, perfiles, estados, parámetros

### 1.2 Limitaciones Técnicas

| Limitación | Impacto |
|------------|---------|
| Frontend obsoleto (WebForms) | Difícil mantenimiento, sin responsive |
| Sin soporte multi-tenant | No sirve para múltiples organizaciones |
| Sin theming dinámico | Cada cliente requiere desarrollo personalizado |
| Sin API pública | No integra con sistemas externos |
| Sin ClaveUnica | No cumple requisitos licitaciones públicas |
| Búsqueda limitada (sin FTS5) | Performance pobre en grandes volúmenes |
| UI no responsive | No funciona en móviles/tablets |

### 1.3 Reutilización de Código (v2 → v3)

| Componente | Porcentaje Reutilizable |
|------------|------------------------|
| Entidades (Document, Expediente, User, Derivacion, Tarea, Attachment) | ~70% |
| Servicios (DocumentService, ExpedienteService, DerivacionService) | ~70% |
| Controllers API | ~60% |
| Lógica de negocio (workflows, validaciones, notificaciones) | ~70% |
| Frontend (WebForms) | 0% |

---

## 2. ARQUITECTURA DEL SISTEMA

### 2.1 Stack Tecnológico

| Capa | Tecnología | Versión |
|------|------------|---------|
| **Frontend** | Angular | 17+ |
| **UI Components** | Angular Material + Custom | - |
| **Backend** | .NET | 8.0 |
| **Framework** | ASP.NET Core | 8.0 |
| **ORM** | Entity Framework Core | 8.0 |
| **Base de Datos** | SQL Server | 2019+ |
| **Autenticación** | JWT + Identity + MFA + SSO + ClaveUnica | - |
| **Búsqueda** | FTS5 (Full-Text Search) | - |
| **OCR** | Tesseract / Azure Vision | - |
| **Despliegue** | Docker + Kubernetes | - |

### 2.2 Arquitectura Multi-tenant

El sistema utiliza estrategia **Database per Tenant**:

```
┌─────────────────────────────────────────────────────────────┐
│                    SERVIDOR COMPARTIDO                      │
│                                                             │
│  ┌──────────────────────────────────────────────────────┐   │
│  │              Base de datos Central                    │   │
│  │  (Tenants, Users, Licenses, Configuration)            │   │
│  └──────────────────────────────────────────────────────┘   │
│                           │                                 │
│           ┌───────────────┼───────────────┐                │
│           │               │               │                 │
│           ▼               ▼               ▼                 │
│  ┌────────────┐   ┌────────────┐   ┌────────────┐          │
│  │ DB         │   │ DB         │   │ DB         │          │
│  │ Villarrica │   │ Lampa      │   │ Las Condes │          │
│  │ (tenant_1) │   │ (tenant_2) │   │ (tenant_3) │          │
│  └────────────┘   └────────────┘   └────────────┘          │
└─────────────────────────────────────────────────────────────┘
```

### 2.3 Modelo de Despliegue

```
┌─────────────────────────────────────────────────────────────┐
│                     ON-PREMISE / CLOUD                     │
│                                                             │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐   │
│  │   Frontend  │    │   Backend   │    │  SQL Server │   │
│  │  (Angular)  │◄──►│  (.NET 8)   │◄──►│   (DB)      │   │
│  │   :4200     │    │   :5000     │    │   :1433     │   │
│  └─────────────┘    └─────────────┘    └─────────────┘   │
│          │                  │                               │
│          └──────────────────┴───────────────────────────────│
│                        NGINX / NETSTART                        │
│                   (Reverse Proxy + SSL)                    │
└─────────────────────────────────────────────────────────────┘
```

### 2.4 Requisitos de Infraestructura

| Componente | Requerimiento Mínimo |
|------------|---------------------|
| **CPU** | 4 núcleos (x86_64) |
| **RAM** | 16 GB |
| **Disco** | 500 GB SSD |
| **OS** | Windows Server 2019+ / Linux (Ubuntu 22.04) |
| **Web Server** | IIS (Windows) o NGINX (Linux) |

### 2.5 Estructura del Proyecto

#### Backend (.NET 8)
```
src/
├── DocFlow.sln
│
├── src/
│   ├── DocFlow.Core/              # Domain, Entities, Interfaces
│   │   ├── Entities/
│   │   │   ├── Tenant.cs
│   │   │   ├── User.cs
│   │   │   ├── Role.cs
│   │   │   ├── Document.cs
│   │   │   ├── Expedient.cs
│   │   │   ├── Task.cs
│   │   │   └── ...
│   │   ├── Interfaces/
│   │   └── Enums/
│   │
│   ├── DocFlow.Application/       # Use Cases, DTOs, Services
│   │   ├── DTOs/
│   │   ├── Services/
│   │   ├── Validators/
│   │   └── Mapping/
│   │
│   ├── DocFlow.Infrastructure/    # EF Core, Repositories, Auth
│   │   ├── Data/
│   │   ├── Repositories/
│   │   └── Auth/
│   │
│   └── DocFlow.Api/               # API Controllers, Middleware
│       ├── Controllers/
│       ├── Middleware/
│       └── Program.cs
│
└── tests/
    ├── DocFlow.Core.Tests/
    ├── DocFlow.Application.Tests/
    └── DocFlow.Api.Tests/
```

#### Frontend (Angular 17)
```
docflow-frontend/
├── src/
│   ├── app/
│   │   ├── core/                    # Singleton services, guards
│   │   │   ├── services/
│   │   │   ├── guards/
│   │   │   └── interceptors/
│   │   │
│   │   ├── shared/                  # Componentes reutilizables
│   │   │   ├── components/
│   │   │   ├── directives/
│   │   │   └── pipes/
│   │   │
│   │   ├── features/                # Módulos por funcionalidad
│   │   │   ├── auth/
│   │   │   ├── dashboard/
│   │   │   ├── documents/
│   │   │   ├── expedients/
│   │   │   ├── tasks/
│   │   │   └── settings/
│   │   │
│   │   └── layout/                  # Layout principal
│   │
│   ├── assets/
│   │   └── i18n/                    # Internacionalización
│   │
│   ├── environments/
│   └── styles/
│       ├── _variables.scss
│       └── styles.scss
│
├── angular.json
└── package.json
```

---

## 3. AUTENTICACIÓN

### 3.1 Métodos Soportados

| Método | Descripción | Configurable por Tenant |
|--------|-------------|-------------------------|
| **Local** | Usuario + Contraseña (BD local del tenant) | ✅ Sí |
| **ClaveUnica** | OIDC con ChileAutentica (requiere RUN) | ✅ Sí |
| **MFA (Doble Factor)** | TOTP (Google Authenticator, Authy) | ✅ Sí |
| **SSO Corporativo** | SAML 2.0 / OIDC externo | ✅ Sí |

### 3.2 Flujo de Autenticación

```
1. LOGIN
   ├── Local (usuario/password)
   └── ClaveUnica (OIDC)

2. MFA (si está habilitado)
   ├── TOTP (código de 6 dígitos)
   └── Código de respaldo autentificación

3. SELECCIÓN DE CARGO (si corresponde)
   └── Elegir con qué rol trabajar
```

### 3.3 Configuración por Organización

Cada tenant puede configurar:
- ✅ Habilitar/deshabilitar cada método de auth
- ✅ Forzar MFA para todos los usuarios
- ✅ Timeout de sesión
- ✅ Políticas de contraseña
- ✅ SSO externo (SAML/OIDC)
- ✅ Roles y permisos
- ✅ Correlativos, formatos de doc.
- ✅ Niveles o deptos, subdepto, subrogante y permisos
- ✅ Permiso de derivación

---

## 4. MÓDULOS DEL SISTEMA

### 4.1 Módulos Principales

| Módulo | Descripción | Estado |
|--------|-------------|--------|
| **Dashboard** | KPIs, gráficos, accesos rápidos | Demo |
| **Bandeja de Entrada** | Documentos recibidos, derivados. Incluye sub-bandejas: | Demo |
| Bandeja de email | Bandeja de email/leer documentos | Demo |
| Bandeja DocDigital del Gob | Documentos DocDigital del Gobierno | Demo |
| Bandeja de Facturas | Bandeja de facturas recibidas | Demo |
| Bandeja de Órdenes de Compra | Bandeja de órdenes de compra | Demo |
| **Expedientes** | Gestión de carpetas de documentos | Demo |
| **Registro de Documentos** | CRUD completo de documentos | Demo |
| **Tareas** | Gestión de tareas asignadas | Demo |
| **Consultas** | Búsqueda avanzada de docs/tareas | Demo |
| **Reportes** | Gráficos y estadísticas | Demo |
| **Archivadores** | Gestión física de documentos | Demo |
| **Administración** | Configuración del sistema | Por hacer |

### 4.2 Flujo de Documentos

Todo documento puede seguir uno de estos 3 flujos:

| Flujo | Descripción | Ejemplos |
|-------|-------------|----------|
| **LLEGAN** | Documentos que llegan desde el exterior | Recibido, Ordinario externo |
| **CIRCULAN** | Documentos que circulan solo internamente (nunca salen) | Memo, Despacho, Circular |
| **SALEN** | Documentos que se envían al exterior | Oficio, Resolución, Ordinario |

```
                    ┌─────────────────────────┐
                    │   REGISTRO DOCUMENTO    │
                    └───────────┬─────────────┘
                                │
      ┌─────────────────────────┼─────────────────────────┐
      │                         │                         │
      ▼                         ▼                         ▼
┌─────────────┐      ┌─────────────────┐      ┌─────────────┐
│    LLEGAN   │      │   CIRCULAN      │      │    SALEN    │
│ (Exterior → │      │   (Interno →    │      │ (Interior → │
│  Interior)  │      │    Interno)     │      │  Exterior)  │
│             │      │                 │      │             │
│ - Recibido  │      │ - Memo          │      │ - Oficio    │
│ - Partes    │      │ - Despacho      │      │ - Resolución│
│ - Externos  │      │ - Circular      │      │ - Ordinario │
│             │      │ - Informe        │      │ - Carta     │
└─────────────┘      └─────────────────┘      └─────────────┘
```

### 4.3 Tipos de Documentos (Configurables)

Los tipos de documentos son **100% configurables**. Por defecto se incluyen:

| Tipo | Categoría | Requiere Firma | Requiere Cite |
|------|-----------|----------------|---------------|
| Ordinario | LLEGAN/SALEN | ✅ | ✅ |
| Memo | CIRCULAN | ❌ | ✅ |
| Oficio | SALEN | ✅ | ✅ |
| Despacho | CIRCULAN | ✅ | ✅ |
| Circular | CIRCULAN | ✅ | ✅ |
| Resolución | SALEN | ✅ | ✅ |

### 4.4 Creación de Documentos

```
┌─────────────────────────────────────────────────────────────┐
│              CREAR NUEVO DOCUMENTO                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│ Tipo Documento:  [Ordinario ▼]                            │
│                                                             │
│ Remitente:      [________________________]                 │
│ Destinatario:   [________________________]                 │
│                                                             │
│ N° Cite:        [____-____]    Fecha: [DD/MM/AAAA]        │
│                                                             │
│ Materia:        [________________________________]         │
│                 [________________________________]         │
│                                                             │
│ ─────────────────────────────────────────────────────     │
│ ADJUNTOS                                                   │
│                                                             │
│ [Seleccionar archivos...] [+ Subir]                       │
│                                                             │
│ 📎 documento.pdf (2.4 MB)                    [✕]          │
│ 📎 anexo.docx (500 KB)                      [✕]          │
│                                                             │
│ [ ] Ejecutar OCR en imágenes adjuntas                      │
│                                                             │
│ ─────────────────────────────────────────────────────     │
│                     [Crear] [Cancelar]                     │
└─────────────────────────────────────────────────────────────┘
```

### 4.5 OCR de Imágenes

| Característica | Descripción |
|----------------|-------------|
| **Input** | Imágenes (JPG, PNG, TIFF, PDF escaneado) |
| **Proceso** | Detección de texto con Tesseract/Azure Vision |
| **Output** | Texto extraído + PDF con capa de texto |
| **Idiomas** | Español (predeterminado), inglés |
| **Almacenamiento** | Guardar imagen original + texto extraído |

---

## 5. MÓDULO DE ADMINISTRACIÓN / CONFIGURACIÓN

Este módulo contiene toda la parametrización del sistema:

### 5.1 Configuraciones Disponibles

| Configuración | Descripción |
|---------------|-------------|
| **Organizaciones** | Gestión de tenants |
| **Correlativos** | Numeración por tipo de documento |
| **Plantillas** | Plantillas de documentos |
| **Tipos de Documento** | Tipos configurables |
| **Estados** | Estados de documentos |
| **Unidades** | Unidades organizacionales |
| **Tipos de Derivación** | Tipos de derivación |
| **Causales** | Causales de devolución |
| **Variables** | Variables del sistema |
| **Parámetros SIS** | Parámetros del sistema |
| **Usuarios** | Gestión de usuarios |
| **Perfiles** | Roles y permisos |
| **Theming** | Configuración visual |
| **Auth** | Configuración de autenticación |

### 5.2 Theming Dinámico

Cada organización puede personalizar:

| Aspecto | Configurable |
|---------|---------------|
| **Color primario** | Color picker, se generan: hover, active, light, dark |
| **Logo** | Upload de imagen, se muestra en sidebar y login |
| **Nombre del sistema** | Reemplaza "DocFlow" en header y login |
| **Modo sidebar** | Dark (default), Light, Color (usa el primario como fondo) |
| **Favicon** | Upload o generación automática desde logo |

### Presets de Ejemplo

| Preset | Color Primario | Uso |
|--------|---------------|-----|
| DocFlow (default) | Azul `#1a56db` | Default del producto |
| Villarrica | Verde `#166534` | Municipalidad de Villarrica |
| Lampa | Violeta `#7c3aed` | Municipalidad de Lampa |
| Las Condes | Rojo `#b91c1c` | Municipalidad de Las Condes |

---

## 6. API PÚBLICA

### 6.1 Endpoints Públicos (Sin autenticación JWT)

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| POST | `/api/v1/auth/login` | Autenticación usuario/password |
| POST | `/api/v1/auth/claveunica` | Autenticación con ClaveUnica |
| GET | `/api/v1/public/{tenant}/documents/{id}/download` | Descarga documento (con token temporal) |
| GET | `/api/v1/public/{tenant}/documents/{id}/qr` | Generar QR del documento |
| POST | `/api/v1/public/{tenant}/receive` | Receptor externo (para otras instituciones) |

### 6.2 Endpoints Protegidos (Requiere JWT)

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| **Documents** | | |
| GET | `/api/v1/documents` | Listar documentos |
| POST | `/api/v1/documents` | Crear documento |
| GET | `/api/v1/documents/{id}` | Obtener documento |
| PUT | `/api/v1/documents/{id}` | Actualizar documento |
| DELETE | `/api/v1/documents/{id}` | Eliminar documento |
| POST | `/api/v1/documents/{id}/derive` | Derivar documento |
| POST | `/api/v1/documents/{id}/sign` | Firmar documento |
| **Expedients** | | |
| GET | `/api/v1/expedients` | Listar expedientes |
| POST | `/api/v1/expedients` | Crear expediente |
| GET | `/api/v1/expedients/{id}` | Obtener expediente |
| **Tasks** | | |
| GET | `/api/v1/tasks` | Listar tareas |
| POST | `/api/v1/tasks/{id}/execute` | Ejecutar tarea |
| **Search** | | |
| GET | `/api/v1/search/documents` | Buscar documentos |
| GET | `/api/v1/search/expedients` | Buscar expedientes |

### 6.3 Formato de Respuesta Estándar

```json
{
  "success": true,
  "data": { ... },
  "meta": {
    "page": 1,
    "pageSize": 20,
    "total": 150,
    "timestamp": "2026-04-07T10:30:00Z"
  }
}

{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "El campo 'materia' es requerido",
    "details": [
      { "field": "materia", "message": "Requerido" }
    ]
  }
}
```

---

## 7. DISEÑO UI — DESIGN SYSTEM

### 7.1 Estilo Elegido: Corporate Clean

**Razón**: Profesional, transmite confianza institucional, familiar para usuarios de gobierno acostumbrados a Microsoft Office.

Características clave:
- **Sidebar** colapsable, modo dark por defecto
- **Densidad compacta** — más información visible sin scroll
- **Tipografía**: Inter o similar sans-serif, pesos 400/500/600/700
- **Iconografía**: Outlined, línea fina, estilo Lucide/Heroicons
- **Layout**: Sidebar izquierda + área de contenido con header breadcrumb

### 7.2 Tokens de Diseño

#### Colores

| Token | Valor Default | Uso |
|-------|---------------|-----|
| `--primary` | `#1a56db` | Botones principales, links, acentos |
| `--primary-light` | `#e8effc` | Fondos de highlight |
| `--primary-dark` | `#1240a8` | Hover states |
| `--success` | `#0d9f6e` | Estados exitosos |
| `--warning` | `#e3a008` | Warnings, pendientes |
| `--danger` | `#e02424` | Errores, urgencia |
| `--info` | `#3f83f8` | Información |
| `--gray-50` a `--gray-900` | escala | Fondos, textos, bordes |

#### Tipografía

- **Familia**: Inter, -apple-system, BlinkMacSystemFont, Segoe UI, Roboto
- **Tamaños**: 12px (small), 14px (body), 16px (section title), 18px (page title), 24px (h1)
- **Pesos**: 400 (regular), 500 (medium), 600 (semibold), 700 (bold)

#### Espaciado

| Token | Valor |
|-------|-------|
| `--sidebar-width` | `260px` |
| `--sidebar-collapsed` | `72px` |
| `--header-height` | `64px` |
| `--content-padding` | `24px` |

### 7.3 Demos UI Creados (16)

| # | Archivo | Descripción |
|---|---------|-------------|
| 01-05 | Estilos base | Layout, tipografía, colores, spacing, theming |
| 06 | Login + Cargo | Autenticación y selección de rol |
| 07 | Dashboard | Panel principal básico |
| 08 | Bandeja | Bandeja con tabs y acordeón |
| 09 | Documento | Crear/ver/editar documento |
| 10 | Expedientes | Gestión de expedientes |
| 11 | Dashboard KPIs | Dashboard con 4 indicadores |
| 12 | Dashboard Premium | Dashboard premium |
| 13 | Consulta Docs | Búsqueda avanzada |
| 14 | Consulta Tareas | Gestión de tareas |
| 15 | Archivadores | Gestión física |
| 16 | Reportes | Gráficos y estadísticas |

---

## 8. INTEGRACIONES

### 8.1 ClaveUnica (Gobierno de Chile)

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Usuario    │────►│  DocFlow     │────►│ ClaveUnica   │
│              │     │  (OIDC)      │     │  (IDP)       │
└──────────────┘     └──────────────┘     └──────────────┘
```

- **Protocolo**: OpenID Connect
- **Auth endpoint**: `https://accounts.claveunica.gob.cl/authorize`
- **Token endpoint**: `https://accounts.claveunica.gob.cl/token`
- **Scope**: `openid run run identifiers`

### 8.2 FirmaDigital (FirmaGob - Chile)

- Integración con servicio de firma digital
- Endpoint: `POST /api/v1/signatures/sign`
- Soporte para firma de documentos PDF

### 8.3 QR de Documento

- Generación de QR con hash del documento
- QR contiene: URL pública de verificación
- Verificación: `GET /api/v1/public/{tenant}/verify/{qrHash}`

---

## 9. SEGURIDAD

### 9.1 Autenticación

- **JWT** con access token (15 min) + refresh token (7 días)
- **ClaveUnica** vía OpenID Connect (gobierno Chileno)
- **Password**: Hash con bcrypt, política de complejidad

### 9.2 Autorización

- **RBAC** (Role-Based Access Control)
- **Permisos granulares** por documento/expediente
- Claims en JWT para validación rápida

### 9.3 Protección

| Medida | Detalle |
|--------|---------|
| **HTTPS** | TLS 1.3 obligatorio |
| **CORS** | Whitelist de orígenes permitidos |
| **Rate Limiting** | 100 req/min por usuario |
| **Input Validation** | FluentValidation + Data Annotations |
| **SQL Injection** | EF Core parameterized queries |
| **XSS** | Angular built-in sanitization |
| **CSRF** | Anti-forgery tokens |

---

## 10. ACCESSIBILIDAD (WCAG 2.1 AA)

### Criterios Prioritarios

| Criterio | Implementación |
|----------|----------------|
| **1.3.1** Información y relaciones | Semántica HTML correcta |
| **1.4.3** Contraste mínimo | 4.5:1 para texto normal |
| **2.1.1** Teclado | Toda funcionalidad accesible por teclado |
| **2.4.1** Saltar bloques | Link "Saltar al contenido principal" |
| **2.4.7** Focus visible | Outline visible en todo elemento |
| **3.3.1** Identificación de errores | Mensajes claros y específicos |
| **4.1.2** Nombre, rol, valor | ARIA para componentes complejos |

---

## 11. PLAN DE TRABAJO

### Fases del Proyecto

| Fase | Duración | Tareas |
|------|-----------|--------|
| **Preparación** | Mes 1 | 4 |
| **Backend Core** | Meses 2-3 | 7 |
| **Frontend** | Meses 3-5 | 12 |
| **Integración** | Meses 5-6 | 5 |
| **Despliegue** | Meses 6-7 | 5 |
| **Total** | **6-8 meses** | **21** |

### Cronograma Visual

```
Mes:    1    2    3    4    5    6    7    8
        |----|----|----|----|----|----|----|
Fase 1: [██████████████████████]
Fase 2:       [████████████████████████████████████]
Fase 3:            [████████████████████████████████████████████████████]
Fase 4:                              [████████████]
Fase 5:                                    [████████████]
```

---

## 12. PRESUPUESTO

| Concepto | Costo Estimado |
|----------|----------------|
| Desarrollo (3 devs x 6 meses) | $45.000.000 CLP |
| Licencias y herramientas | $5.000.000 CLP |
| Infraestructura (cloud) | $8.000.000 CLP |
| Contingencia (20%) | $11.600.000 CLP |
| **Total** | **$69.600.000 CLP** |

---

## 13. MÉTRICAS DE ÉXITO

- [ ] Sistema operativo en producción
- [ ] 100% funcionalidades implementadas
- [ ] Cobertura de tests > 80%
- [ ] Tiempo de respuesta < 2s
- [ ] Usuario satisfecho (UAT)
- [ ] Ganar al menos 1 licitación
- [ ] Sistema configurable por cada organización

---

## 14. RIESGOS Y MITIGACIONES

| Riesgo | Probabilidad | Impacto | Mitigación |
|--------|--------------|---------|-------------|
| Retrasos en desarrollo | Media | Alto | Buffer en cronograma |
| Cambios en requisitos | Alta | Medio | Flexible scope |
| Problemas de integración | Baja | Alto | Testing continuo |
| Issues de rendimiento | Baja | Alto | Optimización temprana |
| OCR con precisión variable | Media | Medio | Validación manual |

---

## 15. PRÓXIMOS PASOS INMEDIATOS

1. Revisar y aprobar plan de trabajo
2. Asignar equipo de desarrollo
3. Iniciar Fase 1: Preparación
4. Configurar repositorio y entorno
5. Comenzar análisis de requisitos

---

## 16. DOCUMENTACIÓN ENTREGABLE

### Documentos Creados

| Documento | Descripción |
|----------|-------------|
| Informe_Ejecutivo | Resumen ejecutivo completo |
| Plan_Trabajo | Plan con 21 tareas |
| Especificaciones_Tecnicas | Stack y arquitectura |
| Analisis_Screenshots | Análisis del sistema v2 |
| Analisis_Reutilizacion | Componentes reutilizables |
| Design_System | Sistema de diseño UI |
| Decisiones_UI | Decisiones de diseño |
| Accesibilidad | Normas WCAG 2.1 AA |
| Micro_interacciones | Animaciones |
| Informe_Detallado_Sistema | Este documento |

### Demos HTML

16 prototipos interactivos en `contexto/demos/`

---

*Documento consolidado — Abril 2026*  
*Proyecto: DocFlow v3 - Sistema de Gestión Documental*  
*Producto genérico, configurable y adaptable para cualquier organización*