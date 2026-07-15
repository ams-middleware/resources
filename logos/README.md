# logos

Logos de clientes. **Convención, no configuración**: el sistema no guarda ninguna ruta en la base
de datos — la deduce del UID del cliente.

```
/resources/logos/<UID>.svg      → logo del cliente (UID en MAYÚSCULAS)
/resources/logos/default.svg    → fallback cuando el archivo no existe
```

Ejemplo: el cliente `JMC` se sirve desde `/resources/logos/JMC.svg`.

Para dar de alta el logo de un cliente **solo hay que dejar el archivo acá** y pushear: el volumen
`resources_vol` lo distribuye a todos los servicios. No hay que tocar la base ni desplegar nada.

- El nombre del archivo debe coincidir **exactamente** con el UID en mayúsculas.
- Formato SVG, cuadrado (viewBox 1:1), idealmente sin texto (se muestra chico, en un avatar).
- ⚠️ Un SVG es código ejecutable: **no pegar SVGs de origen desconocido sin revisarlos**
  (`<script>`, `on*`, `foreignObject` = XSS servido desde nuestro dominio).

La lógica vive en `go-core/om/client` (`LogoPath`, `SetLogo`) — ver `arquitectura/logo-por-convencion.md`.
