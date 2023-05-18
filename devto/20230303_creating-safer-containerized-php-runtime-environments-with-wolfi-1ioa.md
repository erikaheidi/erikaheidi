---
title: Creating Safer Containerized PHP Runtimes with Wolfi 
published: true
description: In this tutorial, we'll learn about Wolfi and how to leverage this tiny distro for safer PHP container runtime environments.
tags: containers, devops, wolfi, linux
cover_image: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/m4ifydbmk4ad8hz5pg9z.png
---

## Introduction

[Wolfi](https://edu.chainguard.dev/open-source/wolfi/overview/) is a minimal open source Linux distribution created specifically for cloud workloads, with emphasis on software supply chain security. Using [apk](https://wiki.alpinelinux.org/wiki/Alpine_Package_Keeper) (just as Alpine), Wolfi has its own package repository and build ecosystem providing nightly builds and quick patch turnaround times. The main difference here is that Wolfi is glib-c based, while Alpine uses Musl.

The combination of a smaller attack surface and up-to-date, patched packages in Wolfi results in less (always aiming for ZERO) CVEs. This can be demonstrated in the results obtained from [Trivy](https://github.com/aquasecurity/trivy) when scanning the most popular PHP images on Docker Hub (with data from March 2, 2023) and comparing them with the Wolfi-based PHP image maintained by [Chainguard](https://chainguard.dev):

**php**
```
php (debian 11.4)
=================
Total: 483 (UNKNOWN: 7, LOW: 272, MEDIUM: 103, HIGH: 94, CRITICAL: 7)
```

**php:cli** (Debian)
```
php:cli (debian 11.6)
=====================
Total: 368 (UNKNOWN: 6, LOW: 267, MEDIUM: 51, HIGH: 43, CRITICAL: 1)
```

**php:alpine**
```
php:alpine (alpine 3.16.2)
==========================
Total: 32 (UNKNOWN: 0, LOW: 1, MEDIUM: 11, HIGH: 16, CRITICAL: 4)
```

**cgr.dev/chainguard/php** (Wolfi)
```
cgr.dev/chainguard/php (wolfi 20230201)
=======================================
Total: 18 (UNKNOWN: 0, LOW: 0, MEDIUM: 4, HIGH: 12, CRITICAL: 2)
```
_Results from Trivy on March 2, 2023_

Although the Wolfi PHP image still has some CVEs, it's not nearly the same number as the popular Debian-based `php:cli` image. Because patches are quickly applied and images rebuilt, it is likely that these few CVEs will be back to zero in just a few days.

In this article, we'll see how to leverage Wolfi to create safer PHP application environments based on containers. To demonstrate Wolfi usage in a Dockerfile workflow (using a Dockerfile to build your image), we'll create an image based on the [wolfi-base](https://github.com/chainguard-images/images/tree/main/images/wolfi-base) image maintained by Chainguard. The goal is to have a final runtime image able to execute a PHP command-line script. By definition, this image won't be completely _[distroless](https://www.chainguard.dev/unchained/minimal-container-images-towards-a-more-secure-future)_, because it will require APK to be present in order to install system dependencies described in the Dockerfile. For building pure distroless images, you should have a look at [apko](https://edu.chainguard.dev/open-source/apko/overview/).

### Requirements:

You'll need PHP 8.1+ and Composer to create the demo app, and Docker to build and run the image.

## Creating a Demo App
Let's create a simple demo app using [Minicli](https://docs.minicli.dev), to demonstrate dependency management with Composer. The app will output a random combination of adjective + noun. 

First, bootstrap a new Minicli app with:

```shell
mkdir wolfi-php-demo && cd $_
composer require minicli/minicli
```

Next, create the executable PHP script that will be the entry point for this image. You can call it `randomizer`, without the `.php` extension.

This script follows the [Minicli example for simple apps](https://docs.minicli.dev/en/latest/getting_started/creating-apps/#creating-a-simple-app). It defines a single command "get" that will output a random name combination based on two arrays.

```php
#!/usr/bin/php
<?php

require __DIR__ . '/vendor/autoload.php';

use Minicli\App;

$app = new App();

$app->registerCommand('get', function () use ($app) {

    $animals = [ 'turtle', 'seagull', 'octopus', 'shark', 'whale', 'dolphin', 'walrus', 'penguin', 'seahorse'];
    $adjectives = [ 'ludicrous', 'mischievous', 'graceful', 'fortuitous', 'charming', 'ravishing', 'gregarious'];

    $app->getPrinter()->info($adjectives[array_rand($adjectives)] . '-' . $animals[array_rand($animals)]);
});

$app->runCommand($argv);


```

Save the file. Then, set its permissions as an executable with:

```shell
chmod +x randomizer
```

Now you can test that it works as expected:

```shell
./randomizer get
```

And you'll get a random name combination such as:
```
ludicrous-turtle
```

## Creating the Dockerfile

Now we'll create the Dockerfile to run the application. This Dockerfile will set up a new WORKDIR, copy relevant files, and install dependencies with Composer. It will also define the entry point and command that will be executed when we run this image with `docker run`.

```
FROM cgr.dev/chainguard/wolfi-base

WORKDIR /app
COPY composer.json composer.lock randomizer /app/
RUN apk add php composer && \
    composer install --no-progress --no-dev --prefer-dist

ENTRYPOINT ["php", "/app/randomizer"]
CMD ["get"]

```

After saving the file, you can finally build your application runtime image with:

```shell
docker build . -t wolfi-php-demo 
```

Finally, run the image with:

```shell
docker run --rm wolfi-php-demo
```

And you should get a similar result as the previous time you run the script.

```
ravishing-seahorse

```

## Using apko for Improved CVE Scanning

The caveat for scanning images generated with the previous method is that CVE scanners will most likely only evaluate your base image, so if you run trivy on your `wolfi-php-demo` now you should only get results for CVEs affecting `wolfi-base`:

```
wolfi-php-demo (wolfi 20220914)

Total: 1 (UNKNOWN: 0, LOW: 0, MEDIUM: 0, HIGH: 0, CRITICAL: 1)
```

This can be misleading since the final image adds new packages (php and composer), each with its own dependency tree. To tighten your setup even more, you can build a base image with [apko](https://edu.chainguard.dev/open-source/apko/overview/) and include all system dependencies there (php, composer). This will assure that the base image you use in your Dockerfile has all system dependencies accounted for in an SBOM (Software Bill of Materials), which can be scanned or monitored.

Check the [getting started with apko](https://edu.chainguard.dev/open-source/apko/getting-started-with-apko/) tutorial if you want to learn more about apko. Check also the public [PHP Chainguard Images](https://edu.chainguard.dev/chainguard/chainguard-images/reference/php/overview/) which are all based on Wolfi and offer a few different variants for general use cases.