---
title: How to Create a GitHub Action to Import Posts from DEV to a Repository you Own
published: true
description: In this tutorial you'll learn how to create a GitHub Action that will import your posts from DEV into a GitHub repository you own. We'll build this in PHP using Minicli.
tags: tutorial, php, github, intermediate
cover_image: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/7xoyz9t9ue3myo8iu2uk.png
---

In this tutorial, you'll learn how to create a GitHub Action that will import your posts from DEV into a GitHub repository that you own. We'll build this in PHP using Minicli, a microframework for the command line.

This content was presented as a talk at [Laracon EU 2022](https://laracon.eu), slides are available [in this link](https://speakerdeck.com/erikaheidi/building-github-actions-in-php-with-minicli).

If you want to jump right to the finished code, you can check the repository [erikaheidi/importDevTo](https://github.com/erikaheidi/importDevTo).

### Requirements

- PHP CLI 8.0+ development environment with `php-curl`
- Composer

## Creating the Minicli App

In this step we'll create the Minicli application that will query the DEV.to API for the latest posts of a user or organization.

### Bootstrapping a new Minicli app

You can create a new Minicli application with the `composer create-project` command:

```shell
composer create-project minicli/application importDevTo
```

### Including required libraries

In this demo, we'll use the `minicli/curly` library to query the DEV API. This library is a simple curl wrapper to perform GET and POST queries.

```shell
composer require minicli/curly
```

### Creating a config

Because our application will be turned into a GitHub action later and will require the use of ENV variables for extended customization, we'll start by creating a configuration file to set two important variables defining the DEV user or organization we want to pull content from, and where to save this content. 

Create a new file named `config.php` in the root of the application:

```shell
touch config.php
```
We'll set up a configuration array that we'll require from the application entry point script (minicli). Copy the following content to your `config.php` file:

```php
<?php

return [
    'app_path' => __DIR__ . '/app/Command',
    'debug' => true,
    'devto_username' => getenv('DEVTO_USERNAME') ?: 'erikaheidi',
    'data_path' => getenv('APP_DATA_DIR') ?: __DIR__ . '/devto'
];
```

This uses `getenv` to fetch an environment variable if that exists, otherwise uses a default value for both the DEV user and the data path where to save the markdown files.

Next, change the line that instantiates the application to use this config in the `minicli` script:

```php
$app = new App(require __DIR__ .'/config.php');
```

Save the file and run `./minicli help` from your terminal to make sure things are working from expected.

### Creating a command to import posts from DEV

With a structured Minicli app, commands are organized as controllers that follow a pre-defined directory structure. To create a command that is called as `minicli import dev`, we need to create a folder named `Import` inside `app/Command`, and then create a controller named `DevController.php` in the new folder. The command controller must extend from the `CommandController` class and implement a method named `handle()`.

```shell
mkdir app/Command/Import
touch app/Command/Import/DevController.php
```

You can copy the following code to your new command controller. This will use values defined in the application config we created in the previous step.

The handle method will:

- connect to the DEV api and fetch posts from the user defined in the configuration;
- make a second query to obtain the full contents of each post;
- save each post in a `.md` file in the location defined by `data_path` in the config, using a combination of date and article slug as file name.

```php
#app/Command/Import/DevController.php
<?php


namespace App\Command\Import;

use Minicli\Curly\Client;
use Minicli\Command\CommandController;

class DevController extends CommandController
{
    public string $API_URL = 'https://dev.to/api';

    public function handle(): void
    {
        $this->getPrinter()->display('Fetching posts from DEV...');
        $crawler = new Client();

        if (!$this->getApp()->config->devto_username) {
            throw new \Exception('You must set up your devto_username config.');
        }

        $devto_username = $this->getApp()->config->devto_username;

        $articles_response = $crawler->get($this->API_URL . '/articles?username=' . $devto_username);

        if ($articles_response['code'] !== 200) {
            throw new \Exception('Error while contacting the dev.to API.');
        }

        if (!$this->getApp()->config->data_path) {
            throw new \Exception('You must define your data_path config value.');
        }

        $data_path = $this->getApp()->config->data_path;

        if (!is_dir($data_path) && !mkdir($data_path)) {
            throw new \Exception('You must define your data_path config value.');
        }

        $articles = json_decode($articles_response['body'], true);
        foreach($articles as $article) {
            $get_article = $crawler->get($this->API_URL . '/articles/' . $article['id']);

            if ($get_article['code'] !== 200) {
                $this->getPrinter()->error('Error while contacting the dev.to API.');
                continue;
            }

            $article_content = json_decode($get_article['body'], true);
            $date = new \DateTime($article_content['published_at']);
            $filepath = $data_path . '/' . $date->format('Ymd') . '_' . $article_content['slug'] . '.md';
            $file = fopen($filepath, 'w+');
            fwrite($file, $article_content['body_markdown']);
            fclose($file);

            $this->getPrinter()->info("Saved article: " . $article_content['title'] . " to $filepath");
        }

        $this->getPrinter()->info("Finished importing.", true);
    }
}
```

Once you have that controller in place, the application should be fully functional. Go to your terminal and run:

```shell
./minicli import dev
```

You should see output like this, with your own post titles:

```
Fetching posts from DEV...

Saved article: Como criar uma GitHub Action para importar posts do DEV.to em PHP com o Minicli - Vídeo + Tutorial to /home/erika/Projects/importDevTo/devto/20220422_como-criar-uma-github-action-para-importar-posts-do-devto-em-php-com-o-minicli-video-tutorial-2lnd.md

Saved article: How to Edit Videos with OpenShot on Ubuntu Linux to /home/erika/Projects/importDevTo/devto/20220411_how-to-edit-videos-with-openshot-on-ubuntu-linux-2k2h.md

Saved article: Minicli 3.0 for PHP 8: release notes, upgrade guide to /home/erika/Projects/importDevTo/devto/20220309_minicli-30-for-php-8-release-notes-upgrade-guide-k7k.md
(...)
```

Check your defined data_path directory to make sure the files were correctly saved.

With the application ready, you can now go ahead and turn it into a GitHub Action.

## Creating the app Dockerfile

This Dockerfile will allow you to run the application with a single `docker run` command. Based on PHP-cli 8.1, it will install the dependencies, install Composer, copy the application files to the location `/application` inside the container, move to the application directory, and finally run the command `php /application/minicli import dev`.

```
FROM php:8.1-cli

RUN apt-get update && apt-get install -y \
    git \
    curl \
    libxml2-dev \
    zip \
    unzip

RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Install Composer and set up application
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer
RUN mkdir /application
COPY . /application/
RUN cd /application && composer install

ENTRYPOINT [ "php", "/application/minicli" ]
CMD ["import", "dev"]
```

## Creating the action.yml file
The `action.yml` file is a requirement to set up the application as a GitHub Action. This file must be deployed to the root of the application repository.

```yaml
name: 'Import DEV.to posts'
description: 'Imports posts from DEV.to as markdown files'
outputs:
  response:
    description: 'Output from command'
runs:
  using: 'docker'
  image: 'Dockerfile'
```

## Publishing the GitHub Action

Once you have a `Dockerfile` and an `action.yml` set at the root of the application repository, you can commit your changes and tag the application. Tagging is required for referencing this action from a workflow.

```shell
git add .
git commit -m "Initial commit"
git tag -a -m "Version 1.0" v1
git push --follow-tags
```
## Creating a workflow to open a PR whenever you have new posts on DEV

The following workflow will:

- run once a day at 1am UTC;
- check out the repository where this workflow is defined inside the `$GITHUB.WORKSPACE` location inside the runner;
- build and execute the action `erikaheidi/importDevTo@v1.2`, using the provided `DEVTO_USERNAME` as user to pull content from, and the `APP_DATA_DIR` to specify where to save the generated markdown files. The `${{ github.workspace }}`  variable is where the workflow origin repository was checked out;
- check for a diff between the checked out repo and the generated files. If there are changes, a pull request will be initiated.

The workflow file should be defined in the repository where you want to save your markdown posts from DEV. This file can have any name, but it must go in a specific directory in your repo: `.github/workflows`. You can also use GitHub's interface to create a new workflow and paste these contents there.

```yaml
name: Import posts from DEV
on:
  schedule:
    - cron: "0 1 * * *"
  workflow_dispatch:
jobs:
  main:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: erikaheidi/importDevTo@v1.2
        name: "Import posts from DEV"
        env:
          DEVTO_USERNAME: erikaheidi
          APP_DATA_DIR: ${{ github.workspace }}/devto
      - name: Create a PR
        uses: peter-evans/create-pull-request@v3
        with:
          commit-message: Import posts from DEV
          title: "[automated] Import posts from DEV"
          token: ${{ secrets.GITHUB_TOKEN }}
```

Commit the changes.

## Testing the workflow

Because we have a `workflow_dispatch` directive in our workflow, we can run it manually from the `Actions` tab on the repository. Click on the "Run Workflow" button to initiate the workflow execution.

When the execution is finished, if you have posts that weren't imported before, you should see a new pull request like the following:

![pull request criado automaticamente por esse workflow](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3lshy3552idlfch88svq.png)

## Recap and Conclusion

GitHub Actions are a powerful tool that allows unlimited workflow runs for open source projects. That creates lots of possibilities for running small workers, bots, and other CLI scripts and applications. The fact that you can use a Dockerfile to run your app makes it a great use case for PHP in the command line.

### Relevant Links

- [ImportDevTo Action Repository](https://github.com/erikaheidi/importDevTo)
- [GitHub Actions documentation](https://docs.github.com/en/actions)
- [Minicli documentation](https://docs.minicli.dev)
- Other actions built with Minicli
  - [Dynacover](https://github.com/erikaheidi/dynacover-actions)
  - [Update CONTRIBUTORS](https://github.com/minicli/action-contributors) (winner of DEV GitHub Actions hackaton 2021)