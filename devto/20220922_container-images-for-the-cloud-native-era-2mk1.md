---
title: Container Images for the Cloud Native Era
published: true
description: Announcing Wolfi and Chainguard Images
tags: containers, opensource, linux, security
cover_image: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/kqgxzk5qmvq38vqoxy84.png
---

Today is a very special day for Chainguard, as we release to the public a few projects that will lay the foundation for important improvements in the cloud-native and container ecosystems. The following announcement (video) provides an overview of these projects and how they fit together to enable safer build and runtime environments, with a focus on securing the software supply chain:

{% youtube -FermtgIc-I %}

During the past few months, I have been working closely with the team that built Wolfi and Chainguard Images, testing and documenting these projects as they evolved into the version that is being released today. Some of this work is already available at [Chainguard Academy](https://edu.chainguard.dev), our educational hub for software supply chain resources. More to come, for sure!

![gif of a purple octopus throwing confetti](https://media.giphy.com/media/Yw0ZpX6d3sQnxsjaQx/giphy.gif)

In this post I'll talk a bit about these projects, how they came to be, and what they represent in the context of container images for the cloud native era.

## How Wolfi was Born
A long time ago, in a galaxy far far away, some folks realized that the most popular base container images were bloated with stuff that could make sense in bare metal servers, but were totally superfluous in containerized environments. The first distroless images were developed at Google, and Chainguard's founders Matt Moore and Dan Lorenc were directly involved with the initiative.

![gif of kid saying "oh say less!"](https://media.giphy.com/media/dzPDaQLABRZkBfWQFE/giphy.gif)

Fast-forward to May 2022, when the Chainguard Images project was still in its first iterations. It didn't take long for the team to realize that the Linux distributions commonly used as bases for container images were not really designed for what we wanted to build. Some of the main challenges included the lag behind upstream updates, lack of provenance information, and an unnecessary increased attack surface caused by software that didn't need to be there.

The only way to solve these problems was to build a distribution designed for container/cloud native environments. So, we built Wolfi. You can read more about the design principles and decisions behind Wolfi in [this blog post](http://www.chainguard.dev/unchained/introducing-wolfi-the-first-linux-un-distro). The [Wolfi documentation](https://edu.chainguard.dev/open-source/wolfi/overview/) at Chainguard Academy has more context on why we call it an _undistro_ and how it is different from other Linux distributions. In a nutshell, Wolfi is a tiny distribution that doesn't have a kernel of its own, it's designed to support both `glibc` and `musl`, and includes high-quality SBOMs (software bill of materials) covering all packages in the distro.

## A Brief Look at the New Chainguard Images

Powered by Wolfi, Chainguard Images are a suite of distroless images that consolidate the base features of the Wolfi undistro into end-user container images that can be integrated into existing workflows. Chainguard Images are fully declarative and reproducible, and include SBOMs that cover all image dependencies. In addition, Chainguard Images are signed via [Sigstore](https://sigstore.dev), which attests the provenance of all artifacts. All images and corresponding signatures, as well as their SBOMs, are hosted in Chainguard's OCI registry `cgr.dev`.

The easiest way to see the advantages of using a Chainguard Image instead of their traditional (or even official) counterpart is by comparing the results of security scanners analyzing those images. Here's an overview of Nginx, as an example:

![trivy scan results: nginx official vs chainguard](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/16z56m4puzoec2ca3svh.png)

On the left, you have an overview of CVEs detected by [Trivy](https://github.com/aquasecurity/trivy) on the `nginx:latest` container image, from August to September. This includes low, medium, high, and critical CVEs (classified by color). On the right side, you can see the results from a Trivy run on our distroless Nginx image: zero CVEs.

But don't take my word for it; install Trivy and run the scan on your own terminal, you will be surprised with the results.

To learn more about how to get started using Chainguard Images, please visit the official documentation at [Chainguard Academy](https://edu.chainguard.dev/chainguard/chainguard-images/overview/).
 
## Chainguard Academy: the one-stop resource for software supply chain security

Last but not least, [Chainguard Academy](https://edu.chainguard.dev) comes as a one-stop educational resource to close gaps in knowledge and elevate our collective understanding about software supply chain security, approaching conceptual subjects and industry frameworks as well as technical guides and tutorials on how to get things done in practice. 

It's worth noting that our knowledge base is open source and we are committed to keep iterating on it in order to provide the best documentation around the software supply chain, and the wider community is invited to propose improvements and new topics. We are still working on contributing guidelines, but they should be available soon (and in time for Hacktoberfest, dare I say!). Yay! You can find us on [GitHub](https://github.com/chainguard-dev/edu).

Finally, if you have any questions, feel free to join us today in a [Twitter Spaces](https://twitter.com/i/spaces/1lDGLndgWpyxm?s=20) where we'll discuss all things software supply chain! 

![gif - mic drop](https://media.giphy.com/media/10JDn0oEPKp6da/giphy.gif)


