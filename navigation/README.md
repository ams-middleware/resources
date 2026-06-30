# Navegación (`navigation/`)

Define el **árbol de navegación** que alimenta —con una sola fuente por app— el **menú lateral** y el
**breadcrumb**. Se consume en **runtime** (fetch), por lo que se edita sin rebuild.

## Ubicación — un archivo por aplicación

| App | Archivo (fuente) | URL en la app |
|-----|------------------|---------------|
| client-web | `resources/navigation/client-web.json` | `/resources/navigation/client-web.json` |
| control-web | `resources/navigation/control-web.json` | `/resources/navigation/control-web.json` |

Cada app fetchea **su** archivo. En dev, `client-web/public/resources` es symlink al repo `resources`;
`control-web/public/resources/navigation` es symlink a esta carpeta (conserva su `languages/` local).

## Quién lo consume (cada app, misma lógica)

- **Menú lateral:** `<app>/src/layouts/Layouts/LayoutMenuData.tsx` (`useMenuItems`).
- **Breadcrumb:** `<app>/src/components/Common/Breadcrumbs.tsx`.
- **Helpers:** `<app>/src/lib/navigation.ts` (`loadNavigation`, `matchTrail`, `activeOpenIds`).
  La `NAVIGATION_URL` de cada lib apunta al archivo de su app.

## Formato

Propiedades en **snake_case**. Los textos de cara al usuario (`title`, `description`) se renderizan
vía `t()` → **traducibles** (multiidioma).

| Campo | Tipo | Descripción |
|--------|------|-------------|
| `navigations` | array | Árbol de nodos, evaluado **en orden**. |

Cada **nodo**:

| Campo | Tipo | Lo usa | Descripción |
|--------|------|--------|-------------|
| `id` | string | menú | Id estable (estado del acordeón). |
| `title` | string | ambos | Texto visible (menú + breadcrumb). Traducible (`t()`). |
| `icon` | string (opcional) | ambos | Ícono Remix (ej. `ri-store-2-line`). En el menú solo se muestra en el nivel superior; en el breadcrumb va junto al título. |
| `link` | string (opcional) | ambos | Ruta. Ausente en headers de sección. |
| `permission` | string (opcional) | menú | Permiso requerido (gating). El breadcrumb lo ignora. |
| `description` | string (opcional) | breadcrumb | Subtítulo breve bajo el título. Traducible (`t()`). |
| `help_url` | string (opcional) | breadcrumb | **URL completa** de la página del manual (GitBook), con dominio. Renderiza el ícono de ayuda (`HelpButton`). |
| `header` | bool (opcional) | menú | Separador de sección (no navegable, no entra al trail del breadcrumb). |
| `hidden` | bool (opcional) | — | No se muestra en el menú, pero provee datos de breadcrumb (ej. `/profile`). |
| `children` | array (opcional) | ambos | Subnodos (submenú + ancestros del trail). |

## Cómo se resuelve

- **Menú:** se renderiza el árbol (filtrado por `permission`); el acordeón abre los ancestros de la ruta activa.
- **Breadcrumb:** se busca el nodo cuyo `link` matchea la ruta (exacto **más profundo**; si no, el prefijo
  más específico). De ese nodo salen `title`/`icon`/`description`/`helpHref`; el **trail** se arma con la
  cadena de ancestros (labels consecutivos duplicados se colapsan).
- **Vistas de detalle** (`/orders/{uid}`, etc.) no van en el árbol: setean su identidad por código con
  `useBreadcrumbOptions(...)` y el trail cae al nodo padre por prefijo.

## Ejemplo

```json
{
  "navigations": [
    {
      "id": "orders",
      "title": "Órdenes",
      "icon": "ri-shopping-bag-3-line",
      "link": "/orders",
      "permission": "ORDER_LIST",
      "children": [
        {
          "id": "orders-list",
          "title": "Órdenes",
          "link": "/orders",
          "permission": "ORDER_LIST",
          "icon": "ri-shopping-bag-line",
          "description": "Pedidos de todas las tiendas: seguimiento, edición y gestión.",
          "help_url": "https://docs.e-middleware.com/operativa/ordenes"
        }
      ]
    }
  ]
}
```

## Referencia de código

- Tipos y helpers en `client-web/src/lib/navigation.ts`.
