---
Los nombres de los archivos siguen el patrón NNNN-titulo-con-guiones.md (ADR-0005), donde

    NNNN es un número consecutivo y asumimos que no habrá más de 9.999 ADRs en un repositorio.
    El título se almacena usando guiones y minúsculas, porque [adr-tools] también lo hace así.

-- decisions
    |-- backend
    |   |-- 0001-use-quarkus.md
    -- ui
        |-- 0001-use-vuejs.md



# Estos son elementos de metadatos opcionales. Siéntete libre de eliminar cualquiera de ellos.
status: "{proposed (propuesto) | rejected (rechazado) | accepted (aceptado) | deprecated (obsoleto) | … | superseded by (reemplazado por) ADR-0123"
date: { AAAA-MM-DD cuando la decisión se actualizó por última vez }
decision-makers: { lista de todos los involucrados en la decisión }
consulted:
  {
    lista de todos a quienes se les pide opinión (típicamente expertos en la materia); y con quienes existe una comunicación bidireccional,
  }
informed:
  {
    lista de todos los que se mantienen actualizados sobre el progreso; y con quienes hay una comunicación unidireccional,
  }
---

# {título corto, representativo del problema resuelto y la solución encontrada}

## Contexto y Declaración del Problema

{Describe el contexto y la declaración del problema, por ejemplo, en formato libre usando dos o tres oraciones o en forma de una historia ilustrativa. Quizás desees articular el problema en forma de pregunta y agregar enlaces a tableros de colaboración o sistemas de gestión de problemas.}

<!-- Este es un elemento opcional. Siéntete libre de eliminarlo. -->

## Impulsores de la Decisión

- {impulsor de decisión 1, ej., una fuerza, preocupación que se enfrenta, …}
- {impulsor de decisión 2, ej., una fuerza, preocupación que se enfrenta, …}
- … <!-- la cantidad de impulsores puede variar -->

## Opciones Consideradas

- {título de la opción 1}
- {título de la opción 2}
- {título de la opción 3}
- … <!-- la cantidad de opciones puede variar -->

## Resultado de la Decisión

Opción elegida: "{título de la opción 1}", porque {justificación. ej., única opción que cumple con el criterio excluyente del impulsor de decisión | que resuelve la fuerza {fuerza} | … | sale mejor parada (ver abajo)}.

<!-- Este es un elemento opcional. Siéntete libre de eliminarlo. -->

### Consecuencias

- Bueno, porque {consecuencia positiva, ej., mejora de una o más cualidades deseadas, …}
- Malo, porque {consecuencia negativa, ej., compromete una o más cualidades deseadas, …}
- … <!-- la cantidad de consecuencias puede variar -->

<!-- Este es un elemento opcional. Siéntete libre de eliminarlo. -->

### Confirmación

{Describe cómo se puede/podrá confirmar la implementación/cumplimiento del ADR. ¿Están el diseño que se decidió y su implementación en línea con la decisión tomada? Ej., una revisión de diseño/código o una prueba con una biblioteca como ArchUnit pueden ayudar a validar esto. Nota: aunque clasificamos este elemento como opcional, se incluye en muchos ADRs.}

<!-- Este es un elemento opcional. Siéntete libre de eliminarlo. -->

## Pros y Contras de las Opciones

### {título de la opción 1}

<!-- Este es un elemento opcional. Siéntete libre de eliminarlo. -->

{ejemplo | descripción | enlace a más información | …}

- Bueno, porque {argumento a}
- Bueno, porque {argumento b}
<!-- usa "neutral" si el argumento dado no pesa ni para bien ni para mal -->
- Neutral, porque {argumento c}
- Malo, porque {argumento d}
- … <!-- la cantidad de pros y contras puede variar -->

### {título de otra opción}

{ejemplo | descripción | enlace a más información | …}

- Bueno, porque {argumento a}
- Bueno, porque {argumento b}
- Neutral, porque {argumento c}
- Malo, porque {argumento d}
- …

<!-- Este es un elemento opcional. Siéntete libre de eliminarlo. -->

## Más Información

{Es posible que desees proporcionar evidencia/confianza adicional para el resultado de la decisión aquí y/o documentar el acuerdo del equipo sobre la decisión y/o definir cuándo/cómo esta decisión debería realizarse y si/cuándo debería ser revisada. Los enlaces a otras decisiones y recursos también podrían aparecer aquí.}
