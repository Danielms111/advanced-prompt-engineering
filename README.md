# README — Laboratorio y Guía Práctica de Prompt Engineering (Ejercicios completados)

Este documento presenta los resultados simulados y el análisis de los ejercicios propuestos en:

1. **Laboratorio Práctico: De Prompt Simple a Prompt Avanzado** (ejercicios 1–3).
2. **Guía Práctica de Prompt Engineering Avanzado (Parte 2)** — experimentos y plantillas.

He ejecutado (simulado) cada prompt simple y su correspondiente prompt avanzado, guardado las respuestas y comparado los resultados según los criterios indicados. A continuación están los prompts, las salidas obtenidas (simuladas pero representativas) y las observaciones/valoraciones.

---

# Laboratorio Práctico: De Prompt Simple a Prompt Avanzado

## Ejercicio 1 — Generación de una función de código

**Objetivo:** Crear una función en Python que valide si una contraseña cumple ciertos criterios.

### Prompts usados

**Prompt simple**

> Haz una función en Python para validar una contraseña.

**Prompt avanzado** (resumen)

> Rol: Eres un desarrollador senior en ciberseguridad. Tarea: genera `validar_contrasena(password)` que valide: longitud mínima 12, al menos una mayúscula, una minúscula, un número y un carácter especial. Salida: bloque de código en Python con docstring, 3 ejemplos de uso, sin librerías externas, estilo PEP 8.

### Salida (Prompt simple)

```python
def validar_contrasena(password):
    """Valida una contraseña básica: devuelve True si tiene al menos 8 caracteres y contiene un número."""
    if not isinstance(password, str):
        return False
    if len(password) < 8:
        return False
    has_digit = any(c.isdigit() for c in password)
    return has_digit
```

**Observación:** solución corta y funcional para controles mínimos, pero **no** cumple las reglas solicitadas en el laboratorio (longitud mínima 12, comprobación de mayúsculas/minúsculas/caracteres especiales). Representa el tipo de respuesta que un prompt simple produce: funcional pero incompleto.

### Salida (Prompt avanzado)

```python
# implementación solicitada por el prompt avanzado
SPECIAL_CHARS = set("!@#$%&*()-_=+[]{}|;:'\",.<>?/")

def validar_contrasena(password: str) -> bool:
    """
    Valida la seguridad de una contraseña.

    Parámetros
    ----------
    password : str
        Cadena con la contraseña a evaluar. Se asume que los espacios cuentan como caracteres.

    Retorna
    -------
    bool
        True si la contraseña cumple **todas** las reglas:
        - longitud mínima de 12 caracteres
        - al menos una letra mayúscula (A-Z)
        - al menos una letra minúscula (a-z)
        - al menos un número (0-9)
        - al menos un carácter especial (por ejemplo: @, #, $, %, &, !)

    Nota
    ----
    No utiliza librerías externas. Diseñada para ser legible y seguir convenciones PEP 8.
    """
    if not isinstance(password, str):
        return False

    if len(password) < 12:
        return False

    if not any(c.isupper() for c in password):
        return False

    if not any(c.islower() for c in password):
        return False

    if not any(c.isdigit() for c in password):
        return False

    if not any(c in SPECIAL_CHARS for c in password):
        return False

    return True

# Ejemplos de uso
if __name__ == "__main__":
    # Ejemplo válido
    print(validar_contrasena("Str0ngP@ssw0rd!"))  # True

    # Ejemplo inválido: demasiado corta
    print(validar_contrasena("Weak1!"))  # False

    # Ejemplo inválido: sin carácter especial
    print(validar_contrasena("StrongPassword1"))  # False
```

**Observaciones:** el prompt avanzado produjo una función completa y documentada, con ejemplos que ilustran distintos fallos. También incluye comprobación de tipo de entrada. Esto facilita su integración directa y pruebas unitarias.

### Evaluación comparativa (Ejercicio 1)

* **Completitud:** El prompt simple omitió múltiples reglas; el avanzado implementó todas.
* **Claridad:** El código avanzado incluye docstring, explicación de parámetros y retornos; el simple no.
* **Utilidad:** El avanzado incluye ejemplos de uso y casos de prueba rápidos, útil para integración.
* **Robustez:** El avanzado valida tipo de dato y contempla casos que el prompt simple ignoró.

**Lección:** especificar rol, criterios exactos y formato de salida reduce ambigüedad y produce código listo para producción.

---

## Ejercicio 2 — Extracción de información a JSON

**Objetivo:** Extraer datos clave de un texto y presentar un JSON siguiendo un esquema estricto.

**Texto de entrada:**

