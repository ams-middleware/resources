# Breadcrumbs (`breadcrumbs.json`)

Define **títulos de página** para la barra superior / breadcrumb según la **ruta** actual. Se consume desde el componente `Breadcrumbs` en control-web.

## Ubicación

- **Repositorio (fuente):** `resources/breadcrumbs/breadcrumbs.json`
- **URL en la app:** `/resources/breadcrumbs/breadcrumbs.json`

## Formato

| Campo | Tipo | Descripción |
|--------|------|-------------|
| `rules` | array | Lista de reglas evaluadas **en orden**. Se usa la **primera** que coincida con la ruta actual. |

Cada regla:

| Campo | Tipo | Descripción |
|--------|------|-------------|
| `match` | string | Ruta a comparar (ej. `/auth/login`, `/dashboard`). |
| `mode` | `"exact"` \| `"prefix"` | `exact`: la ruta debe ser igual a `match`. `prefix`: la ruta actual debe **empezar** por `match`. |
| `title` | string | Texto mostrado como título de página y último ítem del breadcrumb. |

Si ninguna regla coincide, el componente usa el **último segmento** de la URL con formato título (ej. `mi-pagina` → `Mi Pagina`).

## Ejemplo

```json
{
  "rules": [
    {
      "match": "/auth/login",
      "mode": "exact",
      "title": "Login"
    },
    {
      "match": "/dashboard",
      "mode": "exact",
      "title": "Dashboard"
    }
  ]
}
```

## Referencia de código

- Tipos implícitos en `control-web/src/components/Common/Breadcrumbs.tsx`.
