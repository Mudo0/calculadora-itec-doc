---
estado: accepted
fecha: 2026-10-22
tomadores-de-decision: Mateo Del Lungo, Tomas Aparicio
---

# 0001 - Adopción de Photon y OSRM (Self-hosted) para Servicios de Geolocalización y Rutas

## Contexto y Planteamiento del Problema

Se requiere implementar una solución que permita calcular automáticamente la distancia entre un punto A y un punto B. El sistema debe ser capaz de obtener las coordenadas geográficas a partir de nombres de lugares (geocodificación) y devolver los kilómetros recorridos. El problema radica en elegir la herramienta adecuada que soporte cálculo tanto en línea recta (matemático) como con alta precisión a través de las calles reales, manteniendo los costos operativos nulos y garantizando soporte a nivel global.

## Factores de Decisión

- **Costo operativo:** Necesidad de una solución que sea 100% gratuita, sin límites de peticiones (rate limits) a largo plazo.
- **Independencia (No Vendor Lock-in):** Evitar depender de las cuotas, términos de servicio o aumentos de precios de proveedores externos (Google Maps, APIs Freemium).
- **Flexibilidad de cálculo:** Capacidad de soportar cálculo en línea recta (Fórmula de Haversine) y cálculo preciso por rutas, incluyendo el tipo de vehículo.
- **Escalabilidad y control:** Potencial para escalar la infraestructura según nuestra propia demanda e integrar nuevas funcionalidades.
- **Agnóstico al cliente:** La solución debe ser una API limpia que pueda ser consumida por cualquier tipo de aplicación (Web, Mobile, Desktop).

## Opciones Consideradas

- Nominatim Público (OpenStreetMap)
- APIs Freemium de terceros (LocationIQ, Geoapify)
- **Solución Self-hosted: Photon (Geocodificación) + OSRM (Cálculo de rutas)**

## Resultado de la Decisión

Opción elegida: **"Solución Self-hosted: Photon + OSRM"**, porque es una arquitectura 100% gratuita e independiente que cumple con todos los requisitos. Photon es sumamente ligero y fácil de implementar para resolver las coordenadas (Punto A y Punto B), y OSRM permite el cálculo preciso de rutas por calles, considerando perfiles de vehículos. Al ser agnóstico del tipo de aplicación web/mobile/desktop y ejecutarse en nuestros propios servidores, nos da control total para escalar sin depender de cuotas de terceros y cubre cualquier ubicación del mundo mediante los datos de OpenStreetMap.

### Consecuencias

- **Bueno, porque** los costos operativos por consumo de API se reducen a cero (peticiones ilimitadas).
- **Bueno, porque** tenemos soberanía total sobre los datos y la latencia, pudiendo ejecutarlo incluso en redes privadas.
- **Bueno, porque** permite extender el uso a autocompletado predictivo (búsqueda mientras se escribe) gracias a la velocidad de Photon.
- **Malo, porque** asumimos la responsabilidad de la infraestructura (levantar y mantener los contenedores de Docker o procesos locales).
- **Malo, porque** es necesario realizar actualizaciones manuales periódicas de los mapas de OpenStreetMap si queremos tener las calles nuevas al día.

### Confirmación

La decisión será validada mediante la implementación de una Prueba de Concepto (PoC) donde se levantarán los servicios de Photon y OSRM localmente. Se realizarán peticiones simulando los clientes web y móvil para comprobar la latencia y la precisión al calcular un viaje de prueba, verificando la respuesta tanto con distancia en línea recta como por calles vehiculares.

## Pros y Contras de las Opciones

### Solución Self-hosted: Photon + OSRM

- **Bueno, porque** es 100% gratuita y de código abierto.
- **Bueno, porque** soporta todo tipo de cálculos: geocodificación (Photon), distancia en línea recta con coordenadas devueltas, y distancia en ruta considerando tipo de vehículo (OSRM).
- **Bueno, porque** la arquitectura es escalable a medida que crecen los usuarios.
- **Malo, porque** requiere administrar el servidor o entorno donde estarán alojados.

### APIs Freemium de terceros (LocationIQ / Geoapify)

- **Bueno, porque** la infraestructura y el mantenimiento de los mapas los gestiona un tercero.
- **Bueno, porque** son muy rápidas de integrar con una simple API Key.
- **Malo, porque** existe un límite estricto de peticiones diarias gratuitas (entre 3,000 y 5,000) que bloquea el potencial de escalado sin tener que pagar.

### Nominatim Público (Servidor de OpenStreetMap)

- **Bueno, porque** no requiere registro ni servidor propio para empezar a usarse.
- **Malo, porque** prohíbe terminantemente su uso masivo (límite de 1 petición por segundo) y bloquea el tráfico de aplicaciones que superen esta restricción.
