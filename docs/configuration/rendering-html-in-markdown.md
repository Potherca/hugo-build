---
title: "Rendering HTML in Markdown"
tags:
  - configuration
  - html
  - markdown
weight: 90
---

By default, Hugo does not allow rendering HTML in Markdown files.
To enable this, the `markup.goldmark.renderer.unsafe` configuration key needs to be set to `true`.

This can be done by using the `config-root-keys` input parameter:

```yaml
jobs:
    build:
        uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
        with: |
          "markup": { "goldmark": { "renderer": { "unsafe": true } } }
```

For more information about Hugo configuration, see the [Various Hugo configurations](../various-hugo-configurations/) section.
