# GHG Protocol (Greenhouse Gas Protocol)

## 1. Qué es y cómo funciona

El **Greenhouse Gas Protocol (GHG Protocol)** es el marco metodológico global de referencia (desarrollado por el *World Resources Institute - WRI* y el *World Business Council for Sustainable Development - WBCSD*) para cuantificar, gestionar y reportar inventarios corporativos de emisiones de Gases de Efecto Invernadero (GEI). Proporciona la base de cálculo de normas como ISO 14064-1, esquemas de reporte ESG (CDP, GRI, CSRD) y regulaciones internacionales.

### Definición de Límites

1. **Límites Organizacionales**:
   - **Control Operacional**: La organización contabiliza el 100% de las emisiones de las operaciones sobre las cuales tiene autoridad plena para introducir e implementar sus políticas operativas (enfoque más común y recomendado para la Calculadora ITEC).
   - **Control Financiero**: Contabiliza el 100% si puede dirigir las políticas financieras y operativas con el fin de obtener beneficios económicos.
   - **Participación Accionaria (Equity Share)**: Contabiliza según el porcentaje de participación en el capital o derechos económicos de cada operación/sede.

2. **Límites Operacionales (Los 3 Alcances)**:
   - **Alcance 1 (Directas)**:
     - *Combustión fija*: Calderas, hornos, generadores diésel (gas natural, GLP, fuel oil).
     - *Combustión móvil*: Vehículos de flota propia o arrendada bajo control operacional (nafta, gasoil, GNC).
     - *Emisiones fugitivas*: Pérdidas o recargas de gases refrigerantes (R-410A, R-134a, R-22) en sistemas HVAC, chillers o cámaras frigoríficas.
     - *Emisiones de proceso*: Reacciones químicas directas (cemento, cal, química pesada).
   - **Alcance 2 (Indirectas por energía adquirida)**:
     - Generación de electricidad, vapor, calefacción o refrigeración comprada y consumida por la organización.
     - **Enfoque Doble (Dual Reporting)**:
       - *Location-based*: Refleja la intensidad media de emisiones de la red eléctrica donde opera la instalación (en Argentina: factor estandarizado de la red interconectada provisto anualmente por la Secretaría de Energía / CAMMESA en $\text{tCO}_2\text{e/MWh}$).
       - *Market-based*: Refleja emisiones contractuales derivadas de instrumentos directos de energía limpia (contratos MATER, certificados de atributos I-RECs o autogeneración in situ).
   - **Alcance 3 (Cadena de valor aguas arriba y aguas abajo)**:
     - 15 categorías que cubren el ciclo de vida completo: compras de bienes y servicios (Cat. 1), transporte y distribución de insumos (Cat. 4), generación de residuos (Cat. 5), viajes de negocios (Cat. 6), commuting o traslado del personal (Cat. 7), fin de vida de productos vendidos (Cat. 12), etc.

### Fórmula Matemática Operativa del Inventario

Para cada actividad y gas:

$$\text{Emisiones } (\text{tCO}_2\text{e}) = \sum \left( \text{Dato de Actividad} \times \text{Factor de Emisión} \times \text{GWP} \right)$$

Donde:
- **Dato de Actividad**: Magnitud del consumo medible con respaldo documental (ej. $\text{m}^3$ de gas natural, $\text{kWh}$ de electricidad, $\text{L}$ de gasoil, $\text{kg}$ de refrigerante recargado).
- **Factor de Emisión (FE)**: Tasa de emisión por unidad de actividad para cada gas específico ($\text{CO}_2$, $\text{CH}_4$, $\text{N}_2\text{O}$).
- **GWP (Global Warming Potential)**: Potencial de Calentamiento Global de cada gas relativo al $\text{CO}_2$ en un horizonte de 100 años (según los reportes del IPCC, ej. AR5 o AR6).

---

## 2. Riesgos y Beneficios

### Beneficios
- **Estandarización y Comparabilidad**: Permite auditar organizaciones de distintos sectores bajo criterios homogéneos y trazables en el observatorio provincial/regional.
- **Preparación ante Barreras Comerciales**: Cumple con las bases metodológicas exigidas por mecanismos de ajuste en frontera (ej. CBAM en la Unión Europea) y estándares bancarios de finanzas sostenibles.
- **Doble Reporte Transparente de Renovables**: Permite a las empresas con contratos MATER o paneles solares demostrar su descarbonización real (vía *market-based*) sin ocultar el impacto físico en la red eléctrica (*location-based*).
- **Escalabilidad Modular**: Permite implementar un MVP centrado estrictamente en Alcance 1 y 2 (lo auditable con facturas de EPEC y Ecogas) y habilitar módulos de Alcance 3 progresivamente.

