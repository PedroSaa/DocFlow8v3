# CARTA GANTT — Plan de Desarrollo Premium DocFlow8v2

**Fecha**: Abril 2026  
**Duracion total**: 41 semanas (~10 meses)

---

## Carta Gantt (Mermaid)

> Para visualizar: abrir en GitHub, VS Code con extension Mermaid, o https://mermaid.live

```mermaid
gantt
    title Plan de Desarrollo Premium — DocFlow8v2
    dateFormat  YYYY-MM-DD
    axisFormat  %b %d

    section E0 - Estabilizacion
    Tests frontend                    :e0a, 2026-04-14, 2w
    Code review + security audit      :e0b, 2026-04-14, 1w
    Corregir deuda tecnica (FTS, PDF) :e0c, 2026-04-21, 2w
    Performance baseline              :e0d, 2026-04-28, 1w
    HITO: Sistema estable             :milestone, m0, 2026-05-05, 0d

    section E1 - Core Municipal
    V.B. (Visto Bueno)                :e1a, 2026-05-05, 3w
    Clave Unica (OpenID Connect)      :e1b, 2026-05-12, 2w
    QR en documentos                  :e1c, 2026-05-26, 1w
    Subrogancia                       :e1d, 2026-05-26, 2w
    Rubricas multiples                :e1e, 2026-06-09, 2w
    Correspondencia formal            :e1f, 2026-06-09, 3w
    HITO: Villarrica 85%              :milestone, m1, 2026-06-30, 0d

    section E2 - Portal + Plantillas
    Portal Ciudadano                  :e2a, 2026-06-30, 4w
    Plantillas Word/PDF               :e2b, 2026-07-07, 2w
    Firma Masiva                      :e2c, 2026-07-21, 2w
    HITO: Villarrica 95%              :milestone, m2, 2026-08-04, 0d

    section E3 - Modulos Gobierno (paralela E2)
    Control Horario                   :e3a, 2026-06-30, 2w
    Permisos/Vacaciones               :e3b, 2026-07-07, 2w
    PMG                               :e3c, 2026-07-14, 3w
    Vehiculos/Cometidos               :e3d, 2026-07-28, 2w
    Calendario Institucional          :e3e, 2026-08-04, 1w
    HITO: Lampa 100%                  :milestone, m3, 2026-08-11, 0d

    section E4 - On-Premise + Flujos
    Modalidad On-Premise              :e4a, 2026-08-11, 2w
    Flujos especializados (9)         :e4b, 2026-08-18, 5w
    App Movil completa                :e4c, 2026-09-08, 3w
    HITO: Las Condes 70%              :milestone, m4, 2026-09-29, 0d

    section E5 - Workflow Visual
    Motor BPMN-like                   :e5a, 2026-09-29, 4w
    Designer visual drag-drop         :e5b, 2026-10-20, 4w
    Templates precargados             :e5c, 2026-11-16, 2w
    Compras Multi-area                :e5d, 2026-11-23, 2w
    HITO: Villarrica 100%             :milestone, m5, 2026-12-07, 0d

    section E6 - IA y GenAI
    OCR inteligente                   :e6a, 2026-12-07, 3w
    Extraccion metadatos (NER)        :e6b, 2026-12-21, 3w
    Consultas lenguaje natural (RAG)  :e6c, 2027-01-11, 3w
    Resumen automatico                :e6d, 2027-01-25, 2w
    HITO: Las Condes 95%              :milestone, m6, 2027-02-08, 0d

    section E7 - Integraciones (paralela E5/E6)
    SIM (financiero)                  :e7a, 2026-09-29, 2w
    CRM beneficiarios                 :e7b, 2026-10-13, 1w
    Portal Banco                      :e7c, 2026-10-20, 2w
    Mercado Publico                   :e7d, 2026-10-27, 1w
    HITO: Las Condes 100%             :milestone, m7, 2027-02-08, 0d
```

---

## Vista Tabular

```
SEMANA:  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41
MES:     |--- ABR ---|--- MAY ---|--- JUN ---|--- JUL ---|--- AGO ---|--- SEP ---|--- OCT ---|--- NOV ---|--- DIC ---|--- ENE ---|--

E0       [========]
          Tests FE
          Security
          FTS+PDF

E1                  [==============================]
                     V.B.    Clave   QR   Subrog  Rubric  Correspondencia
                             Unica        ancia   as

E2                                                [================]
                                                   Portal Ciudadano
                                                   Plantillas  Firma
                                                   Word/PDF    Masiva

E3                                                [==================]                                                              PARALELA
                                                   Horario  Permisos  PMG  Vehic  Calend                                           con E2

E4                                                                        [======================]
                                                                           OnPrem  9 Flujos     App
                                                                                   Especializados Movil

E5                                                                                                [====================================]
                                                                                                   Motor    Designer   Templates  Compras
                                                                                                   BPMN     Visual     Precargados Multi-area

E6                                                                                                                                 [=========================]
                                                                                                                                    OCR   NER   LLM/RAG  Resumen

E7                                                                                                [===========]                    PARALELA
                                                                                                   SIM  CRM  Banco  MPublico       con E5/E6

HITOS:      *E0                    *E1                  *E2  *E3            *E4                              *E5                         *E6/*E7
         Sistema              Villarrica            Villarrica           Las Condes                      Villarrica                   Las Condes
         estable                85%                95% Lampa               70%                             100%                        100%
                                                    100%                                              Las Condes
                                                                                                        85%
```

---

## Fechas Estimadas de Hitos

| Hito | Fecha estimada | Que se desbloquea |
|------|---------------|-------------------|
| **E0 completa** | 5 Mayo 2026 | Sistema estable, sin deuda tecnica |
| **E1 completa** | 30 Junio 2026 | Villarrica postulable (85%) |
| **E2 completa** | 4 Agosto 2026 | Villarrica 95% |
| **E3 completa** | 11 Agosto 2026 | Lampa 100% |
| **E4 completa** | 29 Septiembre 2026 | Las Condes 70% |
| **E5 completa** | 7 Diciembre 2026 | Villarrica 100% + Las Condes 85% |
| **E6 + E7 completa** | 8 Febrero 2027 | Las Condes 100% |

---

## Ruta Critica

```
E0 (3s) --> E1 (8s) --> E2 (5s) --> E4 (7s) --> E5 (10s) --> E6 (8s)
                                                              = 41 semanas

Paralelas (no afectan ruta critica):
- E3 corre en paralelo con E2
- E7 corre en paralelo con E5/E6
```

---

## Resumen de Recursos por Mes

| Mes | Etapas activas | Features principales |
|-----|---------------|---------------------|
| **Abril 2026** | E0 | Tests, security, deuda tecnica |
| **Mayo 2026** | E1 | V.B., Clave Unica, QR |
| **Junio 2026** | E1 | Subrogancia, rubricas, correspondencia |
| **Julio 2026** | E2 + E3 | Portal Ciudadano + modulos gobierno |
| **Agosto 2026** | E2 + E3 + E4 | Plantillas + On-Premise |
| **Septiembre 2026** | E4 | 9 flujos + app movil |
| **Octubre 2026** | E5 + E7 | Motor BPMN + integraciones |
| **Noviembre 2026** | E5 | Designer visual |
| **Diciembre 2026** | E5 + E6 | Templates + OCR |
| **Enero 2027** | E6 | LLM/RAG + resumen automatico |
| **Febrero 2027** | E6 | Cierre y estabilizacion |

---

*Carta Gantt generada para planificacion estrategica — DocFlow8v2*  
*Abril 2026*
