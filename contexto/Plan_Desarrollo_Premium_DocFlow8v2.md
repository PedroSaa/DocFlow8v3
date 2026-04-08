# PLAN DE DESARROLLO PREMIUM — DocFlow8v2
## Informe Ejecutivo de Etapas y Carta Gantt

**Fecha**: Abril 2026  
**Proyecto**: DocFlow8v2 — Sistema de Gestion Documental  
**Objetivo**: Producto premium que cubra 3 licitaciones municipales  
**Duracion total estimada**: 41 semanas (AI-asistido)

---

## 1. RESUMEN EJECUTIVO

DocFlow8v2 es un sistema de gestion documental con .NET 8 + Angular 17 + SQL Server que actualmente cuenta con 130+ endpoints, 55+ entidades, 110+ tests backend y 18 modulos frontend.

Se identificaron 3 oportunidades comerciales en municipalidades chilenas:

| Municipalidad | Presupuesto | Contrato | Modalidad | Cobertura actual |
|---------------|-------------|----------|-----------|-----------------|
| **Villarrica** | $75.000.000 | 36 meses | SaaS | ~70% |
| **Lampa** | $25.000.000 | 12 meses | SaaS | ~55% |
| **Las Condes** | $30.000.000 (garantia) | 48 meses | On-Premise | ~40% |

**Revenue potencial total**: $130.000.000 CLP  
**Inversion estimada**: ~$41.500.000 CLP  
**ROI bruto**: 3.1x

---

## 2. ESTADO ACTUAL DEL PRODUCTO

### Modulos implementados
- Autenticacion JWT + refresh tokens
- Usuarios, Organizaciones (multi-tenant)
- Documentos CRUD completo + tipos
- Bandejas entrada/salida + distribucion
- Expedientes y archivadores
- Firma digital (FirmaGob + DocDigital)
- DTE/Facturas con SII
- OIRS / Transparencia (DECRETO 968)
- Tareas y notificaciones
- Dashboard y reportes (QuestPDF)
- Auditoria y trazabilidad
- API externa + webhooks
- Docker Compose + K8s manifests + CI/CD

### Deuda tecnica
- 0 tests frontend
- Search usa LIKE en vez de Full-Text Search
- 2 TODOs en ExpedientesController (retornan listas vacias)
- Redis definido pero no integrado

---

## 3. ANALISIS DE GAPS POR MUNICIPALIDAD

### 3.1 Gaps criticos (piden 2-3 municipalidades)

| # | Feature | Lampa | Villarrica | Las Condes |
|---|---------|:-----:|:----------:|:----------:|
| 1 | V.B. (Visto Bueno) | SI | SI | SI |
| 2 | Clave Unica | SI | SI | - |
| 3 | QR en documentos | SI | SI | - |
| 4 | Subrogancia | - | SI | SI |
| 5 | Rubricas multiples | SI | SI | - |
| 6 | Correspondencia formal | SI | - | SI |

### 3.2 Gaps importantes (diferenciadores)

| # | Feature | Lampa | Villarrica | Las Condes |
|---|---------|:-----:|:----------:|:----------:|
| 7 | Portal Ciudadano | SI | SI | - |
| 8 | Workflow visual Low-Code | - | - | SI |
| 9 | Plantillas Word/PDF | SI | SI | - |
| 10 | Firma Masiva | SI | - | - |
| 11 | Busqueda Full-Text | - | SI | - |
| 12 | App Movil completa | SI | - | - |

### 3.3 Gaps especificos Las Condes (premium)

| # | Feature |
|---|---------|
| 13 | IA/GenAI: OCR, extraccion metadatos, consultas lenguaje natural |
| 14 | 9 flujos especializados (Decretos, subsidios, personal, etc.) |
| 15 | Integraciones: SIM, CRM, Portal Banco, Mercado Publico |
| 16 | Modalidad On-Premise |

### 3.4 Gaps especificos Lampa (gobierno)

