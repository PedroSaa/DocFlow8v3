# ESPECIFICACIONES TECNICAS — DocFlow8v3

**Fecha**: Abril 2026  
**Estado**: En desarrollo  
**Proyecto**: DocFlow8v3 - Sistema de Gestion Documental Municipal

---

## 1. ARQUITECTURA GENERAL

### 1.1 Stack Tecnológico

| Capa | Tecnologia | Version |
|------|------------|---------|
| **Backend** | .NET | 8.0 |
| **Framework** | ASP.NET Core | 8.0 |
| **ORM** | Entity Framework Core | 8.0 |
| **Frontend** | Angular | 17+ |
| **UI Components** | Angular Material + Custom | — |
| **Base de datos** | SQL Server | 2019+ |
| **Autenticacion** | JWT + Identity | — |
| **API** | RESTful + OpenAPI | — |

### 1.2 Modelo de Despliegue

```
┌─────────────────────────────────────────────────────────────┐
│                     ON-PREMISE                              │
│                                                             │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐   │
│  │   Frontend  │    │   Backend   │    │  SQL Server │   │
│  │  (Angular)  │◄──►│  (.NET 8)   │◄──►│   (DB)      │   │
│  │   :4200     │    │   :5000     │    │   :1433     │   │
│  └─────────────┘    └─────────────┘    └─────────────┘   │
│          │                  │                               │
│          └──────────────────┴───────────────────────────────│
│                        NGINX / IIS                         │
│                   (Reverse Proxy + SSL)                    │
└─────────────────────────────────────────────────────────────┘
```

### 1.3 Requisitos de Infraestructura

| Componente | Requerimiento Minimo |
|------------|---------------------|
| **CPU** | 4 nucleos (x86_64) |
| **RAM** | 16 GB |
| **Disco** | 500 GB SSD |
| **OS** | Windows Server 2019+ / Linux (Ubuntu 22.04) |
| **Web Server** | IIS (Windows) o NGINX (Linux) |

---

## 2. ARQUITECTURA MULTI-TENANT

### 2.1 Estrategia: Database per Tenant

Cada municipalidad tendra su propia base de datos, aislada y segura.

```
┌─────────────────────────────────────────────────────────────┐
│                    SERVIDOR COMPARTIDO                      │
│                                                             │
│  ┌──────────────────────────────────────────────────────┐  │
│  │              Base de datos Central                    │  │
│  │  (Tenants, Users, Licenses, Configuration)            │  │
│  └──────────────────────────────────────────────────────┘  │
│                           │                                 │
│           ┌───────────────┼───────────────┐                │
│           │               │               │                 │
│           ▼               ▼               ▼                │
│  ┌────────────┐   ┌────────────┐   ┌────────────┐        │
│  │ DB         │   │ DB         │   │ DB         │        │
│  │ Villarrica │   │ Lampa      │   │ Las Condes │        │
│  │ (tenant_1) │   │ (tenant_2) │   │ (tenant_3) │        │
│  └────────────┘   └────────────┘   └────────────┘        │
└─────────────────────────────────────────────────────────────┘
```

### 2.2 Tablas Base de Datos Central (Master)

```sql
-- Tenants (Municipalidades)
Tenants
├── Id (GUID, PK)
├── Name (string)          -- "Municipalidad de Villarrica"
├── Alias (string)         -- "villarrica"
├── SchemaName (string)    -- "tenant_villarrica"
├── PrimaryColor (string)  -- "#166534"
├── LogoUrl (string)
├── SystemName (string)    -- "SGD Villarrica"
├── SidebarMode (enum)     -- dark/light/color
├── IsActive (bool)
├── CreatedAt (datetime)
└── LicenseExpiration (date)

-- Users (Users across all tenants)
Users
├── Id (GUID, PK)
├── Email (string, unique)
├── PasswordHash (string)
├── ClaveUnicaId (string, nullable)  -- Para autenticacion CU
├── FullName (string)
├── IsSuperAdmin (bool)               -- Admin global
├── IsActive (bool)
├── CreatedAt (datetime)
└── LastLoginAt (datetime)

-- UserTenants (vinculacion user-tenant)
UserTenants
├── UserId (FK)
├── TenantId (FK)
├── RoleId (FK)
├── IsDefault (bool)    -- Para seleccion de cargo por defecto
└── LastUsedAt (datetime)

-- Roles
Roles
├── Id (GUID, PK)
├── TenantId (FK, nullable)  -- Si es null, es rol global
├── Name (string)            -- "Administrador", "Usuario", "Jefe Depto"
├── Permissions (string)     -- JSON con permisos
└── IsDefault (bool)

-- Licenses
Licenses
├── Id (GUID, PK)
├── TenantId (FK)
├── PlanType (enum)         -- basic/premium/enterprise
├── MaxUsers (int)
├── MaxStorageGB (int)
├── StartDate (date)
├── EndDate (date)
├── IsActive (bool)
└── Price (decimal)
```

