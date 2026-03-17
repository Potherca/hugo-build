---
tags:
  - configuration
  - build
  - debug
---

## Debugging the build

By default, the Hugo build only shows errors and warnings.

To show more information about the build, the `build-log-level` input parameter can be used to set the log level of the Hugo build.

Available log levels are "debug", "info", "warn" (the default), and "error".

To set the log level to "debug":

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      build-log-level: debug
```
