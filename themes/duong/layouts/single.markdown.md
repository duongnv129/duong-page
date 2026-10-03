{{- /* Plain Markdown copy of a page, linked from llms.txt and <link rel="alternate">. Shortcodes are kept as written; charts carry a data table alongside. */ -}}
# {{ .Title }}
{{ with .Params.subtitle }}
{{ . }}
{{ end }}
{{- if not .Date.IsZero }}
Published {{ .Date.Format "2006-01-02" }}{{ if ne (.Lastmod.Format "2006-01-02") (.Date.Format "2006-01-02") }}, updated {{ .Lastmod.Format "2006-01-02" }}{{ end }} by {{ site.Params.author.name }}. Canonical URL: {{ .Permalink }}
{{ end }}
{{ .RawContent }}
