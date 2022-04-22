---
title: How To Create a Composer Bin Command with Minicli
published: true
description: Learn how to create a simple Minicli application and distribute it as a Composer Bin Command.
tags: php, tutorial, showdev, cli
cover_image: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6p3jyxvhkcdydjo6y4we.png
---

When creating and distributing PHP software with [Composer](https://getcomposer.org), the PHP dependency manager, there are mainly two types of projects you can set up: a **library** and an **application**.

If your code is intended to be included in other applications so that classes and other components are available at code level, then you are most likely building a **library**. If your code is self-contained as something to be installed and executed as a standalone piece, then you're most likely building an **application**. 

Sometimes, however, the line between these two types of projects is not very clear. You may want to provide executable code in addition to your library components. There is also the case when you build a tool that needs access to the application context, so that it can't be installed as a separate project and thus needs to be included in the bigger application with a `composer require`.

With [Composer Bin Commands](https://getcomposer.org/doc/articles/vendor-binaries.md), you can include executable scripts within your project and they will be installed (via a symbolic link) on `vendor/bin`. Examples of projects using this feature include [PestPHP](https://pestphp.com/docs/installation) and Laravel [Sail](https://laravel.com/docs/8.x/sail), both using Composer bin commands to distribute executable scripts as application dependencies.

In this tutorial, we'll build a bin Composer command with [Minicli](https://minicli.dev), using the [Advice Slip API](https://api.adviceslip.com). The code for this project is available as [minicli/advice-slip](https://github.com/minicli/advice-slip) on GitHub and on [Packagist](). The benefit of using Minicli for something like this is that you won't be bringing a bunch of new dependencies to the projects that will install your tool, the only dependency added with Minicli is `minicli/minicli`.

![Screenshot of terminal with the composer require command installing minicli/advice-slip](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/926sovftwwb3183rltno.png)

## Requirements

You'll need a PHP development environment with:
- PHP 7.4+ (CLI only)
- [Composer Installed](https://getcomposer.org)

## Step 1: Setting Up a Minicli application

Start by requiring `minicli/minicli` in your existing application. In case you don't have an existing application and you would like to create a standalone tool with Minicli, create a new directory for your application and run Composer from there, so it will generate a new `composer.json` file for you.

```shell
composer require minicli/minicli
```

Next, create a new `bin` directory in the root of your application folder. 

```shell
mkdir bin
```
Create a new file called `minicli` in this folder:

```shell
touch bin/minicli
```

This script will be the entry point of your CLI application that will be made available as Composer bin command.

### Note About Composer autoload

When including the Composer autoload script, we'll need to first check if the application is being called from `vendor/bin` (as a required dependency) or if it's being called from the application folder itself (as a standalone project). 

To do that, we'll verify the existence of a `vendor/autoload.php` file. If that file doesn't exist, it's likely that the application is being called as a dependency in the `vendor` folder, which make the autoload script available at a different location.

### Step 2: Creating a `bin/minicli` Script

Open the`minicli` script file inside the `bin` folder, which is currently empty. The following code will implement the autoload verification and bootstrap a new Minicli application using the provided `app_path` as command location, similar to the [basic demo from official docs](https://docs.minicli.dev/en/latest/01-getting_started/).

```php
#!/usr/bin/php
<?php

if (php_sapi_name() !== 'cli') {
    exit;
}

$root_app = dirname(__DIR__);

if (!is_file($root_app . '/vendor/autoload.php')) {
    $root_app = dirname(__DIR__, 4);
}

require $root_app . '/vendor/autoload.php';

use Minicli\App;
use Minicli\Command\CommandCall;

$app = new App();
$input = new CommandCall($argv);

$app->registerCommand('help', function() use ($app) {
    $app->getPrinter()->info("minicli help");
});

$app->runCommand($input->getRawArgs());
```

Copy these contents to your `minicli` script and don't forget to save the file. Then, run the registered `help` command with:

```shell
php bin/minicli help
```

Once you confirm the script is working, you can create a new command to fetch contents from the API.

### Step 3: Creating a Command To Obtain Advice Slips

The structure is ready and you can now go ahead and implement your command (s).

Here we'll build a command to fetch an advice slip from the free [Advice Slip API](https://api.adviceslip.com).

This is a free API that doesn't require authentication, but if you want to build something more fun, check out the [Giphy API](https://developers.giphy.com/docs/api/endpoint#translate) (it requires registering an application and providing API keys). I won't cover it here for simplicity.

Our command will obtain a random advice, and will also accept a `search=term` parameter to search for specific advice. Replace the current content of your `minicli` script with the following code, which implements the new "advice" command:

```php
#!/usr/bin/php
<?php

if (php_sapi_name() !== 'cli') {
    exit;
}

$root_app = dirname(__DIR__);

if (!is_file($root_app . '/vendor/autoload.php')) {
    $root_app = dirname(__DIR__, 4);
}

require $root_app . '/vendor/autoload.php';

use Minicli\App;
use Minicli\Command\CommandCall;

$app = new App();
$app->setSignature('./bin/minicli advice');
$input = new CommandCall($argv);

$app->registerCommand('help', function() use ($app) {
    $app->getPrinter()->info("./bin/minicli advice");
});

$app->registerCommand('advice', function() use ($app, $input) {
    $query_url = "https://api.adviceslip.com/advice";

    if ($input->hasParam('search')) {
        $query_url = "https://api.adviceslip.com/advice/search/" . $input->getParam('search');
    }

    $result = file_get_contents($query_url);

    if ($result) {
        $content = json_decode($result, true);

        if (isset($content['total_results'])) {
            $search_results = $content['slips'];
            $advice = $search_results[array_rand($search_results)]['advice'];
        } else {
            if (isset($content['slip'])) {
                $advice = $content['slip']['advice'];
            }
        }

        $app->getPrinter()->newline();
        $app->getPrinter()->info($advice ?? "No results found.");
        $app->getPrinter()->newline();
    }

    return 0;
});

$app->runCommand($input->getRawArgs());
```

You can now try the command with:

```shell
php bin/minicli advice
```

And also:

```shell
php bin/minicli advice search=spiders
```

## Step 4: Updating `composer.json`

Once your command is functional, you want to update your `composer.json` to include the Bin definition.

If this is a new application, you may also want to update the name and other information, since you'll need to submit your code to packagist.org in order to make it available to use via `composer require`.

This is how my `composer.json` file looks after the update (I set this package name to `minicli/advice-slip`, you should use a different name). Notice the `bin` entry including the location of the `minicli` script. You could include other scripts here if you wanted to.

```json
{
  "name": "minicli/advice-slip",
  "description": "Demo - Advice Slip",
  "license": "MIT",
  "homepage": "https://github.com/minicli/advice-slip",
  "keywords": ["cli","command-line", "demo"],
  "require": {
    "minicli/minicli": "^2.0",
    "ext-json": "*"
  },
  "bin": [
    "bin/minicli"
  ]
}
```
If your project is already on Packagist, then you'll need to commit and push your changes to the remote repository so that they are picked up by Packagist. You may need to create a new release of your project in order to deliver the changes to existing users. 

If your project is not yet published, follow the next step to set it up with Packagist.

## Step 5: Submitting the Package to Packagist 

To make the command available to anyone who would like to use it through Composer, your package / application must be submitted on [Packagist.org](https://packagist.org). To do that, first you need to push your application to a public repository on GitHub. Once you have the repository URL, you can log in on Packagist and submit your new package:

![Submitting Package on Packagist](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/k7624mpqvqamdb0tlois.png)

Ideally, you should [create at least one release](https://github.com/minicli/advice-slip/releases/tag/0.1.0) in your GitHub repository (such as `0.1.0`). If you don't have a release, Composer will complain about the minimum stability whenever a user requires this package on their own projects.

Once everything is set up, including the release, you can include you Minicli application in other projects with a regular `composer require org/project`:

```shell
composer require minicli/advice-slip
```

```
Using version ^0.1.0 for minicli/advice-slip
./composer.json has been updated
Running composer update minicli/advice-slip
Loading composer repositories with package information
Updating dependencies
Lock file operations: 1 install, 0 updates, 0 removals
  - Locking minicli/advice-slip (0.1.0)
Writing lock file
Installing dependencies from lock file (including require-dev)
Package operations: 1 install, 0 updates, 0 removals
  - Downloading minicli/advice-slip (0.1.0)
  - Installing minicli/advice-slip (0.1.0): Extracting archive
Generating autoload files
1 package you are using is looking for funding.
Use the `composer fund` command to find out more!
```

The command we built will then be available as:

```shell
./vendor/bin/minicli advice
```

Or:

```shell
php vendor/bin/minicli advice
```

![Screenshot of terminal with the composer require command installing minicli/advice-slip](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/926sovftwwb3183rltno.png)

## Conclusion

In this tutorial, we've seen how to create a single command application with Minicli and how to turn it into a Composer Bin Command.

You can have access to this project's codebase at https://github.com/minicli/advice-slip .