### 2.3 Configuracion de Conexion (Connection String per Request)

```csharp
// Middleware que obtiene el tenant desde el request
public class TenantMiddleware {
    public async Task InvokeAsync(HttpContext context) {
        // Obtener tenantId desde:
        // 1. Subdomain (villarrica.docflow.cl)
        // 2. Header X-Tenant-Id
        // 3. JWT claim
        
        var tenantId = GetTenantId(context);
        var connectionString = await _tenantService.GetConnectionString(tenantId);
        
        // Guardar en contexto para uso en servicios
        context.Items["TenantConnection"] = connectionString;
    }
}
```

---

## 3. API PUBLICA

### 3.1 Endpoints Publicos (Sin autenticacion JWT)

| Metodo | Endpoint | Descripcion |
|--------|----------|-------------|
| POST | `/api/v1/auth/login` | Autenticacion usuario/password |
| POST | `/api/v1/auth/claveunica` | Autenticacion con ClaveUnica |
| GET | `/api/v1/public/{tenant}/documents/{id}/download` | Descarga documento (con token temporal) |
| GET | `/api/v1/public/{tenant}/documents/{id}/qr` | Generar QR del documento |
| POST | `/api/v1/public/{tenant}/receive` | Receptor externo (para otras instituciones) |

### 3.2 Endpoints Protegidos (Requiere JWT)

| Metodo | Endpoint | Descripcion |
|--------|----------|-------------|
| **Documents** |||
| GET | `/api/v1/documents` | Listar documentos |
| POST | `/api/v1/documents` | Crear documento |
| GET | `/api/v1/documents/{id}` | Obtener documento |
| PUT | `/api/v1/documents/{id}` | Actualizar documento |
| DELETE | `/api/v1/documents/{id}` | Eliminar documento |
| POST | `/api/v1/documents/{id}/derive` | Derivar documento |
| POST | `/api/v1/documents/{id}/sign` | Firmar documento |
| **Expedients** |||
| GET | `/api/v1/expedients` | Listar expedientes |
| POST | `/api/v1/expedients` | Crear expediente |
| GET | `/api/v1/expedients/{id}` | Obtener expediente |
| **Tasks** |||
| GET | `/api/v1/tasks` | Listar tareas |
| POST | `/api/v1/tasks/{id}/execute` | Ejecutar tarea |
| **Search** |||
| GET | `/api/v1/search/documents` | Buscar documentos |
| GET | `/api/v1/search/expedients` | Buscar expedientes |

### 3.3 Formato de Respuesta Estandar

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

### 3.4 Versionado de API

- Version en URL: `/api/v1/`, `/api/v2/`
- Cada version vigentes minimo 12 meses
- Deprecation con 6 meses de aviso

---

## 4. ESTRUCTURA DEL PROYECTO

### 4.1 Estructura Backend (.NET 8)

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
│   │   │   ├── IDocumentRepository.cs
│   │   │   └── ...
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
│   │   │   ├── DbContext.cs
│   │   │   ├── Migrations/
│   │   │   └── Configurations/
│   │   ├── Repositories/
│   │   └── Auth/
│   │
│   └── DocFlow.Api/               # API Controllers, Middleware
│       ├── Controllers/
│       │   ├── AuthController.cs
│       │   ├── DocumentsController.cs
│       │   ├── ExpedientsController.cs
│       │   └── ...
│       ├── Middleware/
│       ├── Filters/
│       ├── Program.cs
│       └── appsettings.json
│
└── tests/
    ├── DocFlow.Core.Tests/
    ├── DocFlow.Application.Tests/
    └── DocFlow.Api.Tests/
