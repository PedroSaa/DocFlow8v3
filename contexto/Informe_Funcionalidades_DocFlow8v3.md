# INFORME DE FUNCIONALIDADES DEL SISTEMA DOCFLOW 8 INFINITY

**Versión:** 1.0  
**Fecha:** 9 de Abril 2026  
**Proyecto:** DocFlow 8 Infinity - Sistema de Gestión Documental

---

## 1. FLUJOS DE DOCUMENTOS

### 1.1 Flujo LLEGAN (Exterior → Interior)

Documentos que vienen de fuera de la organización hacia el interior.

**Tipos de documentos:**
- Recibido
- Partes (Oficina de Partes)
- Externos
- Email Recibido
- Email con Adjunto
- DocDigital del Gobierno

**Funcionalidades:**
- Registro manual de documentos recibidos
- Importación automática desde DocDigital del Gobierno
- Captura de documentos desde email
- Clasificación automática por tipo de documento
- Distribución automática al destinatario

**Demo:** `17-registrar-documento-recibido.html`

---

### 1.2 Flujo CIRCULAN (Interno → Interno)

Documentos que circulan solo dentro de la organización y nunca salen al exterior.

**Tipos de documentos:**
- Memo
- Despacho (desde Alcaldía)
- Circular
- Informe

**Funcionalidades:**
- Crear documentos internos
- Derivación entre departamentos
- Aprobación por V°B°
- Firma interna (opcional)
- Historial de movimientos

**Demo:** `18-crear-documento-interno.html`

---

### 1.3 Flujo SALEN (Interior → Exterior)

Documentos que se envían desde la organización hacia el exterior.

**Tipos de documentos:**
- Oficio
- Resolución
- Ordinario
- Carta

**Funcionalidades:**
- Flujo completo de 6 pasos:
  1. Crear
  2. Derivar (a revisión/aprobación)
  3. Recibir (por área destinataria)
  4. Firmar (firma electrónica)
  5. Oficina de Partes (despacho autorizado)
  6. Despacho (físico o electrónico)

**Demo:** `19-crear-documento-externo.html`

---

## 2. BANDEJA DE ENTRADA

### 2.1 Características Principales

**Ubicación:** `demos/08-bandeja.html`, `demos/20-bandeja-completa.html`

**Filtros disponibles:**
- Filtro por fecha (desde/hasta)
- Filtro por tipo de documento
- Filtro por número de documento
- Filtro por materia
- Filtro por correlativo

**Acciones sobre documentos:**
| Acción | Descripción |
|--------|-------------|
| Recibir | Aceptar documento derivado |
| Derivar | Enviar a otro usuario/unidad |
| Reenviar | Reenviar manteniendo historial |
| Devolver | Devolver al remitente original |
| Archivar | Mover a archivador físico |
| Anular | Cancelar documento (solo admin) |
| Destacar | Marcar como importante/urgente |
| Firmar | Aplicar firma digital |

**Indicadores visuales:**
| Indicador | Significado |
|-----------|-------------|
| T | Documento con timbre/firma |
| I | Documento con imagen escaneada |
| A | Documento con adjuntos |
| R | Documento con referencias |
| O | Documento con observaciones |

### 2.2 Sub-Bandejas

La bandeja completa incluye sub-bandejas especializadas:

1. **Bandeja Principal** - Documentos pendientes
2. **DocDigital del Gob.** - Documentos desde plataforma gobierno
3. **Email** - Documentos recibidos por email
4. **Facturas** - DTE del SII
5. **Órdenes de Compra** - Órdenes de compra

---

## 3. REGISTRO Y CREACIÓN DE DOCUMENTOS

### 3.1 Formulario de Documento

**Campos del formulario:**