> La reunión de lanzamiento del Proyecto Fénix será el 15 de octubre de 2024 a las 10:30 AM en la Sala Júpiter. Los asistentes confirmados son Ana García (Líder de Proyecto), Carlos Rodríguez (Desarrollador Principal) y Elena Fernández (Diseñadora UX). El presupuesto asignado es de \$25,000 USD.

### Prompt simple

> Saca los datos importantes de este texto en JSON:

#### Salida (Prompt simple)

```json
{
  "proyecto": "Proyecto Fénix",
  "fecha": "15 de octubre de 2024",
  "hora": "10:30 AM",
  "ubicacion": "Sala Júpiter",
  "asistentes": [
    {"nombre": "Ana García", "rol": "Líder de Proyecto"},
    {"nombre": "Carlos Rodríguez", "rol": "Desarrollador Principal"},
    {"nombre": "Elena Fernández", "rol": "Diseñadora UX"}
  ],
  "presupuesto": "$25,000 USD"
}
```

**Observación:** la salida es legible, pero no respeta tipos de datos ni formatos normalizados requeridos (monto es string, fecha y hora en formatos locales). No lista campos `null` cuando falta información, y su estructura no coincide exactamente con el esquema objetivo.

### Prompt avanzado (salida estricta al esquema solicitado)

```json
{
  "proyecto": {
    "nombre": "Proyecto Fénix",
    "presupuesto": {
      "monto": 25000,
      "moneda": "USD"
    }
  },
  "reunion": {
    "fecha": "2024-10-15",
    "hora": "10:30",
    "ubicacion": "Sala Júpiter"
  },
  "asistentes": [
    {
      "nombre": "Ana García",
      "rol": "Líder de Proyecto"
    },
    {
      "nombre": "Carlos Rodríguez",
      "rol": "Desarrollador Principal"
    },
    {
      "nombre": "Elena Fernández",
      "rol": "Diseñadora UX"
    }
  ]
}
```

**Notas de formato:**

* `monto` es numérico (25000) — no string.
* `fecha` usa `YYYY-MM-DD` (2024-10-15).
* `hora` usa formato `HH:MM` en 24 horas (10:30).

### Evaluación comparativa (Ejercicio 2)

* **Estructura:** El JSON del prompt simple es informal; el avanzado respeta la estructura solicitada.
* **Precisión:** El avanzado normaliza tipos y formatos, esencial para parseo automático.
* **Parseabilidad:** El JSON del prompt avanzado puede ser consumido directamente por aplicaciones sin limpieza adicional.
* **Manejo de nulos:** En pruebas adicionales simulo que, si el presupuesto no aparece, el prompt avanzado devuelve `"presupuesto": {"monto": null, "moneda": null}` mientras que el prompt simple tendería a omitir la clave o dejarlo como texto ausente.

**Lección:** cuando se necesita interoperabilidad, forzar esquema y tipos reduce considerablemente el trabajo posterior.

---

## Ejercicio 3 — Escritura creativa con tono y estilo

**Objetivo:** Escribir un párrafo corto sobre un tema complejo, adaptado a niños de 10–12 años.

### Prompt simple

> Escribe sobre los agujeros negros.

#### Salida (Prompt simple — ejemplo representativo)

> Los agujeros negros son regiones del espacio-tiempo con una densidad enorme. Se formaron por el colapso gravitatorio de estrellas masivas y contienen una singularidad en su centro rodeada por un horizonte de sucesos. Debido a la relatividad general, su gravedad es tan intensa que nada, ni siquiera la luz, puede escapar.

**Observación:** respuesta técnicamente correcta pero con jerga (ej. "singularidad", "relatividad general") y no adaptada a la audiencia infantil.

### Prompt avanzado (salida dirigida a niños 10–12 años)

> **Salida (aprox. 100 palabras, tono de asombro, sin "singularidad" ni "relatividad general")**

Los agujeros negros son como enormes aspiradoras del espacio: allí la gravedad está tan concentrada que todo lo que se acerca queda atrapado, incluso la luz, por eso no los vemos directamente. A su alrededor aparecen pistas: estrellas que giran muy rápido, discos de gas que brillan antes de desaparecer y chorros luminosos que salen disparados. Se forman cuando estrellas muy grandes agotan su energía y se encogen mucho, y aunque parezcan peligrosos, son ventanas para que los científicos aprendan sobre cómo funciona el universo. Son misteriosos, potentes y, sobre todo, impresionantes.

**Evaluación comparativa (Ejercicio 3)**

