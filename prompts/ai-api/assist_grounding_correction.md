{{/*
  Instrucción interna (ai-api) cuando la respuesta del asistente trae URLs o códigos que no salieron de
  ninguna herramienta. El comprador no la ve. Variables: .Datos (los datos sin respaldo, separados por coma).
  Ver docs_technical/propuesta/modulo-ai-client-web.md §18 y §20
*/ -}}
VERIFICACIÓN INTERNA (el comprador no ve este mensaje): tu respuesta incluye datos que NO salieron de ninguna herramienta en este turno: {{.Datos}}. No podés dar un código, SKU, URL o número que no te haya devuelto una herramienta. Consultá la herramienta que corresponda (por ejemplo consultar_catalogo o buscar_productos con el nombre del producto, o detalle_producto) y respondé SOLO con lo que devuelva. Si la herramienta no trae ese dato, decile al comprador que no lo tenés disponible. Reescribí la respuesta completa.
