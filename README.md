# Reusable GitHub Workflows for Hugo

This repo contains a reusable GitHub Workflow for building a Hugo website.

<!-- toc -->

## Install

No installation is required, the reusable workflows can be called directly from a workflow.

## Usage

[GitHub Workflow][github-using-workflows] files must live in the `.github/workflows` directory of a project, and must be named `[workflow-name].yml`. For instance, [`.github/workflows/call-hugo-build.yaml`](https://github.com/potherca-blog/hugo-build-example/blob/main/.github/workflows/call-hugo-build.yaml)

The reusable workflow can be used by adding a `uses` key to a job in the workflow file:

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

#### Build directory

If the site should be generated from another location (for instance the `./docs/` directory) the `path` will need to be set:

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

All hugomod images are available on Docker Hub (docker.io), but they can also be pulled from the GitHub Container Registry (ghcr.io).

To select a specific image, visit https://docker.hugomods.com/choose/, or create a custom image with the any needed features.

#### Excluding files from the artifact

After the Hugo site is built, it is uploaded as an artifact named "github-pages". To create the artifact, `tar` is used.

By default, the `.git` and `.github` folders are excluded from the artifact.

If other files or folders also need to be ignored, they can be added using the `exclude` input parameter:

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

## Contributing

Questions or feedback can be given by [opening an issue](https://github.com/Potherca/hugo-build/issues) on GitHub.

<!-- For a list of changes see the the GitHub releases page. -->

## License

Created by [Potherca](https://pother.ca) under a [MPL-2.0 License](LICENSE).