| # | Feature |
|---|---------|
| 17 | Control Horario |
| 18 | Permisos/Vacaciones |
| 19 | PMG (Programa Mejoramiento Gestion) |
| 20 | Vehiculos/Cometidos |
| 21 | Calendario Institucional |

### 3.5 Gaps especificos Villarrica

| # | Feature |
|---|---------|
| 22 | Compras Multi-area |

---

## 4. PLAN DE DESARROLLO POR ETAPAS

### ETAPA 0: Estabilizacion
**Duracion**: 3 semanas  
**Objetivo**: Base solida antes de nuevos features  
**Prerequisito para**: Todas las etapas

| Tarea | Detalle | Semana |
|-------|---------|--------|
| Tests frontend | 40+ unit tests, 10 e2e con Playwright | S1-S2 |
| Code review general | Revisar API contracts, corregir TODOs | S1 |
| Security audit | OWASP Top 10, headers, CORS | S2 |
| Performance baseline | Medir tiempos de respuesta actuales | S3 |
| Corregir deuda tecnica | Search FTS, Redis, ExportService PDF | S2-S3 |

**Entregables**:
- Suite de tests frontend funcional
- Reporte de seguridad
- Baseline de performance documentado
- Search con Full-Text Search real
- Export PDF funcional

---

### ETAPA 1: Core Municipal
**Duracion**: 8 semanas  
**Objetivo**: Implementar los 6 features que piden 2-3 municipalidades  
**Prerequisito**: Etapa 0  
**Desbloquea**: Villarrica ~85% | Lampa ~70% | Las Condes ~55%

| Tarea | Detalle | Semana | Esfuerzo |
|-------|---------|--------|----------|
| **V.B. (Visto Bueno)** | Modelo de visacion: solicitar, aprobar, rechazar V.B. con fecha limite. Cadena de visacion configurable. Registro en historial del documento. UI de aprobacion. | S4-S6 | Alto |
| **Clave Unica** | Integracion OpenID Connect con ClaveUnica.gob.cl. Login alternativo al JWT actual. Mapeo de RUT a usuario interno. | S5-S6 | Medio |
| **QR en documentos** | Generacion de QR con URL de verificacion. Insercion en PDF generados. Endpoint publico de validacion de firma. | S7 | Bajo |
| **Subrogancia** | Modelo de subrogancia: usuario titular + subrogante + periodo. Herencia de permisos y bandejas. Activacion automatica por fecha. | S7-S8 | Medio |
| **Rubricas multiples** | CRUD de rubricas por usuario (imagen + nombre). Seleccion al momento de firmar. Insercion en documento generado. | S8-S9 | Medio |
| **Correspondencia formal** | Modulo de correspondencia: registro, clasificacion (oficio, carta, memorandum). Destinos multiples, respuestas, plazos, confidencialidad. | S9-S11 | Medio |

**Riesgos**:
- Clave Unica requiere acceso a sandbox de gobierno (tramite previo)
- V.B. impacta el flujo core de documentos — requiere testing exhaustivo

---

### ETAPA 2: Portal Ciudadano + Plantillas
**Duracion**: 5 semanas  
**Objetivo**: Interfaz publica + generacion de documentos  
**Prerequisito**: Etapa 1  
**Desbloquea**: Villarrica ~95% | Lampa ~85%

| Tarea | Detalle | Semana | Esfuerzo |
|-------|---------|--------|----------|
| **Portal Ciudadano** | SPA publica separada: consulta de tramites, OIRS digital, verificacion QR, noticias/avisos. Login con Clave Unica. Responsive/movil. | S12-S15 | Alto |
| **Plantillas Word/PDF** | Motor de plantillas: variables dinamicas (fecha, folio, firmante, RUT). Import desde Word. Preview antes de firmar. | S13-S14 | Medio |
| **Firma Masiva** | Seleccion multiple de documentos. Firma batch con barra de progreso. Confirmacion individual o grupal. | S15-S16 | Medio |

**Riesgos**:
- Portal Ciudadano es una SPA separada — arquitectura de deployment adicional
- Plantillas Word requieren libreria de procesamiento (DocX o similar)

