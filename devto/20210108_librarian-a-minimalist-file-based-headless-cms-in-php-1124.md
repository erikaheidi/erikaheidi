---
title: Librarian - a minimalist file-based, headless CMS in PHP
published: true
description: Librarian is a minimalist file-based CMS in PHP, on top of Minicli. This is a sample PHP submission to the DigitalOcean Hackaton with DEV.
tags: dohackathon, php, showdev, digitalocean
cover_image: https://dev-to-uploads.s3.amazonaws.com/i/jiq9eevpwjvs8p0px3y7.png
---

*Update: Librarian now has its own documentation website, built with Librarian :) check it out: https://librarianphp.dev*


>Disclaimer: as a DigitalOcean employee, I am not entitled to win any prizes in this hackaton, but I was encouraged to participate as an exercise and to provide a sample PHP application that can help others get inspired :)

## What I built
A headless CMS that can be used as a personal portfolio of your DEV content.

### Category Submission: 
Personal Site/Portfolio

### App Link
https://librarian-iz6yi.ondigitalocean.app/

### Screenshots
![Screenshot: Librarian as headless CMS pulling content from dev.to](https://dev-to-uploads.s3.amazonaws.com/i/jiq9eevpwjvs8p0px3y7.png)

### Description
[Librarian](https://github.com/minicli/librarian) is a minimalist file-based, headless CMS written in PHP, on top of [minicli](https://github.com/minicli/minicli). 

### Link to Source Code
https://github.com/minicli/librarian

### Permissive License
MIT

## Background
Last year, I wanted a simple website to show all my DEV posts in a more "branded", personal way. So I started working on this application, but I never really finished it. 

As the year started, I decided to finally get this application finished and submit it to the DigitalOcean + DEV hackaton as an exercise.

The great thing about this project is that it is very small, it uses only a small $5 instance and doesn't require databases or other components. You can quickly spin up a sample app using the _Deploy to DO_ button below, but for a more serious usage you should [fork the project on GitHub](https://github.com/minicli/librarian) so you're able to customize the templates to your own liking.

<p align="center">
<a title="Deploy this application to DigitalOceans App Platform in a few clicks!" href="https://cloud.digitalocean.com/apps/new?repo=https://github.com/minicli/librarian/tree/main"><img src="https://mp-assets1.sfo2.digitaloceanspaces.com/deploy-to-do/do-btn-blue.svg" alt="Deploy to DO button"></a>
</p>

### How I built it 
This project uses PHP to parse markdown files based on pre-defined directory structure conventions. It is built on top of a dependency-free CLI library I built called [Minicli](https://docs.minicli.dev).

Although I have created a good starting point last year, there were a lot of things missing, so this time around I included an RSS feed, fixed dozens of bugs, improved the layout, and made sure it is deployable to App Platform.

I learned a lot about [BulmaCSS](https://bulma.io/documentation/) while working in the updated front end, and also learned more about the [App Spec format](https://www.digitalocean.com/docs/app-platform/references/app-specification-reference/) and the "Deploy to DO" button. My favorite thing is that I can now quickly spin up small project documentation sites and content-focused, segmented sites for only $5 a month using this app.

### Additional Resources/Info 
I will be updating the project repository with more docs soon.

The following video shows the whole process of deploying the sample app using the DO button and importing DEV posts to the new blog.

{% youtube Yb_lkbCZxOY %}