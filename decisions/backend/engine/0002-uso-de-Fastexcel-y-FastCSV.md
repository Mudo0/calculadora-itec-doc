## status: "accepted"
date: 2026-10-22
decision-makers: Mateo y Tomas

# Selección de FastExcel y FastCSV como motor unificado de planillas (.xlsx y .csv) para encuestas

## Contexto y Declaración del Problema

La aplicación requiere implementar un pipeline integral de encuestas basado en planillas de cálculo sin acoplamiento a una plataforma o entorno específico (debe operar offline u online, tanto en aplicaciones Desktop como Web/Backend en Java).

El ciclo de vida del dato comprende tres etapas críticas:

1. **Generación de plantilla base:** Crear programáticamente una planilla con preguntas y estructura variable para definir o alimentar la encuesta (en `.xlsx` formateado o `.csv` plano según el canal de distribución).

2. **Ingesta de respuestas:** Leer e interpretar la planilla resultante tras la recolección de respuestas, donde la fuente puede proveer tanto archivos nativos de Excel (`.xlsx`) como exportaciones tabulares estándar de plataformas web (`.csv`). En ambos casos, las columnas, respuestas abiertas y tipos de datos son dinámicos.

3. **Generación de reporte procesado:** Transformar y calcular métricas mediante el motor propio de la aplicación y emitir una tercera planilla con los resultados agregados y scores (en formato `.xlsx` con soporte visual o `.csv` para exportación masiva).

Dado que el soporte para formato **CSV es un requerimiento obligatorio** junto a **XLSX**, ¿qué combinación de herramientas en el ecosistema Java proporciona soporte de ciclo completo, alta velocidad de procesamiento, mínimo consumo de memoria y adaptabilidad a estructuras dinámicas?

## Impulsores de la Decisión

* **Soporte dual obligatorio (XLSX y CSV):** Capacidad para procesar tanto hojas de cálculo formateadas multietiqueta (`.xlsx`) como exportaciones masivas de texto plano (`.csv`).

* **Soporte de ciclo completo (Lectura y Escritura):** Capacidad para generar plantillas base desde cero, leer datos recopilados y escribir reportes finales en ambos formatos.

* **Flexibilidad ante estructuras dinámicas:** Soporte para esquemas de columnas no fijos (las preguntas y opciones de una encuesta varían dinámicamente y no pueden depender de clases Java/DTOs estáticas compiladas).

* **Agnóstico del entorno de ejecución:** Compatibilidad total tanto para clientes Desktop locales (lectura/escritura offline vía `File` / `Path`) como para servicios Web (flujos reactivos o REST vía `InputStream` / `OutputStream` y `byte[]`).

* **Eficiencia y huella de memoria:** Procesamiento en streaming puro para evitar desbordes de memoria (`OutOfMemoryError`) durante la generación y lectura de conjuntos medianos y grandes de respuestas.

* **Baja complejidad accidental:** Minimizar el código boilerplate necesario para interactuar con filas, delimitadores y celdas.

## Opciones Consideradas

* **Opción A: FastExcel (`fastexcel` + `fastexcel-reader`) + FastCSV (`fastcsv`)**

* **Opción B: EasyExcel (Alibaba)**

* **Opción C: Apache POI + Apache Commons CSV**

## Resultado de la Decisión

Opción elegida: **FastExcel + FastCSV**, porque ofrece la arquitectura de streaming más rápida, liviana y flexible para estructuras dinámicas:

1. **FastExcel** se encarga de la generación y lectura de `.xlsx` de forma no acoplada a DTOs.
2. **FastCSV** (la biblioteca de parseo de CSV más rápida del ecosistema Java, zero-dependency) se encarga de la lectura y escritura de flujos `.csv` con idéntico paradigma de streams por índices.
3. Se implementa una **interfaz común de servicio (`SpreadsheetEngine`)** que abstrae el formato hacia el motor de cálculo según la extensión o MIME type (`.xlsx` vs `.csv`).