---

### ETAPA 3: Modulos Gobierno — Lampa
**Duracion**: 6 semanas  
**Objetivo**: Modulos especificos de gestion municipal  
**Prerequisito**: Etapa 1  
**Paralelizable con**: Etapa 2  
**Desbloquea**: Lampa 100%

| Tarea | Detalle | Semana | Esfuerzo |
|-------|---------|--------|----------|
| **Control Horario** | Registro entrada/salida (multiples por dia). Restriccion por equipo (no smartphones). Historial por usuario. Vista admin con calculo horas. Export Excel. | S12-S13 | Medio |
| **Permisos/Vacaciones** | Tipos: vacaciones, horas extras, matrimonio, nacimiento, defuncion, otros. Solicitud, aprobacion, saldo. Calendario de ausencias. | S13-S14 | Medio |
| **PMG** | Definicion de objetivos, metas, verificadores. Subida de evidencias. Revision por comite (aceptar/rechazar). Progreso por meta. | S14-S16 | Medio |
| **Vehiculos/Cometidos** | CRUD vehiculos con datos tecnicos e historial mantencion. Conductores + ausencias. Planificacion viajes (disponibilidad automatica). Alertas revision tecnica. Cometidos con formulario. | S16-S17 | Medio |
| **Calendario Institucional** | Calendario compartido. Edicion restringida por rol. Categorias. Export ICS para celular. | S17 | Bajo |

**Riesgos**:
- Son modulos aislados del core documental — bajo riesgo de regresion
- PMG es especifico gobierno chileno — requiere validacion con usuario real

---

### ETAPA 4: On-Premise + Flujos Especializados
**Duracion**: 7 semanas  
**Objetivo**: Habilitar deployment local + flujos Las Condes  
**Prerequisito**: Etapa 2  
**Desbloquea**: Las Condes ~70%

| Tarea | Detalle | Semana | Esfuerzo |
|-------|---------|--------|----------|
| **Modalidad On-Premise** | Installer/script de despliegue local. Configuracion de DB, storage, SMTP. Backup/restore automatizado. Documentacion de requisitos HW. | S18-S19 | Medio |
| **Flujos especializados** | 9 flujos hardcoded inicialmente: (1) Decreto Seccion 1 y 2 Pagos, (2) Decreto Subsidios, (3) Pago Facturas, (4) Decretos Personal, (5) Visacion Bases Licitacion, (6) Transparencia Activa, (7) Transparencia Pasiva/SAI, (8) Tramitacion Libre, (9) Oficios DocDigital. Cada flujo: participantes, cadena V.B., documentos, integraciones. | S19-S23 | Alto |
| **App Movil completa** | Extender app React Native existente: ver documentos, dar V.B., firmar, acceso Clave Unica. | S22-S24 | Medio |

**Riesgos**:
- 9 flujos son mucho scope — pueden requerir mas tiempo si la logica es compleja
- On-Premise requiere testing en infraestructura diferente a SaaS
- App movil requiere publicacion en stores (proceso separado)

---

### ETAPA 5: Workflow Visual Low-Code
**Duracion**: 10 semanas  
**Objetivo**: Motor de workflows configurable sin codigo  
**Prerequisito**: Etapa 4  
**Desbloquea**: Villarrica 100% | Las Condes ~85%

| Tarea | Detalle | Semana | Esfuerzo |
|-------|---------|--------|----------|
| **Motor BPMN-like** | WorkflowDefinition: steps, transitions, conditions, actions. WorkflowInstance: estado actual, historial. Evaluador de condiciones (reglas JSON). Acciones: notificar, derivar, firmar, V.B., cambiar estado. | S25-S28 | Alto |
| **Designer visual** | Componente Angular drag & drop. Canvas con nodos y conexiones. Configuracion de cada step (participantes, condiciones, acciones). Preview y simulacion. | S28-S32 | Alto |
| **Templates precargados** | Migrar los 9 flujos Las Condes a WorkflowDefinition. Templates reutilizables entre municipalidades. Import/export de templates. | S32-S33 | Medio |
| **Compras Multi-area** | Workflow de compras configurable por area. Requisitos, visacion, firma por area. Areas ilimitadas. | S33-S34 | Medio |

