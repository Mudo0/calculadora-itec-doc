### Plan de Desarrollo Incremental: Calculadora de Huella de Carbono ITEC

Este plan maestro estructura la evolución de la plataforma desde un motor local automatizado hasta un SaaS multi-tenant auditable, aislando el motor de cálculo basado en el GHG Protocol y la ISO 14064-1.

---

### Fase 1: Prototipo Core y Automatización Batch (Mes 1 - 2)

El objetivo es validar el motor matemático y los factores de emisión sin invertir esfuerzo en interfaces de usuario que luego serán descartadas. El sistema operará de manera desatendida o mediante ejecución manual por consola.

**Arquitectura de la Fase 1:**

```text
[Google Sheets / CSV Input] ──Lectura──┐
                                       ▼
                             ┌────────────────────────┐
                             │    SPRING BOOT CORE    │
                             │                        │
                             │ 1. Adaptador Entrada   │
                             │ 2. Motor GHG Protocol  │
                             │ 3. Generador Reporte   │
                             └────────────────────────┘
                                       │
[Google Sheets / PDF Output] ◄─Cálculo─┘

```

**Hitos y Tareas:**

* **Configuración del Motor Core:** Inicializar el proyecto Spring Boot definiendo entidades puras de Java para los Alcances 1, 2 y 3 (GHG Protocol).
* **Integración con Google Workspace:** Desarrollar los adaptadores de entrada y salida para leer celdas de un Google Sheets específico y escribir los resultados en otra hoja.
* **Lógica de Factores de Emisión:** Hardcodear o leer desde un CSV local los factores estáticos iniciales (CAMMESA, DEFRA, EPA).


* **Entregable:** Un script `.jar` ejecutable que toma un ID de Google Sheets, procesa las mediciones de la empresa y devuelve el balance de $tCO_2e$.

---

### Fase 2: Aplicación Web Single-Tenant MVP (Mes 3 - 4)

El objetivo es abandonar el Google Sheets como interfaz y exponer el motor a través de una API REST conectada a una base de datos relacional y una interfaz web (frontend), incorporando la generación formal de certificados.

**Arquitectura de la Fase 2:**

```text
[Frontend UI] (Angular / React)
       │
       ▼ (Llamadas REST / JSON)
┌─────────────────────────────────────────────────────┐
│                 SPRING BOOT API REST                │
│                                                     │
│  [Controladores] ────────► [Motor GHG Fase 1]       │
│                                   │                 │
│  [Módulo de PDF (PDFBox)]         │                 │
│         │ (QR / Hash SHA-256)     ▼                 │
└─────────┼──────────────────┬────────────────────────┘
          ▼                  ▼
   [Certificado PDF]    [PostgreSQL] (Tablas de Factores y Mediciones)[cite: 3]

```

**Hitos y Tareas:**

* **Migración a Base de Datos Relacional:** Implementar PostgreSQL para persistir el modelo de "Factores Versionados" (emission_factor) y el registro histórico de mediciones.


* **Desarrollo de API REST:** Exponer endpoints para la carga manual de datos de actividad (ej. consumo eléctrico, gas natural, combustibles).
* **Generación de Sello Digital:** Integrar Apache PDFBox/OpenPDF y ZXing para generar el reporte de emisiones en PDF con un código QR inyectado y el hash SHA-256 congelado de la medición.
* **Portal de Validación Público:** Crear el endpoint web `/validar/{hash}` para que cualquier persona que escanee el QR del PDF pueda confirmar la inalterabilidad del reporte contra la base de datos.
* **Entregable:** Una aplicación web funcional desplegada en un servidor (ej. VPS o Cloud Run) donde un analista del ITEC puede cargar datos y emitir certificados oficiales.

---

### Fase 3: Escalado SaaS Multi-Tenant (Mes 5 - 6)

El objetivo es transformar la plataforma en un portal donde múltiples empresas interactúen simultáneamente con sus propios datos aislados, bajo la supervisión y auditoría del ITEC.

**Arquitectura de la Fase 3:**

```text
       [Empresa A]           [Empresa B]           [Auditor ITEC]
            │                     │                      │
            └─────────┬───────────┘                      │
                      ▼ (Tokens JWT con Tenant_ID)       ▼
┌─────────────────────┼──────────────────────────────────┼──────┐
│ SPRING BOOT         │                                  │      │
│                     ▼                                  ▼      │
│            [Spring Security] ◄──────────────► [Roles y Auth]  │
│                     │                                         │
│            [Filtro TenantContext]                             │
│                     │                                         │
│                     ▼                                         │
│      [Motor GHG + Módulo PDF (PAdES)]       │
└─────────────────────┬─────────────────────────────────────────┘
                      ▼
   [PostgreSQL] (Row-Level Security o Discriminador @TenantId)
   ┌────────────────────────────────────────────────────────┐
   │ Empresa_ID: A (Mediciones A)                           │
   │ Empresa_ID: B (Mediciones B)                           │
   │ Factores_Globales (Solo lectura para tenants)          │
   └────────────────────────────────────────────────────────┘

```

**Hitos y Tareas:**

* **Estrategia de Multi-Tenancy:** Implementar un discriminador de inquilinos (`tenant_id`) en la base de datos de PostgreSQL, interceptando cada petición HTTP para inyectar el contexto de la empresa mediante Hibernate y evitar fugas de información.
* **Seguridad y Roles (RBAC):** Configurar Spring Security con JWT. Definir roles estrictos:
* *Empresa/Usuario:* Solo carga datos y solicita verificación.
* *Auditor ITEC:* Aprueba inventarios, gestiona factores oficiales y firma.


* **Firma Digital Avanzada:** Evolucionar el PDF básico de la Fase 2 a una firma PAdES utilizando Bouncy Castle y los certificados criptográficos del ITEC (.p12).
* **Entregable:** Plataforma SaaS completa, multi-empresa, lista para comercialización y emisión de certificados con presunción de autoría legal.
