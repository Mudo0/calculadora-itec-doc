---
status: "accepted"
date: 2026-10-29
decision-makers: [Equipo de Desarrollo Backend]
---

# Adopción de Test-Driven Development (TDD) para el Núcleo del Sistema

## Contexto y Declaración del Problema

El sistema requiere procesar cálculos críticos de alta precisión y prevé incorporar a futuro un modelo riguroso de autorización y permisos por roles/recursos. Desarrollar estas funcionalidades complejas mediante pruebas manuales o escribiendo tests a posteriori eleva sustancialmente el riesgo de errores aritméticos, desbordamientos, regresiones no detectadas y brechas de seguridad en las reglas de acceso. 

¿Cómo garantizamos desde la concepción del código que los algoritmos de cálculo sean matemáticamente exactos y que las políticas de seguridad se cumplan de manera inalterable y auditable?

## Impulsores de la Decisión

- **Precisión y robustez en cálculos:** Cero margen de error en operaciones matemáticas complejas, redondeos y casos borde.
- **Seguridad y autorización progresiva:** Necesidad de validar permisos, aislamiento de datos y matrices de roles sin riesgo de regresiones futuras.
- **Diseño desacoplado:** Fomentar una arquitectura limpia (puertos y adaptadores / capas aisladas) impulsada por interfaces fácilmente testeables.
- **Red de seguridad para refactorización:** Libertad para optimizar modelos de datos y algoritmos con retroalimentación inmediata sobre roturas.

## Opciones Consideradas

- **Test-Driven Development (TDD):** Red-Green-Refactor como guía de diseño e implementación.
- **Test-After (Pruebas unitarias posteriores al código de producción):** Escribir primero la implementación funcional y añadir cobertura después.
- **Testing exclusivamente manual y E2E tardío:** Depender de pruebas funcionales de caja negra sobre la API/UI construida.

## Resultado de la Decisión

Opción elegida: **"Test-Driven Development (TDD)"**, porque es la única metodología que fuerza la delimitación exacta de los casos de borde y reglas de negocio antes de introducir complejidad técnica, garantizando una cobertura estructural intrínseca en algoritmos de cálculo y matrices de permisos.

### Consecuencias

- Bueno, porque los cálculos complejos quedan blindados con pruebas parametrizadas y verificación de casos límite desde el día cero.
- Bueno, porque las reglas de seguridad y control de acceso quedan documentadas de forma ejecutable (tests de autorización por rol/recurso).
- Bueno, porque previene la sobre-ingeniería al obligar a escribir únicamente el código de producción necesario para pasar los casos definidos.
- Bueno, porque reduce drásticamente el costo temporal de debugging en etapas avanzadas de integración.
- Malo, porque introduce una curva inicial de desarrollo percibida como más lenta durante los primeros sprints.
- Malo, porque exige disciplina rigurosa de equipo para no saltear el ciclo Red-Green-Refactor cuando apremien los tiempos de entrega.

### Confirmación

- **Pipeline de CI/CD:** El pipeline bloqueará cualquier Pull Request que contenga código de producción sin sus correspondientes tests asociados o que reduzca el umbral de cobertura estipulado (mínimo 85% en módulos de cálculo y autorización).
- **Code Reviews:** Se verificará que los commits reflejen la evolución de los casos de prueba o que el diseño del código demuestre testabilidad directa sin acoplamiento a infraestructura.
- **Auditoría de Mutation Testing:** Integración periódica de herramientas como PIT (Java) o Stryker (.NET) para validar la efectividad real de los tests frente a mutaciones en algoritmos críticos.

## Pros y Contras de las Opciones

### Test-Driven Development (TDD)

Desarrollo iterativo guiado por pruebas: definir el comportamiento esperado (falla), escribir el código mínimo indispensable (éxito) y refactorizar para optimizar legibilidad y rendimiento.

- Bueno, porque asegura que la suite de tests se ejecute en segundos y no existan caminos de código de cálculo huérfanos de verificación.
- Bueno, porque expone tempranamente acoplamientos innecesarios con bases de datos o frameworks en la lógica de negocio pura.
- Neutral, porque requiere acostumbrar al equipo a modularizar con precisión interfaces y dobles de prueba (fakes, stubs).
- Malo, porque demanda mayor inversión de tiempo inicial antes de obtener un primer entregable funcional visible.

### Test-After (Pruebas posteriores)

Implementar controladores, servicios y persistencia primero; redactar los tests unitarios e integrados al finalizar la funcionalidad.

- Bueno, porque permite prototipado rápido inicial sin fricción de diseño orientada a tests.
- Malo, porque suele omitir casos borde de cálculo complejos, testeando únicamente el "camino feliz".
- Malo, porque el código suele quedar fuertemente acoplado a la infraestructura, dificultando mockear dependencias al momento de escribir los tests.
- Malo, porque existe alta tendencia a abandonar la escritura de tests cuando los plazos de entrega se acortan.

### Testing exclusivamente manual y E2E tardío

Probar el sistema interactuando con la API o interfaz gráfica una vez desplegado el entorno.

- Bueno, porque no requiere configurar herramientas de mocking, fixtures ni runners locales en las primeras semanas.
- Malo, porque no escala: verificar todas las combinaciones de permisos y cálculos matemáticos manualmente en cada cambio es inviable y propenso a errores humanos.
- Malo, porque diagnosticar el origen exacto de un error de cálculo a través de respuestas HTTP tardías consume horas innecesarias de depuración.

## Más Información

- Los módulos de **Cálculos de Dominio** deben desarrollarse como código puro (sin dependencias a bases de datos ni frameworks externos) usando tests unitarios y parametrizados.
- El módulo de **Seguridad/Permisos** debe estructurarse mediante pruebas unitarias de políticas (validación de claims/roles) y pruebas de integración focalizadas que verifiquen el bloqueo de endpoints no autorizados con código HTTP 401/403.
- La decisión entra en vigencia a partir del commit inicial del repositorio backend y se mantendrá como estándar mandatario para todo módulo de lógica crítica.
