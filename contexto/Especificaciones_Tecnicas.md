# ESPECIFICACIONES TÉCNICAS — DocFlow 8 Infinity

**Fecha**: Abril 2026  
**Estado**: En desarrollo  
**Proyecto**: DocFlow 8 Infinity - Sistema de Gestión Documental Municipal
**Versión**: 8.0 (Infinity)
**Origen**: Basado en DocFlow 6 (manual legacy 2018)

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
| **Autenticacion** | JWT + Identity + MFA + SSO + ClaveUnica | — |
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

## 3. AUTENTICACIÓN MULTI-FACTOR (MFA/SSO)

### 3.1 Métodos de Autenticación Soportados

| Método | Descripción | Configurable por Tenant |
|--------|-------------|-------------------------|
| **Local** | Usuario + Contraseña (BD local del tenant) | ✅ Sí |
| **ClaveUnica** | OIDC con ChileAutentica (requiere RUN) | ✅ Sí |
| **MFA (Doble Factor)** | TOTP (Google Authenticator, Authy) | ✅ Sí |
| **SSO Corporativo** | SAML 2.0 / OIDC externo | ✅ Sí |

### 3.2 Flujo de Autenticación

```
┌─────────────────────────────────────────────────────────────┐
│                    PANTALLA LOGIN                          │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  [Logo Organization]                                 │   │
│  │                                                     │   │
│  │  Usuario: [____________]                             │   │
│  │  Contraseña: [____________]                         │   │
│  │                                                     │   │
│  │  [ ] Recordarme                                     │   │
│  │  [x] Iniciar sesión                                 │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  ¿No tienes cuenta? [Registrarse con ClaveUnica]          │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                VERIFICACIÓN MFA (si está habilitada)       │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  Ingrese código de 6 dígitos                       │   │
│  │  [__] [__] [__] [__] [__] [__]                     │   │
│  │                                                     │   │
│  │  [Reenviar código] | [Usar código de respaldo]     │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                SELECCIÓN DE CARGO (si corresponde)         │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  Seleccione el cargo con el que desea trabajar:      │   │
│  │                                                     │   │
│  │  [🔵] Director de Obras                            │   │
│  │  [🔵] Funcionario Dept. Normativo                  │   │
│  │  [🔵] Admin - Sistemas                              │   │
│  │                                                     │   │
│  │  [x] Recordar esta selección                        │   │
│  │  [Ingresar]                                        │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

### 3.3 Configuración por Tenant

Cada organización puede configurar:
- ✅ Habilitar/deshabilitar cada método de auth
- ✅ Forzar MFA para todos los usuarios
- ✅ Timeout de sesión
- ✅ Políticas de contraseña
- ✅ SSO externo (SAML/OIDC)

### 3.4 Tablas para Configuración de Auth

```sql
-- Configuración de Auth por Tenant
TenantAuthSettings
├── TenantId (FK)
├── EnableLocalLogin (bool)           -- true/false
├── EnableClaveUnica (bool)           -- true/false
├── EnableMFA (bool)                  -- true/false
├── ForceMFA (bool)                   -- true/false
├── SSOProvider (string, nullable)   -- "azuread", "okta", "google"
├── SSOConfig (json)                 -- Configuración SSO
├── SessionTimeoutMinutes (int)       -- 30, 60, 120, etc.
├── PasswordMinLength (int)           -- 8, 10, 12
├── PasswordRequireUppercase (bool)
├── PasswordRequireNumber (bool)
├── PasswordRequireSpecial (bool)
├── PasswordHistoryCount (int)        -- No reutilizar últimas X contraseñas
├── MaxLoginAttempts (int)            -- Bloquear tras X intentos
├── LockoutMinutes (int)              -- Tiempo de bloqueo
└── CreatedAt (datetime)

