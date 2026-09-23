{{/*
  Memoria de la conversación (ai-api): consultas de catálogo de turnos anteriores. Se agrega al prompt del
  sistema solo si hay alguna. Variables: .Consultas (cada una con .Minutos, .Tool, .Args y .Result).
  Ver docs_technical/propuesta/modulo-ai-client-web.md §19 y §20
*/ -}}
DATOS YA CONSULTADOS EN ESTA CONVERSACIÓN (son reales, podés usarlos; precio y stock pueden haber cambiado desde entonces: si el comprador va a comprar, volvé a consultar para confirmarlos):
{{- range .Consultas}}
- hace {{.Minutos}} min · {{.Tool}}({{.Args}}) → {{.Result}}
{{- end}}
