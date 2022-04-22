---
title: Creating a new Laravel application with Sail and Docker (no PHP setup required)
series: Getting Started with Laravel
published: true
description: In the first part of our livestream series "Getting Started with Laravel", we'll see how to bootstrap a new Laravel application with Sail and Docker, no PHP required.
tags: beginners, php, laravel, tutorial
cover_image: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/cjgiz4bh47afxqvwrdha.png
---

In the first episode of our livestreaming series _Getting Started with Laravel_ on [Sourcegraph's Twitch channel](https://twitch.tv/sourcegraph), we've seen how to bootstrap a new Laravel application with [Sail](https://laravel.com/docs/8.x/sail) and Docker. This allows you to develop your Laravel application using Docker containers instead of having to set up a fully functional PHP development environment on a local machine or development server.

In this recap post, we'll share all steps that were executed during the live session, so that you're able to reproduce them at your own pace. If you prefer, you can also watch the video version of this recap [on youtube](https://www.youtube.com/watch?v=YH216RDiGkc).

{% youtube YH216RDiGkc %}

## Preparation
Before moving along, make sure you have the following software installed and configured on your local machine or development server:

- Docker
- Curl
- Git

Windows and MacOS users will need Docker Desktop installed. Linux users need only the Docker engine installed. Make sure you add your user to the `docker` group so that you are able to execute Docker with your regular user.

## 1. Create a new Laravel application with the official builder script

The demo application is a blog-like application that pulls content from a user's profile at [DEV](https://dev.to). We'll call it **laravel-dev-blog**.

The next command will download the builder script from an official Laravel site and run it using `bash`.

```shell
curl -s https://laravel.build/laravel-dev-blog | bash
```

This operation may take a few minutes the first time you run the installer, since it will download a suitable PHP image to execute Composer and install the application dependencies using Docker.

Before finishing, the installation script will ask you to confirm your `sudo` password in order to set the correct permissions on the application directories:

```
Application ready! Build something amazing.
Sail scaffolding installed successfully.

Please provide your password so we can make some final adjustments to your application's permissions.

[sudo] password for erika: 

Thank you! We hope you build something incredible. Dive in with: cd laravel-dev-blog && ./vendor/bin/sail up
```

You can now explore the files in your freshly installed Laravel application.

```shell
cd laravel-dev-blog/
ls
```

The `artisan` script, located at the root of the application folder, is an important tool that you can use to generate boilerplate code, manipulate the database, run jobs and queues, among other things.

Following, a list with the relevant directories within a freshly installed Laravel application:

```
.
├── app/ # models, controllers, and app-specific logic
├── bootstrap/
├── config/ # configuration files
├── database/ # database-related classes and scripts
├── public/ # the document root for the application
├── resources/ # front end resources that aren't public: views, base CSS and JS 
├── routes/ # where the application routes are defined
├── storage/ # file uploads, cache, and logs are stored here
├── tests/ #application tests

```


## 2.  Running `sail up`

With the files in place, you can now bring your development environment up with the following command:

```shell
sail up
```

This will run your development environment in foreground mode, which allows you to see container logs in real time, but it will block your terminal. To stop the execution and save the state of containers, you can hit `CTRL+C`.

To run the environment in background mode (detached), include `-d` as an argument to the previous command:

```shell
sail up -d
```

Whether you choose to run your environment in foreground or background mode, your new Laravel application should now be available at `http://localhost`. Open this URL on your browser and you'll see a page like this:

![Laravel welcome page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/uoci8k1kpbpbcr12rur7.png)

### Sail quick reference

The following list contains a short reference on the main Sail commands:

| Command        | Description                                                                                  |
|----------------|----------------------------------------------------------------------------------------------|
| `sail up`      | Brings the Docker environment up.                                                            |
| `sail down`    | Brings the Docker environment down and remove associated containers, storage, and network.   |
| `sail start`   | Starts an environment that was previously stopped with `sail stop`.                          |
| `sail stop`    | Stops an environment that is currently running, saving the state of containers and services. |
| `sail artisan` | Runs the `artisan` tool on the application container.                                        |
| `sail php`     | Runs a PHP script on the application container.                                              |

For more information on all available Sail commands, please visit the [official documentation](https://laravel.com/docs/8.x/sail#executing-sail-commands).

## Troubleshooting

Here are a few tips for troubleshooting common problems that may happen while you run this tutorial.

#### 'Docker' is not running
This error can appear if the Docker service is not enabled on your system, or if your current system user doesn't have permission to execute Docker. On new installations of the Docker engine on Linux, only the **root** user is able to manage containers. You need to make sure your system user is added to the **docker** group on your system.

#### I added my user to the docker group, but it still doesn't work
After adding the user to the group, you need to restart your session so that the changes are valid. You can log out and log on again on your system. Another option is to run the following command:

```shell
u - ${USER}
```

Although this is a temporary solution and you'll still need to restart your session so that the change is permanent.


## Conclusion

With the Laravel builder script and Sail, it is possible to install and run a Laravel application without the need to have PHP installed on your local machine or development server. This can be very useful to explore the Laravel framework and the PHP language in general, with the additional advantage of using a standardized development environment that is compatible and optimized for running Laravel.

In the next part of this series, we'll start building the front end of our project using TailwindCSS.

Follow [Sourcegraph on Twitch](https://twitch.tv/sourcegraph]) to be notified when we go live with new episodes on this series.
