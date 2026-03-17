---
title: "Build environment"
tags:
  - configuration
  - build
  - docker
weight: 40
---

By default, the workflow uses the `hugomods/hugo:std-go-git` Docker image to build the Hugo site.

This is a standard image with Go and Git support. For simple Hugo sites, this is fine. But if specific features are needed, a different Docker image can be specified using the `docker-image` input parameter.

```
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      docker-image: ghcr.io/hugomods/hugo:dart-sass-node-git
```

All [HugoMods](https://hugomods.com/) docker images are available on Docker Hub (docker.io), but they can also be pulled from the GitHub Container Registry (ghcr.io).

To select a specific image, visit https://docker.hugomods.com/choose/, or create a custom image with the any needed features.
