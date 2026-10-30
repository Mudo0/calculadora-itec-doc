# US-[ID]: [Título corto y específico de la funcionalidad]

**Como:** [Rol exacto del usuario o sistema actor]  
**Quiero:** [Acción medible, concreta y atómica]  
**Para:** [Impacto o razón de negocio real y comprobable]  

---

### Descripción
[Contexto breve sobre qué motiva esta US. No incluir descripciones genéricas como "para que sea mejor". Mencionar si existe dependencia con otra US previa].

---

### Criterios de Aceptación (Escenarios BDD)

> **Regla estricta:** Los escenarios deben detallar datos específicos y resultados esperables. Evitar palabras vagas como "correctamente", "bien", "apropiado", "rápido" o "error genérico". Todo debe ser medible.

#### Escenario 1: [Flujo principal con datos específicos]
- **Dado** [Estado inicial, ej: "un factor de emisión de 0.285400 tCO2e/MWh almacenado en 'emission_factor'"]
- **Y** [Condición adicional, ej: "el usuario ingresa un consumo mensual válido"]
- **Cuando** [Acción ejecutada con ejemplos numéricos o llamadas específicas]
- **Entonces** [Resultado exacto, ej: "el sistema retorna el valor multiplicado con 2 decimales de precisión"]
- **Y** [Efecto colateral específico, ej: "se guarda el registro en la tabla X con el estado Y"]

#### Escenario 2: [Manejo de un límite numérico, formato o validación]
- **Dado** [Ej: "un usuario ingresa un consumo negativo (-10)"]
- **Cuando** [Ej: "se presiona calcular o se envía el request a /api/calcular"]
- **Entonces** [Respuesta medible, ej: "el sistema retorna el código 400 Bad Request"]
- **Y** [Mensaje específico esperado, ej: "con el mensaje 'El consumo no puede ser menor a cero'"]
- **Y** [Comportamiento del sistema, ej: "no se registra la transacción en la base de datos"]

#### Escenario 3: [Control de acceso, roles o concurrencia]
- **Dado** [Ej: "un usuario con rol 'EMPRESA_LECTURA' autenticado con JWT"]
- **Cuando** [Ej: "intenta modificar el factor de emisión de CAMMESA"]
- **Entonces** [Respuesta estricta, ej: "la API deniega el acceso con 403 Forbidden"]

---

### Reglas de Dominio, Archivos y Restricciones

- **Fórmulas Matemáticas:** [Detallar la fórmula exacta si aplica. Ej: $Emisiones = Consumo \times Factor$].
- **Archivos/Módulos Impactados:** [Rutas o nombres exactos, ej: `src/main/java/.../CalculationService.java`, `docs/requirements/domain-rules.md`, `dataset-aceptacion-v1.sql`].
- **Tipos de Datos Restringidos:** [Definir tipos estrictos. Ej: "Los cálculos intermedios de factor de emisión deben usar `BigDecimal` con precisión 6. No usar `Double` o `Float`"].

---

### Notas Técnicas para TDD
- **Casos Unitarios Obligatorios:** 
  - `metodoDePrueba_con_input_X_espera_Y()`
  - `metodoDePrueba_falla_si_input_es_nulo()`