```

### 4.2 Estructura Frontend (Angular 17)

```
docflow-frontend/
├── src/
│   ├── app/
│   │   ├── core/                    # Singleton services, guards
│   │   │   ├── services/
│   │   │   │   ├── auth.service.ts
│   │   │   │   ├── tenant.service.ts
│   │   │   │   ├── api.service.ts
│   │   │   │   └── theme.service.ts
│   │   │   ├── guards/
│   │   │   │   ├── auth.guard.ts
│   │   │   │   └── tenant.guard.ts
│   │   │   └── interceptors/
│   │   │       └── auth.interceptor.ts
│   │   │
│   │   ├── shared/                  # Componentes reuseables
│   │   │   ├── components/
│   │   │   │   ├── sidebar/
│   │   │   │   ├── header/
│   │   │   │   ├── data-table/
│   │   │   │   ├── modal/
│   │   │   │   ├── toast/
│   │   │   │   └── ...
│   │   │   ├── directives/
│   │   │   └── pipes/
│   │   │
│   │   ├── features/                # Modulos por funcionalidad
│   │   │   ├── auth/
│   │   │   │   ├── login/
│   │   │   │   └── cargo-selection/
│   │   │   ├── dashboard/
│   │   │   ├── documents/
│   │   │   │   ├── list/
│   │   │   │   ├── create/
│   │   │   │   ├── detail/
│   │   │   │   └── edit/
│   │   │   ├── expedients/
│   │   │   ├── tasks/
│   │   │   └── settings/
│   │   │
│   │   ├── layout/                  # Layout principal
│   │   │   ├── main-layout/
│   │   │   └── auth-layout/
│   │   │
│   │   └── app.component.ts
│   │
│   ├── assets/
│   │   ├── i18n/                    # Internacionalizacion
│   │   │   ├── es.json
│   │   │   └── en.json
│   │   ├── images/
│   │   └── icons/
│   │
│   ├── environments/
│   │   ├── environment.ts
│   │   └── environment.prod.ts
│   │
│   └── styles/
│       ├── _variables.scss         # Design tokens
│       ├── _mixins.scss
│       ├── _typography.scss
│       └── styles.scss
│
├── angular.json
├── package.json
└── tsconfig.json
```

---

## 5. PATRONES DE DISENO

### 5.1 Backend

| Patron | Aplicacion |
|--------|------------|
| **Repository Pattern** | Abstraccion de acceso a datos |
| **Unit of Work** | Transacciones multiples |
| **Mediator/CQRS** | Separacion de commands y queries |
| **Dependency Injection** | Inyeccion de servicios |
| **Factory** | Creacion de entidades complejas |

### 5.2 Frontend

| Patron | Aplicacion |
|--------|------------|
| **Smart/Dumb Components** | Container vs Presentational |
| **Feature Modules** | Modularizacion por funcionalidad |
| **State Management** | NgRx o Signals (Angular 17+) |
| **OnPush Change Detection** | Performance |
| **Lazy Loading** | Carga de modulos bajo demanda |

---

## 6. SEGURIDAD

### 6.1 Autenticacion

- **JWT** con access token (15 min) + refresh token (7 dias)
- **ClaveUnica** via OpenID Connect (gobierno Chileno)
- **Password**: Hash con bcrypt, politica de complejidad

### 6.2 Autorizacion

- **RBAC** (Role-Based Access Control)
- **Permisos granulares** por documento/expediente
- Claims en JWT para validacion rapida

### 6.3 Proteccion

| Medida | Detalle |
|--------|---------|
| **HTTPS** | TLS 1.3 obligatorio |
| **CORS** | Whitelist de origenes permitidos |
| **Rate Limiting** | 100 req/min por usuario |
| **Input Validation** | FluentValidation + Data Annotations |
| **SQL Injection** | EF Core parameterized queries |
| **XSS** | Angular built-in sanitization |
| **CSRF** | Anti-forgery tokens |

---

## 7. INTEGRACIONES

### 7.1 ClaveUnica (Gobierno de Chile)

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Usuario    │────►│  DocFlow     │────►│ ClaveUnica   │
│              │     │  (OIDC)      │     │  (IDP)       │
└──────────────┘     └──────────────┘     └──────────────┘
```

- Protocolo: OpenID Connect
- Auth endpoint: `https://accounts.claveunica.gob.cl/authorize`
- Token endpoint: `https://accounts.claveunica.gob.cl/token`
- Scope: `openid run run identifiers`

### 7.2 FirmaDigital (FirmaGob -chile)

- Integracion con servicio de firma digital
- Endpoint por definir (propuesta: `POST /api/v1/signatures/sign`)
- Soporte para firma de documentos PDF

### 7.3 QR de Documento

- Generacion de QR con hash del documento
- QR contiene: URL publica de verificacion
- Verificacion: `GET /api/v1/public/{tenant}/verify/{qrHash}`

---

## 8. PERFORMANCE

### 8.1 Metas

| Metrica | Target |
|---------|--------|
| **TTFB** | < 200ms |
| **Time to Interactive** | < 3s |
| **API Response** | < 500ms (p95) |
| **DB Queries** | < 100ms (p95) |

### 8.2 Estrategias

- **Caching**: Redis para sesiones y datos frecuentes
- **Pagination**: Siempre paginar listas (> 20 items)
- **Indexing**: Indices en columnas de filtro frecuentes
- **Lazy Loading**: Componentes y modulos bajo demanda
- **Compression**: Gzip/Brotli para respuestas JSON

---

## 9. MONITOREO

### 9.1 Logging

- **Serilog** para .NET (archivo + console)
- **Log levels**: Debug, Information, Warning, Error, Fatal
- **Estructura**: JSON con contexto (tenantId, userId, correlationId)

### 9.2 Health Checks

- `/health` — Estado general del sistema
- `/health/db` — Conexion a base de datos
- `/health/external` — Servicios externos (ClaveUnica, etc.)

---

*Especificaciones tecnicas v1.0 — Abril 2026*
*Proyecto: DocFlow8v3ClaudeCode*