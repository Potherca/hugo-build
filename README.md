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

For configuration details, see the [`docs/configuration/`](docs/configuration/) directory.

## Contributing

Questions or feedback can be given by [opening an issue](https://github.com/Potherca/hugo-build/issues) on GitHub.

<!-- For a list of changes see the the GitHub releases page. -->

## License

Created by [Potherca](https://pother.ca) under a [MPL-2.0 License](LICENSE).
