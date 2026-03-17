---
title: "Build directory"
tags:
  - configuration
  - build
  - path
weight: 30
---

If the site should be generated from another location (for instance the `./docs/` directory) the `path` input parameters needs to be set:

```yml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      # Set a path to build the website from, instead of the default `./`
      path: ./docs/
```