**Riesgos**:
- Es la etapa mas compleja del plan — motor de reglas + designer visual
- Puede requerir libreria BPMN.js o similar para el designer
- Testing del motor de reglas es critico — edge cases en condiciones

> **NOTA ESTRATEGICA**: Esta etapa transforma DocFlow de PROYECTO a PRODUCTO.
> Sin workflow visual: cada municipalidad nueva = desarrollo custom.
> Con workflow visual: cada municipalidad nueva = configuracion.

---

### ETAPA 6: IA y GenAI
**Duracion**: 8 semanas  
**Objetivo**: Capacidades de inteligencia artificial  
**Prerequisito**: Etapa 5  
**Desbloquea**: Las Condes ~95%

| Tarea | Detalle | Semana | Esfuerzo |
|-------|---------|--------|----------|
| **OCR inteligente** | Integracion Azure AI Document Intelligence o Tesseract. Conversion imagen/scan a texto buscable. Insercion automatica en SearchIndex. | S35-S37 | Alto |
| **Extraccion metadatos** | NER (Named Entity Recognition): personas, fechas, montos, RUTs, numeros de decreto. Indicador de confianza por dato extraido. Sugerencia al usuario para confirmacion. | S37-S39 | Alto |
| **Consultas lenguaje natural** | Integracion LLM (Azure OpenAI / Claude API). RAG: indexar documentos en vector store. Endpoint de consulta: "Cuantos decretos de pago se emitieron en marzo?" | S39-S41 | Alto |
| **Resumen automatico** | Resumen de documentos largos y expedientes. Extraccion de puntos clave. Clasificacion automatica sugerida. | S41-S42 | Medio |

**Riesgos**:
- Costos de API de IA (Azure OpenAI, Document Intelligence)
- Privacidad de datos municipales enviados a servicios cloud
- Precision de OCR en documentos escaneados de baja calidad
- RAG requiere vector database (pgvector, Pinecone, etc.)

---

### ETAPA 7: Integraciones Especificas Las Condes
**Duracion**: 4 semanas  
**Objetivo**: Conectar con sistemas municipales  
**Prerequisito**: Etapa 4  
**Paralelizable con**: Etapa 5 y 6  
**Desbloquea**: Las Condes 100%

| Tarea | Detalle | Semana | Esfuerzo |
|-------|---------|--------|----------|
| **SIM (Sistema Informatico Municipal)** | API de consulta presupuestaria. Disponibilidad de fondos. Imputacion de gastos. | S25-S26 | Medio |
| **CRM** | Recepcion de nominas de beneficiarios (JSON). Inyeccion en flujo de subsidios. | S26-S27 | Medio |
| **Portal Banco** | Generacion de archivos de pago. Consulta de estado de transferencias. Adjuntar comprobantes. | S27-S28 | Medio |
| **Mercado Publico** | Consulta de licitaciones. Vinculacion con flujo de visacion de bases. | S28 | Bajo |

**Riesgos**:
- Cada integracion depende de APIs de terceros (disponibilidad, documentacion)
- SIM y Portal Banco son sistemas criticos — requieren sandbox de testing
- Las APIs municipales pueden no estar documentadas

---

## 5. CARTA GANTT

