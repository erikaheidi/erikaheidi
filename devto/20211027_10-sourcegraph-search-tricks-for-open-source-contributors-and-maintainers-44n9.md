---
title: 10 Sourcegraph Search Tricks for Open Source Contributors and Maintainers
published: true
description: In this post, you'll learn 10 Sourcegraph search tricks to help identify projects and issues where your contribution can really make a difference in these last days of Hacktoberfest.
tags: opensource, hacktoberfest, beginners, cheatsheet
cover_image: https://dev-to-uploads.s3.amazonaws.com/uploads/articles/oki45yjc5fa2k666stu7.png
--- 

October is nearly finished, which means [Hacktoberfest](https://hacktoberfest.com) is on the hearts and minds of developers around the world, with many last-minute participants. And although the idea of contributing to open source and getting some cool swag is very inviting for most devs (including myself), I know that finding the right project as well as the right issue where you can be helpful can become a challenging task for contributors.

One of the biggest challenges of open source project maintainers, especially during Hacktoberfest, is to scope issues to all different skill levels of potential contributors. "Good first issues" are easier to spot, but intermediate to advanced issues are often full of unknowns and require a deeper dive into the codebase.

Apart from enabling contributors to efficiently navigate codebases of all sizes, a comprehensive code search tool like [Sourcegraph](https://sourcegraph.com) can be helpful to find more specific issues in open source projects that may have not (yet) been flagged by maintainers. Such problems can range from outdated dependencies to the use of functionality that is about to be deprecated in future releases.

In this post, you'll learn 10 Sourcegraph search tricks to help identify projects and issues where your contribution can really make a difference. 

## 1. Find projects that welcome contributions

Repositories that have documentation around how to contribute are usually very friendly towards contributions, with tagged issues and contribution guidelines that will make it easier for your pull request to be considered valid and accepted into the project. To be sure you're looking for documentation, include the `lang:Markdown` filter.

The following query will run a global search on open source repositories for the string `contributing` in markdown files:

**Search string:**
```
contributing lang:Markdown
```

**Direct link:** https://sourcegraph.com/search?q=context:global+contributing+lang:Markdown+&patternType=literal

To search repositories with documentation specific to Hacktoberfest, you can use the following search:

**Search string:**
```
hacktoberfest lang:Markdown
```

**Direct link:** https://sourcegraph.com/search?q=context:global+hacktoberfest+lang:Markdown+&patternType=literal

![Sourcegraph search: finding repositories that welcome contributions](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/34kytfm4t4znysgo9gm2.png)


## 2. Find Hacktoberfest-friendly projects using a certain language or framework

While looking for your next project to contribute to, you can include a filter to only select repositories which contain a certain file. This allows you to search for projects that have a known structure or a file that is very specific. For instance, if I were to search for PHP projects that are hacktoberfest-friendly, I could include a filter to only return repositories that contain a `composer.json` file:

**Search string:**
```
hacktoberfest lang:Markdown repohasfile:"^composer.json$" patterntype:regexp
```

**Direct link:** https://sourcegraph.com/search?q=context:global+hacktoberfest+lang:Markdown+repohasfile:%22%5Ecomposer.json%24%22&patternType=regexp

This query  uses the `regexp` pattern type to search for a file with the exact name of `composer.json`.

Similarly, you could look for a hacktoberfest-friendly Laravel project by using `artisan` as a filter in your search:

**Search string:**
```
hacktoberfest lang:Markdown repohasfile:"^artisan$" patterntype:regexp
```

**Direct link:** https://sourcegraph.com/search?q=context:global+hacktoberfest+lang:Markdown+repohasfile:%22artisan%22&patternType=regexp
 
![Sourcegraph search: hacktoberfest friendly repositories that contain a specific file](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/uqprnipyiugkzmwbbd2c.png)

## 3. Find projects that rely on specific dependencies

If you are a maintainer, looking for all repositories that depend on your library can be a fun experiment, and you can also use this trick to find projects using libraries you are very familiar with. This way, you might be able to provide meaningful help in issues that are aligned with your background and expertise.

For example, the following query will look for projects that reference [TailwindCSS](https://tailwindcss.com) as a dependency within their `package.json` file:

**Search String:**
```
tailwindcss file:package.json
```

**Direct Link:** https://sourcegraph.com/search?q=context:global+file:package.json+tailwindcss&patternType=literal

![Sourcegraph search: projects that have a specific dependency](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/tjt5s2fh7ldlmyf5cwvj.png)

You could combine this search with the `repohasfile` filter to filter the results down to a specific language or framework. For example, the following query limits results from the previous query to repositories containing a `composer.json` file (for PHP):

**Search string:**
```
file:package.json tailwindcss repohasfile:"composer.json" patterntype:regexp
```

**Direct Link:** https://sourcegraph.com/search?q=context:global+file:package.json+tailwindcss+repohasfile:%22composer.json%22&patternType=regexp


## 4. Find how an object is used across multiple repositories

Developers of all levels and backgrounds often find themselves in a situation where documentation is not available for a method, and just looking at the method signature isn't enough to understand where the parameters come from. You need a real example to see the connections between different components of the application. 

The following search query will look into all repositories inside the [minicli](https://github.com/minicli) organization on GitHub, and return all occurrences for when a new object of type `TableHelper` was created:

**Search string:**
```
repo:^github\.com/minicli/.* new TableHelper lang:PHP
```

**Direct link:** https://sourcegraph.com/search?q=context:global+repo:%5Egithub%5C.com/minicli/.*+new+TableHelper+lang:PHP&patternType=literal

![Sourcegraph search: how an object is used across repositories in an organization](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/2oivbbyxbdlmfprkteya.png)

The search results give you context around the fact that the `TableHelper` class can be instantiated with an optional argument. To see the full code, you can click on the code snippet, and that will bring you to the full contents of that file. 

Now let's say you have a method call that can accept an optional second parameter, and you want to only look for cases where the second parameter is provided. For that you can use [structural search](https://docs.sourcegraph.com/code_search/reference/structural), which will give you the ability to search for code patterns.

For example, the method `getPrinter()->out()` is used in Minicli to output messages in the command line, and accepts a mandatory first parameter (text to be printed) and an optional second parameter that specifies the color style. The following query will search for instances where the custom style was applied, within the repositories of the Minicli organization on GitHub:

**Search String:**
```
repo:^github\.com/minicli/.* getPrinter()->out(...,...) patterntype:structural
```

**Direct Link:** https://sourcegraph.com/search?q=context:global+repo:%5Egithub%5C.com/minicli/.*+getPrinter%28%29-%3Eout%28...%2C...%29&patternType=structural

![Sourcegraph search: using structural search for specific method call](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/364b7b16wudkygxtnfjw.png)


## 5. Find keys and secrets that should not have been committed to the codebase

It is not uncommon that a key or secret is committed to a code versioning system by mistake. This can happen to anyone, especially in new projects.

The following query will use the `regexp` pattern type to search for files with content that looks like an API key token or secret. 

**Search String:**
```
repo:^github\.com/sourcegraph/.* (key|secret|token)-[\w+]{32,} patterntype:regexp
```

**Direct Link:** https://sourcegraph.com/search?q=context:global+repo:%5Egithub%5C.com/sourcegraph/.*+%28key%7Csecret%7Ctoken%29-%5B%5Cw%2B%5D%7B32%2C%7D&patternType=regexp


## 6. Find usage of compromised dependencies

Every now and then we hear about libraries being compromised with malware and other suspicious code. A new technique called "package typosquatting"() exploits the popularity of framework libraries to share malicious code as packages with a similar name, which can go unnoticed for a while under a big dependency tree. That's what [happened recently](https://www.kernelmode.blog/typosquatting-malware-found-in-composer-repository/) in PHP land with the `symfont/process` package, a malware disguised as Symfony package that would open up a web shell for attackers to exploit the server.


A global search for the `symfont/process` package in JSON files indicates that most PHP projects have already removed this dependency from their `composer.json` files, while some cached lock files still contain a reference to the library:

**Search string:**
```
symfont/process lang:JSON
```

**Direct link:** https://sourcegraph.com/search?q=context:global+symfont/process+lang:JSON+&patternType=literal

![Sourcegraph search: find usage of compromised libraries](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/91p6t43106klsjxbrl1a.png)

You could use a similar search to look for compromised NPM packages, since they are also defined in a JSON file. 

## 7. Audit an organization for outdated or vulnerable dependencies across repositories

Outdated dependencies can become an issue for maintainers, especially when they represent a security vulnerability. For instance, the [lodash](https://snyk.io/vuln/npm:lodash) NPM package has a history of known CVEs. The latest critical issue was reported on [version `4.17.19` ](https://snyk.io/test/npm/lodash/4.17.19). The following query will search for this specific `lodash` version globally, using `regexp` as search pattern type:

**Search string:**
```
file:package.json lodash 4.17.19 patterntype:regexp
```

**Direct link:** https://sourcegraph.com/search?q=context:global+file:package.json+lodash+4.17.19&patternType=regexp


## 8. Find code that is not up to language standards across multiple repositories

Most programming languages have a superset of rules that are adopted by the community as standards, even though they are not syntactically enforced. With a single search, you can identify code that does not follow certain coding standards, which can be helpful for both maintainers and contributors working across multiple repositories.
For instance, the following query will search globally for code that doesn't adhere to the [PSR-12](https://www.php-fig.org/psr/psr-12/) PHP coding standards, which advises you to use one blank space character between the `if` keyword and the opening parenthesis of the evaluation expression:

**Search String:**
```
lang:PHP ^if([(...)]) patterntype:regexp
```

**Direct Link:** https://sourcegraph.com/search?q=context:global+lang:PHP+%5Eif%28%5B%28...%29%5D%29&patternType=regexp

![Sourcegraph Search: find code that doesn't adhere to language standards](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/oh9msmwspjlqdtnkbsnr.png)

## 9. Search for recent changes in large or multiple projects

If you are a regular user of larger projects such as frameworks or CMSs, you may want to be watching for any recent changes to be on top of new features. This also might be useful if you update to a newer version and start facing issues that are difficult to debug, or if your website is down after a code update and you are not sure what may have caused the issue.

The following search query will look for recent commits on the Laravel framework and list all changes merged in the past week:

**Direct Link:** https://sourcegraph.com/search?q=context:global+repo:%5Egithub%5C.com/laravel/laravel%24+type:commit+after:lastweek

**Search String**
```
repo:^github\.com/laravel/laravel$ type:commit after:lastweek
```

For more context, you can change the search `type` to `diff`, and this will give you the information about the commit _diff_, or in other words what changed after the commit was merged. The following query will search for recent changes in all Laravel repositories, showing the corresponding diff for each change:

**Search String**
```
repo:^github\.com/laravel/.*  type:diff after:lastweek
```

**Direct Link:** https://sourcegraph.com/search?q=context:global+repo:%5Egithub%5C.com/laravel/.*++type:diff+after:lastweek&patternType=literal

![Sourcegraph search: finding the recent changes in a project](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6kzi5vk6v5q5nbi3juam.png)

This provides a quick way to look for recent changes. To nail it down and reduce the number of results, you may want to specify the type of file you want to see in the results with `lang:PHP` for instance to show only `.php` files in the result.


## 10. Find deprecated function calls among OSS projects in your language of choice

When working with languages and frameworks that evolve rapidly, it might be useful to be on the lookout for deprecated function calls, and usage of methods that are about to be deprecated in future releases.

As an example, the new PHP 8.1 release will remove several methods that have been marked as deprecated, which will cause backwards compatibility issues for projects using those methods. The `mhash()` method is part of the list of methods to be deprecated.

The following query will use [structural search](https://docs.sourcegraph.com/code_search/reference/structural) to look for `mhash` method calls on all open source projects currently indexed:

**Search Query:**
```
mhash(...) lang:PHP select:content patterntype:structural
```

**Direct Link:** https://sourcegraph.com/search?q=context:global+mhash%28...%29+lang:PHP+select:content&patternType=structural

![Sourcegraph search: find deprecated method calls across repositories](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/czink9hvhq5e1qawhq8g.png)

The structural search can be extremely helpful for refactoring code in situations like this, for projects in any language. Just replace the `lang` with your preferred language and the search string with the method you want to search for.

## Conclusion

Advanced code search can give you the insights you miss from only looking at the surface of things. That can be extremely useful when working with open source software, since contributors hardly have the time for long deep dives into the code and need a quick onboarding to the project. Maintainers can also benefit from advanced code search in order to keep their projects up to date and welcoming to new contributors. For more information on Sourcegraph's search query syntax, please refer to the [official documentation](https://docs.sourcegraph.com/code_search/reference/queries).

---

Have suggestions or questions? Leave a comment, or join our [Community Slack Space](https://about.sourcegraph.com/community/?utm_medium=social&utm_source=devto&utm_campaign=slacklaunch) where our team will be happy to answer any questions you may have about Sourcegraph.
