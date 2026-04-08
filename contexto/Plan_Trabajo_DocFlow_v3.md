# Plan de Trabajo - DocFlow v3

## Resumen Ejecutivo

Desarrollar la versión 3 del sistema DocFlow (Sistema de Gestión Documental) con tecnología moderna, configurable y adaptable para cualquier organización en Chile.

**Duración estimada**: 6-8 meses  
**Equipo**: 2-3 desarrolladores  
**Tipo de producto**: SaaS multi-tenant genérico

---

## Características del Producto

### Autenticación (Configurable por Organización)
- ✅ Local (usuario + contraseña)
- ✅ ClaveUnica (OIDC ChileAutentica)
- ✅ MFA / Doble Factor (TOTP)
- ✅ SSO Corporativo (SAML 2.0 / OIDC)

### Tipos de Documentos (Flujo Completo)
- **Recibido Externo**: Documentos que llegan desde afuera
- **Generado Interno**: Documentos que nunca salen (Memo, Despacho, Circular)
- **Generado Externo**: Documentos que se envían afuera (Oficio, Resolución, Ordinario)

### Funcionalidades Clave
- ✅ OCR para imágenes escaneadas
- ✅ Theming dinámico por organización
- ✅ Módulo de configuración/administración completo
- ✅ Multi-tenant (base de datos por organización)

---

## Fases del Proyecto

### Fase 1: Preparación (Mes 1)

| Tarea | Descripción | Entregable |
|-------|-------------|------------|
| 1.1 | Análisis detallado del sistema v2 | Documento de requisitos |
| 1.2 | Definición de arquitectura técnica | Diagrama de arquitectura |
| 1.3 | Setup entorno de desarrollo | Repositorio configurado |
| 1.4 | Diseño de base de datos multi-tenant | Modelo de datos |

**Hitos**: 
- [ ] Requisitos funcionales documentados
- [ ] Arquitectura aprobada
- [ ] Entorno de desarrollo operativo

---

### Fase 2: Backend Core (Meses 2-3)

| Tarea | Descripción | Entregable |
|-------|-------------|------------|
| 2.1 | Implementar multi-tenancy | Sistema multi-tenant funcional |
| 2.2 | Migrar entidades de v2 | Entidades reutilizadas |
| 2.3 | Implementar autenticación multi-factor | Local + ClaveUnica + MFA + SSO |
| 2.4 | Sistema de autenticación completo | Login con selección de cargo |
| 2.5 | Crear API RESTful | Endpoints documentados |
| 2.6 | Implementar búsqueda FTS5 | Búsqueda full-text |
| 2.7 | Implementar OCR para imágenes | Extracción de texto |

**Hitos**:
- [ ] API RESTful operativa
- [ ] Autenticación funcionando (todos los métodos)
- [ ] Búsqueda implementada
- [ ] OCR operativo

---

### Fase 3: Frontend (Meses 3-5)

| Tarea | Descripción | Entregable |
|-------|-------------|------------|
| 3.1 | Setup Angular 17 + Material | Proyecto inicializado |
| 3.2 | Implementar sistema de diseño | Componentes UI |
| 3.3 | Desarrollar módulo Login + Selección Cargo | Login funcional |
| 3.4 | Desarrollar Dashboard | Dashboard con KPIs |
| 3.5 | Desarrollar Bandeja de Entrada | Bandeja completa |
| 3.6 | Desarrollar Expedientes | Gestión de expedientes |
| 3.7 | Desarrollar Documentos (crear/editar/ver) | CRUD documentos |
| 3.8 | Desarrollar Registro de Documentos | Recepción + Creación |
| 3.9 | Desarrollar Consultas | Búsqueda docs/tareas |
| 3.10 | Desarrollar Reportes | Gráficos y estadísticas |
| 3.11 | Desarrollar Archivadores | Gestión física |
| 3.12 | Desarrollar Administración | Módulo de configuración |

**Hitos**:
- [ ] Todos los módulos UI funcionales
- [ ] Theming dinámico operativos
- [ ] Demos interactivos disponibles

---

### Fase 4: Integración y Testing (Mes 5-6)

| Tarea | Descripción | Entregable |
|-------|-------------|------------|
| 4.1 | Integración frontend-backend | Sistema completo |
| 4.2 | Testing unitario y de integración | Cobertura > 80% |
| 4.3 | Pruebas de usuario (UAT) | Usuario acepta sistema |
| 4.4 | Corrección de bugs | Sistema estable |
| 4.5 | Optimización de rendimiento | Benchmarks OK |

