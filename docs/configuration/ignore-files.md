---
tags:
  - configuration
  - build
  - files
---

## Ignore files in the build

> [!IMPORTANT]
> The `config-ignore-files` input parameter has no effect if the `config-file` input parameter is used.

The `exclude` input parameter excludes files from the artifact created _after_ the build has completed.

To exclude files from the Hugo build, the `config-ignore-files` input parameter can be used.

Define a comma-separated list of file patterns to ignore in the Hugo build:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      # Exclude all files in the `content` folder from the Hugo build
      config-ignore-files: "folder-to-ignore/*", "file-to-ignore.md"
```

> [!WARNING]
> Each `config-ignore-files` entry MUST be wrapped in quotes and have a trailing comma.
> The last entry (or a single entry) MUST NOT have a trailing comma.

When there are many entries that need to be ignored, "folded scalar" YAML operator `>` can be used to make it more readable:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      config-ignore-files: >
        "go.*",
        "file-to-ignore.md",
        "folder-to-ignore/*",
        "hugo.yml",
        "public/*"
```
