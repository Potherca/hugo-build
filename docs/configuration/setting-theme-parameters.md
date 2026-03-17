---
tags:
  - configuration
  - theme
  - params
---

## Setting a Theme's parameters

> [!IMPORTANT]
> The `config-params` input parameter has no effect if the `config-file` input parameter is used.

Most themes have parameters that can be set to configure the theme. Such parameters are defined by the theme, and can be found in the theme's documentation.

To configure and customize a theme, set the `config-params` input parameter.

For instance, the Relearn theme [configuration](https://mcshelby.github.io/hugo-theme-relearn/configuration/reference/) has parameters to configure the theme's menu and styling (i.e. theme variants). An example of setting these parameters would be:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      config-module-imports: |
        { "path": "github.com/McShelby/hugo-theme-relearn" }
      config-params: |
        {
          "collapsibleMenu": true,
          "themeVariant": ["auto", "zen-dark", "zen-light"]
        }
```

> [!WARNING]
> The value of the `config-params` input parameter MUST be a valid JSON object. It MUST NOT have a trailing comma.

For more information about non-theme site parameters, visit [the Hugo "Configure params" documentation](https://gohugo.io/configuration/params/).
