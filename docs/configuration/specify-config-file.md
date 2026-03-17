---
title: "Specify a Hugo config file"
tags:
  - configuration
  - config-file
weight: 120
---

> [!IMPORTANT]
> All other input parameters that start with `config-` have no effect if the `config-file` input parameter is used.

To have more control over the Hugo build, a Hugo configuration file can be created.
For the workflow to use such a configuration file, it must be passed to the workflow using the `config-file` input parameter:

```yaml
jobs:
    build:
        uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
        with:
            config-file: path/to/config.yaml
```

If this input parameter is used, some other input params will not work, as they are set in an ad-hoc config file used by the workflow.

To make sure everything works as expected, make sure to add any workflow configuration to the Hugo config file:

```yaml
# config.yaml
# ...

disableHugoGeneratorInject: true
ignoreFiles:
  - go.*
  - hugo.yml
  - public/*
languageCode: en-us
module:
  imports:
    - path: github.com/McShelby/hugo-theme-relearn
params:
  - collapsibleMenu: true,
  - themeVariant:
    - auto
    - zen-dark
    - zen-light
title: My Website
```
