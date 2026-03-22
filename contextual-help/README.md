# Ayuda contextual (`contextual-help.json`)

Define **ayuda contextual**: para cada pantalla o control relevante se indica un **selector CSS** y la **URL del manual de usuario** (GitBook). En tiempo de ejecución, la app inserta un icono de ayuda junto al elemento encontrado; al hacer clic se abre la documentación en una pestaña nueva.

## Ubicación

- **Repositorio (fuente):** `resources/contextual-help/contextual-help.json`
- **URL en la app:** `/resources/contextual-help/contextual-help.json`

El manual público está en [docs.e-middleware.com](https://docs.e-middleware.com). La base URL también se configura en la app con `NEXT_PUBLIC_DOCS_URL` (por defecto coincide con ese dominio).

## Cómo funciona

1. Al cargar la aplicación, el componente `ContextualHelpInjector` (en `ClientProviders`) solicita el JSON anterior.
2. Por cada entrada del array `entries`, se ejecuta `document.querySelector(selector)` sobre el documento.
3. Si hay coincidencia, se añade un enlace con icono (Material Design Icons) según `placement`.
4. Un `MutationObserver` vuelve a evaluar el manifiesto cuando cambia el DOM (modales, rutas, etc.), para que también funcione en contenido que aparece después del primer render.

No hace falta definir un `id` en el JSON: el sistema genera internamente un identificador estable a partir de `selector`, `url`, `placement`, `title` y `emphasis`, y marca el nodo para no duplicar el icono.

## Formato del archivo

| Campo | Obligatorio | Descripción |
|--------|-------------|-------------|
| `version` | Sí | Número de versión del manifiesto (reservado para evoluciones futuras). |
| `entries` | Sí | Lista de reglas de ayuda. |

Cada elemento de `entries`:

| Campo | Obligatorio | Descripción |
|--------|-------------|-------------|
| `selector` | Sí | Selector CSS válido del elemento ancla (p. ej. generado desde DevTools). |
| `url` | Sí | Enlace al manual. Puede ser URL absoluta (`https://…`) o **ruta relativa** al dominio del manual (p. ej. `/multi-tienda`), que se concatena con la base configurada en la app. |
| `title` | Sí | Texto del tooltip y etiqueta accesible (`aria-label`) del enlace. |
| `placement` | No | Dónde colocar el icono respecto al elemento: `after` (por defecto), `before` o `inside-end`. |
| `emphasis` | No | Si es `true` u **omitido**: icono con círculo, pulso y hover destacado. Si es **`false`**: icono discreto (sin animación ni halo). |

### Efecto visual (`emphasis`)

- **`true` o ausente**: estilo resaltado (círculo, borde, animación de pulso suave, hover fuerte).
- **`false`**: icono discreto alineado al texto, sin pulso.

Los estilos en control-web están en `control-web/src/assets/scss/_contextual-help.scss` (clases `contextual-help-anchor--emphasis` y `contextual-help-anchor--minimal`).

### Valores de `placement`

- **`after`**: el icono se inserta **justo después** del elemento (hermano siguiente).
- **`before`**: el icono va **justo antes** del elemento.
- **`inside-end`**: el icono se añade **al final del contenido** del elemento (útil en `<label>` o títulos).

### Cómo elegir el `selector`

1. Abre la aplicación en el navegador y las herramientas de desarrollo (F12).
2. Usa el inspector para seleccionar el nodo donde quieras la ayuda.
3. Clic derecho sobre el nodo en el árbol DOM → **Copy** → **Copy selector** (Chrome/Edge) o equivalente en Firefox.

**Recomendaciones:** prioriza selectores estables (`id`, `data-*`, `label[for="…"]`) frente a cadenas largas con muchos `nth-child`.

### Ejemplo mínimo

```json
{
  "version": 1,
  "entries": [
    {
      "selector": "label[for=\"multiclients-select\"]",
      "url": "/multi-tienda",
      "title": "Ayuda: multiclientes y multi-tienda",
      "placement": "inside-end"
    }
  ]
}
```

Tras editar el JSON, basta con desplegar de nuevo los archivos estáticos (o el volumen); no hace falta cambiar código TypeScript salvo que modifiques la forma de los datos (tipos en `control-web/src/types/contextual-help.ts`).

### Referencia de tipos

La definición TypeScript del manifiesto está en `control-web/src/types/contextual-help.ts` y debe mantenerse alineada con este JSON.
