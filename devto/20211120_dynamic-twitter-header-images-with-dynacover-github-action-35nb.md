---
title: Dynamic Twitter header images with Dynacover GitHub Action!
published: true
description: This GitHub action uses erikaheidi/dynacover to generate dynamic Twitter header images showing latest followers and/or GitHub sponsors.
tags: actionshackathon21, github, opensource, php
---

### My Workflow
Earlier this year, I've built a command-line application to generate dynamic Twitter header images showing latest followers and GitHub sponsors, called [Dynacover](https://github.com/erikaheidi/dynacover). I shared [the full tutorial on how I built this](https://dev.to/erikaheidi/how-to-dynamically-update-twitter-cover-image-to-show-latest-followers-using-php-gd-and-twitteroauth-62n) here on DEV.

![screenshot showing the dynamic header image on my Twitter profile](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/yilj90o4tysav70aeay8.png)

With the GitHub Actions Hackaton coming up, and considering it's also possible to run GH Actions on schedule, I thought it would be a very interesting challenge to containerize this application and package it as a GH Action. It took me a couple weeks to figure out how to optimize the build and how to allow users to customize their banner templates, but today I was finally able to wrap everything up.

The [Dynacover GitHub Action](https://github.com/marketplace/actions/dynacover) is now published to the Marketplace and can be used by anyone with a GitHub account.

The action files are available in this repository:

{% github erikaheidi/dynacover-actions %}

### Submission Category: 

Wacky Wildcards

### Yaml File or Link to Code
You'll need to set up your Twitter API credentials and user tokens using [repository secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets), on the same repository
you set up your action workflow. The GitHub token is optional, only required if you want to use one of the covers that showcase GitHub Sponsors.

**Secrets**
- `DYNA_TWITTER_KEY`: your Twitter application consumer key or App Key.
- `DYNA_TWITTER_SECRET`: your Twitter application consumer secret or App Secret.
- `DYNA_TWITTER_TOKEN`: your personal user token.
- `DYNA_TWITTER_TOKEN_SECRET`: your personal user token secret.
- `DYNA_GITHUB_TOKEN` (optional): your GitHub personal token (for pulling GitHub Sponsors).

```yml
name: Update Twitter Header Image with Dynacover
on:
  schedule:
    - cron: "0 * * * *"
  workflow_dispatch:
jobs:
  main:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          path: 'dynacover_custom'
      - name: 'Update Dynacover image and upload to Twitter'
        uses: erikaheidi/dynacover-actions@v4.2
        env:
          # Uncomment and change accordingly to customize your cover
          #DYNA_DEFAULT_TEMPLATE: dynacover.json
          #DYNA_TEMPLATES_DIR: ${{ github.workspace }}/dynacover_custom
          #DYNA_IMAGES_DIR: ${{ github.workspace }}/dynacover_custom
          DYNA_TWITTER_KEY: ${{ secrets.TW_CONSUMER_KEY }}
          DYNA_TWITTER_SECRET: ${{ secrets.TW_CONSUMER_SECRET }}
          DYNA_TWITTER_TOKEN: ${{ secrets.TW_USER_TOKEN }}
          DYNA_TWITTER_TOKEN_SECRET: ${{ secrets.TW_USER_TOKEN_SECRET }}
          DYNA_GITHUB_TOKEN: ${{ secrets.DYNA_GITHUB_TOKEN }}
```

### Additional Resources / Info

For a detailed step by step guide on how to set this up, check out [this Dynacover wiki page](https://github.com/erikaheidi/dynacover/wiki/Setting-Up-Dynacover-with-GitHub-Actions). You can set up your workflow using the GitHub web interface, no coding needed!

To see this workflow in action using a custom template and cover, check my own [erikaheidi/github-actions](https://github.com/erikaheidi/github-actions) repository, where I have my own Dynacover set up.
