# Config de DataTables (override híbrido)

Estos JSON **ajustan en runtime** las tablas de `client-web` sin rebuild (mismo patrón que
`resources/navigation/`). Se sirven en `/resources/datatables/<name>.json` y los carga el hook
`useConfiguredDataTable(name, base, t)`.

**Modelo híbrido:** la **base la define el código** (columnas + renderers + qué campos se piden al
back). Este archivo solo **overridea**. Si el archivo no existe o falla, se usa la base tal cual.

## Qué se puede hacer

```jsonc
{
  // Orden e inclusión: los ids listados van primero (en este orden); el resto detrás.
  "order": ["uid", "status", "grand_total", "actions"],

  // Quitar columnas de la tabla por completo.
  "remove": ["stock"],

  // Overrides por id (merge sobre la base): label, width, center, sortable, defaultHidden, rowActions…
  "columns": [
    { "id": "grand_total", "width": 140, "defaultHidden": false }
  ],

  // Columnas nuevas: su `renderer` debe existir en el código (base o de dominio).
  "add": [
    { "id": "ref_code", "labelKey": "Order col code", "renderer": "clipboard", "fields": ["ref_code"] }
  ]
}
```

## Acciones de fila (`actions`)

La columna de acciones lleva un array `actions` (se define **desde acá** o en el código como
fallback). Cada acción se define **entera** y es **navegación**: al hacer clic va a `url`, con
tokens `{id}` (primer no vacío de `uid`/`order_uid`/`id`/`_id`) o `{ruta.anidada}`.

```jsonc
{
  "columns": [
    {
      "id": "actions",
      "actions": [
        {
          "title": "Ver pedido",
          "tooltip": "Ver pedido",
          "icon": "ri-eye-line",          // override del ícono del preset
          "style": "primary btn-sm",      // override del estilo (→ btn-primary btn-sm)
          "renderer": "button-view",      // botón genérico base (icono/estilo por defecto)
          "url": "/orders/{id}",
          "external_url": false,          // true = abre en pestaña nueva
          "permission": "ORDER_SHOW"      // sin permiso no se muestra
        },
        { "renderer": "button-edit", "title": "Editar", "url": "/orders/edit/{id}", "permission": "ORDER_UPDATE" }
      ]
    }
  ]
}
```

Campos: `renderer` (`button-view`|`button-edit`|`button-delete`|`button` — aporta ícono/estilo por
defecto), `icon`/`style` (override), `title`, `tooltip`, `url`, `external_url`, `permission`.
**Solo navegación** (URL): acciones con lógica propia (borrar con confirmación, etc.) siguen en código.
Un botón nuevo requiere agregar su preset a `ROW_ACTION_BUTTON_PRESETS` en `@/components/buttons`.

## Límite

Agregar una columna con una **pintura nueva** requiere un renderer nuevo en el código (los renderers
son funciones React). Desde el JSON solo se **componen** renderers ya existentes.
