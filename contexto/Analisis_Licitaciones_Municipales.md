# ANALISIS DE LICITACIONES MUNICIPALES

**Fecha**: Abril 2026  
**Objetivo**: Ganar 3 licitaciones municipales chilenas con DocFlow  
**Revenue potencial total**: $130.000.000 CLP

---

## 1. RESUMEN DE OPORTUNIDADES

| Municipalidad | Presupuesto | Contrato | Modalidad | Usuarios | Cobertura actual |
|---------------|-------------|----------|-----------|----------|-----------------|
| **Villarrica** | $75.000.000 | 36 meses | SaaS | 600 | ~70% |
| **Lampa** | $25.000.000 | 12 meses | SaaS | — | ~55% |
| **Las Condes** | $30.000.000 (garantia) | 48 meses | On-Premise | 600 | ~40% |

### Estrategia recomendada
- **Prioridad 1**: Villarrica — mayor presupuesto, mayor cobertura, ROI 4.7x solo con ella
- **Prioridad 2**: Lampa — complementaria, modulos gobierno especificos
- **Prioridad 3**: Las Condes — mas exigente (IA, On-Premise, 9 flujos), pero contrato largo (48 meses)

---

## 2. GAP ANALYSIS — FEATURES CRITICOS

### 2.1 Gaps que piden 2-3 municipalidades (maxima prioridad)

| # | Feature | Lampa | Villarrica | Las Condes | Descripcion |
|---|---------|:-----:|:----------:|:----------:|-------------|
| 1 | **V.B. (Visto Bueno)** | SI | SI | SI | Modelo de visacion: solicitar, aprobar, rechazar con fecha limite. Cadena configurable. |
| 2 | **Clave Unica** | SI | SI | - | Integracion OpenID Connect con ClaveUnica.gob.cl. Login alternativo. Mapeo RUT a usuario. |
| 3 | **QR en documentos** | SI | SI | - | QR con URL de verificacion en PDF generados. Endpoint publico de validacion. |
| 4 | **Subrogancia** | - | SI | SI | Usuario titular + subrogante + periodo. Herencia de permisos y bandejas. Activacion automatica. |
| 5 | **Rubricas multiples** | SI | SI | - | CRUD de rubricas por usuario (imagen + nombre). Seleccion al firmar. Insercion en documento. |
| 6 | **Correspondencia formal** | SI | - | SI | Registro, clasificacion (oficio, carta, memorandum). Destinos multiples, respuestas, plazos. |

### 2.2 Gaps diferenciadores (valor agregado)

| # | Feature | Lampa | Villarrica | Las Condes | Descripcion |
|---|---------|:-----:|:----------:|:----------:|-------------|
| 7 | **Portal Ciudadano** | SI | SI | - | SPA publica: consulta tramites, OIRS digital, verificacion QR, login Clave Unica. |
| 8 | **Workflow visual Low-Code** | - | - | SI | Motor BPMN-like con designer drag & drop. Convierte DocFlow de proyecto a producto. |
| 9 | **Plantillas Word/PDF** | SI | SI | - | Motor de plantillas con variables dinamicas. Import desde Word. Preview antes de firmar. |
| 10 | **Firma Masiva** | SI | - | - | Seleccion multiple + firma batch con barra de progreso. |
| 11 | **Busqueda Full-Text** | - | SI | - | Ya existe la entidad SearchIndex pero usa LIKE. Conectar a FTS real. |
| 12 | **App Movil completa** | SI | - | - | Ver documentos, dar V.B., firmar, Clave Unica desde movil. |

### 2.3 Gaps especificos Las Condes (premium)

| # | Feature | Descripcion |
|---|---------|-------------|
| 13 | **IA/GenAI** | OCR inteligente, extraccion metadatos (NER), consultas lenguaje natural (RAG), resumen automatico |
| 14 | **9 flujos especializados** | Decreto Pagos S1/S2, Decreto Subsidios, Pago Facturas, Decretos Personal, Visacion Bases Licitacion, Transparencia Activa, Transparencia Pasiva/SAI, Tramitacion Libre, Oficios DocDigital |
| 15 | **Integraciones municipales** | SIM (financiero), CRM beneficiarios, Portal Banco, Mercado Publico |
| 16 | **On-Premise** | Installer local, config DB/storage/SMTP, backup/restore, documentacion HW |

### 2.4 Gaps especificos Lampa (gobierno)

| # | Feature | Descripcion |
|---|---------|-------------|
| 17 | **Control Horario** | Registro entrada/salida, restriccion por equipo, historial, calculo horas, export Excel |
| 18 | **Permisos/Vacaciones** | Tipos multiples (vacaciones, horas extras, matrimonio, etc.), solicitud, aprobacion, saldo |
| 19 | **PMG** | Programa Mejoramiento Gestion: objetivos, metas, verificadores, evidencias, revision comite |
| 20 | **Vehiculos/Cometidos** | CRUD vehiculos, conductores, planificacion viajes, alertas revision tecnica, cometidos |
| 21 | **Calendario Institucional** | Calendario compartido, edicion por rol, categorias, export ICS |

