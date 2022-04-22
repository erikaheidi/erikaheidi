---
title: Parsing DEV markdown within a Laravel Tailwind application
series: Getting Started with Laravel
published: true
description: In the fourth and last part of our livestream series "Getting Started with Laravel", we'll see how to parse the markdown format from DEV and exhibit posts within a Laravel + TailwindCSS application.
tags: php, laravel, tutorial, beginners
cover_image: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/a5ax0zi6hf3plef8bupt.png
---

In a [previous part of our series](https://dev.to/sourcegraph/creating-a-laravel-artisan-command-to-import-posts-from-dev-via-the-command-line-4iak), we saw how to create an Artisan command to import your latest DEV articles and save them as markdown files within the storage folder of your Laravel application. The only thing missing now is to exhibit these posts in the main page of the demo application.

In this post, you'll learn how to parse the DEV markdown using the [librarianphp/parsed](https://github.com/librarianphp/parsed) library, which enables support for liquid tags in addition to parsing the front matter to obtain metadata related to the post. You can also watch the [recap video on YouTube](https://youtu.be/DQrPfTPcVms).

{% youtube DQrPfTPcVms %}

![Final result - Headless DEV blog demo in Laravel + TailwindCSS](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3gkxscfgzrqweuvgkpd6.png)

_Disclaimer: this methodology works only with posts that are created with the classic markdown DEV editor, which uses a [Jekyll front matter](https://dev.to/p/editor_guide) within the markdown body of the article. If you use the rich editor, you'll need to generate a valid front matter based on the returning API information for each article, before saving them as .md files. This is not covered within this series._

## Preparation
Before moving along, make sure you have followed all steps in the [previous tutorials of the series](https://dev.to/sourcegraph/creating-a-new-laravel-application-with-sail-and-docker-no-php-required-4c2n) in order to bootstrap your Laravel application and development environment, to create the basic front-end view, and to create an Artisan command to import posts from DEV. This will require you to have Docker, Curl, and Git installed on your system.

This tutorial assumes that you have an alias to `./vendor/bin/sail` on the root of the application folder, so that you can run Sail with `./sail`. You can create such an alias with the following commands, executed from the application root folder:

```shell
ln -s ./vendor/bin/sail sail
chmod +x sail
```

If you haven't yet, bring your environment up with:

```shell
./vendor/bin/sail up -d
```
This command will start up your environment and keep it running in the background. Your Laravel application should now be available at `http://localhost` from your browser.

## 1. Installing Parsed via Composer

To parse the content imported from DEV, we'll use [librarianphp/parsed](https://github.com/librarianphp/parsed), a library based on [league/commonmark](https://packagist.org/packages/league/commonmark) that parses the metadata content from traditional DEV posts that use the Jekyll front matter, in addition to parsing liquid tags and allowing you to implement your own custom liquid tags. 

Run the following command to include `librarianphp/parsed` as a Composer dependency:

```shell
./sail composer require librarianphp/parsed
```

Once the dependencies are installed, you can move on to the next step.

## 2. Fetching the list of posts from local markdown files

If you followed along with all tutorials in this series, you should now have your DEV articles as `.md` files in the local application storage folder,  `storage/app`. Now you'll need to create a list with your most recent posts and make the list available to the front-end view so that you can exhibit your posts on the main application page.

We'll need to modify the `routes/web.php` file, since that is where you have your main route defined. Open this file in your code editor.

The following code uses the `glob()` function to loop through all files in the specified directory, which is defined with the help of the `storage_path()` helper function. This function will return the absolute path for directories inside your storage folder. When specifying the search pattern, we use a `/*.md` filter to make sure we skip any files that aren't markdown files. Then, we create a `Content` object by loading the contents from each file, and add that object to an array called `articles`. We then use the `krsort` method to order the array in reverse order, which will bring your latest articles first in the list. We also use the `array_slice()` function to keep a fixed number of results, defined by the variable `limit` which is set to `10` by default. Finally, we return the template view, passing in the list of articles so that it can be used in a loop.

You can replace the current content on your `routes/web.php` with the following:

```php
<?php

use Illuminate\Support\Facades\Route;
use Parsed\Content;
use Parsed\ContentParser;

/*
|--------------------------------------------------------------------------
| Web Routes
|--------------------------------------------------------------------------
|
| Here is where you can register web routes for your application. These
| routes are loaded by the RouteServiceProvider within a group which
| contains the "web" middleware group. Now create something great!
|
*/

Route::get('/', function () {
   $articles = [];
   $limit = 10;

   foreach (glob(storage_path('app') . '/*.md') as $file) {
       $article = new Content(file_get_contents($file));
       $article->parse(new ContentParser());

       $articles[] = $article;
   }

   krsort($articles);
   $articles = array_slice($articles, 0, $limit);
   return view('index', [
       'articles' => $articles
   ]);
});

```

Save the file when you're done.

## 3. Listing the posts on the application's main page

If you reload the application on your browser now, nothing has changed, since you still need to update your main front-end view to list the content obtained in the previous step. The content was passed along to the front-end view in an array named `articles`, so we'll need to loop through this array in order to show each article's summary.

Open the index view file at `resources/views/index.blade.php` on your code editor. Locate the main content area, where the example posts are statically defined. You can remove the static posts because you're going to replace them with a `foreach` Blade loop.

Each item in the `articles` array is an object of type `Parsed\Content`. This object is a wrapper around markdown content using the Jekyll front matter format to define metadata about the content, such as title, cover image, and tags. All items defined in the content's front matter are available through a method called `frontMatterGet()`. To check if a metadata information exists, you can use the method `frontMatterHas()`.

The following piece of Blade-compatible code loops through the `articles` array to exhibit a summary of each post. It checks whether a `cover_image` property is set within the frontmatter of the article. When this property is not present, it will use a random image from [Lorem Picsum](https://picsum.com) to illustrate the post. For simplicity, the code assumes that `title`,  `description`, and `tags` are always set:

```php
   @foreach ($articles as $article)
       <div class="mb-10">
           <img src=@if ($article->frontMatterHas('cover_image'))
                      "{{ $article->frontMatterGet('cover_image') }}"
                    @else "https://picsum.photos/1000/420"
                    @endif alt="Post header image" class="rounded-lg my-4"
            />
           <h1 class="text-4xl mb-4">{{ $article->frontMatterGet('title') }}</h1>
           <p class="text-md">{{ $article->frontMatterGet('description') }}</p>
           <p>Posted in: <span class="text-gray-700 font-bold">{{ $article->frontMatterGet('tags') }}</span></p>
       </div>
   @endforeach

```

Here is the [fully updated `index.blade.php` file](https://sourcegraph.com/github.com/sourcegraph-community/laravel-dev-blog/-/blob/resources/views/index.blade.php) for your reference:

```php
<!doctype html>

<html lang="en">
<head>
   <meta charset="utf-8">
   <meta name="viewport" content="width=device-width, initial-scale=1.0" />

   <title>My DEV Blog</title>
   <meta name="description" content="My DEV Blog">
   <meta name="author" content="Sourcegraph">

   <meta property="og:title" content="A headless blog in Laravel">
   <meta property="og:type" content="website">
   <meta property="og:url" content="https://github.com/sourcegraph-community/laravel-dev-blog">
   <meta property="og:description" content="A demo Laravel blog">

   <link href="{{ asset('css/app.css') }}" rel="stylesheet">

</head>

<body class="bg-gradient-to-r from-blue-600 via-pink-500 to-purple-900">

   <div class="container text-white mx-auto mt-6">
       <div class="grid grid-cols-3 gap-4">
           <div>
               <h1 class="text-3xl">My DEV Blog: latest posts</h1>
           </div>
           <div class="col-span-2 text-right">
               menu
           </div>
       </div>
   </div>

   <div class="container mx-auto px-10 py-10 bg-gray-100 my-10 text-gray-600 rounded-md shadow-md">
       <div class="grid grid-cols-3 gap-8">
           <div class="col-span-2">
               @foreach ($articles as $article)
                   <div class="mb-10">
                       <img src=@if ($article->frontMatterHas('cover_image'))
                                   "{{ $article->frontMatterGet('cover_image') }}"
                                @else "https://picsum.photos/1000/420"
                                @endif alt="Post header image" class="rounded-lg my-4"
                        />
                       <h1 class="text-4xl mb-4">{{ $article->frontMatterGet('title') }}</h1>
                       <p class="text-md">{{ $article->frontMatterGet('description') }}</p>
                       <p>Posted in: <span class="text-gray-700 font-bold">{{ $article->frontMatterGet('tags') }}</span></p>
                   </div>
               @endforeach
           </div>

           <div>
               <img src="https://picsum.photos/100" class="rounded-full mx-auto p-4" alt="avatar"/>
               <p class="text-gray-700 text-xl mb-10">Hi, I'm a demo Laravel application built with Tailwind CSS, pulling content from DEV.to.
                   You can find my code <a class="text-purple-800 font-bold" href="https://github.com/sourcegraph-community/laravel-dev-blog" title="Laravel DEV blog demo on GitHub">here</a> and you can learn more about me <a class="text-purple-800 font-bold" href="https://dev.to/sourcegraph/creating-a-new-laravel-application-with-sail-and-docker-no-php-required-4c2n" title="Getting started with Laravel tutorial series">here</a>.</p>

               <h2 class="text-gray-400 text-2xl">Links</h2>

               <ul class="py-2">
                   <li class="py-2"><a class="px-1 py-2 bg-purple-300" href="https://github.com/sourcegraph-community/laravel-dev-blog">Demo Laravel DEV Blog on GitHub</a></li>
                   <li class="py-2"><a class="px-1 py-2 bg-pink-400" href="https://dev.to/sourcegraph/creating-a-new-laravel-application-with-sail-and-docker-no-php-required-4c2n">Getting started with Laravel on DEV</a></li>
                   <li class="py-2"><a class="px-1 py-2 bg-purple-300" href="https://laravel.com/docs/8.x">Laravel Documentation</a></li>
                   <li class="py-2"><a class="px-1 py-2 bg-pink-400"  href="https://tailwindcss.com/docs/installation">TailwindCSS Documentation</a></li>
                   <li class="py-2"><a class="px-1 py-2 bg-purple-300" href="https://php.net">Official PHP Documentation</a></li>
                   <li class="py-2"><a class="px-1 py-2 bg-pink-400" href="https://sourcegraph.com">Sourcegraph Code Search</a></li>
               </ul>
           </div>
       </div>
   </div>

</body>
</html>

```


The sidebar was also slightly updated with a few resourceful links to improve the overall appearance of the page. Once you save the template and reload the application on your browser, you should see a page similar to this, but showcasing your own DEV articles:

![Animated gif showing the final result of the demo headless DEV blog built with Laravel and TailwindCSS](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/11n8mjs0v632vaavymht.gif)


## Conclusion

In this article, which is the fourth and last part of our _Getting started with Laravel_ series, we've seen how to create a collection of DEV.to markdown posts and parse them using the [librarianphp/parsed](https://github.com/librarianphp/parsed) library.

The demo application is open source and fully [available on GitHub](https://github.com/sourcegraph-community/laravel-dev-blog). Feel free to clone or fork the project to create a custom version that better suits your needs.

### Helpful Resources

Check the following list with useful resources to learn more about the open source projects and libraries used in this series:

- [Laravel documentation](https://laravel.com/docs/8.x/installation)
- [TailwindCSS documentation](https://tailwindcss.com/docs/installation)
- [Curly](https://docs.minicli.dev/en/latest/xtras/extending-minicli/#miniclicurly)
- [Parsed](https://github.com/librarianphp/parsed)
- [DEV.to front matter and markdown reference](https://dev.to/p/editor_guide)
- [DEV.to API reference](https://developers.forem.com/api#tag/articles)
- [Search code examples using the DEV API to fetch articles](https://sourcegraph.com/search?q=context:global+https://dev.to/api/articles%3Fusername%3D&patternType=literal)

---

You can follow [Sourcegraph on Twitch](https://twitch.tv/sourcegraph) to be notified when we go live. Follow our [YouTube channel](https://youtube.com/c/sourcegraph) for the full recap videos of our livestreams.