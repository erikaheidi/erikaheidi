---
title: Automatically update your CONTRIBUTORS file with this GitHub Action + Workflow
published: true
description: This is my first submission to the GitHub Actions x DEV Hackathon 2021. This action will update your CONTRIBUTORS file with top contributors and commit the changes to the repository where the workflow is defined.
tags: actionshackathon21, github, opensource, contributors
---

### My Workflow
I wanted to build something simple but yet useful for open source maintainers. So here's what I built: an action to automatically generate a `CONTRIBUTORS.md` file based on a project's top contributors, using the GitHub API to pull information about the project. The workflow then uses another action to create a pull request or commit the changes directly to the same repository where the workflow is configured.

![Automated pull request with github actions](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/g9rz7y5tmedisyahxfv6.png)

The action runs a [single-command application](https://github.com/minicli/action-contributors/blob/main/minicli) created with [Minicli](https://docs.minicli.dev), a minimalist command-line framework for building PHP CLI commands.

The application, action, and example workflows can be found here:

{% github https://github.com/minicli/action-contributors %}

> **Note:** If you want to learn how I built this, check out this post: [How to build GitHub Actions in PHP with Minicli and Docker](https://dev.to/sourcegraph/how-to-build-github-actions-in-php-with-minicli-and-docker-1k6m).

### Submission Category: 
Maintainer Must-Haves

### Yaml File or Link to Code

Here is an example workflow to run this action once a month and commit the changes directly to the main project's branch:

```yml
name: Update CONTRIBUTORS file
on:
  schedule:
    - cron: "0 0 1 * *"
  workflow_dispatch:
jobs:
  main:
    runs-on: ubuntu-latest
    steps:
      - uses: minicli/action-contributors@v2
        name: 'Update a projects CONTRIBUTORS file'
        env:
          CONTRIB_REPOSITORY: 'minicli/minicli'
          CONTRIB_OUTPUT_FILE: 'CONTRIBUTORS.md'
      - name: Update resources
        uses: test-room-7/action-update-file@v1
        with:
          file-path: 'CONTRIBUTORS.md'
          commit-msg: Update Contributors
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

You need to replace the `CONTRIB_REPOSITORY` value with the GitHub project you want to pull contributors from.

If you'd prefer to create a pull request instead of committing the changes directly to the main branch, you can use the [create-pull-request](https://github.com/marketplace/actions/create-pull-request) action instead. For that, you'll also need to include the [actions/checkout](https://github.com/actions/checkout) GitHub Action:

```yml
name: Update CONTRIBUTORS file
on:
  schedule:
    - cron: "0 0 1 * *"
  workflow_dispatch:
jobs:
  main:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: minicli/action-contributors@v3
        name: "Update a projects CONTRIBUTORS file"
        env:
          CONTRIB_REPOSITORY: 'minicli/minicli'
          CONTRIB_OUTPUT_FILE: 'CONTRIBUTORS.md'
      - name: Create a PR
        uses: peter-evans/create-pull-request@v3
        with:
          commit-message: Update Contributors
          title: "[automated] Update Contributors File"
          token: ${{ secrets.GITHUB_TOKEN }}
```

### Additional Resources / Info

Projects using this action:

- [minicli/minicli](https://github.com/minicli/minicli)
- [minicli/docs](https://github.com/minicli/docs)

If you want to learn how I built this, check out this post: [How to build GitHub Actions in PHP with Minicli and Docker](https://dev.to/sourcegraph/how-to-build-github-actions-in-php-with-minicli-and-docker-1k6m).