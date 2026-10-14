# Ejercicio 6. El lenguaje de cada integrante

**Integrante:** José (jcmj3256-collab) · **Proyecto:** Clínica Veterinaria · **Gestor:** PostgreSQL

## 1. Lenguaje elegido y por qué

**Lenguaje: JavaScript, ejecutado en el servidor con Node.js.**

Ya he escrito backend en PHP (una base de datos de jugadores, entrenadores y equipos de la NFL), así que conozco el ciclo básico: recibir una petición, consultar la base de datos y devolver una respuesta. Para este proyecto prefiero aprender Node.js por estas razones:

- **Un solo lenguaje en todo el sitio.** El sitio de la clínica ya usa HTML y JavaScript en el navegador. Con Node.js el backend usa el mismo lenguaje, y los datos viajan como JSON sin conversiones.
- **Uso en la industria.** JavaScript/Node.js es de las tecnologías de backend más usadas, con mucha documentación y bibliotecas para PostgreSQL.
- **Buen manejo de muchas peticiones.** Su modelo asíncrono atiende varias consultas a la vez sin bloquearse, útil para una clínica con consultas de agenda simultáneas.
- **Continuidad con lo que ya sé.** Mi experiencia con PHP (rutas, consultas SQL, validar datos de formularios) se traslada directo; lo nuevo es la sintaxis y el ecosistema.

PHP seguiría siendo una opción válida (de hecho es el lenguaje del proyecto asignado), pero elijo Node.js porque quiero aprender una herramienta más usada y reutilizar JavaScript.

## 2. Marco de trabajo y biblioteca de conexión

| Elemento | Elección | Para qué sirve |
|---|---|---|
| Marco de trabajo | **Express.js** | Define las rutas HTTP (`GET`, `POST`…), lee el cuerpo JSON y envía respuestas. |
| Biblioteca de conexión | **`pg` (node-postgres)** | Controlador oficial de la comunidad para conectar Node.js con **PostgreSQL**, el gestor de la Práctica 3. |

Con `pg` se usa un `Pool` de conexiones y **consultas parametrizadas** (`$1, $2…`), que evitan la inyección SQL. Los datos de conexión (host, puerto, usuario, contraseña, base `practica3`) irían en variables de entorno, no dentro del código.

## 3. Dos operaciones que ofrecería el backend

### Operación 1: Registrar una cita

- **Ruta:** `POST /api/citas`
- **Recibe (JSON):**

```json
{
  "codigo_mascota": 3,
  "codigo_veterinario": 2,
  "fecha": "2026-11-05",
  "hora": "10:30",
  "notas": "Revisión anual y vacuna"
}
```

- **Qué hace:** valida que los campos obligatorios existan y calcula el siguiente `codigo_cita` de esa mascota (la cita es entidad débil, su clave es `codigo_mascota` + `codigo_cita`). Luego inserta en la tabla `cita`.
- **Devuelve:**
  - Éxito (`201 Created`): la cita creada.

```json
{
  "codigo_mascota": 3,
  "codigo_cita": 4,
  "fecha": "2026-11-05",
  "hora": "10:30",
  "codigo_veterinario": 2,
  "notas": "Revisión anual y vacuna"
}
```

  - Error (`400`/`404`): mensaje cuando falta un dato, o cuando la mascota o el veterinario no existen (violación de la clave foránea).

### Operación 2: Consultar las citas de un día

- **Ruta:** `GET /api/citas?fecha=2026-11-05`
- **Recibe:** la fecha como parámetro de la URL (opcionalmente `codigo_veterinario` para filtrar por veterinario).
- **Qué hace:** une `cita` con `mascota` y `veterinario` y ordena por hora.
- **Devuelve (`200 OK`):** una lista en JSON, vacía si no hay citas.

```json
[
  {
    "codigo_cita": 4,
    "hora": "10:30",
    "mascota": "Luna",
    "especie": "Gato",
    "veterinario": "Ana Torres",
    "notas": "Revisión anual y vacuna"
  }
]
```

- **Error (`400`):** si la fecha falta o no tiene formato válido.

> Nota: este documento solo identifica y justifica la elección; no se instala el marco de trabajo ni se escribe el backend, que corresponde a las prácticas siguientes.
