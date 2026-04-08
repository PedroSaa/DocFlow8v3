# INFORME DETALLADO DEL SISTEMA
## DocFlow v3 - Sistema de Gestión Documental

**Versión**: 3.0  
**Fecha**: Abril 2026  
**Estado**: En Desarrollo  
**Tipo**: Producto SaaS Multi-tenant Genérico

---

## 1. RESUMEN EJECUTIVO

DocFlow v3 es un sistema de gestión documental desarrollado para competir en licitaciones públicas en Chile. A diferencia de versiones anteriores, esta versión está diseñada como un **producto genérico** que puede ser configurado y adaptado para **cualquier organización**.

### Objetivos Comerciales

| Organización | Valor Estimado | Probabilidad |
|--------------|----------------|--------------|
| Villarrica | $75.000.000 CLP | Alta |
| Las Condes | $30.000.000 CLP | Media-Alta |
| Lampa | $25.000.000 CLP | Media |
| **Total Potencial** | **$130.000.000 CLP** | |

---

## 2. ARQUITECTURA DEL SISTEMA

### 2.1 Stack Tecnológico

| Capa | Tecnología | Versión |
|------|------------|---------|
| Frontend | Angular | 17+ |
| UI Components | Angular Material + Custom | - |
| Backend | .NET | 8.0 |
| Framework | ASP.NET Core | 8.0 |
| ORM | Entity Framework Core | 8.0 |
| Base de Datos | SQL Server | 2019+ |
| Autenticación | JWT + Identity + MFA + SSO + ClaveUnica | - |
| Búsqueda | FTS5 (Full-Text Search) | - |
| OCR | Tesseract / Azure Vision | - |
| Despliegue | Docker + Kubernetes | - |

### 2.2 Arquitectura Multi-tenant

El sistema utiliza una estrategia de **Database per Tenant**, donde cada organización tiene su propia base de datos aislada y segura.

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

---

## 3. MÓDULOS DEL SISTEMA

### 3.1 Módulos Principales

| Módulo | Descripción | Estado |
|--------|-------------|--------|
| Dashboard | KPIs, gráficos, accesos rápidos | Demo |
| Bandeja de Entrada | Documentos recibidos, derivados. Incluye sub-bandejas: | Demo |
| Bandeja de email | Bandeja de email/leer documentos | Demo |
| Bandeja DocDigital del Gob | Documentos DocDigital del Gobierno | Demo |
| Bandeja de Facturas | Bandeja de facturas recibidas | Demo |
| Bandeja de Órdenes de Compra | Bandeja de órdenes de compra | Demo |
| Expedientes | Gestión de carpetas de documentos | Demo |
| Registro de Documentos | CRUD completo de documentos | Demo |
| Tareas | Gestión de tareas asignadas | Demo |
| Consultas | Búsqueda avanzada de docs/tareas | Demo |
| Reportes | Gráficos y estadísticas | Demo |
| Archivadores | Gestión física de documentos | Demo |
| Administración | Configuración del sistema | Por hacer |

### 3.2 Flujo de Documentos

Todo documento puede seguir uno de estos 3 flujos:

| Flujo | Descripción | Ejemplos |
|-------|-------------|----------|
| **LLEGAN** | Documentos que llegan desde el exterior | Recibido, Ordinario externo |
| **CIRCULAN** | Documentos que circulan solo internamente (nunca salen) | Memo, Despacho, Circular |
| **SALEN** | Documentos que se envían al exterior | Oficio, Resolución, Ordinario |

### 3.3 Tipos de Documentos (Configurables)

Por defecto se incluyen:

| Tipo | Categoría | Requiere Firma | Requiere Cite |
|------|-----------|----------------|---------------|
| Ordinario | LLEGAN/SALEN | ✅ | ✅ |
| Memo | CIRCULAN | ❌ | ✅ |
| Oficio | SALEN | ✅ | ✅ |
| Despacho | CIRCULAN | ✅ | ✅ |
| Circular | CIRCULAN | ✅ | ✅ |
| Resolución | SALEN | ✅ | ✅ |


> **Nota**: Los tipos de documentos son 100% configurables. Cada organización puede crear, modificar o eliminar tipos según sus necesidades.

---

