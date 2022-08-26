---
title: Enable Gitsign Today and Start Signing your Commits
published: true
description: The issue of code provenance is ultimately an industry-wide threat. In this post, I will address something rather simple you can do today if you are an open source maintainer and/or contributor.
tags: git, security, opensource
cover_image: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/h5pe465s8tn11rxz9u39.png
---

I have been meaning to write this post for some time already, but the extra motivation came after looking at the [recent news of PyPI phishing attacks](https://www.techtarget.com/searchsoftwarequality/news/252524191/PyPI-phishing-renews-call-for-mandatory-2FA-package-signing) that have compromised several Python library maintainers and their packages.

The issue of code provenance is ultimately an industry-wide threat that needs to be addressed sooner rather than later. In this post, I will address something rather simple you can do **today** if you are an open source maintainer and/or contributor, and it can help you attest that the code pushed to a repository really comes from where it's supposed to come: from you.

## The Threat

A common attack would rely on a lost keypair (maybe something you left unnattended in an old server, or in an old backup) to push commits with your identity, which makes it almost impossible to differentiate from real commits performed by you.

The following screenshot shows an example of two commits pushed to a repository I own, from a completely different environment that I set up with an old keypair that still happens to be registered within my profile:

![Improptu commit with old keypair](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/m3fdnruulq9dx1k0fauk.png)

The only difference between these to commits is that in the top commit I have set my git config to use the same email I have registered with GitHub. Both "improptu" commits rely on an old but still valid keypair being temporarily set up, but even without a valid keypair, someone invested in creating a typosquatting package could fake being you, configuring Git with your information, then committing to the fake repository and tricking people into believing they're a known package maintainer.

That is why we need additional layers of security to prove the provenance of code that is committed to a repository, especially when it comes to libraries that are used as dependencies by hundreds or thousands of other projects.

## Why Commit Signing 

Commit signing is actually nothing new. It has been implemented as a way to attest that the code committed to a code repository really comes from a certain user, although this feature initially has been limited to simple adding a "signed by username" signature to commits.

If I go back to my improptu "hacked" environment and instead of just committing a change normally I add `-s` to my commit, this is what will come up within the GitHub interface:

![improptu commit with traditional signing](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/nvqzz20yxee1nov3713x.png)

This is what I mean with just adding a "signed by usernaname" message to the commit. This helps nobody, it proves nothing, as anyone can add a "Signed off by Erika Heidi" to their commits. You can try it right now with any repository you have the rights to push commits to.

What we want here is to actually use **encryption keys** to sign each commit, which then creates the mechanism that allows end-users to verify the signature and attest that the commit really comes from where it says it comes.

Using GPG keys is one way you can do that, as [explained in the GitHub documentation](https://docs.github.com/en/authentication/managing-commit-signature-verification/signing-commits). The issue here is that we face the same problem that caused a bad actor to be able to push to your repository in the first place: we are depending on a keypair that might get lost of fall in the hands of unauthorized users.

## Adopting Keyless Signing

To solve this issue, a new methodology called **keyless signing** is gaining a lot of traction among developers and security engineers. In fact, keyless signing relies on ephemeral keys to sign and provide attestations, eliminating the issue of long-lived keys that can be exploited by bad actors.

[Gitsign](https://github.com/sigstore/gitsign) offers a keyless commit signing implementation based on OIDC, which is an identity layer built on top of the OAuth 2.0 framework. Gitsign supports verifying your identity either through GitHub, Microsoft, or a Google account.

## Installing and Enabling Gitsign

Setting up Gitsign to start signing your commits is a two-step process: first, you'll need to install Gitsign on the system you're using to commit your code. Then, it's just a matter of letting Git know that you will be signing all your commits with Gitsign by default.

### Installing Gitsign with Homebrew

On macOS and Linux systems that support Homebrew, you can get Gitsign installed with:

```shell
brew tap sigstore/tap
brew install gitsign
```

### Installing Gitsign on Linux with .deb or .rpm Packages

The [Gitsign releases page](https://github.com/sigstore/cosign/releases) contains `.deb` and `.rpm` packages for download. Download the latest version and install with one of the following:

```shell
#.deb (Ubuntu, Debian etc)
sudo dpkg -i gitsign_0.2.0_linux_amd64.deb
```

```shell
#.rpm (Fedora)
rpm -ivh gitsign_0.2.0_linux_amd64.rpm
```

After you're done, check that you're able to run Gitsign with:

```shell
gitsign --version
```

For more installation options, please check the [official documentation](https://docs.sigstore.dev/gitsign/installation).

### Configuring Gitsign within Git

There are mainly two ways to configure your Git projects to use Gitsign by default: per project and system-wide.

To configure Gitsign per project, access your project's directory and run:

```shell
git config --local commit.gpgsign true  # Sign all commits
git config --local tag.gpgsign true  # Sign all tags
git config --local gpg.x509.program gitsign  # Use Gitsign for signing
git config --local gpg.format x509  # Gitsign expects x509 args
```

This might be a good way to try out Gitsign with a single project, with no strings attached.

If you'd prefer to configure Gitsign globally and enable it for all your projects, run the following:

```shell
git config --global commit.gpgsign true  # Sign all commits
git config --global tag.gpgsign true  # Sign all tags
git config --global gpg.x509.program gitsign  # Use Gitsign for signing
git config --global gpg.format x509  # Gitsign expects x509 args
```

Once you are finished, try creating a commit on your project the same way you always do. There is no need to provide a `-s` additional parameter. 

If the installation and configuration worked as expected, when creating a commit you will be redirected to a browser window, where you'll be able to choose a supported OIDC provider to verify your identity:

![OIDC authorization with Gitsign](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/77gfmla6qqdzdra0ei0q.png)

Once you confirm your identity, the commit will go through. After pushing it to your remote repository on GitHub, you should see a note on the right of your commit signaling that the commit was signed, but saying "unverified". For the time being this is normal because GitHub still doesn't validate the certificate authority used by the Gitsign ecosystem (Sigstore), although this is likely to change once more users are actively signing their commits with Gitsign.

![A commit properly signed with Gitsign and keyless signing](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/tbakkeeie9ljgqutln8t.png)

## Verifying Signatures

Verifying a signature involves two steps: checking if the specified signature matches the commit, and also if the commit exists in the [Rekor](https://docs.sigstore.dev/rekor/overview) transparency log. I see this process being a very interesting opportunity for package managers to create some automation around providing built-in verification in the future. Let's hope so!

Gitsign's [documentation](https://docs.sigstore.dev/gitsign/inspecting) page about signature inspection provide this command to check for the latest commit within the transparency log, using `rekor-cli`:

```shell
uuid=$(rekor-cli search --artifact <(git rev-parse HEAD | tr -d '\n') | tail -n 1)
rekor-cli get --uuid=$uuid --format=json | jq .
```

If you get outut from this command it means that the latest commit was indeed signed and have their signature recorded within the Rekor transparency log.

If you want, you can [verify the commit signature](https://docs.sigstore.dev/gitsign/inspecting#verifying-the-commit-signature) and cross-check this information with the info obtained from the transparency log.

## Conclusion

Signing your commits is a step you can start doing today to improve the resilience of your open source projects, allowing users to trace back your commits to you. The more we do this as a community and an industry, the safer our software ecosystem will be, turning commit signing a new default and pushing package managers to implement additional security measures that rely on commit signatures to validate software releases, which will be the next step in the quest for a more trustworthy software supply chain.