### 2.5 Gaps especificos Villarrica

| # | Feature | Descripcion |
|---|---------|-------------|
| 22 | **Compras Multi-area** | Workflow de compras configurable por area. Requisitos, visacion, firma por area. |

---

## 3. COBERTURA POR ETAPA DE DESARROLLO

| Etapa | Semanas | Villarrica | Lampa | Las Condes |
|-------|---------|-----------|-------|-----------|
| Estado actual | — | 70% | 55% | 40% |
| + E0 (Estabilizacion) | 3 | 70% | 55% | 40% |
| + E1 (Core Municipal) | +8 | **85%** | 70% | 55% |
| + E2 (Portal + Plantillas) | +5 | **95%** | 85% | 55% |
| + E3 (Gobierno/Lampa) | +6 | 95% | **100%** | 55% |
| + E4 (On-Premise + Flujos) | +7 | 95% | 100% | **70%** |
| + E5 (Workflow Visual) | +10 | **100%** | 100% | **85%** |
| + E6 (IA/GenAI) | +8 | 100% | 100% | **95%** |
| + E7 (Integraciones) | +4 | 100% | 100% | **100%** |

---

## 4. ANALISIS FINANCIERO

### Inversion por etapa

| Etapa | Semanas | Costo estimado |
|-------|---------|---------------|
| E0 - Estabilizacion | 3 | $3.000.000 |
| E1 - Core Municipal | 8 | $8.000.000 |
| E2 - Portal + Plantillas | 5 | $5.000.000 |
| E3 - Modulos Gobierno | 6 | $6.000.000 |
| E4 - On-Premise + Flujos | 7 | $7.000.000 |
| E5 - Workflow Visual | 10 | $7.500.000 |
| E6 - IA y GenAI | 8 | $5.000.000 |
| **TOTAL** | **41** | **~$41.500.000** |

### ROI por escenario

| Escenario | Revenue | Inversion | ROI | Etapas necesarias |
|-----------|---------|-----------|-----|-------------------|
| Solo Villarrica | $75.000.000 | $16.000.000 | **4.7x** | E0 + E1 + E2 |
| Villarrica + Lampa | $100.000.000 | $22.000.000 | **4.5x** | + E3 |
| Las 3 municipalidades | $130.000.000 | $41.500.000 | **3.1x** | Todas |

### Estrategia de priorizacion

| Prioridad | Etapas | Semanas | Revenue habilitado |
|-----------|--------|---------|-------------------|
| **INNEGOCIABLE** | E0 + E1 + E2 | 16 | Villarrica $75M |
| ALTA | + E3 | +6 | + Lampa $25M |
| MEDIA-ALTA | + E4 + E5 | +17 | + Las Condes parcial + producto configurable |
| MEDIA | + E6 + E7 | +12 | + Las Condes completo $30M |

> **Break-even**: Con solo ganar Villarrica ($75M) se recupera la inversion de las primeras 5 etapas y se financia el desarrollo restante.

---

## 5. RIESGOS POR MUNICIPALIDAD

### Villarrica (riesgo bajo)
- Cobertura actual mas alta (70%)
- SaaS — no requiere On-Premise
- Solo necesita E0 + E1 + E2 para 95%
- **Riesgo principal**: Clave Unica requiere tramite con gobierno

### Lampa (riesgo medio)
- Modulos gobierno son especificos de Chile (PMG, horarios)
- Requiere validacion con usuario real para PMG
- **Riesgo principal**: Modulos aislados del core — bajo riesgo de regresion pero mucho scope

### Las Condes (riesgo alto)
- Cobertura actual mas baja (40%)
- Requiere On-Premise, IA/GenAI, 9 flujos, integraciones
- Contrato mas largo (48 meses) pero garantia ($30M no es el total)
- **Riesgo principal**: Scope muy amplio — workflow visual + IA son las etapas mas complejas

---

## 6. HITOS DE POSTULACION

| Hito | Fecha estimada | Municipalidad postulable |
|------|---------------|--------------------------|
| E0 completa | Mayo 2026 | Sistema estable |
| E1 completa | Junio 2026 | **Villarrica postulable (85%)** |
| E2 + E3 completas | Agosto 2026 | **Villarrica 95% + Lampa 100%** |
| E4 completa | Septiembre 2026 | **Las Condes postulable (70%)** |
| E5 completa | Diciembre 2026 | **Todas con workflow visual** |
| E6 + E7 completas | Febrero 2027 | **Las Condes 100%** |

---

*Documento de referencia para planificacion comercial — DocFlow8v3*  
*Abril 2026*
