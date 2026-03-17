---
title: "Adding files to the site"
tags:
  - configuration
  - files
  - mounts
weight: 20
---

> [!IMPORTANT]
> The `config-module-mounts` input parameter has no effect if the `config-file` input parameter is used.

To add files to the Hugo build, the `config-module-mounts` input parameter can be used.

This will add files to the Hugo build by "mounting" them to the expected location in the Hugo build environment.

A "mount" is a configuration object that maps a file system path (or `source`) to a component path (`target`) within Hugo's file system.

For example, to add a custom CSS file to the Hugo build, the following mount can be added:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      config-module-mounts: |
        { "source": "_/custom.css", "target": "assets/css/custom.css" }
```

> [!WARNING]
> Each `config-module-mounts` entry MUST be a valid line of JSON and have a trailing comma. The last entry (or a single entry) MUST NOT have a trailing comma.

The above example is exactly what happens when the `css` input parameter is used for adding custom CSS.

If `config-root-keys` is used to set the `module` root key, the same mount can be added like this:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      config-root-keys: |
          "languageCode": "en-us"
```

For more details, see the [Mounts section of the Hugo Modules documentation](https://gohugo.io/configuration/module/#mounts).
