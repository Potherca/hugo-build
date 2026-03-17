---
title: "Changing a file's path in the build"
tags:
  - configuration
  - files
  - mounts
weight: 50
---

By default, files are mounted to the same path in the Hugo build as they are in the repository.

For instance, the file `./introduction.md` in the repository will be mounted to `./introduction/` in the Hugo website.

To change the path of a file in the website, the `config-module-mounts` input parameter can be used to mount the file to a different path.

Where the target is mounted to in the Hugo build determines where the file will be available in the website.
Hugo makes a distinction between various mount paths. The most common ones are:

- `assets/` This directory contains resources that can be passed through an "asset pipeline" (for minification, compiling Sass or TypeScript, etc.)
  For instance CSS, images, JavaScript, Sass, TypeScript, etc.
- `content/` This directory contains the Markdown files to be rendered into HTML by the Hugo build.
  Hugo expects index files to be named `_index.md` (for more information about this, see the [Using README.md files as folder index](../using-readme-as-folder-index/) section).
- `static/` This directory contains files that will be copied to the website by the build as-is, without any processing.
  For example: `favicon.ico`, `robots.txt`, etc.

For more information about the Hugo file system, see [Hugo's Directory structure documentation](https://gohugo.io/getting-started/directory-structure/).

Some examples to demonstrate this:

To mount the `./introduction.md` file to the root of the website, the following mount can be added:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      config-module-mounts: |
        { "source": "introduction.md", "target": "content/_index.md" }
```

By mounting the `introduction.md` file to `content/`, it will be available as the HTML homepage of the website.

Another example, for instance to mount an `./img/` folder to `/img/`, would be:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      config-module-mounts: |
        { "source": "img/", "target": "assets/img/" }
```

But if the images should not be processed by Hugo and just be copied as-is to the website, the `static/` directory can be used instead:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      config-module-mounts: |
        { "source": "img/", "target": "static/img/" }
```
