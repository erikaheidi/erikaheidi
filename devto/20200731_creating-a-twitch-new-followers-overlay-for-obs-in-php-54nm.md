---
title: Creating a Twitch New Followers Overlay for OBS in PHP
published: true
description: In this post, you'll see a high-level overview of the whole process to create something such as this, and how to use the tool I've been working on as a base / starting point for your stream customizations using the Twitch API.
tags: showdev, php, livecoding, twitch
cover_image: https://dev-to-uploads.s3.amazonaws.com/i/iqife1e9nu3nhs7n6wdi.jpg
---

## Introduction

Twitch has their own ecosystem of points and rewards, and leveraging [their API](https://dev.twitch.tv/) to showcase new followers on screen is a good starting point for those who want to give more recognition to their growing community.

[I've been building](https://twitch.tv/erikaheidi) something like this for my own stream sessions. Please don't reply with comments such as "there's extension X that does just that", since the exact objective of this experiment is to not depend on external services and build your own custom widgets or overlays. Freedom, at last! ;)

## Building Your Own Twitch New Followers Overlay in Any Language

Here's a TL;DR of the whole process, using Twitch's implicit OAuth workflow.

- **Step 1**: Create a [new Twitch Application](https://dev.twitch.tv/console/apps) and obtain the `client_id`.
- **Step 2**: Create a script to generate your application's [authorization link](https://dev.twitch.tv/docs/authentication/getting-tokens-oauth#oauth-implicit-code-flow) and receive Twitch's callback request. This will require the application's `client_id` and the `callback_url` you set up with Twitch.
- **Step 3**: Extract the user's access token from the callback URL. This will come in the following format: `http://localhost:8000/twitch#access_token=TOKEN&scope=user%3Aedit&token_type=bearer`, where TOKEN is your unique access token. Save it somewhere safe.
- **Step 4:** Make a request to Twitch's API to validate the user token and get the user's ID. Include the [Auth header](https://dev.twitch.tv/docs/authentication/#sending-user-access-and-app-access-tokens) with the user's access token.
- **Step 5:** Make a request to Twitch's API to obtain [recent followers](https://dev.twitch.tv/docs/api/reference#get-users-follows) for that user ID, and include the [Auth header](https://dev.twitch.tv/docs/authentication/#sending-user-access-and-app-access-tokens) with the user's access token.
- **Step 6:** Display the results nicely so that you can include that via an OBS **Browser Source**. 

_Note: If you're on Linux, as I am, you'll probably need to install [a plugin to enable browser source](https://github.com/bazukas/obs-linuxbrowser) (totally worth it!)._

In the next section you'll see how to use the tool I've built during my live coding streams.

## Using StreamWidgets

[StreamWidgets](https://github.com/erikaheidi/streamwidgets) is an experimental tool, and it's open source, so you can help improve it - actually, it's just a base, a starting point to give you an idea or inspiration. The idea really is to take control over our own overlays and interactive features, build fun things, and be totally free to experiment in our live streams. After all, we are coders ;)

And yes, I hate installing extensions!

### StreamWidgets Requirements

- PHP 7.3+ (CLI-only is OK)
- [Composer](https://getcomposer.org)
- A Twitch app that you can create at https://dev.twitch.tv/console/apps, using `http://localhost:8000/twitch` as redirect URI.

### Installation Steps

1. Clone (or fork and clone) [the project](https://github.com/erikaheidi/streamwidgets)
2. Run `composer install` to install the dependencies. This will create a new `config.php` using example values.
3. Edit your `config.php` to include your application's **CLIENT ID** and Twitch username.
4. Run the built-in PHP server on the root of the project with `php -S 0.0.0.0:8000 -t web/`
5. Access the Twitch Auth endpoint from your browser: `http://localhost:8000/auth`
6. Follow the instructions, clicking on the auth link. You will be redirected to authorize the application on Twitch.
7. After authorizing, you will be redirected back to the /twitch endpoint. Look at the browser URL, your access token will be there in the following format:

>http://localhost:8000/auth#access_token=TOKEN&scope=user%3Aedit&token_type=bearer

Where TOKEN is your unique access token. Copy that token to your `config.php` and keep it safe.

Once your access token is set up within your config file, you can get your latest followers at `http://localhost:8000/followers`. 

Open a new browser source on OBS and point it to that address, then adjust the styling as needed! The CSS can be found at `web/css/widgets.css`.

>Don't forget the web server (whether it's built-in, container-based or on a remote server) must be running so that you can pull the endpoint from your OBS studio. A JavaScript auto-refresh thing might be necessary to keep things updated from time to time!

## Update: Subs Endpoint

I made a little update over the weekend to include a new endpoint that lists your recent subs. This uses the endpoint [https://api.twitch.tv/helix/subscriptions](https://api.twitch.tv/helix/subscriptions) and is available at `/subs`.

## Meta

The following video has the live coding session where I explain things a bit more and implement support for [Twig templates](https://twig.symfony.com/) and [Bulma CSS](https://bulma.io/).

{% youtube UHEKOfjG9w4 %}