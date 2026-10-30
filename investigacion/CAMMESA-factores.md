## 1. ¿Qué es CAMMESA?

**CAMMESA** (Compañía Administradora del Mercado Mayorista Eléctrico S.A.) es la entidad mixta (estado nacional y agentes del mercado) que coordina el despacho técnico del SADI (Sistema Argentino de Interconexión) y administra las transacciones económicas del Mercado Eléctrico Mayorista (MEM).

En el marco de la huella de carbono (específicamente para **Alcance 2 - Método Location-based** según el GHG Protocol), CAMMESA junto con la **Secretaría de Energía de la Nación** calculan y publican el **Factor de Emisión de la Red Eléctrica Argentina** ($tCO_2e/MWh$ o $kgCO_2e/kWh$). Este factor refleja la intensidad de carbono promedio de toda la electricidad inyectada al sistema interconectado nacional, considerando la mezcla de fuentes de generación en el país (térmica a gas/fuel oil/gasoil, hidroeléctrica, nuclear y renovables).

---

## 2. Cómo se obtiene el valor de los factores

Los factores oficiales no se publican en un feed JSON en tiempo real; se calculan de forma consolidada de dos maneras:

1. **Factor Oficial Anual Consolidado (Secretaría de Energía / CAMMESA)**:

- **Publicación**: La Dirección Nacional de Generación Eléctrica / Secretaría de Energía publica anualmente el informe oficial _"Factor de emisión de la red eléctrica argentina"_.

- **Unidad**: Expresado habitualmente en $tCO_2/MWh$ (o $gCO_2/kWh$).

- **Uso estándar**: Es el valor que exige la ISO 14064-1 y el GHG Protocol para los inventarios corporativos anuales de GEI.

2. **Datos Operativos Mensuales (CAMMESA)**:

- A través de los **Informes Mensuales del MEM** y los balances de combustibles consumidos y energía generada en bornes de central.
- Contienen el despacho por tecnología (térmica, renovable, etc.) y permiten calcular factores marginales o mensuales aproximados, aunque para inventarios oficiales corporativos se utiliza siempre el consolidado anual de la autoridad de aplicación.

---

## 3. ¿Se puede automatizar la obtención de estos datos para el sistema?

Técnicamente sí, pero **no es necesario ni recomendable hacerlo mediante peticiones en tiempo real en cada cálculo**.

### Por qué no en tiempo real

- **Frecuencia de actualización baja**: La matriz se publica de forma **anual** (o con periodicidad mensual retrospectiva consolidada). No cambia minuto a minuto en términos de inventario oficial.
- **Inmutabilidad de auditoría**: El informe de emisiones de un año específico (por ejemplo, el inventario 2025 o 2026 de una empresa) **debe quedar congelado** con el factor oficial vigente para ese período fiscal/calendario. Si el factor cambiara dinámicamente, los reportes previamente certificados alterarían su resultado, perdiendo validez legal y de auditoría.

### Estrategia recomendada de implementación en Spring Boot + PostgreSQL

#### 2. Nivel de Automatización Factible

- **Opción A: Scraping / Ingesta Batch Programada (Scheduled Task)**:
- Se implementa un `@Scheduled(cron = "0 0 3 1 * ?")` mensual o semestral en Spring Boot.
- La tarea consulta el repositorio de datos abiertos del portal oficial (`datos.gob.ar` o la sección de descargas de CAMMESA/Secretaría de Energía) mediante un cliente HTTP (`RestClient` o `WebClient`).
- Si detecta un nuevo archivo CSV/XLSX del factor anual del año consolidado, parsea el valor e inserta un nuevo registro en `emission_factor` dejando el anterior cerrado con `valid_to`.

- **Opción B: Carga y Validación Administrativa (Recomendada para auditoría ITEC)**:
- Dado que la publicación es solo una o dos veces al año y requiere respaldo normativo, un rol de **Administrador / Auditor del ITEC** actualiza el factor desde el panel de control o aprueba la actualización sugerida por el scraper antes de que impacte en los cálculos corporativos.

- **Snapshot en cada cálculo (Congelamiento de datos)**:
- Cuando una empresa calcula y sella su informe para el período $N$, el backend copia el `factor_id` y el `factor_value` exacto dentro de la fila de la medición:

$$\text{Emisiones } (tCO_2e) = \text{Consumo (MWh)} \times \text{Factor CAMMESA del período } (tCO_2e/MWh)$$

- Esto asegura que el hash criptográfico del PDF y del QR no varíe jamás en el tiempo.
