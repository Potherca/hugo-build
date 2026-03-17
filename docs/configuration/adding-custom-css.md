---
title: "Adding custom CSS to the site"
tags:
  - configuration
  - css
  - assets
---

> [!IMPORTANT]
> The `css` input parameter has no effect if the `config-file` input parameter is used.
>
> The workflow will still create the `_/custom.css` file, but cannot mount it to the Hugo build.
>
> In such cases, `{ "source": "_/custom.css", "target": "assets/css/custom.css" }` MUST be added to the  `module.mounts` settings in the config file.

For simple cases, custom CSS can be passed directly to the workflow using the `css` input parameter:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      css: |
        body { background-color: red; }
```

The contents of the `css` input parameter will be written to a file `_/custom.css`, which will be "mounted" to path `assets/css/custom.css` in the website.

> [!TIP]
> For more complex CSS, or when multiple CSS files are needed, files can be added to the website.

For details on file mounts, see the [Adding files to the site](../adding-files/) section.