| Campo | Tipo | Requerido | Descripción |
|-------|------|-----------|-------------|
| Tipo Documento | Select | Sí | Seleccionar tipo (Oficio, Memo, etc.) |
| Número | Auto | Sí | Se genera automáticamente |
| Fecha | Date | Sí | Fecha del documento |
| Remitente | Select | Sí | Usuario autor del documento |
| Destinatario | Select | Sí | Receptor del documento |
| Código Cite | Text | No | Número de cite/referencia |
| Fecha Cite | Date | No | Fecha del cite |
| Materia | Text | Sí | Descripción/sumilla |
| Fecha Vencimiento | Date | No | Plazo de respuesta |
| Adjuntos | File | No | Archivos adjuntos |
| Observaciones | Textarea | No | Notas adicionales |

### 3.2 Tipos de Documento

**Configurables por organización:**

| Código | Nombre | Categoría | Requiere Firma |
|--------|--------|-----------|----------------|
| REC | Recibido | LLEGAN | No |
| PAR | Partes | LLEGAN | No |
| MEM | Memo | CIRCULAN | No |
| DES | Despacho | CIRCULAN | Sí |
| CIR | Circular | CIRCULAN | No |
| INF | Informe | CIRCULAN | Sí |
| OFI | Oficio | SALEN | Sí |
| RES | Resolución | SALEN | Sí |
| ORD | Ordinario | SALEN | Sí |
| CAR | Carta | SALEN | Sí |

---

## 4. EXPEDIENTES

### 4.1 Funcionalidades

**Demo:** `demos/10-expedientes.html`

- Crear expediente con nombre y descripción
- Agrupar documentos por proyecto/hito
- Tree view de documentos
- Compartir expediente con otros usuarios
- 8 tabs de información:
  - Resumen
  - Documentos
  - Tareas
  - Derivaciones
  - Bitácora
  - Historial
  - Archivadores
  - Adjuntos

### 4.2 Estados

| Estado | Descripción |
|--------|-------------|
| Pendiente | Expediente creado, sin actividad |
| Proceso | Documentos en tramitación |
| Cerrado | Tramitación finalizada |

---

## 5. TAREA

### 5.1 Funcionalidades

**Demo:** `demos/14-consulta-tareas.html`

- Asignar tareas a usuarios específicos
- Definir nivel responsable (quien asigna)
- Definir usuario ejecutor (quien realiza)
- Incluir glosa (descripción de la tarea)
- Establecer fecha de vencimiento
- Adjuntar documentos de respuesta
- Cambiar estados

### 5.2 Estados de Tarea

| Estado | Descripción |
|--------|-------------|
| Pendiente | Tarea asignada, sin completar |
| Cumplida | Tarea completada |
| Semicumplida | Completada parcialmente |

---

## 6. ARCHIVADORES

### 6.1 Funcionalidades

**Demo:** `demos/15-archivadores.html`

- Organización por Año
- Organización por Mes
- Identificación por Caja
- Descripción del contenido
- Estado del archivador

### 6.2 Estados

| Estado | Descripción |
|--------|-------------|
| Archivrado | Documentos guardados físicamente |
| En préstamo | Documentos en préstamo temporal |

---

## 7. REPORTES

### 7.1 Tipos de Reportes

**Demo:** `demos/16-reportes.html`

| Reporte | Descripción |
|---------|-------------|
| Por correlativos | Lista de documentos por número correlativo |
| Por rango de fechas | Documentos entre fechas específicas |
| Por tipo | Documentos filtrados por tipo |
| Despacho interno | Distribución por departamento |
| Diario por departamento | Reporte diario agrupado |
| Mensual por formato | Reporte mensual por tipo |
| Docs no recibidos | Documentos pendientes de recepción |

---

## 8. AUTENTICACIÓN

### 8.1 Métodos de Login

**Demo:** `demos/06-login-cargo.html`

| Método | Descripción |
|--------|-------------|
| Local | Usuario y password tradicional |
| ClaveUnica | Autenticación OIDC del Gobierno |
| MFA | Segundo factor de autenticación |
| SSO | Single Sign-On corporativo |

### 8.2 Selección de Rol/Cargo

- Un usuario puede tener múltiples cargos
- Selector de rol al hacer login
- Permisos basados en el rol seleccionado

---

## 9. INTEGRACIONES GUBERNAMENTALES