-- Códigos MFA temporales
MFACodes
├── Id (GUID, PK)
├── UserId (FK)
├── Code (string)                    -- Código TOTP o backup
├── CodeType (enum)                  -- totp, backup
├── IsUsed (bool)
├── ExpiresAt (datetime)
└── CreatedAt (datetime)
```

---

## 4. MÓDULOS DEL SISTEMA

### 4.1 Módulos Principales (Aplicación)

| Módulo | Descripción | CRUD |
|--------|-------------|------|
| **Dashboard** | KPIs, gráficos, accesos rápidos | Lectura |
| **Bandeja de Entrada** | Documentos recibidos, derivados, pendientes de acción | Lectura/Escritura |
| **Expedientes** | Gestión de carpetas de documentos agrupados por proyecto/hito | Completo |
| **Archivadores** | Organización física de documentos (año/mes/caja) | Completo |
| **Documentos** | Crear/editar/ver documentos con editor integrado | Completo |
| **Tareas** | Gestión de tareas asignadas | Completo |
| **Consultas** | Búsqueda avanzada de documentos y tareas | Lectura |
| **Reportes** | Gráficos, estadísticas y auditoría | Lectura |
| **Administración** | Configuración del sistema | Completo |

### 4.2 Módulos Especiales (del Manual Legacy)

| Módulo | Descripción | Funcionalidad |
|--------|-------------|---------------|
| **Editor de Documentos** | Editor de texto para redactar documentos | Crear documentos internos (Memo, Circular, Despacho) |
| **Firma Digital** | Firma electrónica avanzada (FirmaGob) | Firma en editor → convierte a PDF certificado con registro |
| **V° Bueno (Aprobaciones)** | Workflow de aprobación jerárquica | Revisor → Aprobador → Firmante |
| **Oficina de Partes** | Gestión de despacho exterior | Único módulo autorizado para enviar documentos fuera de la organización |
| **Auditoría** | Trazabilidad completa | Registro de todas las acciones sobre documentos |

### 4.3 Acciones sobre Documentos

Del manual legacy, las acciones disponibles en la Bandeja de Entrada:

| Acción | Descripción |
|--------|-------------|
| **Recibir** | Aceptar un documento derivado |
| **Derivar** | Enviar documento a otro usuario/unidad |
| **Reenviar** | Reenviar a otra persona manteniendo historial |
| **Devolver** | Devolver al remitente original |
| **Archivar** | Mover a archivador físico |
| **Anular** | Cancelar documento (solo admin) |
| **Destacar** | Marcar como importante/urgente |
| **Firmar** | Aplicar firma digital (PDF certificado) |

### 4.2 Tipos de Documentos (Configurables por Organización)

Los siguientes tipos son los **tipos base del sistema** (del manual legacy) — cada organización puede crear, modificar o eliminar tipos según sus necesidades:

#### Tipos de Documentos (Base del Sistema)

| Tipo | Código | Categoría | Descripción | Requiere Firma |
|------|--------|-----------|-------------|----------------|
| **Ordinario** | ORD | SALEN | Documentos oficiales externos | ✅ Sí |
| **Memo** | MEM | CIRCULAN | Comunicación interna | ❌ No |
| **Oficio** | OFI | SALEN | Comunicaciones formales externas | ✅ Sí |
| **Despacho** | DES | CIRCULAN | Comunicaciones desde Alcaldía | ✅ Sí |
| **Circular** | CIR | CIRCULAN | Comunicados generales internos | ❌ No |
| **Resolución** | RES | SALEN | Decisiones administrativas | ✅ Sí |
| **Recibido** | REC | LLEGAN | Documentos externos recibidos | ❌ No |
| **Partes** | PAR | LLEGAN | Documentos registrados en Oficina de Partes | ❌ No |
| **Informe** | INF | CIRCULAN | Informes técnicos | ✅ Sí |
| **Carta** | CAR | SALEN | Comunicaciones formales | ✅ Sí |

> **Nota**: Los tipos de documentos son **100% configurables**. Cada organización puede definir sus propios tipos y asociarlos a los flujos necesarios.

#### Flujo de Documentos

Todo documento sigue uno de estos 3 flujos, según su origen y destino:

```
                    ┌─────────────────────────┐
                    │   REGISTRO DOCUMENTO    │
                    └───────────┬─────────────┘
                                │
           ┌─────────────────────┼─────────────────────┐
           │                     │                     │
           ▼                     ▼                     ▼
┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│    LLEGAN       │  │   CIRCULAN     │  │     SALEN       │
│   (Exterior →   │  │   (Interno →   │  │   (Interior →  │
│    Interior)    │  │    Interno)    │  │    Exterior)    │
│                 │  │                 │  │                 │
│ - Recibido      │  │ - Memo          │  │ - Oficio        │
│ - Partes        │  │ - Despacho      │  │ - Resolución   │
│ - Externos      │  │ - Circular      │  │ - Ordinario    │
│                 │  │ - Informe       │  │ - Carta         │
│                 │  │                 │  │                 │
│ [llegan a la    │  │ [circulan solo  │  │ [se envían al   │
│  organización]  │  │  dentro]       │  │   exterior]     │
└─────────────────┘  └─────────────────┘  └─────────────────┘
```

#### Flujo de Documento de SALIDA (del Manual Legacy)

Cuando un documento "SALEN" (se envía al exterior), sigue este flujo:

```
┌─────────┐    ┌──────────┐    ┌─────────┐    ┌─────────────┐    ┌────────────────┐    ┌─────────────┐
│ Crear   │───►│ Derivar  │───►│ Recibir │───►│ Firmar      │───►│ Oficina de    │───►│ Despacho    │
│         │    │ (tramite │    │         │    │ (electrónica│    │ Partes        │    │ (físico o   │
│ Documento│    │ interno) │    │         │    │ → PDF certif)│    │               │    │  electrónico)│
└─────────┘    └──────────┘    └─────────┘    └─────────────┘    └────────────────┘    └─────────────┘
                                                                                            
   1              2              3             4                5                   6                    