```
SEMANA:  1  2  3  4  5  6  7  8  9  10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41
         |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

E0       [=====]                                                                                                                    Estabilizacion
         Tests FE  Security  Perf                                                                                                   (3 sem)

E1                [=========================]                                                                                       Core Municipal
                  V.B.      Clave  QR  Subr  Rubr  Correspondencia                                                                 (8 sem)
                            Unica

E2                                           [================]                                                                     Portal + Plantillas
                                             Portal Ciudadano   Firma                                                              (5 sem)
                                             Plantillas Word    Masiva

E3                                           [==================]                                                                   Modulos Gobierno
                                             Horario  Permisos  PMG     Vehic  Calen                                               (6 sem)
                                                                                                                                    PARALELA con E2

E4                                                                      [======================]                                    On-Premise + Flujos
                                                                        OnPrem  9 Flujos           App                             (7 sem)
                                                                                Especializados     Movil

E5                                                                                                 [=====================================]  Workflow Visual
                                                                                                   Motor BPMN     Designer Visual   Templates  Compras       (10 sem)

E6                                                                                                                                  [===========================]  IA y GenAI
                                                                                                                                    OCR    NER    LLM/RAG  Resumen  (8 sem)

E7                                                                                                 [===========]                    Integraciones Las Condes
                                                                                                   SIM  CRM  Banco  MPublico        (4 sem)
                                                                                                                                    PARALELA con E5/E6

         |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
SEMANA:  1  2  3  4  5  6  7  8  9  10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41

HITOS:         E0*              E1*                  E2* E3*             E4*                                    E5*                  E6* E7*
               |                |                    |   |               |                                      |                    |   |
               v                v                    v   v               v                                      v                    v   v
            Sistema          Villarrica          Villarrica          Las Condes                             Villarrica           Las Condes
            estable            85%              95% + Lampa            70%                                    100%                  100%
                                                  100%                                                   Las Condes 85%
```

### Vista simplificada de la Carta Gantt

```
         MES 1      MES 2      MES 3      MES 4      MES 5      MES 6      MES 7      MES 8      MES 9      MES 10
         S1-S4      S5-S8      S9-S12     S13-S16    S17-S20    S21-S24    S25-S28    S29-S32    S33-S36    S37-S41
         -----      -----      ------     -------    -------    -------    -------    -------    -------    -------

E0       [===]
E1       [   =============================]
E2                             [==================]
E3                             [======================]
E4                                                    [=========================]
E5                                                                              [====================================]
E6                                                                                                         [=========================]
E7                                                                              [===========]
```

---

## 6. HITOS Y MUNICIPALIDADES DESBLOQUEADAS

| Hito | Semana | Mes | Etapas completadas | Municipalidad postulable |
|------|--------|-----|-------------------|--------------------------|
| **H1** | 3 | Mes 1 | E0 | Sistema estable, sin deuda tecnica |
| **H2** | 11 | Mes 3 | E0 + E1 | **Villarrica 85%** (postulable con reservas) |
| **H3** | 16 | Mes 4 | E0 + E1 + E2 + E3 | **Villarrica 95%** + **Lampa 100%** |
| **H4** | 24 | Mes 6 | + E4 | **Las Condes 70%** (postulable con reservas) |
| **H5** | 34 | Mes 8.5 | + E5 | **Villarrica 100%** + **Las Condes 85%** |
| **H6** | 41 | Mes 10 | + E6 + E7 | **Las Condes 100%** |

---

## 7. DEPENDENCIAS ENTRE ETAPAS

```
E0 (Estabilizacion)
 |
 v
E1 (Core Municipal)
 |
 +-------+-------+
 |               |
 v               v
E2 (Portal)     E3 (Gobierno/Lampa)    <-- PARALELAS
 |
 v
E4 (On-Premise + Flujos)
 |
 +-------+-------+
 |       |       |
 v       v       v
E5       E6      E7                    <-- E7 PARALELA con E5/E6
(WF)    (IA)    (Integraciones)
```

**Reglas**:
- E0 es prerequisito de TODAS las etapas
- E2 y E3 son PARALELAS (pueden ejecutarse simultaneamente)
- E5, E6 y E7 son parcialmente paralelas (E7 no depende de E5/E6)
- Ruta critica: E0 -> E1 -> E2 -> E4 -> E5 -> E6 (37 semanas)

---

## 8. INVERSION vs. RETORNO

### 8.1 Inversion estimada por etapa

