---
tags:
  - configuration
  - readme
  - index
---

## Using README.md files as folder index

Hugo [expects index files for directories to be named `_index.md`](https://gohugo.io/content-management/organization/#index-pages-_indexmd).
The convention in Markdown is to use `README.md` files for this.

To use `README.md` files as index files in Hugo, the `build-readme-as-index` input parameter can be set to `true`:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      build-readme-as-index: true
```

This will rename all `README.md` files to `_index.md` before the Hugo build.

To have another file as the index file (for instance, `introduction.md`), the `config-module-mounts` input parameter can be used to mount the file to the expected location in the Hugo build.

For more details see the [Changing a file's path in the build](../changing-file-path/) section.