```

1. **Crear**: Redactar documento en editor
2. **Derivar**: Trámite interno (enviar a revisión/aprobación)
3. **Recibir**: Aceptar en bandeja del receptor
4. **Firmar**: Firmar electrónicamente → se convierte en PDF certificado
5. **Oficina de Partes**: Único punto de despacho exterior
6. **Despacho**: Envío físico o electrónico al destinatario

> **Nota importante**: Solo **Oficina de Partes** puede realizar el despacho al exterior. Esto es un control de seguridad del sistema legacy que se mantiene en DocFlow 8 Infinity.
                    ┌─────────────────────────┐
                    │   REGISTRO DOCUMENTO    │
                    └───────────┬─────────────┘
                                │
          ┌─────────────────────┼─────────────────────┐
          │                     │                     │
          ▼                     ▼                     ▼
┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│    LLEGAN       │  │   CIRCULAN     │  │     SALEN       │
│   (Exterior →   │  │   (Interno →   │  │   (Interior →  │
│    Interior)    │  │    Interno)    │  │    Exterior)   │
│                 │  │                 │  │                 │
│ - Recibido      │  │ - Memo          │  │ - Oficio        │
│ - Partes        │  │ - Despacho      │  │ - Resolución   │
│ - Externos      │  │ - Circular      │  │ - Ordinario    │
│                 │  │ - Informe       │  │ - Carta        │
│                 │  │                 │  │                 │
│ [llegan a la    │  │ [circulan solo  │  │ [se envían al  │
│  organización]  │  │  dentro]        │  │   exterior]     │
└─────────────────┘  └─────────────────┘  └─────────────────┘
```

#### Configuración de Tipos por Organización

Cada tenant puede configurar:

| Configuración | Descripción |
|---------------|-------------|
| Nombre | Nombre del tipo |
| Código | Código corto (ej: "MEM", "OFI") |
| Categoría | LLEGAN / CIRCULAN / SALEN |
| Requiere Firma | Si requiere firma digital |
| Requiere Cite | Si requiere número de cite |
| Plantilla | Plantilla asociada |
| Estado Inicial | Estado cuando se crea |
| Color | Color para标识 |

```sql
-- Tabla de configuración de tipos por tenant
DocumentTypes
├── Id (GUID, PK)
├── TenantId (FK)
├── Name (string)              -- "Memo"
├── Code (string)              -- "MEM"
├── Category (enum)            -- LLEGAN, CIRCULAN, SALEN
├── RequiresSignature (bool)   -- false
├── RequiresCite (bool)        -- true
├── TemplateId (FK, nullable)
├── InitialStatusId (FK)
├── Color (string)             -- "#0369a1"
├── IsActive (bool)
└── SortOrder (int)
```

### 4.3 Creación de Documentos

**Formulario de Creación:**

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

### 4.4 OCR de Imágenes

| Característica | Descripción |
|----------------|-------------|
| **Input** | Imágenes (JPG, PNG, TIFF, PDF escaneado) |
| **Proceso** | Detección de texto con Tesseract/Azure Vision |
| **Output** | Texto extraído + PDF con capa de texto |
| **Idiomas** | Español (predeterminado), inglés |
| **Almacenamiento** | Guardar imagen original + texto extraído |

### 4.5 Editor de Documentos y Firma Digital

#### Editor de Documentos

El editor de texto integrado permite:
- Redactar documentos internos (Memo, Circular, Despacho, Informe)
- Formateo básico (negrita, cursiva, listas, tablas)
- Insertar variables dinámicas (fecha, número de cite, remitente)
- Vista previa antes de guardar
- Conversión automática a PDF para firma

#### Firma Digital (FirmaGob - Chile)

Del manual legacy, el sistema de firma electrónica funciona así:

1. **Firma en Editor**: El usuario firma el documento dentro del editor de texto
2. **Conversión a PDF**: El documento firmado se convierte en PDF certificado
3. **Certificación**: El PDF incluye certificación digital que garantiza:
   - Integridad del documento (no fue modificado después de firmado)
   - Identidad del firmante
   - Fecha y hora de la firma
4. **Registro**: Cada firma queda registrada en el sistema de auditoría

```
┌─────────────┐    ┌──────────────┐    ┌───────────────┐    ┌─────────────┐
│   Firmar    │───►│  Convertir   │───►│   Certificar  │───►│  Registrar  │
│  (Editor)   │    │  a PDF       │    │  (FirmaGob)   │    │  (Auditoría)│
└─────────────┘    └──────────────┘    └───────────────┘    └─────────────┘
```

**Tabla de Registro de Firmas** (nueva):
```sql
DocumentSignatures
├── Id (GUID, PK)
├── DocumentId (FK)
├── UserId (FK)
├── SignedAt (datetime)
├── SignatureHash (string)      -- Hash único de la firma
├── CertificateInfo (json)       -- Info del certificado
├── IpAddress (string)
└── SignatureType (enum)         -- electronic, approved, initials
```

### 4.6 Módulo de Administración (Configuración General)

Este módulo es exclusivo para administradores del sistema y contiene toda la parametrización configurable:

