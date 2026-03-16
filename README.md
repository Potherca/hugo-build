# Reusable GitHub Workflow for Hugo

This repo contains a reusable GitHub Workflow for building a Hugo website.

<!-- toc -->

## Install

No installation is required, the [reusable workflow](./.github/workflows/hugo-build.yaml) can [be called directly](https://docs.github.com/en/actions/how-tos/reuse-automations/reuse-workflows#calling-a-reusable-workflow) from a project's workflow.

## Usage

[GitHub Workflow](https://docs.github.com/en/actions/concepts/workflows-and-actions/workflows) files must live in the `.github/workflows` directory of a project, and must be named `[workflow-name].yml`. For instance, [`.github/workflows/call-hugo-build.yaml`](https://github.com/potherca-blog/hugo-build-example/blob/main/.github/workflows/call-hugo-build.yaml)

The reusable workflow can be used by adding a [`uses` key](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax#jobsjob_iduses) to a job in the workflow file:

```yml
---
on:
  # Triggers events go here...
  # (e.g. push, pull_request, workflow_dispatch, etc.)

jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main

  deploy:
    # Deployment steps go here...
```

The workflow works as-is, with no further configuration required.

It will build the Hugo site from the root of the repository (`./`), create a tarball of the built site, and upload the tarball as an artifact named "github-pages".

This artifact can be downloaded and deployed to wherever the site is hosted.

### Deploying to GitHub Pages

To host the result on GitHub Pages, the deploy step would look like this:

```yml
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    name: Deploy to GitHub Pages
    needs: build
    # Grant GITHUB_TOKEN the permissions required to make a Pages deployment
    permissions:
      pages: write      # to deploy to Pages
      id-token: write   # to verify the deployment originates from an appropriate source
    runs-on: ubuntu-latest
    steps:
      - id: deployment
        name: Deploy to GitHub Pages
        uses: actions/deploy-pages@v4
```

### Configuration

To configure the workflow, "input parameters" can be passed to the workflow using the `with` key.

Other configuration options for the Hugo can be set, or a config file can be specified, if more control is needed.

#### Adding custom CSS to the site

> [!IMPORTANT]
> The `css` input parameter has no effect if the `config-file` input parameter is used.
>
> The workflow will still create the `_/custom.css` file, but cannot mount it to the Hugo build.
>
> In such cases, `{ "source": "_/custom.css", "target": "assets/css/custom.css" }` MUST be added to the  `module.mounts` settings in the config file.

For simple cases, custom CSS can be passed directly to the workflow using the `css` input parameter:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      css: |
        body { background-color: red; }
```

The contents of the `css` input parameter will be written to a file `_/custom.css`, which will be "mounted" to path `assets/css/custom.css` in the website.

> [!TIP]
> For more complex CSS, or when multiple CSS files are needed, files can be added to the website.

For details on file mounts, see the "Adding files to the site" section.

#### Adding files to the site

> [!IMPORTANT]
> The `config-module-mounts` input parameter has no effect if the `config-file` input parameter is used.

To add files to the Hugo build, the `config-module-mounts` input parameter can be used.

This will add files to the Hugo build by "mounting" them to the expected location in the Hugo build environment.

A "mount" is a configuration object that maps a file system path (or `source`) to a component path (`target`) within Hugo’s file system.

For example, to add a custom CSS file to the Hugo build, the following mount can be added:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      config-module-mounts: |
        { "source": "_/custom.css", "target": "assets/css/custom.css" }
```

> [!WARNING]
> Each `config-module-mounts` entry MUST be a valid line of JSON and have a trailing comma. The last entry (or a single entry) MUST NOT have a trailing comma.

The above example is exactly what happens when the `css` input parameter is used for adding custom CSS.

If `config-root-keys` is used to set the `module` root key, the same mount can be added like this:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      config-root-keys: |
          "languageCode": "en-us"
```

For more details, see the [Mounts section of the Hugo Modules documentation](https://gohugo.io/configuration/module/#mounts).

#### Build directory

If the site should be generated from another location (for instance the `./docs/` directory) the `path` input parameters needs to be set:

```yml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      # Set a path to build the website from, instead of the default `./`
      path: ./docs/
```

#### Build environment

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

#### Changing a file's path in the build

By default, files are mounted to the same path in the Hugo build as they are in the repository.

For instance, the file `./introduction.md` in the repository will be mounted to `./introduction/` in the Hugo website.

To change the path of a file in the website, the `config-module-mounts` input parameter can be used to mount the file to a different path.

Where the target is mounted to in the Hugo build determines where the file will be available in the website.
Hugo makes a distinction between various mount paths. The most common ones are:

- `assets/` This directory contains resources that can be passed through an "asset pipeline" (for minification, compiling Sass or TypeScript, etc.)
  For instance CSS, images, JavaScript, Sass, TypeScript, etc.
- `content/` This directory contains the Markdown files to be rendered into HTML by the Hugo build.
  Hugo expects index files to be named `_index.md` (for more information about this, see the "Using README.md files as folder index" section).
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

#### Excluding files from the artifact

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

#### Ignore files in the build

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
> Note that each entry MUST be wrapped in quotes and have a trailing comma. The last entry (or a single entry) MUST NOT have a trailing comma.

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

#### Rendering HTML in Markdown

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

For more information about Hugo configuration, see the "Various Hugo configurations" section.

#### Setting a Theme

> [!IMPORTANT]
> The `config-module-imports` input parameter has no effect if the `config-file` input parameter is used.

For a Hugo site, a [theme](https://themes.gohugo.io/) is "just" a [module](https://gohugo.io/hugo-modules/use-modules/) that needs to imported from a specific path.

This can be done by using the `config-module-imports` input parameter. For instance, to import the [Relearn theme](https://mcshelby.github.io/hugo-theme-relearn/), the following can be used:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      config-module-imports: |
        { "path": "github.com/McShelby/hugo-theme-relearn" }
```

> [!WARNING]
> Each entry MUST be a valid line of JSON and have a trailing comma. The last entry (or a single entry) MUST NOT have a trailing comma.

> [!TIP]
> Most themes provide parameters to configure and customize the theme. These can be passed to the workflow using the `config-params` input parameter. See the "Setting a Theme's parameters" section.

More themes can be found on the [Hugo Themes website](https://themes.gohugo.io/).

For details on how to import other types of modules, or how to specify other options, see [the Imports section of the Hugo Modules documentation](https://gohugo.io/configuration/module/#imports).

#### Setting a Theme's parameters

> [!IMPORTANT]
> The `config-params` input parameter has no effect if the `config-file` input parameter is used.

Most themes have parameters that can be set to configure the theme. Such parameters are defined by the theme, and can be found in the theme's documentation.

To configure and customize a theme, set the `config-params` input parameter.

For instance, the Relearn theme [configuration](https://mcshelby.github.io/hugo-theme-relearn/configuration/reference/) has parameters to configure the theme's menu and styling (i.e. theme variants). An example of setting these parameters would be:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      config-module-imports: |
        { "path": "github.com/McShelby/hugo-theme-relearn" }
      config-params: |
        {
          "collapsibleMenu": true,
          "themeVariant": ["auto", "zen-dark", "zen-light"]
        }
```

> [!WARNING]
> The value of the `config-params` input parameter MUST be a valid JSON object. It MUST NOT have a trailing comma.

For more information about non-theme site parameters, visit [the Hugo "Configure params" documentation](https://gohugo.io/configuration/params/).

#### Specify a Hugo config file

> [!IMPORTANT]
> All other input parameter that start with `config-` have no effect if the `config-file` input parameter is used.

To have more control over the Hugo build, a Hugo configuration file can be created.
For the workflow to use such a configuration file, it must be passed to the workflow using the `config-file` input parameter:

```yaml
jobs:
    build:
        uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
        with:
            config-file: path/to/config.yaml
```

If this input parameter is used, some other input params will not work, as they are set in an ad-hoc config file used by the workflow.

To make sure everything works as expected, make sure to add any workflow configuration to the Hugo config file:

```yaml
# config.yaml
# ...

disableHugoGeneratorInject: true
ignoreFiles:
  - go.*
  - hugo.yml
  - public/*
languageCode: en-us
module:
  imports:
    - path: github.com/McShelby/hugo-theme-relearn
params:
  - collapsibleMenu: true,
  - themeVariant:
    - auto
    - zen-dark
    - zen-light
title: My Website
```

#### Using README.md files as folder index

Hugo [expects index files for directories to be named `_index.md`](https://gohugo.io/content-management/organization/#index-pages-_indexmd).
The convention in Markdown is to use `README.md` files for this.

To use `README.md` files as index files in Hugo, the `build-readme-as-index` input parameter can be set to `true`:

```yamljobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      build-readme-as-index: true
```

This will rename all `README.md` files to `_index.md` before the Hugo build.

To have another file as the index file (for instance, `introduction.md`), the `config-module-mounts` input parameter can be used to mount the file to the expected location in the Hugo build.

For more details see the "Changing a file's path in the build" section.

#### Various Hugo configurations

> [!IMPORTANT]
> The `config-root-keys` input parameter has no effect if the `config-file` input parameter is used

Input parameters exist for the most common Hugo configurations, but there are many more Hugo configurations that can be set.

To avoid needing an input parameter for every possible Hugo configuration, the `config-root-keys` input parameter is available to set any Hugo configuration available in the root of the Hugo configuration.

Common examples of such configurations are `disableHugoGeneratorInject`, `ignoreFiles`, `languageCode`, and `title`.

For example:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      config-root-keys: |
        "disableHugoGeneratorInject": true,
        "languageCode": "en-us",
        "title": "My Website"
```

> [!WARNING]
> Each entry MUST be a valid line of JSON and have a trailing comma. The last entry (or a single entry) MUST NOT have a trailing comma.

Although input parameters exist for some configurations keys, those can also be set through the `config-root-keys` input parameter.

For example, instead of using `config-ignore-files`, `config-module-imports`, or `config-params`, the same configuration can be achieved by setting the corresponding root keys:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      config-root-keys: |
        "ignoreFiles": [ "go.*", "hugo.yml", "public/*"],
        "module": {
          "imports": [
            { "path": "github.com/McShelby/hugo-theme-relearn" }
          ]
        },
        "params": {
          "collapsibleMenu": true,
          "themeVariant": ["auto", "zen-dark", "zen-light"]
        }
```

For a full list of all available Hugo configuration options, see [the complete list of Hugo configuration settings.](https://gohugo.io/configuration/all/)

#### Website language

To set the website's language, the `languageCode` root key can be set using the `config-root-keys` input parameter:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      config-root-keys: |
          "languageCode": "en-us"
```

For more information about Hugo configuration, see the "Various Hugo configurations" section.

#### Website title

Usually, at the very least, you'll want to set a title for the website. This can be done using the `config-root-keys` input parameter, with the `title` key:

```yaml
jobs:
  build:
    uses: potherca/hugo-build/.github/workflows/hugo-build.yaml@main
    with:
      config-root-keys: |
          "title": "My Website"
```

For more information about Hugo configuration, see the "Various Hugo configurations" section.

## Contributing

Questions or feedback can be given by [opening an issue](https://github.com/Potherca/hugo-build/issues) on GitHub.

<!-- For a list of changes see the the GitHub releases page. -->

## License

Created by [Potherca](https://pother.ca) under a [MPL-2.0 License](LICENSE).
