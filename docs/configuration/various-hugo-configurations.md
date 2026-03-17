---
title: "Various Hugo configurations"
tags:
  - configuration
  - hugo-config
---

> [!IMPORTANT]
> The `config-root-keys` input parameter has no effect if the `config-file` input parameter is used

Input parameters exist for the most common Hugo configurations, but there are many more Hugo configurations that can be set.

To avoid needing an input parameter for every possible Hugo configuration, the `config-root-keys` input parameter is available to set any Hugo configuration available in the root of the Hugo configuration.

Common examples of such configurations are `disableHugoGeneratorInject`, `ignoreFiles`, `languageCode`, and `title`.

For example:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      config-root-keys: |
        "disableHugoGeneratorInject": true,
        "languageCode": "en-us",
        "title": "My Website"
```

> [!WARNING]
> Each entry MUST be a valid line of JSON and have a trailing comma. The last entry (or a single entry) MUST NOT have a trailing comma.

Although input parameters exist for some configurations keys, those can also be set through the `config-root-keys` input parameter.

For example, instead of using `config-ignore-files`, `config-module-imports`, or `config-params`, the same configuration can be achieved by setting the corresponding root keys:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      config-root-keys: |
        "ignoreFiles": [ "go.*", "hugo.yml", "public/*"],
        "module": {
          "imports": [
            { "path": "github.com/McShelby/hugo-theme-relearn" }
          ]
        },
        "params": {
          "collapsibleMenu": true,
          "themeVariant": ["auto", "zen-dark", "zen-light"]
        }
```

For a full list of all available Hugo configuration options, see [the complete list of Hugo configuration settings.](https://gohugo.io/configuration/all/)