```
┌─────────────────────────────────────────────────────────────┐
│                 ADMINISTRACIÓN / CONFIGURACIÓN             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐           │
│ │ Organizacio-│ │   Correlati-│ │  Plantillas │           │
│ │    nes      │ │    vos      │ │             │           │
│ └─────────────┘ └─────────────┘ └─────────────┘           │
│                                                             │
│ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐           │
│ │    Tipos    │ │   Estados   │ │  Unidades   │           │
│ │  Documento  │ │             │ │             │           │
│ └─────────────┘ └─────────────┘ └─────────────┘           │
│                                                             │
│ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐           │
│ │  Tipos Deri-│ │  Causales   │ │  Variables  │           │
│ │   vación    │ │             │ │  del Sistema│           │
│ └─────────────┘ └─────────────┘ └─────────────┘           │
│                                                             │
│ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐           │
│ │   Parametros│ │   Usuarios  │ │   Perfiles  │           │
│ │   del SIS   │ │             │ │             │           │
│ └─────────────┘ └─────────────┘ └─────────────┘           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

#### 4.6.1 Organizaciones (Tenants)

| Campo | Descripción |
|-------|-------------|
| Nombre | Nombre completo de la organización |
| Alias | Identificador corto (URL) |
| Logo | Imagen del logo |
| Color Primario | Color principal (para theming) |
| Color Secundario | Color secundario |
| Sistema Nombre | Nombre que aparece en el login |
| Sidebar Mode | dark / light / color |
| Estado | Activo/Inactivo |

#### 4.6.2 Correlativos

| Campo | Descripción |
|-------|-------------|
| Tipo Documento | Ordinario, Memo, Oficio, etc. |
| Prefijo | Prefijo del correlativo (ej: "ORD-") |
| Correlativo Actual | Número actual |
| Año | Año del correlativo |
| Formato | Personalizable (ej: {PREFIJO}{AÑO}-{NUMERO}) |

#### 4.6.3 Plantillas

| Campo | Descripción |
|-------|-------------|
| Nombre | Nombre de la plantilla |
| Tipo Documento | Tipo al que aplica |
| Plantilla | Contenido (HTML/texto con variables) |
| Variables | Lista de variables disponibles |

#### 4.6.4 Theming Dinámico

Cada organización puede configurar su propia apariencia:

```sql
TenantThemeSettings
├── TenantId (FK)
├── PrimaryColor (string)          -- Color principal
├── SecondaryColor (string)       -- Color secundario
├── AccentColor (string)          -- Color de énfasis
├── LogoUrl (string)              -- URL del logo
├── FaviconUrl (string)           -- URL del favicon
├── SidebarBackground (string)    -- Fondo del sidebar
├── SidebarTextColor (string)    -- Color del texto sidebar
├── HeaderBackground (string)     -- Fondo del header
├── FontFamily (string)           -- Familia tipográfica
├── CustomCSS (text)              -- CSS personalizado
└── Variables (json)              -- Variables CSS personalizadas
```

### 4.7 Bandeja de Entrada

La Bandeja de Entrada muestra los documentos recibidos según filtros de fecha. Es el punto central de acción sobre documentos.

#### Filtros Disponibles
| Filtro | Descripción |
|--------|-------------|
| **Fechas** | Rango de fechas (filtro inicial) |
| **Tipo de Documento** | Ordinario, Memo, Oficio, etc. |
| **Número** | Número correlativo del documento |
| **Materia** | Contenido/asunto del documento |
| **Correlativo** | Código único del documento |
| **Otros filtros** | Ocultos, expandibles según necesidad |

#### Acciones sobre Documentos (Bandeja)
| Acción | Descripción |
|--------|-------------|
| **Recibir** | Aceptar documento derivado (cambia estado a Recibido) |
| **Devolver** | Devolver al remitente (solo si estado = No Recibido) |
| **Anular** | Cancelar documento (solo si estado = Ingresado) |
| **Destacar** | Marcar como importante para atención permanente |
| **Archivar** | Asociar a un archivador físico |

#### Indicadores de Documento (Columnas T, I, A, R, O)
| Indicador | Significado |
|-----------|--------------|
| **T** | El documento tiene Tareas asociadas |
| **I** | El documento tiene Imagen escaneada |
| **A** | El documento tiene Adjuntos |
| **R** | El documento tiene Referencias |
| **O** | El documento tiene Observaciones |

### 4.8 Expedientes

Los Expedientes permiten agrupar documentos relacionados por proyecto/hito.

#### Funcionalidades de Expedientes
| Funcionalidad | Descripción |
|---------------|-------------|
| **Crear Expediente** | Nombre, número/catalogamiento, descripción detallada |
| **Hitos** | Puntos de control para seguimiento del expediente |
| **Compartir** | Autorizar visualización a otros usuarios |
| **Estados** | Pendiente (inicial), en proceso, cerrado |

> **Restricciones**: No se pueden eliminar expedientes que contengan información ni aquellos donde no se es el autor.

### 4.9 Tareas

Las tareas son acciones asociadas a documentos que requieren seguimiento.

#### Creación de Tareas
| Campo | Descripción |
|-------|-------------|
| **Nivel Responsable** | Departamento/unidad responsable (selección de lista autorizada) |
| **Usuario Ejecutor** | Persona que debe ejecutar la tarea |
| **Glosa de Tarea** | Acción predefinida + observación libre |
| **Fecha de Vencimiento** | Plazo para completar la tarea |
| **Responder con Documento** | Genera documento de respuesta (solo en docs recibidos) |

#### Estados de Tareas
- **Pendiente**: Tarea asignada sin completar
- **Cumplida**: Tarea completada por ejecutor
- **Semicumplida**: Tarea parcialmente completada

> **IMPORTANTE**: Si se crea una tarea y el documento no se envía, la tarea tampoco se envía. Cada tarea va asociada a un documento.

### 4.10 Archivadores

Organización física de documentos en almacenamiento estructurado.

#### Crear Archivador
| Campo | Descripción |
|-------|-------------|
| **Año** | Año del archivador |
| **Mes** | Mes del archivador |
| **Caja** | Número de caja identificador |
| **Descripción** | Descripción del contenido |

#### Estado de Archivado
Cuando un documento se archiva, su estado cambia de "Recibido" a "Archivado", indicando el archivador asociado.

### 4.11 Reportes y Estadísticas

Módulo de generación de informes y estadísticas del sistema.

#### Tipos de Reportes
| Reporte | Descripción |
|---------|-------------|
| **Imprimir por Rango de Correlativos** | Documentos por rango de fechas, tipo y correlativos |
| **Listado Despacho Interno por Depto.** | Hoja de distribución interna |
| **Listado Diario y Mensual por Departamento** | Estadísticas por fecha y departamento |
| **Listado Diario y Mensual por Formato** | Estadísticas por tipo de documento |
| **Imprimir Documentos No Recibidos** | Relación de documentos sin recibir entre fechas |

### 4.12 Flujo Completo de Documento de SALIDA

Resumen del flujo completo de un documento que sale al exterior:

```
┌─────────────┐    ┌──────────┐    ┌─────────┐    ┌─────────────┐    ┌────────────────┐    ┌─────────────┐
│   CREAR     │───►│ DERIVAR  │───►│ RECIBIR │───►│   FIRMAR    │───►│    OFICINA    │───►│  DESPACHO   │
│ Documento   │    │ (trámite │    │         │    │ (electrónica│    │    DE PARTES   │    │  EXTERIOR   │
│ (Editor)   │    │ interno) │    │         │    │ → PDF certif)│    │ (único punto │    │ (físico o    │
│             │    │           │    │         │    │             │    │  de despacho)│    │  electrónico)│
└─────────────┘    └──────────┘    └─────────┘    └─────────────┘    └────────────────┘    └─────────────┘
      1                2              3               4                  5                    6
