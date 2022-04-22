---
title: Deploying a Laravel Application to DigitalOcean App Platform via doctl with an App Spec YAML File
published: true
description: In this post I'll share my experience deploying a Laravel application to DigitalOcean App Platform using DigitalOcean's doctl command-line tool, with an app spec YAML file.
series: do-hackaton
tags: dohackathon, laravel, php, DigitalOcean
cover_image: https://dev-to-uploads.s3.amazonaws.com/i/d2i6gfchtpjs1eolp8qy.png
---

In a nutshell, [DigitalOcean's App Platform](https://www.digitalocean.com/docs/app-platform/) provides a streamlined way to deploy your application and have auto deployments every time new code is pushed to a designated branch, while also providing advanced metrics and control over your app's components. You can do the whole process from a friendly UI, however, some folks might prefer to have more fine-grained control and quickly reuse an environment to new applications that are alike, deploying from the command line. That's when the [App Spec](https://www.digitalocean.com/docs/app-platform/references/app-specification-reference/) file comes in hand.

![Kombucha girl meme: Spend 30 minutes setting up ENV vars through UI vs spend 2 days figuring out how to do that with a YAML file](https://dev-to-uploads.s3.amazonaws.com/i/nfr8smrkw568udqum8ni.jpeg)

Jokes aside, there are a few reasons why you would prefer to use a spec YAML file instead of the UI to deploy your app. If your application has many components, many environment variables to set up, or if it's not being correctly detected by [buildpack](https://www.digitalocean.com/docs/app-platform/concepts/buildpack/), you can use the app spec file to define how your application should be deployed.

The reason why I went this route initially was because I had issues with buildpack detecting my application as a Node app, because of the `package.json` file that comes by default with new Laravel apps. Because other folks might end up having similar issues, I decided to create a post sharing what worked for me.

My application is a rather simple Laravel 8 app that you can build yourself following [this tutorial series on DigitalOcean](https://www.digitalocean.com/community/tutorial_series/how-to-build-a-links-landing-page-in-php-with-laravel-and-docker-compose). It's a links landing page, similar to [another project I built using Laravel Jetstream](https://dev.to/erikaheidi/creating-a-multi-user-to-do-application-with-laravel-jetstream-2p1k). The difference is that the new project is a simplified version that doesn't have a restricted area or user registration, based solely on pure Laravel and using Bulma CSS for the one-page front end. Links are managed via Artisan commands on the CLI.

![Landing Laravel Demo Application](https://dev-to-uploads.s3.amazonaws.com/i/4sibyblkerwuo2lyn3qz.png)

Because this project is rather simple and doesn't use NPM / Tailwind / etc, the first thing I did was to remove the included `package.json` file from the repository, since even with the environment set to PHP this file was being picked up by buildpack and resulting in a build error because I didn't have the rest of the components properly set up for running `npm install` (which I didn't need).

After removing this file, I was able to get the build finished through the UI, but still had some trouble with ENV variables and how to run migrations. After many tries and a lot of experimentation (also some help from my DO friends) I was able to make everything work flawlessly with the app spec file that I will share in a moment. First, I'd like to make some observations.

- **Prefer PostgreSQL**: Whether you're using the dev database or a managed one, using PostgreeSQL will save you some trouble. There are [known issues](https://github.com/laravel/framework/issues/33238) with *some* Laravel applications regarding the use of primary keys and how tables are created when migrations are executed. I run into this problem when I tried to deploy a Laravel + Jetstream app, the issue coming from the Laravel Passport package.
- **Setting the Environment**: It's extremely important that you set up your `environment_slug` to `php`.
-  **Migrations as separate job**: because you need the database information to run migrations, and those are only available after the build is finished, you can't include this command in the build. It shouldn't be in the "run" command either, so you should create a separate job of the `POST_DEPLOY` type to run the migration command.
- **Setting Up ENV vars**: instead of including a `.env` file within your application repository, you should set the environment variables either through the UI or through the app spec file. The UI is very handy to change some value later on, but setting all of them initially can be really laborious and prone to errors. The app spec is ideal for that initial setup, thou. You will notice that I had to repeat the list of ENV variables  on the `jobs` definition, and that is because the job works as a separate component, with a separate environment, and thus needing the variables to be set too. I was informed by my colleagues that this is going to change soon, with ENV variables that are globally available (or replicated among components), but that feature didn't reach the `doctl` tool yet. For now, we have to repeat the variables in each component.

To use the app spec to deploy your application, you'll need to have [doctl](https://www.digitalocean.com/docs/apis-clis/doctl/how-to/install/) installed on your local machine - it's available for Linux, Mac, and Windows. You'll also need to make sure your GitHub account is connected to your DigitalOcean account, so that it can obtain your application code.

Ok, enough with explanations. This is the app spec file that worked for me, and I hope it works for you too:

```yaml
databases:
- engine: PG
  name: db
  num_nodes: 1
  size: db-s-dev-database
  version: "12"
name: landing-laravel
region: ams
services:
- environment_slug: php
  run_command: heroku-php-apache2 public/
  envs:
  - key: APP_NAME
    scope: RUN_TIME
    value:  Landing_Laravel
  - key: APP_ENV
    scope: RUN_TIME
    value: dev
  - key: APP_KEY
    scope: RUN_TIME
    value: base64:ffYPNP8kPeQDf8gE/qh3kWjk59p6gFY66kCKhhKUa2w=
  - key: APP_DEBUG
    scope: RUN_TIME
    value: "1"
  - key: APP_URL
    scope: RUN_TIME
    value: ${APP_URL}
  - key: DATABASE_URL
    scope: RUN_TIME
    value: ${db.DATABASE_URL}
  - key: DB_CONNECTION
    scope: RUN_TIME
    value: pgsql
  - key: DB_HOST
    scope: RUN_TIME
    value: ${db.HOSTNAME}
  - key: DB_PORT
    scope: RUN_TIME
    value: ${db.PORT}
  - key: DB_DATABASE
    scope: RUN_TIME
    value: ${db.DATABASE}
  - key: DB_USERNAME
    scope: RUN_TIME
    value: ${db.USERNAME}
  - key: DB_PASSWORD
    scope: RUN_TIME
    value: ${db.PASSWORD}
  github:
    branch: main
    deploy_on_push: true
    repo: do-community/landing-laravel
  http_port: 8080
  instance_count: 1
  instance_size_slug: basic-xs
  name: landing-laravel
  routes:
  - path: /
jobs:
- name: migrate
  kind: POST_DEPLOY
  github:
    repo: do-community/landing-laravel
    branch: main
    deploy_on_push: true
  run_command: php artisan migrate --seed
  envs:
  - key: APP_NAME
    scope: RUN_TIME
    value:  Landing_Laravel
  - key: APP_ENV
    scope: RUN_TIME
    value: dev
  - key: APP_KEY
    scope: RUN_TIME
    value: base64:ffYPNP8kPeQDf8gE/qh3kWjk59p6gFY66kCKhhKUa2w=
  - key: APP_DEBUG
    scope: RUN_TIME
    value: "1"
  - key: APP_URL
    scope: RUN_TIME
    value: ${APP_URL}
  - key: DATABASE_URL
    scope: RUN_TIME
    value: ${db.DATABASE_URL}
  - key: DB_CONNECTION
    scope: RUN_TIME
    value: pgsql
  - key: DB_HOST
    scope: RUN_TIME
    value: ${db.HOSTNAME}
  - key: DB_PORT
    scope: RUN_TIME
    value: ${db.PORT}
  - key: DB_DATABASE
    scope: RUN_TIME
    value: ${db.DATABASE}
  - key: DB_USERNAME
    scope: RUN_TIME
    value: ${db.USERNAME}
  - key: DB_PASSWORD
    scope: RUN_TIME
    value: ${db.PASSWORD}
```

To use this spec to deploy your application, first make sure that you change the `github.repo` value on both components (under `services` and also `jobs`) to point to your own Laravel application repository, and the `name` to a name of your own. Then, you can run:

```bash
doctl apps create --spec <path-to-spec>
```

You'll see output similar to this:

```
Notice: App created
ID                                      Spec Name           Default Ingress    Active Deployment ID    In Progress Deployment ID    Created At                                Updated At
371e211e-1ad1-4f23-b0d0-aac838cf922f    landing-laravel                                                                            2020-12-23 12:25:10.25023846 +0000 UTC    2020-12-23 12:25:10.25023846 +0000 UTC

```

Then, you can go to your App Platform dashboard to follow the building process from there:

![Checking Deployment Logs](https://dev-to-uploads.s3.amazonaws.com/i/7bf0uz9ywjc14y05ousy.gif)

This is not my submission yet, but I thought it was important to share this information with you all ASAP to help anyone who might run into issues when trying to deploy Laravel apps. Hope it helps! Cheers and see you next time, hopefully with my own submission :)


<a href="https://www.buymeacoffee.com/erikaheidi"><img src="https://img.buymeacoffee.com/button-api/?text=Buy me a coffee&emoji=&slug=erikaheidi&button_colour=BD5FFF&font_colour=ffffff&font_family=Poppins&outline_colour=000000&coffee_colour=FFDD00"></a>