| Etapa | Semanas | Costo estimado (*) |
|-------|---------|-------------------|
| E0 - Estabilizacion | 3 | $3.000.000 |
| E1 - Core Municipal | 8 | $8.000.000 |
| E2 - Portal + Plantillas | 5 | $5.000.000 |
| E3 - Modulos Gobierno | 6 | $6.000.000 |
| E4 - On-Premise + Flujos | 7 | $7.000.000 |
| E5 - Workflow Visual | 10 | $7.500.000 |
| E6 - IA y GenAI | 8 | $5.000.000 |
| **TOTAL** | **41** | **~$41.500.000** |

(*) Estimacion referencial basada en desarrollo AI-asistido.
No incluye costos de infraestructura cloud, licencias de IA, ni publicacion en stores.

### 8.2 Revenue potencial

| Municipalidad | Monto | Etapas requeridas | Inversion acumulada |
|---------------|-------|-------------------|---------------------|
| **Villarrica** | $75.000.000 | E0 + E1 + E2 | $16.000.000 |
| **Lampa** | $25.000.000 | + E3 | $22.000.000 |
| **Las Condes** | $30.000.000 | + E4 + E5 + E6 + E7 | $41.500.000 |
| **TOTAL** | **$130.000.000** | | |

### 8.3 Analisis ROI

| Escenario | Revenue | Inversion | ROI |
|-----------|---------|-----------|-----|
| Solo Villarrica | $75.000.000 | $16.000.000 | **4.7x** |
| Villarrica + Lampa | $100.000.000 | $22.000.000 | **4.5x** |
| Las 3 municipalidades | $130.000.000 | $41.500.000 | **3.1x** |

> **Break-even**: Con solo ganar Villarrica ($75M) se recupera la inversion
> de las primeras 5 etapas y se financia el desarrollo restante.

---

## 9. RIESGOS GENERALES

| Riesgo | Probabilidad | Impacto | Mitigacion |
|--------|-------------|---------|------------|
| Clave Unica requiere tramite gobierno | Alta | Alto | Iniciar tramite en semana 1 |
| APIs de terceros sin documentacion | Media | Alto | Mock services para desarrollo |
| Costos IA superiores a estimado | Media | Medio | Usar modelos open-source como fallback |
| Complejidad workflow visual subestimada | Media | Alto | Evaluar BPMN.js como base |
| Licitaciones con plazos cerrados | Alta | Alto | Priorizar E0+E1+E2 para postular rapido |
| Testing insuficiente en produccion | Media | Alto | E0 establece baseline de testing |

---

## 10. ESTRATEGIA DE PRIORIZACION

### Si hay que recortar scope:

| Prioridad | Etapas | Semanas | Revenue habilitado |
|-----------|--------|---------|-------------------|
| INNEGOCIABLE | E0 + E1 + E2 | 16 | Villarrica $75M |
| ALTA | + E3 | +6 | + Lampa $25M |
| MEDIA-ALTA | + E4 + E5 | +17 | + Las Condes parcial + producto configurable |
| MEDIA | + E6 + E7 | +12 | + Las Condes completo $30M |

### Recomendacion:

**Ejecutar E0 + E1 + E2 + E3 en paralelo (16 semanas)** para postular a Villarrica y Lampa.
Usar el revenue de esos contratos para financiar E4-E7 y cubrir Las Condes.

El **workflow visual (E5)** es el feature mas estrategico a largo plazo: convierte DocFlow
de un proyecto custom a un producto configurable. Cada municipalidad nueva pasa a ser
configuracion en vez de programacion.

---

## 11. PROXIMOS PASOS INMEDIATOS

1. **Decidir prioridad**: Confirmar si se ejecuta el plan completo o se recorta
2. **Iniciar tramite Clave Unica**: Solicitar acceso a sandbox de gobierno
3. **Arrancar Etapa 0**: Estabilizar el producto actual (3 semanas)
4. **Preparar propuesta Villarrica**: Con las capacidades actuales + roadmap comprometido

---

*Documento generado para planificacion estrategica — DocFlow8v2*  
*Abril 2026*