```

1. **Crear**: Redactar documento en editor con plantilla
2. **Derivar**: Enviar a revisión/trámite interno
3. **Recibir**: Receptor acepta el documento
4. **Firmar**: Firma electrónica → PDF certificado
5. **Oficina de Partes**: Único módulo autorizado para despacho exterior
6. **Despacho**: Envío físico o electrónico al destinatario

> **Nota importante**: Solo **Oficina de Partes** puede realizar el despacho al exterior. Esto es un control de seguridad del sistema legacy.

### 4.13 Flujos Especializados (Configurables)

Del sistema legacy, existen 9 flujos especializados que pueden configurarse en DocFlow 8 Infinity:

| # | Flujo | Descripcion | Participantes | Cadena V°B° |
|---|-------|-------------|---------------|-------------|
| 1 | **Decreto Seccion 1 y 2 Pagos** | Decretos de pago Seccion 1 y 2 |DAF, TESORERIA | Revisor → Aprobador → Firmante |
| 2 | **Decreto Subsidios** | Decretos de subsidios | ASISTENCIA, SECRETARIA | Revisor → Aprobador → Firmante |
| 3 | **Pago Facturas** | Workflow de aprobacion de facturas | PROVEEDOR, DAF, ADMIN | Recepcion → Revision → V°B° → Pago |
| 4 | **Decretos Personal** | Decretos de personal nombra/movilidad | RRHH, SECRETARIA, ALCALDIA | Revisor → Aprobador → Firmante |
| 5 | **Visacion Bases Licitacion** | Revision de bases de licitacion | COMPRAS, LEGAL, ADMIN | Revision → V°B° → Publicacion |
| 6 | **Transparencia Activa** | Publicacion proactive de info | TODOS | Revisado → Publicar |
| 7 | **Transparencia Pasiva/SAI** | Respuesta a solicitudes ciudadanas | OIRS, AREA RESP | Recepcion → Derivacion → Respuesta |
| 8 | **Tramitacion Libre** | Tramites no categorizados | VARIABLE | Configurable |
| 9 | **Oficios DocDigital** | Tramites desde bandeja DocDigital | GOB, AREA RESP | Recepcion → Clasificacion → Derivacion |

> **Nota**: En DocFlow 8 Infinity estos flujos son **configurables** via el motor de workflows, no hardcoded como en el legacy. Cada organizacion puede crear sus propios flujos.

---

## 5. API PUBLICA

### 5.1 Endpoints Publicos (Sin autenticacion JWT)

| Metodo | Endpoint | Descripcion |
|--------|----------|-------------|
| POST | `/api/v1/auth/login` | Autenticacion usuario/password |
| POST | `/api/v1/auth/claveunica` | Autenticacion con ClaveUnica |
| GET | `/api/v1/public/{tenant}/documents/{id}/download` | Descarga documento (con token temporal) |
| GET | `/api/v1/public/{tenant}/documents/{id}/qr` | Generar QR del documento |
| POST | `/api/v1/public/{tenant}/receive` | Receptor externo (para otras instituciones) |

### 4.2 Endpoints Protegidos (Requiere JWT)

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

### 4.3 Formato de Respuesta Estandar

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

### 4.4 Versionado de API

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

## 6. PATRONES DE DISENO

### 6.1 Backend

| Patron | Aplicacion |
|--------|------------|
| **Repository Pattern** | Abstraccion de acceso a datos |
| **Unit of Work** | Transacciones multiples |
| **Mediator/CQRS** | Separacion de commands y queries |
| **Dependency Injection** | Inyeccion de servicios |
| **Factory** | Creacion de entidades complejas |

### 6.2 Frontend

| Patron | Aplicacion |
|--------|------------|
| **Smart/Dumb Components** | Container vs Presentational |
| **Feature Modules** | Modularizacion por funcionalidad |
| **State Management** | NgRx o Signals (Angular 17+) |
| **OnPush Change Detection** | Performance |
| **Lazy Loading** | Carga de modulos bajo demanda |

---

## 7. SEGURIDAD

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

## 8. INTEGRACIONES

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

### 7.2 FirmaDigital (FirmaGob - Chile)

FirmaGob es la plataforma de Firma Electrónica Avanzada del Gobierno de Chile, gratuita para instituciones públicas.

#### Roles en FirmaGob

| Rol | Descripcion | Funciones |
|-----|-------------|-----------|
| **Operador** | Administrador de la institucion | Gestionar usuarios, solicitar habilitacion, configurar certificados |
| **Ministro de Fe** | Validador de certificados | Aprobar/rechazar solicitudes de certificados, autocertificacion |
| **Funcionario/Autoridad** | Firmante de documentos | Solicitar certificados, firmar documentos |

#### Tipos de Certificados

| Tipo | Descripcion | Uso |
|------|-------------|-----|
| **Propósito General** | Firma con 2do factor (QR) | Firmas individuales, requiere escanear QR |
| **Propósito Desatendido** | Firma automatica sin interaccion | Integraciones API, foliado automatico |
| **Autocertificacion** | Certificado del propio Ministro de Fe | Firmar como Ministro de Fe |

#### Integracion API FirmaGob

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│    DocFlow   │────►│   FirmaGob   │────►│  Certificado │
│   (API)      │     │   (Firma)    │     │   (PDF)      │
└──────────────┘     └──────────────┘     └──────────────┘
```

