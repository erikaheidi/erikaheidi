---
title: Creating an Automated Documentation Pipeline in PHP with Autodocs and GitHub Actions
published: true
description: In this tutorial, learn how to create an automated documentation pipeline in PHP with Autodocs, Minicli, and GitHub Actions
tags: tutorial, php, documentation, automation
cover_image: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/9vbfuul6vi7q0w39fmoy.png
# Use a ratio of 100:42 for best results.
# published_at: 2024-01-10 16:08 +0000
---

[Autodocs](https://github.com/erikaheidi/autodocs) is a PHP library designed to facilitate building highly customizable automated docs based on markdown templates. Combined with a [Minicli](https://docs.minicli.dev) application, it provides a layer of abstraction and structure on top of which you can create your own documentation factory.

I created this project to help me keep up with the documentation needs for [Chainguard Images](https://edu.chainguard.dev/chainguard/chainguard-images/reference), which now helps keeping more than a thousand doc pages up-to-date in a GitHub-based workflow with nightly runs.

Pages are defined as classes that follow a known interface. Data sources can be autoloaded as JSON cache files, a design that facilitates distributed setups where data comes from varied sources. This distributed approach facilitates integrating Autodocs with existing workflows and pipelines, especially when using GitHub Actions.

In this tutorial we'll create a demo Autodocs application to generate personal GitHub READMEs. If you'd prefer to skip the tutorial and go straight to the code, you can check [the demo repository on GitHub](https://github.com/erikaheidi/autodocs-demo).

## 1. Bootstrapping the Demo App

Let's start by creating the demo application. We're using the [Minicli application template](https://docs.minicli.dev/en/latest/getting_started/creating-apps/) to provide more resources and organization when building out the app.

```shell
composer create-project minicli/minicli autodocs-demo
```
Once Composer is finished installing the new application dependencies, you should be able to enter the directory and run a quick test:

```shell
cd autodocs-demo
./minicli
```

You should get output like this, indicating that the Minicli application runs as expected:

```shell
❯ ./minicli


███╗   ███╗██╗███╗   ██╗██╗ ██████╗██╗     ██╗
████╗ ████║██║████╗  ██║██║██╔════╝██║     ██║
██╔████╔██║██║██╔██╗ ██║██║██║     ██║     ██║
██║╚██╔╝██║██║██║╚██╗██║██║██║     ██║     ██║
██║ ╚═╝ ██║██║██║ ╚████║██║╚██████╗███████╗██║
╚═╝     ╚═╝╚═╝╚═╝  ╚═══╝╚═╝ ╚═════╝╚══════╝╚═╝

Minimalist, dependency-free framework for building CLI-centric PHP applications

```

Now you can rename the executable to **autodocs** or a different name if you'd like:

```shell
mv minicli autodocs
```

Next, we want to include the **autodocs** library as a dependency within Composer:

```shell
composer require erikaheidi/autodocs
```

You now have all requirements set up and can proceed to configuring Autodocs.

## 2. Configuring Autodocs Options
Create a new configuration file to hold Autodocs settings. This should go in the `config` folder of your Minicli applicaiton:

```shell
touch config/autodocs.php
```

Autodocs expects an `autodocs` configuration entry with a few required settings:

- `templates_dir`: where to find `.tpl` files that may be used as page templates. The use of templates is not mandatory, but it can be helpful to facilitate content layout updates without having to change page classes.
- `cache_dir`: `.json` files in this directory will be automatically registered as a **DataFeed** object within the main Autodocs service.
- `output`: location where to save built pages.
- `pages`: an array with classes that should be registered as Reference Pages. These are the documentation pages that will be built at each run.
- `storage`: (Optional) a class that implements `StorageInterface`, able to handle filesystem access to save and retrieve files. The default `FileStorage` is used when no option is defined.

For our demo, let's create a `storage` directory for templates, content, and cache:

```shell
mkdir -p storage/cache storage/templates storage/content
```
Next, open the file we created previously, `config/autodocs.php`, using your PHP editor of choice. Copy the following configuration to your file:

```php
<?php

declare(strict_types=1);

use Autodocs\Page\ExamplePage;

return [
    'autodocs' => [
        // Pages to Build
        'pages' => [
            ExamplePage::class
        ],
        // Build Output Folder
        'output' => envconfig('AUTODOCS_OUTPUT', __DIR__.'/../storage/content'),
        // Cache Folder - where to look for cache json files
        'cache_dir' => envconfig('AUTODOCS_CACHE', __DIR__.'/../storage/cache'),
        // Templates directory
        'templates_dir' => envconfig('AUTODOCS_TEMPLATES', __DIR__.'/../storage/templates')
    ]
];

```
Save the file. Notice we registered an `ExamplePage` that is included for tests. We'll update this later on to include our custom pages.

## 3. Registering the Autodocs Service
With the config in place, you can now register the Autodocs service within the Minicli application. 

Open the `config/services.php` file and make sure it looks like this, with the line including the Autodocs service class registered as `autodocs`:

```php
<?php

declare(strict_types=1);

use Autodocs\Service\AutodocsService;

return [
    /****************************************************************************
     * Application Services
     * --------------------------------------------------------------------------
     *
     * The services to be loaded for your application.
     *****************************************************************************/

    'services' => [
        'autodocs' => AutodocsService::class,
    ],
];

```

Save the file. You can now start working on your build command, which we'll see in the next step.

## 4. Creating a Build Command
We'll now create a command to build our custom automated docs. This will build all pages defined in the `pages` autodocs config, which for now should be set to just include the ExamplePage. Once this simple example page builds, we can start working on our actual doc pages.

In Minicli, [creating a command](https://docs.minicli.dev/en/latest/getting_started/creating-controllers/) is just a matter of setting up some classes and directories in a pre-defined format. Start by creating a directory for your commands inside `app/Commands`:

```shell
mkdir app/Command/Build
```
Next, we'll create the command controller that will handle building the docs.

Create a new file called `DefaultController.php` inside the newly created `app/Command/Build` directory. In this class, we'll set up a method that will be called whenever we run `./autodocs build`.

```shell
touch app/Command/Build/DefaultController.php
```

Open this file on your PHP editor of choice. You can start by copying the following skeleton for your controller class:

```php
<?php

declare(strict_types=1);

namespace App\Command\Build;

use Minicli\Command\CommandController;

class DefaultController extends CommandController
{
    public function handle(): void
    {
        //build command action
    }
}

```

From the `handle` method, we can access the application, the configuration container, and all registered services. The logic here will depend on your documentation needs. 

To build the included `ExamplePage`, update your `handle` method to the following code:

```php
    public function handle(): void
    {
        /** @var AutodocsService $autodocs */
        $autodocs = $this->getApp()->autodocs;

        $this->info("Starting Build...");
        $autodocs->buildPages($this->getParam('pages') ?? "all");
        $this->info("Build finished. Output saved to " . $autodocs->config['output']);

    }
```

This will build the example page and save it in the location defined by `output` in Autodocs config.

### Building the Docs

Now you should be able to run the build command with:

```shell
./autodocs build
```

You should get output like the following:

```
❯ ./autodocs build

Starting Build...

Build finished. Output saved to /home/erika/Projects/autodocs-demo/config/../storage/content

```

Check the output folder. You should find an `example.md` page there.

## 5. Creating Documentation Pages

In the previous step we created a build command and used it to build the included **ExamplePage**, which is a really simple page that basically outputs a title and a description. After having a look at how pages are defined, we'll create a custom page for our demo app.

### 5.1 Reference Pages
Reference Pages are models that represent a document and how it should be built. They should extend from the `ReferencePage` class (or implement `ReferencePageInterface`) and implement the following methods:

- `loadData` - this method receives an optional `$parameters` array and should load any additional data required to build the page.
- `getName` - must return a unique identifier that can be used later on to build only this type of page.
- `getContent` - the actual content that will be saved.
- `getSavePath` - the path where to save this file, to be used by the page builder.

It is easier to see how it works in practice, so this is what the `ExamplePage` looks like:

```php
<?php

declare(strict_types=1);

namespace Autodocs\Page;

use Autodocs\DataFeed\JsonDataFeed;

class ExamplePage extends ReferencePage
{
    public JsonDataFeed $dataFeed;
    public function loadData(array $parameters = []): void
    {
        $this->dataFeed = new JsonDataFeed();
        $this->dataFeed->loadFromArray([
            'title' => 'example',
            'description' => 'description'
        ]);
    }

    public function getName(): string
    {
        return "example";
    }

    public function getContent(): string
    {
        return $this->dataFeed->json['title'].' - '.$this->dataFeed->json['description'];
    }

    public function getSavePath(): string
    {
        return 'example.md';
    }
}

```

This example creates a `JsonDataFeed` and loads it with static data. Instead, you could use JSON data files and have them automatically loaded by Autodocs - you just need to place the files in the path defined by the `cache` configuration option.

Next, we'll create our own custom documentation page using a simple JSON data source. For this demo, we'll generate a markdown page that can be used as your personal README on GitHub (the one that is rendered in your profile).

### 5.1 Creating a `ReadmePage`

The following [ReadmePage](https://github.com/erikaheidi/autodocs-demo/blob/main/app/ReadmePage.php) class loads data from a JsonDataFeed that was pre-loaded into the application container. We'll set that JSON file in the next step.

The `getContent` method uses [Stencil](https://docs.minicli.dev/en/latest/the_ecosystem/stencil/) to render a template called `readme.tpl` with values obtained from the JSON data source.

```php
<?php

declare(strict_types=1);

namespace App;

use Autodocs\DataFeed\JsonDataFeed;
use Autodocs\Page\ReferencePage;

class ReadmePage extends ReferencePage
{
    public JsonDataFeed $dataFeed;
    public function loadData(array $parameters = []): void
    {
        $this->dataFeed = $this->autodocs->getDataFeed('profile.json');
    }

    public function getName(): string
    {
        return "readme";
    }

    public function getContent(): string
    {
        return $this->autodocs->stencil->applyTemplate('readme', [
            'title' => $this->dataFeed->json['user'],
            'about' => $this->dataFeed->json['bio'],
            'projects_list' => $this->getProjects(),
        ]);
    }

    public function getProjects(): string
    {
        $content = "";
        foreach ($this->dataFeed->json['projects'] as $project => $info) {
            $content .= "- [{$project}]({$info['link']}): {$info['description']}\n"; // returns Markdown list
        }

        return $content;
    }

    public function getSavePath(): string
    {
        return 'README.md';
    }
}
```
When you're ready, copy these contents into your own `app/ReadmePage.php` and save the file. But don't run the build just yet; we still need to set up the JSON data source and the template file used by the `ReadmePage` class.

### 5.2 Setting up the `profile.json` Data Source
Next, we'll create a simple JSON file with some data to use when building the Readme page.

Create a new file called `profile.json` in your `cache_path`:

```shell
touch storage/cache/profile.json
```
Open the file in your editor of choice. Copy the following JSON skeleton and update the data with your own info:

```json
{
  "user": "Erika Heidi",
  "bio": "Software and Documentation Engineer",
  "projects": {
    "minicli/minicli": {
      "description": "CLI framework for PHP",
      "link": "https://docs.minicli.dev"
     },
    "erikaheidi/autodocs": {
      "description": "Tiny framework for automating documentation",
      "link": "https://github.com/erikaheidi/autodocs/wiki"
    }
  }
}

```
Save the file when you're finished.

### 5.3 Setting Up the `readme.tpl` Template

Next, create the template file:

```shell
touch storage/templates/readme.tpl
```
Copy the following content to your template:

```tpl
## {{ title }}

{{ about }}

## My Projects

{{ projects_list }}

```
Save the file when you're done. 

### 5.4 Registering the `ReadmePage` within Autodocs Configuration

Now you just need to register your custom page within Autodocs configuration. Edit the `config/autodocs.php` file, and change the `pages` entry to include the new page. You can also remove the `ExamplePage` while you're at it. It should look like this when you're finished:

```php

    'autodocs' => [
        // Pages to Build
        'pages' => [
            ReadmePage::class
        ],
```
Save the file when you're finished.

### 5.5 Building the `ReadmePage`
With the new page registered within the documentation, you can now proceed to build the `ReadmePage`:

```shell
./autodocs build
```
You should get output indicating that the page was successfully built. If should now have a `readme.md` file in your output folder with contents similar to this:

```markdown
## Erika Heidi

Software and Documentation Engineer

## My PHP Projects

- [minicli/minicli](https://docs.minicli.dev): CLI framework for PHP
- [erikaheidi/autodocs](https://github.com/erikaheidi/autodocs/wiki): Tiny framework for automating documentation
```

## 6. Running the Demo with GitHub Actions (Optional)

We'll now create a workflow to run this demo on GitHub Actions. This is optional and requires you to set up your own repository with a copy of the project. You can also [fork the original demo](https://github.com/erikaheidi/autodocs-demo) to your GitHub account and change the JSON data file to have your own info. Once you have your repository set up, go to `Settings` -> `Actions` -> `General`, scroll to the bottom of the page where you'll find the `Workflow permissions` section. 

![enable workflows to create pull requests](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/2xza58bo7aos36ge7tcd.png)

Then, enable "Read and Write permissions" and "Allow GitHub Actions to send Pull requests", so that the workflow can make changes and create a PR with the generated doc(s).

Our workflow will:

- set up some environment variables that will overwrite default configuration values for Autodocs,
- check out the repository,
- install Composer dependencies using cache when available,
- build the docs,
- and send a pull request only when changes are detected.


```yaml
name: Build Docs

on:
  workflow_dispatch:

env:
  AUTODOCS_OUTPUT: "${{ github.workspace }}"
  AUTODOCS_CACHE: "${{ github.workspace }}/storage/cache"
  AUTODOCS_TEMPLATES: "${{ github.workspace }}/storage/templates"
jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3

    - name: Cache Composer packages
      id: composer-cache
      uses: actions/cache@v2
      with:
        path: vendor
        key: ${{ runner.os }}-php-${{ hashFiles('**/composer.lock') }}
        restore-keys: |
          ${{ runner.os }}-php-

    # Install Dependencies
    - name: Install dependencies
      run: composer install --prefer-dist --no-progress

    # Build Docs
    - name: Run Autodocs
      run: ./autodocs build

    # Send a Pull Request
    - name: Create a PR
      uses: peter-evans/create-pull-request@v5
      id: cpr
      with:
        path: "${{ github.workspace }}"
        commit-message: Update content
        title: "[AutoDocs] Updated README"
        body: "README auto-update"
        labels: |
          documentation
          automated

```

You can save this file to `.github/workflows/autodocs.yaml`. Don't forget to commit and push it to your repository. Once the file is there, go to `Actions` and select the `Build Docs` action on the left. This workflow is not set to run on schedule, instead it uses the `workflow_dispatcher` trigger which can only be triggered from the repository page.

Click on the "Run Workflow" button on the right, then confirm to run the workflow for the first time. If the build passes as it should, you'll get a new pull request on your repository:

![Autodocs pull request](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/0b9ltxtdp379oox94q5i.png)

Based on this simplified demo, you can elaborate your workflow to auto-generate your `profile.json` data and have your README always fresh. Maybe pull your latest posts from DEV or a blog feed? Show off your favorite projects, or highlight your sponsors? The only limit is your imagination :)

## Final Considerations
Documentation is a living organism, it changes and evolves as projects grow. After quite a few years working in the field of technical writing and documentation, I believe good documentation needs flexibility, context, and human input. These are hard to combine in a one-size-fits-all software to magically create docs.

Autodocs doesn't magically generate docs for you, that was never the intention. Instead, it provides a very thin layer of abstractions to give you enough structure to engineer your super custom docs and also to scale up your documentation using a distributed approach to data sourcing.

If you want to see a more complex implementation to have an idea of what Autodocs is capable, check [Chainguard's Images Autodocs](https://github.com/chainguard-dev/images-autodocs) project on GitHub.