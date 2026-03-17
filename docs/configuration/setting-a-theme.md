---
tags:
  - configuration
  - theme
  - modules
---

## Setting a Theme

> [!IMPORTANT]
> The `config-module-imports` input parameter has no effect if the `config-file` input parameter is used.

For a Hugo site, a [theme](https://themes.gohugo.io/) is "just" a [module](https://gohugo.io/hugo-modules/use-modules/) that needs to imported from a specific path.

This can be done by using the `config-module-imports` input parameter. For instance, to import the [Relearn theme](https://mcshelby.github.io/hugo-theme-relearn/), the following can be used:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      config-module-imports: |
        { "path": "github.com/McShelby/hugo-theme-relearn" }
```

> [!WARNING]
> Each entry MUST be a valid line of JSON and have a trailing comma. The last entry (or a single entry) MUST NOT have a trailing comma.

> [!TIP]
> Most themes provide parameters to configure and customize the theme. These can be passed to the workflow using the `config-params` input parameter. See the [Setting a Theme's parameters](../setting-theme-parameters/) section.

More themes can be found on the [Hugo Themes website](https://themes.gohugo.io/).

For details on how to import other types of modules, or how to specify other options, see [the Imports section of the Hugo Modules documentation](https://gohugo.io/configuration/module/#imports).
