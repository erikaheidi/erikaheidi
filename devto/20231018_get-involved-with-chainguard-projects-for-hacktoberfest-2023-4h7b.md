---
title: Get Involved with Chainguard Projects for Hacktoberfest 2023
published: true
description: Learn how you can contribute to Chainguard open source projects for Hacktoberfest 2023
tags: hacktoberfest, opensource, containers, linux
cover_image: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3tpxo4veb2izlfplid0a.png
# published_at: 2023-10-16 14:18 +0000
---

Open source has always been at the core of what Chainguard does, which is evidenced by projects such as [Sigstore](https://sigstore.dev) and [Wolfi](https://edu.chainguard.dev/open-source/wolfi/overview/). From [image building](https://github.com/chainguard-dev/apko) to automated [documentation](https://github.com/chainguard-dev/images-autodocs), the majority of the code we write is open source, from the very beginning.

In this month of October, the Developer Enablement team at Chainguard would like to invite [Hacktoberfest](https://hacktoberfest.com/) participants to get involved with our open source projects. We have good first issues and a welcoming team ready to help with any questions you may have. 

If you're interested in containers and Linux, you may be interested in Wolfi, our community Linux _undistro_ built for containers. We appreciate your help in keeping Wolfi-bot accountable - it doesn't always get things right 😅

If you'd like to help with documentation, we welcome your help with READMEs in the [Chainguard Images](https://github.com/chainguard-images/images) repository. 

## Contributing to Wolfi
The [Wolfi](https://edu.chainguard.dev/open-source/wolfi/overview/) Linux undistro is an open source project primarily maintained by Chainguard, but with community contributors from all over the world. Wolfi packages live in the [github.com/wolfi-dev/os](https://github.com/wolfi-dev/os) repository, where we have established a thorough automation process to validate new package builds, check for available package updates, and automatically send PRs when new versions are available. In fact, the Wolfi-bot is a top contributor to the project, but it doesn't always get things right at first.

This year we invite Hacktoberfest participants to help with broken automated PRs from the Wolfi-bot. 

### Identifying Broken PRs
The broken Wolfi-bot PRs are easy to spot — they are tagged as `automated-pr` and `request-update` and have an red `X` icon next to it, indicating that one or more CI checks didn't pass. This is typically due to a build issue that needs to be manually debugged and fixed - maybe a new package version introduces a new dependency that is not met at build time, or something has changed in the make / build process for that package.

We have created a number of issues to fix these broken PRs. You can find them in the repo by searching for issues tagged as `hacktoberfest`.

### What we expect from contributions
- Choose an issue tagged as `hacktoberfest` from [the list](https://github.com/wolfi-dev/os/issues/7017). Make sure to leave a comment on the issue so that others know you are working on it.
- Create a new PR with your changes, so that we can tag it with the `hacktoberfest-accepted` label and make it a valid PR for Hacktoberfest.

Check also the [Wolfi contributing guide](https://github.com/wolfi-dev/os/blob/dfb8f4a553e894db861295be44ef5e4de3072022/CONTRIBUTING.md) for more information on how to get started.

## Contributing to Chainguard Images
The public [https://github.com/chainguard-images/images](chainguard-images/images) repository is an open source repository containing the configurations for all public Chainguard Images. With the number of images growing every day, it is hard to keep up with documentation needs. This year we invite Hacktoberfest participants to test our images and help update their READMEs, which are used for images documentation living in [Chainguard Academy](https://edu.chainguard.dev/chainguard/chainguard-images/reference).

### What we expect from contributions:
- Choose [one of the listed issues](https://github.com/chainguard-images/images/issues/1539). 
- Test the image on your local environment.
- Update the image README with instructions on how to use it and any other important information that you think users should know. You should follow the [README template](https://github.com/chainguard-images/images/blob/main/.github/readme-template.md) for keeping READMEs consistent.
- Optionally, write a test for the image. Check the "Tests" section of our [Best Practices doc](https://github.com/chainguard-images/images/blob/main/BEST_PRACTICES.md#tests) for more details on how to go about that.

When you're finished, create a PR to the Chainguard Images repository with your updated README, so that we can tag it with the `hacktoberfest-accepted` label and make it a valid PR for Hacktoberfest.

## Contributing to Chainguard Academy
You are also welcome to send documentation PRs to [Chainguard Academy](https://edu.chainguard.dev). Maybe you found a typo or an outdated instruction in one of our tutorials? Send a [PR](https://github.com/chainguard-dev/edu). Just be aware that some of those docs are automated and shouldn't be manually altered. When in doubt, create an issue first so that we can discuss.

## Sending PRs to Chainguard Projects
In order to get your PRs passing CI scripts to Chainguard repositories, your commits must be signed with either [Gitsign](https://docs.sigstore.dev/signing/gitsign/) or with regular [gpg signing](https://docs.github.com/en/authentication/managing-commit-signature-verification/signing-commits).

Don't forget to mention the issue ID you're fixing with your PR. For Wolfi PRs, a good title could be something like "Fixing wolfi-bot update: package-name #1234" where `1234` is the number of your assigned issue. For Chainguard Images PRs, we recommend using a `[Docs]` prefix so that the documentation team can be assigned more quickly to review your PR.

## Wolfi Swag
We value all contributors and to show our gratitude, we’ll be giving away a limited number of Wolfi swag packs. If your PR gets accepted, you may be eligible to receive one! Reviewers will leave instructions for you in the eligible PR.

If you still have any questions, don't hesitate to open an issue on the relevant repository or reach out directly on social: https://twitter.com/chainguard_dev .

Happy hacking!