**Hitos**:
- [ ] Sistema integrado
- [ ] Tests pasando
- [ ] UAT completado

---

### Fase 5: Despliegue y Entrega (Meses 6-7)

| Tarea | Descripción | Entregable |
|-------|-------------|------------|
| 5.1 | Configurar Docker/Kubernetes | Ambiente de producción |
| 5.2 | Documentación técnica | Manuales completos |
| 5.3 | Capacitación de usuarios | Usuarios formados |
| 5.4 | Entrega a producción | Sistema en vivo |
| 5.5 | Período de soporte | Sistema estable |

**Hitos**:
- [ ] Sistema en producción
- [ ] Documentación entregada
- [ ] Soporte iniciado

---

## Detalle de Tareas por Módulo

### Módulo: Documentos

| Subtarea | Descripción |
|----------|-------------|
| 3.7.1 | Crear documento recibido (externo) |
| 3.7.2 | Crear documento generado interno (nunca sale) |
| 3.7.3 | Crear documento generado externo (envío) |
| 3.7.4 | Adjuntar archivos (PDF, imágenes) |
| 3.7.5 | Integrar OCR para imágenes |
| 3.7.6 | Firmar digitalmente |
| 3.7.7 | Ver documento con 9 tabs |

### Módulo: Administración (Configuración)

| Subtarea | Descripción |
|----------|-------------|
| 3.12.1 | Gestionar organizaciones (tenants) |
| 3.12.2 | Gestionar correlativos por tipo |
| 3.12.3 | Gestionar plantillas de documentos |
| 3.12.4 | Gestionar tipos de documento |
| 3.12.5 | Gestionar estados |
| 3.12.6 | Gestionar unidades organizacionales |
| 3.12.7 | Gestionar tipos de derivación |
| 3.12.8 | Gestionar causales |
| 3.12.9 | Gestionar variables del sistema |
| 3.12.10 | Gestionar parámetros SIS |
| 3.12.11 | Gestionar usuarios |
| 3.12.12 | Gestionar perfiles/roles |
| 3.12.13 | Configurar theming por organización |
| 3.12.14 | Configurar autenticación por organización |

---

## Cronograma Visual

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

## Recursos Necesarios

### Humanos
- 1 Lead Developer (Full-stack .NET/Angular)
- 1-2 Developers (Full-stack)
- 1 QA (opcional, puede ser mixto)

### Técnicos
- Licencias: Visual Studio, SQL Server, Azure (trial)
- Herramientas: GitHub, Docker, VS Code

### Presupuesto Estimado

| Concepto | Costo Estimado |
|----------|----------------|
| Desarrollo (3 devs x 6 meses) | $45.000.000 CLP |
| Licencias y herramientas | $5.000.000 CLP |
| Infraestructura (cloud) | $8.000.000 CLP |
| Contingencia (20%) | $11.600.000 CLP |
| **Total** | **$69.600.000 CLP** |

---

## Gestión de Riesgos

| Riesgo | Probabilidad | Impacto | Mitigación |
|--------|--------------|---------|-------------|
| Retrasos en desarrollo | Media | Alto | Buffer en cronograma |
| Cambios en requisitos | Alta | Medio | Flexible scope |
| Problemas de integración | Baja | Alto | Testing continuo |
| Issues de rendimiento | Baja | Alto | Optimización temprana |
| OCR con precisión variable | Media | Medio | Validación manual |

---

## Métricas de Éxito

- [ ] Sistema operativo en producción
- [ ] 100% funcionalidades implementadas
- [ ] Cobertura de tests > 80%
- [ ] Tiempo de respuesta < 2s
- [ ] Usuario satisfecho (UAT)
- [ ] Ganar al menos 1 licitación
- [ ] Sistema configurable por cada organización

---

## Próximos Pasos Inmediatos

1. Revisar y aprobar plan de trabajo
2. Asignar equipo de desarrollo
3. Iniciar Fase 1: Preparación
4. Configurar repositorio y entorno
5. Comenzar análisis de requisitos

---

*Documento actualizado: Abril 2026*  
*Proyecto: DocFlow v3 - Sistema de Gestión Documental*
*Nota: Producto genérico, configurable y adaptable para cualquier organización*