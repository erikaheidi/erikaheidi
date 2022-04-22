---
title: Installing and customizing Visual Studio Code: VS Code setup from scratch
published: true
description: In this post, we'll see how to install and customize VS Code from scratch, including useful extensions to improve your productivity while coding in different programming languages.
tags: vscode, beginners, tutorial, productivity
cover_image: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/vw65mgrutwt4avg8jxi5.png
---

[Visual Studio Code](https://code.visualstudio.com/) (VS Code) is a source code editor or IDE (*Integrated Development Environment*) freely available for all major operating systems. Although VSCode has native features to help you develop applications and websites in several languages, what makes VS Code an excellent choice for developers is the vast ecosystem of extensions that allow you to customize and fine tune your VS Code installation to meet your specific needs.

In this article, you'll learn how to install and customize VS Code to make it the right fit for your coding needs. You'll also get a list of recommended extensions for improving your overall productivity, no matter your programming language of choice. As an appendix, I've included a few extensions that I personally use for PHP Development.

## Installing Visual Studio Code

As mentioned earlier, VS Code is available for all major operating systems. To get started, access their [download page](https://code.visualstudio.com/download) and download the adequate installer for your system.

### Installing on Ubuntu
For Ubuntu systems, the operating system in which this article is validated, you should download the `.deb` file. Once it's downloaded, you can install it from your terminal with `sudo dpkg -i file.deb`, like this:

```shell
sudo dpkg -i code_1.63.2-1639562499_amd64.deb
```
### Installing on other systems
For other systems, download and execute the appropriate installer.

## Getting familiar with the interface
After installation is complete, open VS Code. You'll see a "Get Started" page like this:

![VS Code getting started page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/lnksh56hm51lo39l5wds.png)

In this page you have some shortcuts to change your configuration, set up synchronization, and install language-specific extensions. You don't need to do this now; first, let's explore how to navigate the IDE interface.

### Opening a project in VS Code

To open an existing project, use the **files icon** on the left menu (it's the first icon). Then, choose a project directory to open, or clone a remote repository to get started.

![Opening a project at VS Code](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/97rfa6uoe52lgaeaydrk.gif)

### Changing the font size

Open a file from your project and check if you are satisfied with the default font size. With bigger resolutions and screens, there's a chance you may need to increase the font size for more comfortable reading. You can do this by accessing the menu **File** -> **Preferences** -> **Settings** and then searching for "font" on the search bar that appears at the top. Look for the option **Editor font size** and change it to your desired size.

![Changing the font size](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qck2cykozq1pqafxnz5y.gif)

### Increase the IDE fonts / window zoom level

Changing the editor font won't modify the IDE interface font size. To increase the UI fonts, go to the Settings page via the menu **File** -> **Preferences** -> **Settings** and search for "zoom" on the search bar that appears at the top. Change the **Window zoom level** to a value such as 1 or 2 and see if that looks better.

![Increasing zoom level on vscode](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qwb9yuygz8hz69bwtyet.gif)

### Changing the editor theme

To change the default editor color theme, go to **File** -> **Preferences** -> **Color Theme** or hit `CTRL+K CTRL+T` to access the theme selection menu. There are a few different options, in both dark and light schemes.

![Changing VS Code theme](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ireccri3osyc7ohe9sii.gif)
_ The theme chosen in this gif is "Monokai Dark"._

## Using the source control tool
The third icon on the left menu will give you access to VS Code's built-in source control tool, a helper tool that allows you to discard and commit changes to source control without the need to run commands from a terminal.

![Using VS Code source control tool](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/nevmvq3p7c1q2rzdvnds.gif)

## How to install extensions

Once you're satisfied with your basic setup, you may want to start exploring VS Code extensions. The [VS Code Marketplace](https://marketplace.visualstudio.com/VSCode) offers a large collection of useful extensions to customize your VS Code so that it attends all your coding needs.

There are two different ways to install an extension from the marketplace:

### Installing directly through the IDE
You can search and install extensions directly from the VS Code interface by accessing the extensions tab, the 5th button on the top left menu. 

![Installing a VS Code extension through the IDE](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/64cj7bf8e3q4mw4m0l2q.gif)

### Installing with a command
If you are navigating the marketplace looking for extensions and find one you want to install, you can do so with a command executed through VS Code's "quick open" shortcut  (`CTRL+P`). The extension page typically has instructions on how to do so. For instance, let's say you want to install the [Markdown all in one extension](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one), a popular markdown extension for VS Code. From VS Code, type `CTRL+P` and paste in the following command:

```
ext install yzhang.markdown-all-in-one
```
![Installing a VS Code extension using the quick open menu](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/y1hqx1zzky8vvt165eth.gif)

Each extension has a unique name that you can find on the extension's marketplace page. You'll need that name to install the extension with the `ext install` command.

## Recommended productivity extensions

In this section, you'll find a collection of recommended extensions that can be useful to improve your coding productivity regardless of your programming language of choice.

### Sourcegraph code search

The newly released [Sourcegraph VS Code extension](https://marketplace.visualstudio.com/items?itemName=sourcegraph.sourcegraph) allows you to search code across millions of open source repositories right from your IDE. You don't need an account to use the plugin, but connecting to a free account enables you more features such as searching across your private repositories on multiple code host services.

![Sourcegraph code search](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/hamo5ewc7ell24bvrq2a.gif)

#### Installation

```shell
ext install sourcegraph.sourcegraph
```

In the following video, I show a brief overview of how to use the Sourcegraph plugin on VS Code to search for examples of how a method is used across open source repositories.

{% youtube qoo9MdvvuxQ %}

For more information and tips on how to use Sourcegraph's VS Code extension, check [this blog post](https://about.sourcegraph.com/blog/ways-to-use-sourcegraph-extension-for-vs-code/).

### Prettier

[Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) is a popular code formatting tool that is also available as a VS Code extension. It enforces a consistent code style based on a set of rules that take into consideration the maximum line length, wrapping and reformatting code when necessary.

#### Installation

```shell
ext install esbenp.prettier-vscode
```

### Docker

The [Docker extension](ext install ms-azuretools.vscode-docker) allows you to quickly manage your Docker environment through VS Code. It also provides one-click debugging of Node.js, Python, and .NET Core inside a container.

#### Installation

```shell
ext install ms-azuretools.vscode-docker
```

### Color Highlight

Especially useful for developers who work with front end, the [Color Highlight extension](https://marketplace.visualstudio.com/items?itemName=naumovs.color-highlight) adds a colored border around colors, which is very handy to give you an instant reference for web colors without having to reload a page on your browser.

#### Installation

```shell
ext install naumovs.color-highlight
```

### Project Manager

The [Project Manager extension](https://marketplace.visualstudio.com/items?itemName=alefragnani.project-manager) enables advanced project management on VS Code. It allows you to organize your projects into virtual workspaces with support to tags and remote development.

#### Installation

```shell
ext install alefragnani.project-manager
```

### Markdown all in one

With over 3 million downloads, the [Markdown all in one](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one) extension is a must-have for anyone who writes documentation in the markdown language.

#### Installation

```shell
ext install yzhang.markdown-all-in-one
```

### Spell Right

With the [Spell Right extension](https://marketplace.visualstudio.com/items?itemName=ban.spellright) you'll have a lightweight and offline spellcheck right into your VS Code installation. It supports several languages and file types.

#### Installation

```shell
ext install ban.spellright
```

### CodeSnap

The [CodeSnap extension](https://marketplace.visualstudio.com/items?itemName=adpyke.codesnap) allows you to take beautiful screenshots of your code.

#### Installation

```shell
ext install adpyke.codesnap
```

### Emmet Live
The [Emmet Live](https://marketplace.visualstudio.com/items?itemName=ysemeniuk.emmet-live&ssr=false#overview) extensions enables support to [emmet abbreviations](https://docs.emmet.io/abbreviations/), special expressions that are evaluated and turned into structured code snippets, such as HTML code. 


#### Installation

```
ext install ysemeniuk.emmet-live
```

### TailwindCSS Intelesense

The [TailwindCSS Intelesense extension](https://marketplace.visualstudio.com/items?itemName=bradlc.vscode-tailwindcss) adds syntax highlight and autocomplete for TailwindCSS projects on VS Code.

#### Installation

```shell
ext install bradlc.vscode-tailwindcss
``` 

## Appendix: PHP extensions

In this section, you'll find a collection of extensions that are useful for PHP development.

### PHP Intelephense

The [PHP Intelephense extension](https://marketplace.visualstudio.com/items?itemName=bmewburn.vscode-intelephense-client) enables advanced code analysis and gives you context about methods, variables, classes and other components in a PHP application.

![PHP Intelephense](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/9pn8jj7aol0v9tc1bo09.png)

#### Installation

```shell
ext install bmewburn.vscode-intelephense-client
```

### PHP Getters and Setters

The [PHP getters and setters](https://marketplace.visualstudio.com/items?itemName=phproberto.vscode-php-getters-setters) extension enables you to quickly generate getter and setter methods on a PHP class, based on its properties.

#### Installation

```shell
ext install phproberto.vscode-php-getters-setters
```

### PHP Namespace Resolver

The [PHP namespace resolver](https://marketplace.visualstudio.com/items?itemName=MehediDracula.php-namespace-resolver) extension facilitates importing classes and also allows you to easily order your `use` statements by line length or in alphabetical order.

#### Installation

```shell
ext install MehediDracula.php-namespace-resolver
```

### Laravel Artisan

The [Laravel Artisan extension](https://marketplace.visualstudio.com/items?itemName=ryannaddy.laravel-artisan) allows you to run Artisan commands from within your VS Code.

#### Installation

```shell
ext install ryannaddy.laravel-artisan
```

## Conclusion

In this post, we saw how to install and customize VS Code for a better coding experience suited to your individual needs. We also saw some useful extensions for productivity, and a few PHP extensions that can help you improve your development speed in that language.

Give these extensions a try, and if some don't turn out to be useful you can quickly deactivate them through the Extensions tab (the fifth icon on the top left menu).

Have feedback or questions about the Sourcegraph VS Code extension? Leave a comment, or join our [Community Slack Space](https://about.sourcegraph.com/community/?utm_medium=social&utm_source=devto&utm_campaign=slacklaunch) where our team will be happy to answer any questions you may have.



