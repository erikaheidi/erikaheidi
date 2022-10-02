---
title: 3 ways to improve your OSS project's resilience for Hacktoberfest
published: true
description: 
tags: hacktoberfest, opensource, security, beginner
cover_image: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/1xwjj7b5y70p9bfw6uke.png
---

With October just around the corner, [Hacktoberfest](https://hacktoberfest.com) is about to start. While many developers around the world are getting ready to participate and claim their rewards, open source maintainers who opt in their projects are also busy preparing for the event. Maintainers play a very important role in Hacktoberfest each year, enabling participants to contribute by creating adequate issues, reviewing PRs, and helping out newcomers getting started into their projects.

Apart from the usual preparation tasks that focus on enabling contributors to find relevant issues they can work on, as a maintainer you can also work on a few improvements to make your project more resilient and better prepared to accept contributions from the wider community. 

In this article, we'll share 3 things you can do today in order to improve your project's reliability and facilitate long-term maintenance, keeping software supply chain security in mind.


## 1. Set up Automated Quality Checks with GitHub Actions

GitHub Actions is a powerful feature from GitHub that allows you to set up unlimited automated workflows, given your project is open source. Actions that automatically run the application's test suite and check the code style used are ideal to be integrated into your pull request workflow, facilitating reviews and enabling contributors to get unblocked faster.

The [GitHub Marketplace](https://github.com/marketplace?type=actions) has a large offering of community-contributed actions for all kinds of stacks. 

For instance, searching for "php" will give you quite a few different actions that can be combined in a workflow to be executed whenever a new pull request is open. 

![screenshot showing github marketplace search for php actions](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/5a10srrl7jtnpz2gkou2.png)

Check the documentation included with each action for more information on how to implement it on a workflow for your project.

## 2. Sign your Commits (and ask contributors to do the same)

Signing your commits is an important step to improve the reliability of your codebase, since it allows users to confirm that the code committed to the repository actually comes from a legitimate source. It is not very hard to forge commit authorship, especially considering typosquat attacks, where bad actors will create an account with a very similar name to your own account and set up git to use similar credentials.

So why isn't everyone signing their commits already? It has a lot to do with the difficulty in creating and maintaining GPG keypairs, which are used to generate commit signatures. Luckily for us, keyless signing (which in fact still uses GPG keys but make them invisible and ephemeral, only valid at the time of signing) is an alternative that doesn't require dealing with GPG keys.That makes commit signing a lot easier to adopt in the context of open source projects that expect contributions from the wider community.

[Gitsign](https://docs.sigstore.dev/gitsign/overview/) offers a keyless commit signing implementation based on OIDC, which is an identity layer built on top of the OAuth 2.0 framework. Gitsign supports verifying your identity either through GitHub, Microsoft, or a Google account.

![OIDC authorization with Gitsign](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/77gfmla6qqdzdra0ei0q.png)

For more information on commit signing and detailed instructions on how to install and set up Gitsign, check the following article:

{% post https://dev.to/erikaheidi/enable-gitsign-today-and-start-signing-your-commits-2gda %}

Don't forget to update your CONTRIBUTING docs to include information about signing commits, with links to relevant articles to help users set their local git configuration for signing.  Here is an example text in markdown that you can copy and paste to your CONTRIBUTING.md page:

```markdown
### Commit Signing

Please sign all your commits using either GPG keys or a keyless mechanism such as [Gitsign](https://docs.sigstore.dev/gitsign/overview/). This is important to make sure all code comes from trusted sources, which will improve the overall security and quality of our project. For more information on how to set up your local Git environment to user commit signing by default, please refer to one of the following guides:
- [Gitsign Documentation](https://docs.sigstore.dev/gitsign/installation)
- [GitHub Documentation on signing commits with GPG keys](https://docs.github.com/en/authentication/managing-commit-signature-verification/signing-commits)

```

## 3. Include a Software Bill of Materials (SBOM) within your Releases

A Software Bill of Materials is a document that contains a list of all software dependencies that are incorporated into your application or container image. There are a few different formats you can use, but [SPDX](https://spdx.dev/) is generally a good choice since it is a standard maintained by the Linux Foundation.

Having an SBOM allows end-users and project leads to track all software included in the application as well as its versions. It facilitates detecting vulnerabilities within your software supply chain.

[Syft](https://github.com/anchore/syft/) is a popular open source tool that generates SBOMs for software applications and also containers. You can execute it manually and include the generated artifacts into your release, but you can also automate the process using a GitHub Action that will be triggered whenever you have a new release on your repository.

For instance, you can use the following workflow to set up Syft to automatically generate SBOM files for Composer-based projects (PHP) whenever a new release is published in your repository:

```yaml
name: Generate Release SBOM
on:
  release:
    types:
      - published

  workflow_dispatch:
  
jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3
      - name: Install Composer dependencies
        run: composer instal --prefer-dist --no-progress
      - name: Generate SBOM
        uses: anchore/sbom-action@v0
        with:
          path: ${{ github.workspace }}
```

## Final Considerations

The steps listed in this article may often be seen as an afterthought and not a priority for an open source project, but they certainly pay dividends in the long run. In addition to setting up a strong foundation for contributors to do their work, you'll also seize the benefits of having automated processes to help with the project's long term maintenance. By implementing these steps within your open source project, you will also be gathering valuable data that you can use later on to compare and keep track of your project's evolution, how the dependencies changed over time (via SBOMs), who are the legitimate authors of commits (via commit signing), and whether or not new contributions adhere to code standards (via CI Actions).
