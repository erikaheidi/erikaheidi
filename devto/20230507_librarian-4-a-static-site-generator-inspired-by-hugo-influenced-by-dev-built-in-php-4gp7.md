---
title: Librarian 4: a static site generator inspired by Hugo, influenced by DEV, built in PHP
published: true
description: After a long wait, Librarian 4 is finally out
tags: php, netlify, docs, librarian
cover_image: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/mmiema3pxov5a87jr1k1.png
# Use a ratio of 100:42 for best results.
# published_at: 2023-05-07 17:54 +0000
---

About [two years ago](https://dev.to/erikaheidi/librarian-a-minimalist-file-based-headless-cms-in-php-1124) I was releasing Librarian to the public for the first time. It was a fun experiment that I created to experiment with markdown files and test DigitalOcean's App platform, where I used to work. It was a fairly simple project, using a simple one-file CSS framework.

Fast-forward to 2022, I create a three websites using Librarian, including a blog about Linux that I called [Onlinux.Systems](https://onlinux.systems). The process of customizing and improving it so that I was satisfied with these projects drove me to update Librarian to use TailwindCSS, among some other improvements. Things were fine; but it was still a PHP website that required a server running Nginx and PHP-FPM, for instance, to serve it.

Time goes by and I start using Hugo at work. I got really used to Hugo's way of dealing with content and how simple it was to get it deployed to a platform such as Netlify (I even wrote [a tutorial about this](https://dev.to/erikaheidi/setting-up-a-documentation-website-for-your-software-project-with-hugo-and-netlify-4bpj) recently). That made me think: "what if... what if Librarian outputs a static build? That shouldn't be too hard to implement, right?"

I still didn't do anything just yet. Then, 2023 came, and with the new Ubuntu 23.04 release, I felt I **had** to update OnLinux and add more tutorials and reviews there. At the same time, I started to resent the fact that I needed a full Nginx+PHP-FPM server to run such a simple project, while at work I had nice deploy previews for free on Netlify. I thought of just changing to Hugo, but then I would lose some of the features that I had implemented using custom liquid tags in Librarian.

That's when I decided to (finally) go for it and implement the static build feature for Librarian. But to implement that, I had to work on a bunch of other features and updates first, and what started as a simple job became a big quest that **I'm happy to finally be releasing to the public today** :)

![Screenshot of the Librarian documentation website, built with Librarian!](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/yp035fxdmglmdlg33abo.png)

[Librarian can now be hosted on Netlify for free](https://librarianphp.dev/deploying-librarian/deploying-to-netlify/). Commands are  implemented as separate dependencies living on external repos, so that you can keep them up-to-date more easily. With a [familiar but rather simplified file structure](https://librarianphp.dev/getting-started/file-structure/), Librarian tries to channel the elegance of Hugo with the flexibility of PHP.

One of my favorite features of Librarian is the ability to create [custom liquid tags](https://librarianphp.dev/customizing-librarian/custom-liquid-tags/). This allows you to create custom parsers for anything you'd like, in the same style used by DEV. In fact, Librarian was very influenced by dev.to, and it was originally created to serve as a backup mechanism for my DEV posts.

If you'd like to learn more, here are a few resources and examples:

- [Official Librarian Docs, built with Librarian 🤯](https://librarianphp.dev)
- [Librarian Docs Repository](https://github.com/librarianphp/docs) - this uses a custom "docs" theme that you can also check for inspiration.
- [OnLinux repository](https://github.com/erikaheidi/onlinux-systems) - this has custom liquid tags and a few other niceties such as a custom index page template that shows latest posts from each content type.
- Sponsoropensource [site](https://sponsoropensource.dev) and [repository](https://github.com/erikaheidi/sponsoropensource) - this project uses another custom theme that I called "portfolio".

As with most of my open source projects, I have created Librarian to serve my own needs, but instead of keeping it private, I decided from the very beginning that I would build in public and share whatever I would come up with. Open source doesn't need to be about changing the world, it can be about changing *your* world a little bit, making it easier and/or more convenient, and sharing that with the world in case someone also finds that helpful. I hope this can be an alternative for folks who would like something simple yet full of potential for their static site needs :) Cheers! 🥂 