### Riesgos y Desafíos
- **Disponibilidad y Trazabilidad de Factores de Emisión Locales**:
  - En Argentina, los factores de la red eléctrica (CAMMESA) y combustibles líquidos se actualizan con desfase temporal.
  - Para Alcance 3 y gases refrigerantes suele requerirse recurrir a bases internacionales (DEFRA / EPA), lo que introduce incertidumbre si no se parametriza y versiona adecuadamente la fuente.
- **Riesgo de Doble Contabilización (Double Counting)**:
  - Frecuente al reportar Alcance 2 sin respaldos contractuales válidos (ej. atribuirse renovables vendidas como atributos a terceros).
  - Confusión entre límites de Alcance 1 (vehículos propios) y Alcance 3 Cat. 6/7 (vehículos privados de empleados o contratistas logísticos).
- **Complejidad y Brecha de Datos en Alcance 3**: La recolección de datos de proveedores suele basarse en estimaciones de gasto monetario (*spend-based*) con baja precisión, lo que puede distorsionar el balance final si no se explicita el grado de incertidumbre.
- **Cambios en los Factores de Conversión (GWP del IPCC)**: Pasar de valores AR4 a AR5 o AR6 modifica los resultados de emisiones históricas de metano o refrigerantes, obligando al sistema a manejar un historial temporal de matrices de cálculo.

---

## 3. Implementaciones y Stack Tecnológico

### Arquitectura del Motor de Cálculo en Spring Boot

1. **Aislamiento del Motor de Dominio (Hexagonal / Clean Architecture)**:
   - El motor de cálculo debe ser un módulo de Java puro (`engine-core`), sin acoplamiento a bases de datos ni a controladores web.
   - Recibe modelos inmutables de entrada (`ActivityRecord`) y devuelve `EmissionsResult` con el desglose por gas ($\text{CO}_2, \text{CH}_4, \text{N}_2\text{O}$ y total $\text{tCO}_2\text{e}$).

2. **Diseño de Base de Datos para Factores Versionados (PostgreSQL)**:
   - **`emission_factor_sources`**: Registro del emisor (CAMMESA, DEFRA, EPA, IPCC AR6).
   - **`emission_factors`**:
     - `id`: UUID.
     - `scope`: ENUM (`SCOPE_1`, `SCOPE_2_LOCATION`, `SCOPE_2_MARKET`, `SCOPE_3`).
     - `category`: ENUM (`STATIONARY_COMBUSTION`, `MOBILE_COMBUSTION`, `ELECTRICITY_GRID`, `REFRIGERANTS`, etc.).
     - `unit`: Unidad de entrada requerida (`M3`, `KWH`, `LITERS`, `KG`).
     - `co2_factor`, `ch4_factor`, `n2o_factor`, `gwp_version`.
     - `valid_from`, `valid_to`: Rango de vigencia temporal para que un cálculo de 2024 aplique la matriz de 2024 y no la actual.
   - **`tenant_emission_records`**: Datos de actividad con discriminador de tenant, archivo de respaldo adjunto (PDF de factura) y estado de validación.

3. **Manejo de Precisión y Reglas Contables**:
   - Uso estricto de `java.math.BigDecimal` con redondeo configurado (`RoundingMode.HALF_UP`) para evitar errores de punto flotante en balances de auditoría.
   - Estrategia *Strategy Pattern* para seleccionar automáticamente el algoritmo de cálculo según la combinación de tipo de consumo, combustible y unidad provista.

---

## 4. Dónde Investigar Más y Documentación Oficial

- **Estándares Oficiales de GHG Protocol**:
  - *A Corporate Accounting and Reporting Standard (Revised Edition)*: La biblia del cálculo corporativo.
  - *GHG Protocol Scope 2 Guidance*: Especificaciones sobre el doble reporte (*Location-based* vs. *Market-based*) y criterios contractuales de calidad.
  - *Corporate Value Chain (Scope 3) Standard* y la guía técnica *Technical Guidance for Calculating Scope 3 Emissions*.
- **Reportes del IPCC (GWP)**:
  - Tablas de Potencial de Calentamiento Global de los Informes de Evaluación AR5 y AR6 del Panel Intergubernamental sobre el Cambio Climático.
- **Fuentes de Factores de Emisión de Referencia**:
  - *CAMMESA / Secretaría de Energía de la Nación (Argentina)*: Informes anuales del factor de emisión de la Red Eléctrica Argentina.
  - *UK Government GHG Conversion Factors for Company Reporting (DESNZ / DEFRA)*: Factores para combustibles, transporte y residuos.
  - *US EPA GHG Emission Factors Hub*: Coeficientes de combustión y refrigerantes.