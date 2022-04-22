---
title: Setting up TailwindCSS 3.0 on a Laravel project
series: Getting Started with Laravel
published: true
description: In the second part of our livestream series "Getting Started with Laravel", we'll see how to install and set up TailwindCSS within a Laravel project, and how to build a basic blog layout with Tailwind.
tags: css, php, laravel, tutorial
cover_image: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/s74wvvm1ooyodbqldoe4.png
---

In the [first part of our series](https://dev.to/sourcegraph/creating-a-new-laravel-application-with-sail-and-docker-no-php-required-4c2n), we saw how to bootstrap a new Laravel application with [Sail](https://laravel.com/docs/8.x/sail) and Docker. This allows you to develop a Laravel application without having to set up a PHP development environment on your system.

With the base application up and running, the second part of our livestreaming series was focused on setting up a basic blog layout using TailwindCSS. In this post, you'll see a recap with all steps executed during the live session, so that you can reproduce them at your own pace.

You can also watch the [video recap on YouTube](https://www.youtube.com/watch?v=kb73FgJBY6k).

{% youtube kb73FgJBY6k %}

## Preparation
Before moving along, make sure you have followed all steps in the [first tutorial of the series](https://dev.to/sourcegraph/creating-a-new-laravel-application-with-sail-and-docker-no-php-required-4c2n) in order to bootstrap your Laravel application and development environment. This will require you to have Docker, Curl, and Git installed on your system.

If you haven't yet, bring your environment up with:

```shell
./vendor/bin/sail up -d
```
This command will start up your environment and keep it running in the background. Your Laravel application should now be available at `http://localhost`. Open this URL on your browser and you'll see a page like this:

![Laravel welcome page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/uoci8k1kpbpbcr12rur7.png)

For convenience, you may want to create an alias to execute Sail from the application's root directory:

```shell
ln -s ./vendor/bin/sail sail
chmod +x sail
```

Now you can run Sail like this:

```shell
./sail artisan
```

This command will run Artisan on the application container.

## 1. Installing and configuring TailwindCSS 3 with Laravel

Start by installing the frontend dependencies via NPM. The `npm` command is available as a shortcut with Laravel Sail:

```shell
./sail npm install
```
Next, install TailwindCSS with:

```shell
./sail npm install -D tailwindcss postcss autoprefixer
```

Then, run the following command to generate your Tailwind configuration file:

```shell
./sail npx tailwindcss init
```

Now you'll need to tell Laravel that you want to compile Tailwind resources using Laravel Mix. Open the `webpack.mix.js` file (located at the root of your application) and include `tailwindcss`  as a postCss plugin. This is how your `mix.js` definition should look like when you're finished:

_webpack.mix.js_
```js
mix.js('resources/js/app.js', 'public/js')
    .postCss('resources/css/app.css', 'public/css', [
        require("tailwindcss"),
    ]);
```

Next, you should update your `tailwind.config.js` file to include the paths to all your template resources. The following example will include all blade templates and JS files in your `resources` folder:

_tailwind.config.js_
```js
module.exports = {
  content: [
    "./resources/**/*.blade.php",
    "./resources/**/*.js",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

The next step is to import all `@tailwindcss` component layers into your application's CSS. Open the `resources/css/app.css` file and include the following content:

_resources/css/app.css_
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

The configuration is done. You can now run the command that will watch for changes in your frontend resources and build assets in real time while you develop your application:

```shell
./sail npm run watch
```

This command will block your terminal. You can stop it at any time with `CTRL`+`C`.

## 2. Creating a basic blog template

Now that your setup is complete, you can start building the frontend of your application. For this demo, we're building a basic blog template with a main column and a sidebar on the right.

A good idea to get started is to search for a [basic HTML5 boilerplate code / template](https://www.sitepoint.com/a-basic-html5-template/) and build from there.

After some experimentation, we built the following template during the [livestreaming session](https://twitch.tv/sourcegraph). Save this content to a file named `index.blade.php` in your `resources/views` folder:

_resources/views/index.blade.php_
```html
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
                <h1 class="text-3xl">My DEV Blog</h1>
            </div>
            <div class="col-span-2 text-right">
                menu
            </div>
        </div>
    </div>

    <div class="container mx-auto px-6 py-10 bg-gray-100 my-10 text-gray-600 rounded-md shadow-md">
        <div class="grid grid-cols-4 gap-4">
            <div class="col-span-3">

                <div class="mb-10">
                    <img src="https://picsum.photos/1000/420" alt="Post header image" class="rounded-lg my-4"/>
                    <h1 class="text-3xl">This is the title of my first post</h1>

                    <p>This is the description bnablab alba lbal ablabla blab al.</p>
                </div>

                <div class="my-10">
                    <img src="https://picsum.photos/1000/420?sjdj" alt="Post header image" class="rounded-lg my-4"/>
                    <h1 class="text-3xl">This is the title of my second post</h1>

                    <p>This is the description bnablab alba lbal ablabla blab al.</p>
                </div>

            </div>

            <div>
                <h2 class="text-gray-400 text-xl">Categories</h2>

                <ul>
                    <li><a href="#">Linux</a></li>
                    <li><a href="#">PHP</a></li>
                    <li><a href="#">Development</a></li>
                    <li><a href="#">Devops</a></li>
                    <li><a href="#">Career</a></li>
                </ul>
            </div>
        </div>
    </div>

</body>
</html>
```

As you can see from the code, apart from the `asset` call, the rest is purely HTML and CSS styled with TailwindCSS descriptive classes.

After saving the template, open your `routes/web.php` file and update your main route definition so that it renders your new template instead of the default Laravel welcome page. This is how the file should look like once you're done:

_routes/web.php_
```php
Route::get('/', function () {
    return view('index');
});
```

Save the file. If you reload your browser now, you should see a page like this:

![screenshot application with basic blog template built with tailwind css](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/54zmfaypzgtry344muso.png)

In a later part of this series, we'll turn this skeleton into an actual dynamic template, using more advanced Blade features.

## Conclusion

Installing TailwindCSS 3.0 within a Laravel project requires a few configuration steps, but once you get the build command running, you can greatly speed up your frontend development process by making use of the advanced features of Tailwind 3, including just-in-time processing for super fast build times, as well as many other [features included in the newest version](https://tailwindcss.com/blog/tailwindcss-v3).

In the next part of this series, we'll move to the backend and create an Artisan command to import posts from a DEV user profile.

You can follow [Sourcegraph on Twitch](https://twitch.tv/sourcegraph) to be notified when we go live.
