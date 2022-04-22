---
title: Minicli 3.0 for PHP 8: release notes, upgrade guide
published: true
description: Minicli 3 for PHP 8 is released! Find out what changes and how to upgrade in this post. 
tags: php, cli, commandline, releases
//cover_image: https://direct_url_to_image.jpg
---

Last week I merged a pull request to [Minicli](https://minicli.dev) that moves the minimal required PHP version to `8.0`. The PR was contributed by [lotfio](https://twitter.com/_lotfio), and included code styling improvements in addition to introducing strict types to all core Minicli classes.

In this post I share the reasoning behind the decision to merge this PR, what changes, and how to upgrade from Minicli `2.2` to `3.0`.

## Why
When I first developed Minicli, my main goal was to create something very simple and yet extensible to different use cases. With time, people started contributing to the project, which of course helped shaping up Minicli for more stability. And as much as the idea of introducing breaking changes may seem to harm stability, that is not always the case when we look at the long term! 

In the future, I would like Minicli to still be simple and dependency-free, but with improved error handling and more native system support. To accomplish that in the future, we'll need to start moving along as PHP advances; that means not only upgrading to a newer version of PHP but also making use of some important features that will bring more stability to the project, which is the case of enabling strict types in the core classes.

## Breaking Changes
There are three main changes that require attention when upgrading, but shouldn't be big blockers for new applications:

- **PHP 8.0 minimal version**
  - This will require you to have an environment running PHP >= 8.0.
- **Strictly typed core classes**
  - Because of this change, elements which implement or extend from the core classes may need an update to keep compatibility with their parent / interface. This is the case for Service Providers and Command Controllers.
- **Core class properties changed to camelCase**
  - Because of this change, some built-in services are called differently. For instance, the Command Registry changed from `$app->command_registry` to `$app->commandRegistry`.

## Upgrading from Minicli 2

I have started upgrading some of my Minicli projects to version `3.0` so that I could share the pain points. There are **two** important things you'll have to do once you upgrade:

- Update signature for the `handle()` method on your Command Controllers
- Update signature for the `load()` method on your Service Providers

Apart from that, you may face problems if you are accessing some internal Service Providers from the App container such as the Command Registry, which changed to camel case (`command_registry` to `commandRegistry`).

### Command Controllers
You'll need to update your command controllers to make the signature of the `handle()` method match the definition of the parent class, including the `: void` return type:

```php
class RefreshController extends CommandController
{
    public function handle(): void
    {
        ...
    }
}
```

### Service Providers
In case you have created custom Service Providers, you should start by upgrading the signature of their `load()` method to include the `void` return type and match the parent class method signature:

```php
    public function load(App $app): void
    {
      ...
    }
```

## Final Thoughts

We hope these changes will pave the way for more useful integrations and easier testing coverage, improving stability and security of applications and commands that deal with filesystem operations and/or sensitive data.

If you come across bugs or other problems while using or upgrading to Minicli 3, we'll appreciate if you can [create an issue](https://github.com/minicli/minicli/issues/new/choose) on GitHub so that me and other contributors can help address it. Thank you in advance!

