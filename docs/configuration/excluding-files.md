---
title: "Excluding files from the artifact"
tags:
  - configuration
  - artifact
  - files
weight: 70
---

After the Hugo site is built, it is uploaded as an artifact named "github-pages". To create the artifact, `tar` is used.

By default, the `.git` and `.github` folders are excluded from this artifact.

If other files or folders also need to be excluded, they can be added using the `exclude` input parameter:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      # Exclude the `node_modules` folder as well
      exclude: |
        .git
        .github
        node_modules
```

<!-- @TODO: Link to docs with more fine-grained details -->
