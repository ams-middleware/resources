# Recursos compartidos (`resources/`)

Carpeta con **archivos de configuración editables** (breadcrumbs, ayuda contextual, etc.) pensada para **versionarse una sola vez** y **propagarse** a todos los frontends o microservicios que los consuman.

Si este repositorio incluye **otras carpetas** (p. ej. plantillas de email, idiomas, etc.), la convención descrita aquí aplica sobre todo a **`breadcrumbs/`** y **`contextual-help/`**; el resto puede seguir reglas propias.

## Estructura

```
resources/
├── README.md                    ← este índice
├── breadcrumbs/
│   ├── breadcrumbs.json
│   └── README.md
└── contextual-help/
    ├── contextual-help.json
    └── README.md
```

Cada subcarpeta agrupa un **tipo de recurso**, su manifiesto (JSON) y su documentación.

## Relación con **control-web**

La aplicación Next.js sirve estos archivos como estáticos bajo la URL **`/resources/...`**.

La organización debe ser **idéntica** a la de:

`control-web/public/resources/`

En **despliegue** (Docker, Kubernetes, etc.) se puede **montar un volumen** apuntando al contenido de este directorio `resources/` sobre `public/resources` del contenedor de control-web. Así se actualizan breadcrumbs y ayuda contextual sin rebuild, solo cambiando archivos en el volumen.

En **desarrollo local**, mantén esta misma jerarquía dentro de `control-web/public/resources` (copia o enlace simbólico desde el monorepo) para que las rutas coincidan con producción.

## Rutas HTTP de ejemplo

| Recurso | URL |
|--------|-----|
| Breadcrumbs | `/resources/breadcrumbs/breadcrumbs.json` |
| Ayuda contextual | `/resources/contextual-help/contextual-help.json` |

## Añadir nuevos recursos

1. Crear un subdirectorio bajo `resources/` con un nombre estable (`mi-recurso/`).
2. Incluir los archivos de datos y un `README.md` que describa el formato.
3. En cada app consumidora, referenciar la nueva ruta bajo `public/resources/mi-recurso/...`.
4. Documentar aquí la nueva entrada en la tabla de “Rutas HTTP” (o en este README).
