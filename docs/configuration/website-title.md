---
tags:
  - configuration
  - title
---

## Website title

Usually, at the very least, you'll want to set a title for the website. This can be done using the `config-root-keys` input parameter, with the `title` key:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      config-root-keys: |
          "title": "My Website"
```

For more information about Hugo configuration, see the [Various Hugo configurations](../various-hugo-configurations/) section.
