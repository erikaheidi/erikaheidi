---
title: Creating a Laravel Artisan command to import posts from DEV via the command line
series: Getting Started with Laravel
published: true
description: In the third part of our livestream series "Getting Started with Laravel", we'll see how to create an Artisan command to import posts from DEV and save them as local markdown files in your Laravel application storage directory.
tags: php, laravel, tutorial, beginners
cover_image: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/caupf2wpg52eeeh0d2tj.png
---

In a [previous part of our series](https://dev.to/sourcegraph/setting-up-tailwindcss-30-on-a-laravel-project-1cb8), we saw how to install and configure TailwindCSS 3.0 within a Laravel project and how to set up a basic blog layout for our demo application. With that base template in place, the fourth part of the series is focused on the back-end side of the application.

In this post, you'll learn how to create an Artisan command in Laravel to import posts from DEV using their API and how to save the content as markdown files in your local application storage folder. This recap is also available as a video that you can [watch on YouTube](https://www.youtube.com/watch?v=Y0WojCCI60I).

{% youtube Y0WojCCI60I %}


## Preparation
Before moving along, make sure you have followed all steps in the [first tutorial of the series](https://dev.to/sourcegraph/creating-a-new-laravel-application-with-sail-and-docker-no-php-required-4c2n) in order to bootstrap your Laravel application and development environment. This will require you to have Docker, Curl, and Git installed on your system.

If you haven't yet, bring your environment up with:

```shell
./vendor/bin/sail up -d
```
This command will start up your environment and keep it running in the background. Your Laravel application should now be available at `http://localhost` from your browser.

This tutorial assumes that you have an alias to `./vendor/bin/sail` on the root of the application folder, so that you can run Sail with `./sail`. You can create such an alias with the following commands, executed from the application root folder:

```shell
ln -s ./vendor/bin/sail sail
chmod +x sail
```

## 1. Creating a new Artisan command

Start by creating a new Artisan command using the `make:command` utility. This will create a new file with boilerplate code for an Artisan command, saved at `app/Console/Commands/`.

```shell
./sail artisan make:command ImportPosts
```

Open the newly generated file `app/Console/Commands/ImportPosts.php` in your code editor, and you'll see content like this:

```php
<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;

class ImportPosts extends Command
{
   /**
    * The name and signature of the console command.
    *
    * @var string
    */
   protected $signature = 'command:name';

   /**
    * The console command description.
    *
    * @var string
    */
   protected $description = 'Command description';

   /**
    * Create a new command instance.
    *
    * @return void
    */
   public function __construct()
   {
       parent::__construct();
   }

   /**
    * Execute the console command.
    *
    * @return int
    */
   public function handle()
   {
       return 0;
   }
}

```

Start by changing the `command:name` for the command signature you'd like to use. In this demo we'll use `import:dev`.

The `handle()` method is where the command action happens; this is what will be executed when you run `artisan import:dev` from the command line.

## 2. Obtaining latest posts from the DEV API

Luckily for us, DEV has an [API](https://developers.forem.com/api#operation/getArticles) that we can query to obtain the latest posts from a user, without the need to use an authorization token, since that is all public data. The endpoint we want to query is:

```
https://dev.to/api/articles?username=DEV_USERNAME
```

To make the request, we'll  use [minicli/curly](https://docs.minicli.dev/en/latest/xtras/extending-minicli/#miniclicurly), which is a small PHP library based on `curl`. Require that dependency with:

```shell
./sail composer require minicli/curly
```

Once you have the dependency installed, add a `use` directive at the top of the file so that you can easily reference `Minicli\Curly\Client` in your code. We'll also use Laravel's `Carbon/Carbon` library to parse the article's publish date, so you should include that within your `use` directives as well. Lastly, include the `Illuminate\Support\Facades\Storage` class with another `use` directive, since we'll be using this facade class to check and save files within the local file storage system. 

This is how the first lines of your `app/Console/Commands/ImportDev.php` file should look like:

```php
<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;
use Minicli\Curly\Client;
use Illuminate\Support\Facades\Storage;
use Carbon\Carbon;
```

Next, update your `handle()` method with the following code:

```php
public function handle()
{
   $crawler = new Client();
   $headers = [
       'User-Agent: curly 0.1',
   ];

   $articles_response = $crawler->get('https://dev.to/api/articles?username=DEV_USERNAME&per_page=10', $headers);

   if ($articles_response['code'] !== 200) {
       $this->error('Error while contacting the dev.to API.');

       return Command::FAILURE;
   }

   $articles = json_decode($articles_response['body'], true);
   foreach ($articles as $article) {
       $article_slug = $article['slug'];
       $published = new Carbon($article['published_at']);
       $filename = $published->format('YmdH') . '-' . $article_slug .'.md';

       if (Storage::disk('local')->exists($filename)) {
           continue;
       }

       $endpoint = sprintf('https://dev.to/api/articles/%s', $article['id']);
       $article_query = $crawler->get($endpoint, $headers);

       if ($article_query['code'] == 200) {
           $article_full = json_decode($article_query['body'], true);

           Storage::disk('local')->put($filename, $article_full['body_markdown']);

           $this->info("Article saved: " . $filename);
       }
   }

   return Command::SUCCESS;
}
```

This code will make a request to the DEV API to obtain a list with the latest **10** articles from a user. To get the full contents of the article, including the article's metadata defined in the front matter, we'll make a second request once we check that the article hasn't been imported before. Then, we'll use the `Storage` utility to save the `body_markdown` of each article as a markdown file in the application's storage folder. The files are saved using a combination of the article's publish date and the article slug as filename, with the `.md` extension.

Don't forget to change **DEV_USERNAME** to your own username on DEV.to.

Save the file when you're finished.

## 3. Test the command to import posts from DEV

With the command ready, you can now run it with:

```shell
./sail artisan import:dev
```
```
Article saved: 2021121515-setting-up-tailwindcss-30-on-a-laravel-project-1cb8.md
Article saved: 2021121013-creating-a-new-laravel-application-with-sail-and-docker-no-php-required-4c2n.md
Article saved: 2021120318-how-to-set-up-elgatos-stream-deck-on-ubuntu-linux-2110-pdh.md
Article saved: 2021112011-dynamic-twitter-header-images-with-dynacover-github-action-35nb.md
Article saved: 2021111818-how-to-build-github-actions-in-php-with-minicli-and-docker-1k6m.md
Article saved: 2021111618-automatically-update-your-contributors-file-with-this-github-action-workflow-d98.md
Article saved: 2021102717-10-sourcegraph-search-tricks-for-open-source-contributors-and-maintainers-44n9.md
Article saved: 2021092715-3d-design-creating-printable-solid-shapes-from-svg-files-with-inkscape-and-freecad-266e.md
Article saved: 2021090615-3d-design-with-freecad-part-2-working-with-sketcher-part-design-workbenches-3leo.md
Article saved: 2021090614-an-introduction-to-3d-design-with-freecad-part-1-navigation-3gjo.md

```
You should see the titles of your latest posts showing up in the output of the command. Once the command is finished running, have a look at your `storage/app` folder and you'll see your posts as individual markdown files named after each post's publish date and slug.

```shell
ls storage/app
```

```
2021090614-an-introduction-to-3d-design-with-freecad-part-1-navigation-3gjo.md
2021090615-3d-design-with-freecad-part-2-working-with-sketcher-part-design-workbenches-3leo.md
2021092715-3d-design-creating-printable-solid-shapes-from-svg-files-with-inkscape-and-freecad-266e.md
2021102717-10-sourcegraph-search-tricks-for-open-source-contributors-and-maintainers-44n9.md
2021111618-automatically-update-your-contributors-file-with-this-github-action-workflow-d98.md
2021111818-how-to-build-github-actions-in-php-with-minicli-and-docker-1k6m.md
2021112011-dynamic-twitter-header-images-with-dynacover-github-action-35nb.md
2021120318-how-to-set-up-elgatos-stream-deck-on-ubuntu-linux-2110-pdh.md
2021121013-creating-a-new-laravel-application-with-sail-and-docker-no-php-required-4c2n.md
2021121515-setting-up-tailwindcss-30-on-a-laravel-project-1cb8.md
public
```

## Conclusion

In this article, which is part 3 out of 4 in our _Getting started with Laravel_ series, we've seen how to create an Artisan command and how to import posts from a user of DEV.to using the DEV API and the `minicli/curly` library. The command uses the underlying storage system from Laravel to store the posts as individual markdown files in your `storage/app` folder.

In the next part of this series, we'll finish this demo application by updating the index controller and listing the posts on the main application's page.

You can follow [Sourcegraph on Twitch](https://twitch.tv/sourcegraph) to be notified when we go live. Follow our [YouTube channel](https://youtube.com/c/sourcegraph) for the full recap videos of our livestreams.