### Consecuencias

* **Bueno, porque** cubre el 100% de los casos de uso: `.xlsx` para plantillas/reportes con estructura rica y `.csv` para exportaciones masivas ultralivianas de cualquier plataforma web (Google Forms, Typeform, CRM).

* **Bueno, porque** ambas bibliotecas operan nativamente mediante streaming sobre `InputStream` / `OutputStream` o `Reader` / `Writer`, manteniendo la compatibilidad total entre Desktop (offline) y Web.

* **Bueno, porque** preserva la flexibilidad de columnas dinámicas sin obligar a compilar clases DTO fijas (desventaja insalvable de EasyExcel para encuestas variables).

* **Bueno, porque** la combinación de ambas librerías pesa menos de 1 MB y tiene prácticamente nulo overhead de dependencias transitivas.

* **Neutral, porque** requiere exponer una interfaz interna de abstracción (e.g., `SpreadsheetReader` y `SpreadsheetWriter`) que enrute internamente a FastExcel o FastCSV según el formato del archivo.

* **Malo, porque** sigue sin existir soporte para archivos binarios heredados `.xls` (Excel 97-2003), lo cual se considera aceptable y fuera del alcance moderno.

### Confirmación

El cumplimiento de esta decisión se validará mediante:

1. Pruebas de integración que procesen el mismo set de datos de prueba tanto en formato `.xlsx` como en `.csv` a través de la interfaz unificada del motor, verificando paridad exacta de resultados.

2. Pruebas de carga sobre `FastCSV` procesando archivos CSV de más de 500.000 filas para asegurar streaming continuo sin degradación de RAM heap.

3. Verificación de dependencias en `pom.xml` / `build.gradle` asegurando la presencia exclusiva de `fastexcel`, `fastexcel-reader` y `fastcsv`.

## Pros y Contras de las Opciones

### FastExcel + FastCSV (Seleccionada)

Arquitectura desacoplada de alto rendimiento compuesta por dos librerías especializadas en streaming.

* **Bueno, porque** FastCSV es el parser/writer CSV más rápido y conforme a RFC 4180 en Java, sin dependencias externas.

* **Bueno, porque** ambas bibliotecas comparten la misma filosofía: acceso posicional por índices y evaluación reactiva por flujos (`Stream<Row>` / `CsvRecord`).

* **Bueno, porque** no impone estructuras de datos rígidas; las encuestas dinámicas se gestionan naturalmente como matrices de datos.

* **Neutral, porque** introduce dos dependencias de proveedores distintos, aunque ambas son de código abierto permisivo y tamaño mínimo.

* **Malo, porque** no procesa formatos antiguos `.xls`.

### EasyExcel

Biblioteca unificada de Alibaba que soporta tanto `.xlsx` como `.csv` en un solo paquete.

* **Bueno, porque** ofrece soporte integrado de ambos formatos bajo una sola dependencia.

* **Malo, porque** su diseño está pensado para modelos con cabeceras predecibles anotadas con `@ExcelProperty`. En encuestas con preguntas dinámicas o variables, la creación dinámica de esquemas en tiempo de ejecución resulta sumamente engorrosa.

* **Malo, porque** arrastra dependencias adicionales y un modelo conceptual más rígido.

### Apache POI + Apache Commons CSV

La combinación tradicional del ecosistema Apache.

* **Bueno, porque** soporte absoluto de todos los formatos imaginables (`.xls`, `.xlsx`, `.xlsm`, `.csv`).

* **Malo, porque** Apache POI presenta un alto consumo de memoria RAM y una API sobrecargada de boilerplate para lectura y escritura básica.

* **Malo, porque** Apache Commons CSV es significativamente más lenta en benchmarks de streaming que FastCSV.

## Más Información

* Repositorio oficial de FastExcel: <https://github.com/dhatim/fastexcel>
* Repositorio oficial de FastCSV: <https://github.com/osiegmar/FastCSV>