| Endpoint | Descripcion |
|----------|-------------|
| `POST /api/v1/signatures/sign` | Firmar documento con certificado |
| `POST /api/v1/signatures/batch` | Firmar multiples documentos |
| `GET /api/v1/certificates/status` | Consultar estado de certificado |
| `POST /api/v1/certificates/revoke` | Revocar certificado |

#### Flujo de Habilitacion (Institucion)

1. Tramitar documento "Modelo Aceptacion Condiciones de Uso"
2. Designar Operador y Ministro de Fe
3. Solicitar habilitacion a Secretaria de Gobierno Digital
4. Aprobacion e integracion de API
5. Registrar firmantes (funcionarios)
6. Aprobar certificados (Ministro de Fe)
7. Listo para usar

> **Nota para DocFlow 8 Infinity**: La integracion con FirmaGob permite firma electronica avanzada en documentos PDF, convirtiendo el documento en PDF certificado con validez legal.

### 7.3 QR de Documento

- Generacion de QR con hash del documento
- QR contiene: URL publica de verificacion
- Verificacion: `GET /api/v1/public/{tenant}/verify/{qrHash}`

### 7.4 DocDigital del Gobierno (Bandeja Electrónica)

**DocDigital** es la plataforma de comunicaciones oficiales del Gobierno de Chile, provista por la Secretaría de Gobierno Digital.

**Marco Legal**: Ley N° 21.180 de Transformación Digital establece que los Órganos de la Administración del Estado deben registrar las comunicaciones oficiales mediante plataformas digitales.

#### Funciones de DocDigital v3

