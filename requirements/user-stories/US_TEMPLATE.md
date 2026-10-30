# US-[ID]: [Título descriptivo de la funcionalidad]

**Como:** [Rol del usuario o actor del sistema]  
**Quiero:** [Acción o requerimiento específico]  
**Para:** [Beneficio, objetivo de negocio o valor que aporta]  

---

### Descripción y Contexto
[Breve explicación del problema que resuelve, dependencias funcionales o consideraciones técnicas relevantes].

---

### Criterios de Aceptación (Escenarios de Prueba / BDD)

#### Escenario 1: [Camino feliz / Caso estándar de éxito]
- **Dado** [Estado inicial del sistema, entidades cargadas, permisos o datos preexistentes]
- **Y** [Condición adicional de entrada o contexto]
- **Cuando** [Acción ejecutada: llamada al endpoint, método de dominio o evento]
- **Entonces** [Resultado esperado: valor retornado, nuevo estado del sistema o evento emitido]
- **Y** [Validaciones complementarias de salida]

#### Escenario 2: [Caso borde / Validación de datos o reglas de negocio]
- **Dado** [Dato de entrada fuera de rango, formato inválido o estado inconsistente]
- **Cuando** [Se intenta procesar la operación]
- **Entonces** [El sistema debe rechazar la operación lanzando una excepción específica o código de error]
- **Y** [Efecto colateral verificado: ej. la base de datos no sufre modificaciones]

#### Escenario 3: [Seguridad y Permisos / Control de acceso]
- **Dado** [Usuario autenticado con un rol no autorizado o sin los permisos requeridos]
- **Cuando** [Intenta ejecutar la operación o acceder al recurso protegido]
- **Entonces** [El sistema debe denegar el acceso con error 403 Forbidden o UnauthorizedException]

---

### Reglas de Dominio y Fórmulas
- **Fórmula / Algoritmo:** [Expresión matemática formal o regla lógica, ej. $R = A \times B + C$]
- **Tipos de datos y precisión:** [Tipos primitivos/objetos de valor, ej. BigDecimal / decimal con escala fija, manejo de redondeo Half-Up]
- **Validaciones obligatorias:** [Restricciones de formato, valores nulos, rangos válidos]

---

### Notas Técnicas para TDD
- **Entidades / Value Objects involucrados:** [Nombres de clases o módulos del dominio]
- **Casos de prueba unitarios mínimos sugeridos:**
  - `deberia_calcular_correctamente_cuando_datos_son_validos()`
  - `deberia_lanzar_excepcion_cuando_valor_es_negativo()`
  - `deberia_denegar_acceso_cuando_usuario_no_tiene_rol_requerido()`
