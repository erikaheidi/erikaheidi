---
title: How to Dynamically Update Twitter Cover Image to Show Latest Followers Using PHP GD and TwitterOAuth
published: true
description: In this guide you'll learn how you can create a CLI script to dynamically update your Twitter cover image to show your latest Twitter followers, using PHP and the PHP-GD library.
tags: 
cover_image: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/e4vudyzwu51aeqdsx386.png
---

A week ago, I came across this tweet that made me so intrigued:

{% tweet 1397580868747620353 %}

It made me remember a couple other times in my career where I was very excited and actually almost obsessed to figure out how something was made. The first time (long ago) that happened was when I saw this "magazine cover tool" where you uploaded a photo and generated a magazine cover. That led me to a whole world of discoveries and my first successful project that allowed me to leave the job I had at the time to live off Google Adsense. Yes, it was a fake magazine cover generator online :) made with PHP and GD.

The other occasion when I felt as motivated to find out how they did something was when I found this tool to keep track of who unfollowed you on Twitter. Again, that led me to a whole world of discoveries, open source contributions, and lots of digging in the Twitter API.

So when I saw that tweet from [Tony](https://twitter.com/tdinh_me), I realized this would be basically the cover generator + Twitter API tinkering combined. I got very excited to implement it! Took about a week, couple hours each day, because I had small chunks of code here and there that I could reuse and repurpose. 

This is the final result, you can also [check it out live](https://twitter.com/erikaheidi):

![Twitter Header Image with recent followers and featured github sponsors](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/8elhwluz77yqefs2phqg.png)

What makes me excited about this little project is that the possibilities are ENDLESS. You can showcase whatever you want that has a dynamic output and can be programmatically obtained (like, via an API). The same approach can be used to tinker with your profile image.

In this tutorial, we'll build a simpler version of that dynamic header that shows your latest 5 Twitter followers. The [dynacover](https://github.com/erikaheidi/dynacover) GitHub repository contains a few other templates that you may want to experiment with, later.

Ok, enough talk. Let's get to work!

## Before Getting Started

Before moving along, make sure you have the following available to you:

- A registered application on [dev.twitter.com](https://dev.twitter.com) with **read and write** access.
- Obtain the following 4 tokens from the application settings page:
  - Consumer / API Token (App token)
  - Consumer / API Secret (App secret)
  - Access Key (User token)
  - Access Secret (User secret)
- A working PHP development environment in the command line with the following dependencies installed:
  - `php-cli` 7.4+
  - `ext-gd`
  - `ext-json`
  - `ext-curl`
  - [Composer](https://getcomposer.org)
  - A PNG image to serve as cover, which will be pasted on top of all layers. The image must be 1500x500 and have transparent placeholders for the avatar images, like [this one that you are free to use and modify](https://raw.githubusercontent.com/erikaheidi/dynacover/main/app/Resources/images/cover_basic.png). In case you create a custom banner with placeholders in different locations, you'll also need the size and coordinates of each placeholder.

You are encouraged to use a source code editor such as [Visual Studio Code](https://www.digitalocean.com/community/tutorials/how-to-set-up-visual-studio-code-for-php-projects?utm_medium=social_organic&utm_source=twitter&utm_campaign=global_brand_followers_en&utm_content=tutorials) to facilitate working within a PHP project.

For this demo, we'll create a command-line application using [Minicli](https://docs.minicli.dev), a minimalist framework for command line applications in PHP. We'll use the [TwitterOAuth](https://twitteroauth.com/) library to communicate with the Twitter API.

If you'd prefer to follow along with the code from an existing repository, or if you just want to try this demo out, you can check [erikaheidi/dynacover](https://github.com/erikaheidi/dynacover) on GitHub. To match the code in this guide, download [**version 0.1**](https://github.com/erikaheidi/dynacover/releases/tag/0.1), since the `main` branch will change over time.

## 1. Bootstrapping the Application

Start by bootstrapping a small command-line app using [Minicli](https://docs.minicli.dev). Here, we'll call it **dynacover**.

```shell
composer create-project --prefer-dist minicli/application dynacover
```

```
Creating a "minicli/application" project at "./dynacover"
Installing minicli/application (2.0.1)
  - Installing minicli/application (2.0.1): Extracting archive
Created project in /home/erika/Projects/dynacover
Loading composer repositories with package information
Updating dependencies
Lock file operations: 1 install, 0 updates, 0 removals
  - Locking minicli/minicli (2.2.1)
Writing lock file
Installing dependencies from lock file (including require-dev)
Package operations: 1 install, 0 updates, 0 removals
  - Installing minicli/minicli (2.2.1): Extracting archive
Generating autoload files
1 package you are using is looking for funding.
Use the `composer fund` command to find out more!

```

This will bootstrap de application. Enter the new directory and have a look at the files. Here's a quick overview of the directory structure in a new Minicli app:

```shell
cd dynacover
```

```
.
├── app
│   └── Command
├── composer.json
├── composer.lock
├── LICENSE
├── minicli
├── README.md
└── vendor
    ├── autoload.php
    ├── composer
    └── minicli
```

The `app/Command` directory is where all commands are defined. We'll work on that in the next step.

Next, include `abraham/twitteroauth` as dependency via Composer:

```shell
composer require abraham/twitteroauth
```

You can also rename your script to match the application name:

```shell
mv minicli dynacover
```

To test that everything works as expected, run:

```shell
php dynacover help
```

You should see the following output:

```
Available Commands

help
└──table
└──test

```

These commands are implemented at the `app/Command/Help/` directory as example commands that you may want to check out. In the next step, we'll start creating new commands.

## 2. Obtaining New Followers with TwitterOAuth Library

Before writing the code that will dynamically generate the image, first make sure you can obtain your latest followers using the TwitterOAuth library and your application / user keys.

We'll create a simple command to list your latest followers.

First, however, we need to set up the Twitter tokens in such a way that you can obtain them from the command controllers. The easiest way to do so is to create a separate file with the credentials and import them into the app's configuration. You should also make sure this file is included within your `.gitignore` in case you are using version control, so that it is not committed along with the application code.

### Storing Twitter Credentials Safely

Create a new file named `credentials.php` in the root of the application folder. This file should return an array with the application's sensitive data.

Copy the following content to your `credentials.php` file and replace all the keys with your own Twitter credentials:

```shell
#credentials.php
<?php

return [
    'twitter_consumer_key' => 'APP_CONSUMER_KEY',
    'twitter_consumer_secret' => 'APP_CONSUMER_SECRET',
    'twitter_user_token' => 'USER_ACCESS_TOKEN',
    'twitter_token_secret' => 'USER_ACCESS_TOKEN_SECRET',
];
```

To make sure this file isn't included within version control, you should add a line to your `.gitignore` file. You can do this with the following command, which will append a new line to that file:

```shell
echo "credentials.php" >> .gitignore
```

Then, edit the `dynacover` script to merge this data into the main config. This is how you can do it:

```php
#dynacover
#!/usr/bin/php
<?php

if (php_sapi_name() !== 'cli') {
    exit;
}

require __DIR__ . '/vendor/autoload.php';

use Minicli\App;

$config = [
    'app_path' => __DIR__ . '/app/Command'
];

if (is_file(__DIR__ . '/credentials.php')) {
    $config = array_merge($config, include __DIR__ . '/credentials.php');
}

$app = new App($config);

$app->runCommand($argv);

```

Now you should be able to obtain these credentials from any command controller.

## Creating New Command to Fetch Followers

With the credentials in place, you can start working with the Twitter API. Create a new directory within `app/Command` called `Fetch`. 

```shell
mkdir app/Command/Fetch
```

Create a new file at `app/Command/Fetch/FollowersController.php` using your preferred code editor. This is a class that needs to extend from the `Minicli\Command\CommandController` and implement a method named `handle`, which will handle the command when it's invoked from the prompt.

From the `handle` method, you'll obtain the needed credentials from the `$this->getApp()->config` object, accessible as magic properties:

```php
$api_token = $this->getApp()->config->twitter_consumer_key;
$api_secret = $this->getApp()->config->twitter_consumer_secret;
$access_token = $this->getApp()->config->twitter_user_token;
$token_secret = $this->getApp()->config->twitter_token_secret;
```

Then, using the `TwitterOAuth` library, you can create a request to obtain [your latest followers](https://developer.twitter.com/en/docs/twitter-api/v1/accounts-and-users/follow-search-get-users/api-reference/get-followers-list).

```php
$client = new TwitterOAuth($api_token, $api_secret, $access_token, $token_secret);
$followers = $client->get('/followers/list', [
    'skip_status' => true,
    'count' => 10
]);
```

The following code contains the full implementation, also listing the results as output at the end:


```php
#app/Command/Fetch/FollowersController.php
<?php

namespace App\Command\Fetch;

use Minicli\Command\CommandController;
use Abraham\TwitterOAuth\TwitterOAuth;

class FollowersController extends CommandController
{
    public function handle()
    {
        $api_token = $this->getApp()->config->twitter_consumer_key;
        $api_secret = $this->getApp()->config->twitter_consumer_secret;
        $access_token = $this->getApp()->config->twitter_user_token;
        $token_secret = $this->getApp()->config->twitter_token_secret;

        $client = new TwitterOAuth($api_token, $api_secret, $access_token, $token_secret);
        $followers = $client->get('/followers/list', [
            'skip_status' => true,
            'count' => 10
        ]);

        $this->getPrinter()->info("Latest Followers", true);

        foreach ($followers->users as $follower) {
            $this->getPrinter()->info($follower->screen_name);
        }

        $this->getPrinter()->info("Finished.");
        return 0;
    }
}
```

You can now run this command with:

```shell
php dynacover fetch followers
```

If everything worked as expected, you should see a list with your 10 most recent new followers, like this:

![screenshot terminal list recent followers](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/nuowu29veahp59pusj2f.png)

With this data we can start building the dynamic cover image.

## 3. Generating a Cover with User Avatars

You'll now build a new command to generate the cover image. We'll need the base image that will be pasted on top of the avatar images to give the best effect. I used [Canva](https://canva.com) to create mine, but you can use any image / photo editor able to work with PNGs and transparency.

For this demo, we'll use [this example image](https://raw.githubusercontent.com/erikaheidi/dynacover/main/app/Resources/images/cover_basic.png). After creating this on Canva, I opened the image on Gimp and used the *circle selection tool* to select where I want the profile images to go, then hit "delete" to delete that portion of the image and make it transparent:

![Creating transparent placeholders on Gimp](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/sj7cq4hs7zv052k19rl5.png)

Our coordinates will be based on that image.

### Setting Up the Example Cover Image

Create a new folder inside the `app` directory called `Resources` to place this cover image.

```shell
mkdir app/Resources
cp ~/Downloads/cover_basic.png app/Resources/twitter_cover.png
```

### Creating A Cover Generate Command

```shell
mkdir app/Command/Cover
```
Create a new command controller in that folder named `GenerateController`, so it will be reachable as `dynacover cover generate` later on. You can place the following bootstrap code in it:

```php
<?php

namespace App\Command\Cover;

use Minicli\Command\CommandController;

class GenerateController extends CommandController
{
    public function handle()
    {
        // TODO: Implement handle() method.
    }
}
```

Here are the things we'll need to do now:

1) Get Latest Followers (5);
3) Download their avatars to a temporary location;
4) Create a new GD image resource using `imagecreatetruecolor`, sized 1500x500;
5) Loop through an array with the right coordinates to paste the avatar images, and use `imagecopyresampled` to place those images in the blank GD resource;
6) Finalize with the PNG image on top;
7) Write the image to a fixed location that we can use later on to upload to Twitter.

If you want to try and implement that on your own as a challenge, go ahead! I encourage you to try. If you just want to see it working, then you can copy the following controller code to your own `GenerateController.php` file.

```php
#app/Command/Cover/GenerateController.php
<?php

namespace App\Command\Cover;

use Abraham\TwitterOAuth\TwitterOAuth;
use Minicli\Command\CommandController;

class GenerateController extends CommandController
{
    public function handle()
    {
        $api_token = $this->getApp()->config->twitter_consumer_key;
        $api_secret = $this->getApp()->config->twitter_consumer_secret;
        $access_token = $this->getApp()->config->twitter_user_token;
        $token_secret = $this->getApp()->config->twitter_token_secret;

        $client = new TwitterOAuth($api_token, $api_secret, $access_token, $token_secret);
        $followers = $client->get('/followers/list', [
            'skip_status' => true,
            'count' => 5
        ]);

        if (!isset($followers->users)) {
            $this->getPrinter()->error("An error occurred.");
            return 1;
        }

        $users = $followers->users;
        $placeholders = $this->getPlaceholders();

        $cover_final = imagecreatetruecolor(1500, 500);

        foreach ($placeholders as $key => $placeholder) {
            $follower = $users[$key];
            $this->getPrinter()->info("Adding user to banner: $follower->screen_name");

            $path = $this->downloadAvatar($follower->profile_image_url);
            $resource = $this->getResource($path);
            $info = getimagesize($path);

            if ($resource) {
                imagecopyresampled(
                    $cover_final,
                    $resource,
                    $placeholder['pos_x'],
                    $placeholder['pos_y'],
                    0,
                    0,
                    $placeholder['width'],
                    $placeholder['height'],
                    $info[0],
                    $info[1]
                );
            }
        }

        //now finish with the cover image on top
        $cover = imagecreatefrompng(__DIR__ . '/../../Resources/cover_template.png');

        if ($cover) {
            imagecopyresized(
                $cover_final,
                $cover,
                0,
                0,
                0,
                0,
                1500,
                500,
                1500,
                500
            );
        }

        $save_path = __DIR__ . '/../../../latest_header.png';
        imagepng($cover_final, $save_path);
        $this->getPrinter()->info("Finished generating cover at $save_path.");

        return 0;
    }

    public function getPlaceholders(): array
    {
        return [
            [
                'pos_x' => 486,
                'pos_y' => 272,
                'width' => 130,
                'height' => 130
            ],[
                'pos_x' => 670,
                'pos_y' => 272,
                'width' => 130,
                'height' => 130
            ],[
                'pos_x' => 859,
                'pos_y' => 272,
                'width' => 130,
                'height' => 130
            ],[
                'pos_x' => 1049,
                'pos_y' => 272,
                'width' => 130,
                'height' => 130
            ],[
                'pos_x' => 1236,
                'pos_y' => 272,
                'width' => 130,
                'height' => 130
            ]
        ];
    }

    public function getResource($path)
    {
        $info = getimagesize($path);
        $extension = image_type_to_extension($info[2]);

        if (strtolower($extension) == '.png') {
            return imagecreatefrompng($path);
        }

        if (strtolower($extension) == '.jpeg' OR strtolower($extension) == '.jpg') {
            return imagecreatefromjpeg($path);
        }

        return null;
    }

    public function downloadAvatar($url): string
    {
        $file_contents = file_get_contents($url);

        $file_path = "/tmp/" . basename($url);

        $image = fopen($file_path, "w+");
        fwrite($image, $file_contents);
        fclose($image);

        return $file_path;
    }
}
```

You can run the command with:

```shell
php dynacover cover generate
```

Once it's finished, check the root of the application to see the resulting image. It should look similar to this:

![Example Resulting Cover Image](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/t0cqc5e12sdx0xfduu5b.png)

Naturally, you should feel free to customize the image and the position of the placeholders. Just remember to adjust the coordinates accordingly. A few other cover templates are available at the [Dynacover GitHub Repository](https://github.com/erikaheidi/dynacover/tree/main/app/Resources/images).

Once you are happy with the generated cover, you can go ahead and implement a command to update your twitter cover.

## 4. Programmatically Updating Twitter Cover Image

The only thing missing now is the part where we actually update the cover image within our Twitter profile, using the API.

Create a new command controller inside `app/Command/Cover` called `UpdateController`. 

The following controller will:

1) Call the `dynacover cover generate` command to make sure we have an updated image to upload
2) Post the new image to Twitter using the TwitterOAuth library and the [/account/update_profile_banner](https://developer.twitter.com/en/docs/twitter-api/v1/accounts-and-users/manage-account-settings/api-reference/post-account-update_profile_banner) endpoint.

```php
#app/Command/Cover/UpdateController
<?php

namespace App\Command\Cover;

use Abraham\TwitterOAuth\TwitterOAuth;
use Minicli\Command\CommandController;

class UpdateController extends CommandController
{
    public function handle()
    {
        $banner_path = __DIR__ . '/../../../latest_header.png';
        $this->getPrinter()->info("Generating new cover...");
        $this->getApp()->runCommand(['dynacover', 'cover', 'generate']);

        $api_token = $this->getApp()->config->twitter_consumer_key;
        $api_secret = $this->getApp()->config->twitter_consumer_secret;
        $access_token = $this->getApp()->config->twitter_user_token;
        $token_secret = $this->getApp()->config->twitter_token_secret;

        $client = new TwitterOAuth($api_token, $api_secret, $access_token, $token_secret);

        $post = [
            'width' => 1500,
            'height' => 500,
            'offset_top' => 0,
            'offset_left' => 0,
            'banner' => base64_encode(file_get_contents($banner_path))
        ];

        $this->getPrinter()->info("Uploading cover to Twitter...");
        $client->post('/account/update_profile_banner', $post);

        $this->getPrinter()->info("Finished uploading new banner.");
        return 0;
    }
}
```

Once you get this controller set up, you can run:

```shell
php dynacover cover update
```

Then check your Twitter profile to verify that the new cover was uploaded.

## 5. Include Script on Crontab (Optional)

The last stap to automate your cover generation is to include the update script in Crontab or equivalent, to run at a certain fixed interval (every 5 or 10 minutes, for instance). 

I have set mine with Crontab to run every 5 minutes, and this is how I've done it:

```shell
crontab -e
```

This will open a text editor with the crontab for your user. The following line will execute the specified command every 5 minutes (`*/5`) and throw the output to `/dev/null`:

```
*/5 * * * * /usr/bin/php /home/erika/dynacover/dynacover cover update > /dev/null 2>&1
```

Once you save and quit, the new crontab will be installed. **Please notice that this should be set on a remote, live server, and using your local machine to run the scheduled script won't be as reliable**. Always use full paths for both the `php` executable and your `dynacover` script.

## Conclusion

In this tutorial, we saw how it is possible to programmatically generate Twitter cover / header images and upload them to your profile, so that you may show your latest followers.

Although the subject of this guide was Twitter followers and the Twitter profile header image, the same principles apply to generate images for other networks and using other APIs. Check the [dynacover GitHub](https://github.com/erikaheidi/dynacover) for some ideas.

The PHP GD library is very powerful, and this is just scratching the surface of fun things we can build with it! 

I hope you have enjoyed this tutorial and please share your profiles once you have implemented it :)