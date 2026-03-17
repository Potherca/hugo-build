---
title: "Website language"
tags:
  - configuration
  - language
---

To set the website's language, the `languageCode` root key can be set using the `config-root-keys` input parameter:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      config-root-keys: |
          "languageCode": "en-us"
```

For more information about Hugo configuration, see the [Various Hugo configurations](../various-hugo-configurations/) section.