| Proceso | Descripcion |
|---------|-------------|
| **Firma y distribución** | Firma electrónica y distribución de documentos firmados |
| **Comunicaciones internas** | Comunicación entre unidades dentro de la institución |
| **Comunicaciones oficiales** | Gestión entre distintos Órganos del Estado |

#### Integracion con DocDigital

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│  Gobierno    │────►│   DocFlow    │────►│  Bandeja     │
│  (DocDigital)│     │  (Receptor)  │     │  DocDigital  │
└──────────────┘     └──────────────┘     └──────────────┘
```

| Caracteristica | Descripcion |
|----------------|-------------|
| **Protocolo** | API REST / WebService |
| **Base URL** | `https://api.doc.digital.gob.cl` |
| **Frecuencia** | Polling cada 5 minutos (configurable) |
| **Documentos** | Oficios, resoluciones, cartas |
| **Tipo** | Documentos escaneados/digitalizados |
| **Almacenamiento** | Imagen original + metadata |

#### Flujo de Recepcion DocDigital

1. DocFlow consulta Bandeja Electronica del Gob.
2. Descarga documentos nuevos via API
3. Clasifica por tipo/origen
4. Notifica al usuario receptor
5. Registra en historial
6. Genera comprobante de trazabilidad

#### Roles en DocDigital

| Perfil | Descripcion |
|--------|-------------|
| **Administrador Principal** | Gestionar usuarios, permisos, registrar oficinas de partes |
| **Secretaría Virtual** | Gestionar documentación saliente y recibir/derivar documentos |
| **Firmante** | Confección, carga, visar, firmar, enumerar documentos |
| **Receptor** | Recibir y derivar documentos |

> **Nota**: DocFlow 8 Infinity puede integrarse como receptor de DocDigital del Gobierno para recibir comunicaciones oficiales de otros organismos públicos.

### 7.5 DTE - Facturas Electronicas (SII)

Integracion con el Servicio de Impuestos Internos (SII) para procesar facturas electronicas:

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│    SII       │────►│   DocFlow    │────►│   Bandeja    │
│  (DTE/Facturas)│     │  (Procesador)│     │   Facturas   │
└──────────────┘     └──────────────┘     └──────────────┘
```

| Caracteristica | Descripcion |
|----------------|-------------|
| **Protocolo** | API SII (REST/SOAP) |
| **Tipo Docs** | Facturas, Boletas, Notas Credito/Debito |
| **Validacion** | Schema XML DTE |
| **Almacenamiento** | XML original + PDF timbrado |

**Funcionalidades**:
- Recepcion automatica de DTE
- Validacion de firma electronica (timbre)
- Registro en bandeja de facturas
- Vincular a expediente/contrato
- Workflow de aprobacion (V°B°, pago)

### 7.6 OIRS - Transparencia y Atencion Ciudadana

Integracion con modulo OIRS (Oficina de Informaciones, Reclamos y Sugerencias) para transparencia pasiva:

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Portal     │────►│   DocFlow     │────►│    OIRS      │
│  Ciudadano   │     │  (Backend)    │     │  (Tramites)   │
└──────────────┘     └──────────────┘     └──────────────┘
```

| Caracteristica | Descripcion |
|----------------|-------------|
| **DECRETO** | 968 (Transparencia) |
| **Portal** | Consulta publica de tramites |
| **SAI** | Sistema de Atencion Integral |
| **Plazo** | 20 dias habiles (Ley de Transparencia) |
| **Notificacion** | Email/SMS al solicitante |

**Funcionalidades del Portal Ciudadano**:
- Consulta estado de tramites
- OIRS digital (reclamos/sugerencias)
- Verificacion QR de documentos
- Login con ClaveUnica
- Notificaciones push

### 7.7 Portal Ciudadano (SPA Externo)

Aplicacion web publica separada para atencion a ciudadanos:

| Modulo | Descripcion |
|--------|-------------|
| **Consulta Tramites** | Buscar por codigo/RUT |
| **OIRS Digital** | Enviar reclamos online |
| **Verificacion QR** | Validar autenticidad documentos |
| **Noticias/Avisos** | Publicaciones oficiales |
| **Login ClaveUnica** | Autenticacion gov. chile |

```
┌─────────────────────────────────────────────────────────────┐
│                   PORTAL CIUDADANO                          │
│                  (app.docflow.cl/portal)                    │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │  Consulta   │  │   OIRS      │  │   QR        │         │
│  │  Tramites   │  │   Digital   │  │  Verificar  │         │
│  └─────────────┘  └─────────────┘  └─────────────┘         │
│                                                             │
│  [Iniciar Sesion con ClaveUnica]                           │
└─────────────────────────────────────────────────────────────┘
```

---

## 9. PERFORMANCE

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

### 7.8 Modulo de Lectura de Email y Canalizacion a DocFlow

Este modulo permite leer correos electronicos desde cuentas institucionales y canalizar los documentos adjuntos al workflow de DocFlow.

