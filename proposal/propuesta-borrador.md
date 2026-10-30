Para abordar el desafío del Córdoba Open Challenge y transformar la calculadora manual del Instituto de Transformación Energética de Córdoba (ITEC) en una plataforma autogestionable para empresas, aquí tienes una propuesta técnica y visual estructurada para tu presentación.

## Arquitectura y Stack Tecnológico

Dado el objetivo de crear una plataforma B2B (para empresas) escalable y autogestionable, la arquitectura debe enfocarse en seguridad, concurrencia y un esquema multi-tenant (múltiples empresas usando la misma instancia sin mezclar datos).

* **Backend:** API REST en **Java (Spring Boot)** o **C# (.NET 8/9)**. Ambos manejan excelentemente arquitecturas robustas y seguridad empresarial.
* **Base de Datos:** **PostgreSQL**. Ideal para estructurar jerarquías de datos complejas (empresas, sucursales, períodos de medición, factores de emisión variables). Uso de **Flyway** para el control de versiones de la base de datos.
* **Frontend:** **Angular** o **React** (con TypeScript y Tailwind CSS). Angular aporta una estructura sólida para formularios complejos y reactivos, vitales para una calculadora de múltiples pasos.
* **Infraestructura:** Despliegue del Frontend en **Cloudflare Pages** y el Backend/DB containerizados con **Docker** en un VPS (ej. usando NGINX como reverse proxy). Automatización de CI/CD mediante **GitHub Actions**.

## Estructura del Mockup (Pantallas a Diseñar)

El mockup debe demostrar cómo una empresa pasa de usar una hoja de cálculo a una experiencia guiada e intuitiva.

1. **Landing Page & Onboarding:**
* Explicación de la herramienta avalada por el ITEC.
* Registro de la empresa (Datos fiscales, sector industrial, tamaño).


2. **Dashboard Principal (Vista de Empresa):**
* Tarjetas resumen: Huella de carbono total (toneladas de CO2e), variación interanual.
* Gráfico de torta dividiendo las emisiones por Alcance (Alcance 1: Directas, Alcance 2: Energía, Alcance 3: Indirectas).
* Llamado a la acción principal: "Nueva Medición (Periodo actual)".


3. **Flujo de Carga de Datos (El core de la calculadora):**
* **Diseño tipo "Wizard" (Paso a paso):** En lugar de un Excel gigante, dividir la carga por módulos.
* *Paso 1 - Consumo Energético:* Inputs para KWh consumidos en la planta. Integración opcional para subir la factura de EPEC (lectura futura por OCR).
* *Paso 2 - Combustibles y Flota:* Litros de gasoil, nafta, GNC consumidos.
* *Paso 3 - Residuos y Materiales:* Toneladas de residuos generados, porcentaje reciclado.
* *Nota de UX:* Cada campo debe tener un "tooltip" explicando qué cargar, validaciones en tiempo real, y guardado automático para que el usuario no pierda el progreso.


4. **Pantalla de Reporte y Certificación:**
* Visualización del cálculo final usando los factores de emisión del ITEC integrados en el backend.
* Opción para exportar el reporte en PDF con sello del ITEC.
* Recomendaciones automáticas de reducción basadas en el sector (Economía Circular).


5. **Panel de Administración (Vista ITEC):**
* ABM de Empresas registradas.
* Gestor de Factores de Emisión (interfaz para que los técnicos del ITEC actualicen las fórmulas de CO2 sin tocar código).
* Métricas agregadas del impacto total medido en la ciudad de Córdoba.



## Plan de Proyecto (Hoja de Ruta)

Este plan detalla las fases para el documento de la propuesta:

**Fase 1: Levantamiento y Normalización de Datos (Semanas 1-2)**

* Mapeo de las fórmulas matemáticas, tablas de equivalencias y factores de conversión de la calculadora actual del ITEC.
* Diseño del modelo de datos relacional (Entidades principales: `Tenant/Company`, `MeasurementPeriod`, `ActivityData`, `EmissionFactor`, `CarbonFootprintResult`).

**Fase 2: Desarrollo Backend y Base de Datos (Semanas 3-5)**

* Implementación de la base de datos en PostgreSQL mediante migraciones (Flyway).
* Desarrollo de la API REST para autenticación/autorización (JWT).
* Desarrollo del "Motor de Cálculo" (servicios aislados que toman los datos de entrada, aplican las fórmulas del ITEC y devuelven el CO2e).

**Fase 3: Desarrollo Frontend (Semanas 6-8)**

* Maquetado en Angular/React basado en los mockups.
* Implementación de guardias de rutas y consumo de la API.
* Construcción de los formularios reactivos complejos con validación estricta para evitar errores de tipeo que arruinen el cálculo.

**Fase 4: Testing y Automatización (Semanas 9-10)**

* Pruebas unitarias sobre el motor de cálculo para asegurar que los resultados coinciden exactamente con el Excel original del ITEC al introducir los mismos valores.
* Configuración de pipelines en GitHub Actions para build, test y deploy automático.