### 9.1 ClaveUnica

- Autenticación via OpenID Connect
- Endpoint: accounts.claveunica.gob.cl
- Requiere RUN del usuario
- Cumple requisitos licitaciones públicas

### 9.2 FirmaGob

- Firma electrónica avanzada
- Roles: Operador, Ministro de Fe, Funcionario
- Certificados: Propósito General, Desatendido
- API para integración

### 9.3 DocDigital

- Bandeja electrónica del Gobierno
- Recepción de documentos oficiales
- Comunicaciones entre organismos públicos

### 9.4 DTE (SII)

- Recepción de facturas electrónicas
- Validación de timbre
- Workflow de aprobación de pagos

### 9.5 OIRS/Transparencia

- DECRETO 968
- Portal ciudadano
- Plazo 20 días hábiles

### 9.6 Módulo de Email

- IMAP/POP3 para lectura
- Reglas de clasificación automáticas
- Conversión a documento DocFlow

---

## 10. MÓDULOS DE ADMINISTRACIÓN

### 10.1 Gestión de Usuarios

**Demo:** `demos/21-administracion.html`

- Crear/editar usuarios
- Asignar roles
- Gestionar permisos
- Configurar múltiples cargos

### 10.2 Gestión de Documentos

- CRUD completo de documentos
- Historial de modificaciones
- Control de versiones

### 10.3 Configuración del Sistema

- Tipos de documento
- Departamentos
- Plantillas
- Correlativos

---

## 11. DASHBOARD

### 11.1 Dashboard Básico

**Demo:** `demos/07-dashboard.html`

- Estadísticas generales
- Documentos pendientes
- Últimos documentos

### 11.2 Dashboard con KPIs

**Demo:** `demos/11-dashboard-kpis.html`

- Métricas clave
- Gráficos de rendimiento
- Indicadores de productividad

### 11.3 Dashboard Premium

**Demo:** `demos/12-dashboard-premium.html`

- Dashboard avanzado
- Gráficos interactivos
- Análisis de datos

---

## 12. CONSULTAS

### 12.1 Consulta de Documentos

**Demo:** `demos/13-consulta-documentos.html`

- Búsqueda avanzada
- Filtros múltiples
- Resultados paginados

---

## 13. FUNCIONALIDADES ESPECIALES

### 13.1 Editor de Documentos

- Crear documentos internos
- Editor WYSIWYG
- Vista previa

### 13.2 Firma Digital

- Firma electrónica avanzada
- Integración con FirmaGob
- PDF certificado

### 13.3 V° Bueno (Aprobaciones)

- Workflow jerárquico
- Revisor → Aprobador → Firmante
- Registro de approvals

### 13.4 Oficina de Partes

- Único punto de despacho exterior
- Control de envíos
- Registro de despacho

### 13.5 Auditoría

- Trazabilidad completa
- Registro de todas las acciones
- Historial inmutable

---

## 14. INTERFAZ DE USUARIO

### 14.1 Temas Disponibles

| Demo | Estilo |
|------|--------|
| 01-corporate-clean.html | Corporate Clean |
| 02-modern-saas.html | Modern SaaS |
| 03-government-formal.html | Government Formal |
| 04-material-design.html | Material Design |
| 05-corporate-theming.html | Theming Dinámico |

### 14.2 Documento (9 tabs)

**Demo:** `demos/09-documento.html`

1. Detalle
2. Documento
3. Tareas
4. Historial
5. Imagen Doc.
6. Adjuntos Doc.
7. Observaciones
8. Referencias
9. Referencias Zonales

---

## 15. FLUJOS ESPECIALIZADOS

### 9 Flujos Configurables

1. Decreto Sección 1 y 2 Pagos
2. Decreto Subsidios
3. Pago Facturas
4. Decretos Personal
5. Visación Bases Licitación
6. Transparencia Activa
7. Transparencia Pasiva/SAI
8. Tramitación Libre
9. Oficios DocDigital

---

*Documento generado el 9 de Abril de 2026*