## 4. AUTENTICACIÓN

El sistema soporta múltiples métodos de autenticación, cada uno configurable por organización:

### 4.1 Métodos Soportados

| Método | Descripción | Configurable |
|--------|-------------|---------------|
| **Local** | Usuario + Contraseña (BD del tenant) | ✅ Por tenant |
| **ClaveUnica** | OIDC con ChileAutentica (RUN) | ✅ Por tenant |
| **MFA** | TOTP (Google Authenticator, Authy) | ✅ Por tenant |
| **SSO** | SAML 2.0 / OIDC externo | ✅ Por tenant |

### 4.2 Flujo de Autenticación

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

### 4.3 Configuración por Organización

Cada tenant puede configurar:
- Habilitar/deshabilitar cada método
- Forzar MFA para todos
- Timeout de sesión
- Políticas de contraseña
- SSO externo
- Todas las configuraciones previas
- Roles y permisos
- crud: Correlativos, formatos de doc., Niveles o depto, subdepto, subrogante y permisos,  permiso de derivación
---

## 5. CARACTERÍSTICAS TÉCNICAS

### 5.1 OCR (Reconocimiento Óptico de Caracteres)

| Característica | Descripción |
|----------------|-------------|
| **Input** | Imágenes (JPG, PNG, TIFF, PDF escaneado) |
| **Proceso** | Detección de texto con Tesseract/Azure Vision |
| **Output** | Texto extraído + PDF con capa de texto |
| **Idiomas** | Español (predeterminado), inglés |
| **Almacenamiento** | Imagen original + texto extraído |

### 5.2 Theming Dinámico

Cada organización puede personalizar:
- Colors primario, secundario, de énfasis
- Logo y favicon
- Fondo y color del sidebar
- Familia tipográfica
- CSS personalizado
- Variables CSS

### 5.3 Búsqueda Full-Text (FTS5)

El sistema implementa búsqueda avanzada usando SQL Server FTS5:
- Búsqueda por contenido
- Búsqueda por metadatos
- Búsqueda por documentos asociados

---

## 6. MÓDULO DE ADMINISTRACIÓN / CONFIGURACIÓN

Este módulo contiene toda la parametrización del sistema:

### 6.1 Configuraciones Disponibles

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

---

## 7. RESUMEN DE ENTREGABLES

### 7.1 Demos UI Creados (16)

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

### 7.2 Documentación

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

---

## 8. PRESUPUESTO Y CRONOGRAMA

### 8.1 Presupuesto Estimado

| Concepto | Costo |
|----------|-------|
| Desarrollo (3 devs x 6 meses) | $45.000.000 CLP |
| Licencias y herramientas | $5.000.000 CLP |
| Infraestructura (cloud) | $8.000.000 CLP |
| Contingencia (20%) | $11.600.000 CLP |
| **Total** | **$69.600.000 CLP** |

### 8.2 Cronograma

| Fase | Duración | Tareas |
|------|-----------|--------|
| Preparación | Mes 1 | 4 |
| Backend Core | Meses 2-3 | 7 |
| Frontend | Meses 3-5 | 12 |
| Integración | Meses 5-6 | 5 |
| Despliegue | Meses 6-7 | 5 |
| **Total** | **6-8 meses** | **21** |

---

## 9. REUTILIZACIÓN DE CÓDIGO (v2 → v3)

| Componente | Porcentaje |
|-----------|------------|
| Entidades (Document, Expediente, User, etc.) | ~70% reutilizable |
| Servicios (DocumentService, etc.) | ~70% reutilizable |
| Controllers API | ~60% reutilizable |
| Lógica de negocio | ~70% reutilizable |
| Frontend (WebForms) | 0% reutilizable |

---

## 10. PRÓXIMOS PASOS

### Inmediatos
1. Revisar y aprobar plan de trabajo
2. Asignar equipo de desarrollo
3. Iniciar Fase 1: Preparación

### Por Hacer
- Desarrollo backend completo
- Demos UI restantes (Administración)
- Integración y testing
- Despliegue

---

*Documento generado: Abril 2026*  
*Proyecto: DocFlow v3 - Sistema de Gestión Documental*  
*Producto genérico, configurable y adaptable para cualquier organización*