**Fase 5: Despliegue y Capacitación (Semanas 11-12)**

* Configuración del VPS, NGINX, SSL (Let's Encrypt) y contenedores Docker.
* Pase a producción del frontend en Cloudflare Pages.
* Prueba piloto con 2 o 3 empresas reales proporcionadas por el desafío.



### Cosas a tener en cuenta para la construccion
- Conexión fluida con sistemas ERP o software contable existente.
- Adaptación a marcos como CSRD, SBTi o CBAM.
- Cobertura de Alcance 3: Capacidad para medir emisiones indirectas de la cadena de suministro.

### Estandares a seguir:

- GHG Protocol (Greenhouse Gas Protocol): Es el estándar global. Tenés que buscar y leer su "Estándar Corporativo de Contabilidad y Reporte" (Corporate Accounting and Reporting Standard). Define cómo clasificar los datos de la empresa y cómo evitar la doble contabilidad.
- Norma ISO 14064-1: Es la certificación técnica internacional para el diseño, desarrollo y reporte de inventarios de emisiones de GEI en empresas.
- Bases de Datos de Factores de Emisión: Para ver de dónde salen los números multiplicadores, podés consultar las tablas públicas de DEFRA (Reino Unido, muy usadas a nivel mundial), EPA (Estados Unidos) o la Secretaría de Energía de la Nación Argentina (que publica anualmente el factor de emisión de nuestra red eléctrica local).

### Motor de cálculo

- Alcance 1 (Emisiones Directas): Combustible quemado por la empresa (litros de nafta/gasoil de sus vehículos, gas natural de sus calderas).
- Alcance 2 (Emisiones Indirectas por Energía): Electricidad comprada a la red (KWh consumidos en la factura de EPEC).
- Alcance 3 (Otras Indirectas): Viajes de empleados, residuos generados, transporte de proveedores. (Suele ser opcional en calculadoras básicas).

---
## Vistas Mockup
### Grafico de emisiones por alcance

En tu mockup (pensando en tu stack con Angular/React y utilizando librerías como Chart.js, Recharts o Ng2-Charts), el estándar visual es un **gráfico de dona (Doughnut chart)** o de **barras apiladas**, donde la empresa visualiza rápidamente qué área contamina más.

Esta es la división técnica que tenés que representar en el flujo de carga (Wizard) y en los gráficos:

* **Alcance 1 (Emisiones Directas):** Fuentes que la empresa posee o controla físicamente.
* *Qué se carga en el formulario:* Consumo de gas natural en calderas/hornos, litros de combustible (nafta/gasoil) consumidos por la flota de vehículos propios, y fugas de gases de equipos de aire acondicionado.


* **Alcance 2 (Emisiones Indirectas por Energía):** Emisiones de la generación de energía que la empresa compra para funcionar.
* *Qué se carga en el formulario:* Exclusivamente el consumo eléctrico (los KWh facturados por EPEC). La empresa no emite el humo en su oficina, pero la central térmica que generó esa electricidad sí lo hizo.


* **Alcance 3 (Otras Indirectas - Cadena de Valor):** Emisiones que ocurren como consecuencia de la actividad de la empresa, pero en fuentes que no le pertenecen ni controla.
* *Qué se carga en el formulario:* Tratamiento de residuos (toneladas de basura enviadas a predio de enterramiento), viajes de empleados (vuelos comerciales, uso de taxis), y logística subcontratada a terceros.

**Dato clave para la UX de tu prototipo:** En las normativas internacionales, medir el Alcance 1 y 2 es obligatorio, pero el Alcance 3 suele ser opcional o progresivo porque es muy difícil conseguir esos datos para una pyme. Podés reflejar esto en tu frontend haciendo que el paso de Alcance 3 tenga un botón de "Omitir por ahora" o "Completar más adelante".

### Rol del ITEC

El ITEC es el **Super Administrador** del sistema. Su participación ocurre en un panel de control exclusivo (Backoffice) y se centra en dos tareas críticas que hacen que la calculadora sea dinámica y no un simple Excel estático:

* **Gestión de los Factores de Emisión (La variable clave):** Los coeficientes que multiplican el consumo de las empresas cambian constantemente. Por ejemplo, si la red eléctrica de EPEC un año incorpora más energía solar, el factor de emisión baja; si quema más gasoil, sube. El ITEC necesita una vista donde sus técnicos puedan actualizar estos valores (ej: "Factor Eléctrico 2026 = 0.28"). Cuando el backend recibe los datos de una empresa, consulta esta tabla administrada por el ITEC para garantizar que la multiplicación use la normativa vigente.
* **Dashboard Global (Observatorio):** Mientras cada empresa ve exclusivamente su propia huella, el ITEC accede a la sumatoria de toda la plataforma. Necesitan visualizar cuántas toneladas de CO2e reportaron en total las empresas de Córdoba, segmentando los datos por tamaño de empresa o sector industrial para definir políticas públicas.