#### Arquitectura del Modulo

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Servidor   │────►│   DocFlow    │────►│   Workflow   │
│   Email      │     │  (lector)    │     │   DocFlow    │
│  (IMAP)      │     │              │     │              │
└──────────────┘     └──────────────┘     └──────────────┘
```

#### Protocolos Soportados

| Protocolo | Descripcion | Puerto default |
|-----------|-------------|----------------|
| **IMAP** | Lectura y sincronizacion de bandeja | 993 (SSL) |
| **POP3** | Descarga de correos (menos moderno) | 995 (SSL) |
| **SMTP** | Envio de notificaciones | 587 (TLS) |

#### Configuracion de Cuentas

| Campo | Descripcion |
|-------|-------------|
| **Servidor** | hostname del servidor email (imap.gmail.com, etc.) |
| **Puerto** | puerto del servicio |
| **Usuario** | email completo o nombre de usuario |
| **Contrasena** | contrasena o App Password |
| **SSL/TLS** | habilitar cifrado |
| **Carpeta** | carpeta a monitorear (INBOX, etc.) |

#### Flujo de Procesamiento

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Conectar  │───►│  Descargar  │───►│  Analizar   │───►│  Clasificar │
│   a IMAP    │    │   emails    │    │  contenido  │    │   y validar │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
                                                               │
                                                               ▼
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  Notificar  │◄───│  Crear      │◄───│  Adjuntos   │◄───│  Crear      │
│  usuario    │    │  documento  │    │  a DocFlow  │    │  registro   │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
```

1. **Conectar**: Autenticacion IMAP/SSL al servidor
2. **Descargar**: Obtener emails no leidos desde carpeta configurada
3. **Analizar**: Extraer remitente, asunto, cuerpo, adjuntos
4. **Clasificar**: Validar si es documento oficial (por remitente, asunto, adjuntos)
5. **Crear Registro**: Crear documento tipo "Recibido" en DocFlow
6. **Adjuntos**: Guardar archivos adjuntos en DocFlow
7. **Notificar**: Enviar notificacion al usuario/area destino

#### Reglas de Clasificacion

| Regla | Descripcion | Ejemplo |
|-------|-------------|---------|
| **Por remitente** | Dominios autorizados | @municipalidad.cl, @gob.cl |
| **Por asunto** | Palabras clave | "Oficio", "Resolucion", "Carta" |
| **Por adjunto** | Tipos de archivo validos | PDF, DOCX, XLSX |
| **Por exclusion** | Ignorar emails | newsletters, spam |

#### Tipos de Documentos Generados

| Tipo Origen | Categoria | Descripcion |
|-------------|-----------|-------------|
| **Email Recibido** | LLEGAN | Email convertido a documento |
| **Email con Adjunto** | LLEGAN | Email + archivos adjuntos |

#### Funcionalidades Adicionales

| Funcionalidad | Descripcion |
|---------------|-------------|
| **Polling automatico** | Verificar bandeja cada X minutos |
| **Marcado como procesado** | No reprocesar emails ya canalizados |
| **Notificaciones** | Email/SMS al receptor |
| **Historial** | Registro de todos los emails procesados |
| **Filtros avanzados** | Reglas personalizadas por usuario |

#### API Endpoints

| Metodo | Endpoint | Descripcion |
|--------|----------|-------------|
| GET | `/api/v1/email/accounts` | Listar cuentas configuradas |
| POST | `/api/v1/email/accounts` | Agregar cuenta email |
| DELETE | `/api/v1/email/accounts/{id}` | Eliminar cuenta |
| POST | `/api/v1/email/accounts/{id}/test` | Probar conexion |
| GET | `/api/v1/email/accounts/{id}/logs` | Ver historial de procesamiento |
| POST | `/api/v1/email/process` | Procesar manualmente nuevos emails |

#### Tabla de Configuracion

```sql
-- Cuentas de email configuradas
EmailAccounts
├── Id (GUID, PK)
├── TenantId (FK)
├── Email (string)              -- usuario@dominio.com
├── DisplayName (string)       -- "Cuenta Oficial"
├── Server (string)            -- imap.gmail.com
├── Port (int)                 -- 993
├── UseSSL (bool)              -- true
├── Folder (string)            -- INBOX
├── Username (string)
├── PasswordEncrypted (string)
├── IsActive (bool)
├── PollingIntervalMinutes (int) -- 5
├── LastSyncAt (datetime)
└── CreatedAt (datetime)

-- Reglas de clasificacion
EmailRules
├── Id (GUID, PK)
├── EmailAccountId (FK)
├── RuleType (enum)            -- sender, subject, attachment, exclude
├── Pattern (string)           -- regex o texto
├── Action (string)           -- create_document, ignore, notify
├── DocumentTypeId (FK, nullable)
├── UserId (FK, nullable)     -- usuario destino
└── IsActive (bool)
```

> **Nota**: Este modulo permite integrar cuentas de email institucionales (Gmail, Outlook, Office 365, servidores propios) y canalizar automaticamente los documentos recibidos al workflow de DocFlow.

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