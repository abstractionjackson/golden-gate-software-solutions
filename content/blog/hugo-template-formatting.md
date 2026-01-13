+++
date = '2026-01-12T11:57:32-08:00'
draft = false
title = 'Hugo Template Formatting Using Prettier, Zed'
+++

[Zed](https://zed.dev) users coming from more mature 👴🏼 editors may balk at the lack of a Hugo template formatter. The default language server even fails to treat `.gohtml` files as Go templates, and your double-curly-bracket-bearing files will therefore soon become unkempt, if not downright ugg-o.

Here's the few steps you can take to configure the editor to format your Hugo templates, using Prettier and the Go template plugin.

1. `npm i prettier prettier-plugin-go-template`
I don't like making this an npm repo any more than you do, but it must be done. Attempts to locate the formatter's plugin globally have been met with lengthy error messages.
2. `touch .zed/settings.json`

{{< highlight json "style=lovelace, tabwidth=2" >}}
// settings.json

{
  "languages": {
    "HTML": {
      "formatter": {
        "external": {
          "command": "prettier",
          "arguments": ["--stdin-filepath", "{buffer_path}"],
        }
      }
    }
  }
}
{{< /highlight >}}

Additionally, I've added `.gohtml` to my file extensions that count as HTML.

{{< highlight json "style=lovelace, tabwidth=2" >}}
// settings.json

{
  "file_types": {
    "HTML": ["html", "gohtml"]
  }
}
{{< /highlight >}}

I had to confgiure Hugo to recognize `.gohtml`, unfortunately.

{{< highlight toml "style=lovelace, tabiwth=2" >}}
# hugo.toml

[outputformats]
[outputformats.gohtml]
mediaType = "text/html"
isHTML = true
{{< /highlight >}}

This way, Prettier will use the HTML formatter for vanilla HTML, and the Go Template formatter with template files.
3. `touch .prettierrc`

{{< highlight json "style=lovelace, tabwidth=2" >}}
{
  "plugins": [
    "prettier-plugin-go-template"
  ],
  "overrides": [
    {
      "files": [
        "*.gohtml"
      ],
      "options": {
        "parser": "go-template",
        "goTemplateBracketSpacing": true,
        "bracketSameLine": false
      }
    },
  ]
}
{{< /highlight >}}

Your options may differ.

## Bonus: Emmet Snippets for Hugo Templating
One of the nice things about Zed is the built-in snippet support.
From the Command Palette, enter `snippets: configure`, then choose `HTML`.
Here you can define your own snippets, to be available from `.thml` files. I let the AI write mine, and it came up with

{{< highlight json "style=lovelace, tabwidth=2" >}}
// html.json

{
  "block": {
    "prefix": "block",
    "body": ["{{ block \"$1\" . }}", "  $2", "{{ end }}"],
    "description": "Create a Hugo template block",
  },
  "define": {
    "prefix": "define",
    "body": ["{{ define \"$1\" }}", "  $2", "{{ end }}"],
    "description": "Create a Hugo template definition",
  },
  "partial": {
    "prefix": "partial",
    "body": ["{{ partial \"$1\" . }}"],
    "description": "Include a Hugo template partial",
  },
  "range": {
    "prefix": "range",
    "body": ["{{ range .Items }}", "  {{ .Title }}", "{{ end }}"],
    "description": "Hugo range loop",
  },
  "if": {
    "prefix": "if",
    "body": ["{{ if $1 }}", "  $2", "{{ end }}"],
    "description": "Hugo if statement",
  },
  "with": {
    "prefix": "with",
    "body": ["{{ with .Variable }}", "  $1", "{{ end }}"],
    "description": "Hugo with statement",
  },
}
{{< /highlight >}}

Finally, the Emmet extension is available for common html snippets.