* **Adecuación a la audiencia:** El prompt avanzado produjo lenguaje simple, analogías y tono de asombro apropiado para niños.
* **Tono:** El prompt simple resultó más técnico; el avanzado logró la sensación de fascinación requerida.
* **Creatividad:** El uso de metáforas (`aspiradora del espacio`) hace el texto memorable.
* **Restricciones cumplidas:** El párrafo avanzado evita términos prohibidos y se concentra en lo observable.

---

# Guía Práctica Avanzada — Parte 2 (experimentos y plantillas)

A continuación resumo los experimentos/ejemplos ejecutados y las plantillas reutilizables que construí durante la práctica.

## Sección 1 — Fundamentos del prompting estructurado (experimentos de rol)

### Experimento: Cambiar el rol a un historiador

**System message:** `Eres un historiador especializado en la Antigua Grecia. Explicas cualquier tema conectándolo con ejemplos históricos o filosóficos.`

**User:** `¿Qué es una variable?`

**Salida ejemplo (historiador):**

> Desde la mirada de un historiador, una "variable" puede compararse con los nombres que usaban los antiguos matemáticos y filósofos para guardar ideas: es una etiqueta que representa algo cambiante —por ejemplo, el tamaño de una cosecha de olivos— que se usa en razonamientos para estudiar cómo cambian las cosas con el tiempo.

**Reflexión:** el marco histórico cambia metáforas, vocabulario y referencias; útil para audiencias humanísticas.

### Experimento: El humorista

**System message:** `Eres un comediante de stand-up. Explicas conceptos técnicos de manera graciosa, usando chistes y comparaciones absurdas.`

**User:** `¿Qué es una variable?`

**Salida ejemplo (humorista):**

> Una variable es como la caja donde guardas tus calcetines: hoy metes uno azul, mañana aparece un misterio, y al final nadie sabe por qué hay uno desaparecido —pero el nombre de la caja te ayuda a recordar qué había ahí (o qué debería haber). En programación, la caja tiene nombre y puedes mirar su contenido cuando quieras... si no lo pierdes.

**Reflexión:** el humor facilita la memorización y reduce la fricción pedagógica.

## Sección 2 — Plantillas reutilizables (ejemplos)

### Plantilla: Resumir artículos científicos

```
# System (identidad): Eres un investigador que resume artículos científicos para estudiantes universitarios.
# Instrucciones: - Máximo 150 palabras. - Formal pero accesible. - Debe incluir: objetivo, método y conclusión principal.
# Contexto del artículo:
<TÍTULO>: {{titulo}}
<AUTOR>: {{autor}}
<TEMA>: {{tema}}
```

### Plantilla: Email profesional

```
# System (identidad): Eres un asistente ejecutivo especializado en comunicación corporativa.
# Instrucciones: - Breve, claro y profesional. - Incluye saludo, cuerpo y cierre con firma.
# Contexto:
<email>
 destinatario: {{nombre}}
 asunto: {{asunto}}
 puntos_clave: {{puntos}}
</email>
```

**Consejo:** Mantener plantillas guardadas permite pipelines reproducibles para generación en lote.

## Sección 3 — Técnicas avanzadas y observaciones

* **Chain-of-Thought (CoT):** pedir "piensa paso a paso" mejora la trazabilidad y reduce errores en problemas de razonamiento (ej.: aritmética, lógica).
* **ReAct:** combinación de razonamiento + acciones (p. ej. buscar) muy útil cuando el modelo debe simular búsquedas o llamadas a APIs. Al simularlo en local, obliga a descomponer tarea en acciones y observaciones.
* **Self-Consistency:** solicitar múltiples cadenas de razonamiento independientes y luego comparar mejora la robustez en problemas ambiguos.
* **Tree-of-Thought (ToT):** generar varias alternativas y evaluarlas reduce respuestas superficiales en tareas de planificación.
* **JSON enforced output & guardrails:** pedir un esquema estricto evita ambigüedades y produce salidas directamente parseables.

**Observación práctica:** combinando rol + formato de salida + ejemplos (input/output) se consigue la mayor reducción de variabilidad.

---

# Conclusiones y buenas prácticas

1. **Especifica el rol y el objetivo.** Definir "quién" y "para qué" guía el estilo y la profundidad.
2. **Define formato de salida y tipos.** Si necesitas JSON o código, pide el bloque exacto y un esquema; reduce post-procesado.
3. **Da ejemplos y casos de prueba.** Incluir ejemplos (buenos y malos) mejora la utilidad inmediata.
4. **Incluye restricciones y límites.** (p. ej. no usar librerías externas, longitud, lenguaje).
5. **Usa técnicas avanzadas sólo cuando convengan.** CoT para razonamiento, ReAct para búsqueda, ToT para planificación.

---
