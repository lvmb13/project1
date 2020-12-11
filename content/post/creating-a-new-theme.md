+++
author = "Michael Henderson"
date = 2014-09-28
title = "Creating a New Theme"
series = "Hugo 10111"
+++

## Introduction



This tutorial will show you how to create a simple theme in Hugo. I assume that you are familiar with HTML, the bash command line, and that you are comfortable using Markdown to format content. I'll explain how Hugo uses templates and how you can organize your templates to create a theme. I won't cover using CSS to style your theme.

We'll start with creating a new site with a very basic template. Then we'll add in a few pages and posts. With small variations on that, you will be able to create many different types of web sites.

In this tutorial, commands that you enter will start with the "\$" prompt. The output will follow. Lines that start with "#" are comments that I've added to explain a point. When I show updates to a file, the ":wq" on the last line means to save the file.

Here's an example:

```
## this is a comment
$ echo this is a command
this is a command

## edit the file
$ vi foo.md
+++
date = "2014-09-28"
title = "creating a new theme"
+++

bah and humbug
:wq

## show it
$ cat foo.md
+++
date = "2014-09-28"
title = "creating a new theme"
+++

bah and humbug
$
```

## Some Definitions

There are a few concepts that you need to understand before creating a theme.

### Skins

Skins are the files responsible for the look and feel of your site. It’s the CSS that controls colors and fonts, it’s the Javascript that determines actions and reactions. It’s also the rules that Hugo uses to transform your content into the HTML that the site will serve to visitors.

You have two ways to create a skin. The simplest way is to create it in the `layouts/` directory. If you do, then you don’t have to worry about configuring Hugo to recognize it. The first place that Hugo will look for rules and files is in the `layouts/` directory so it will always find the skin.

Your second choice is to create it in a sub-directory of the `themes/` directory. If you do, then you must always tell Hugo where to search for the skin. It’s extra work, though, so why bother with it?

The difference between creating a skin in `layouts/` and creating it in `themes/` is very subtle. A skin in `layouts/` can’t be customized without updating the templates and static files that it is built from. A skin created in `themes/`, on the other hand, can be and that makes it easier for other people to use it.

The rest of this tutorial will call a skin created in the `themes/` directory a theme.

Note that you can use this tutorial to create a skin in the `layouts/` directory if you wish to. The main difference will be that you won’t need to update the site’s configuration file to use a theme.

### The Home Page

The home page, or landing page, is the first page that many visitors to a site see. It is the index.html file in the root directory of the web site. Since Hugo writes files to the public/ directory, our home page is public/index.html.

### Site Configuration File

When Hugo runs, it looks for a configuration file that contains settings that override default values for the entire site. The file can use TOML, YAML, or JSON. I prefer to use TOML for my configuration files. If you prefer to use JSON or YAML, you’ll need to translate my examples. You’ll also need to change the name of the file since Hugo uses the extension to determine how to process it.

Hugo translates Markdown files into HTML. By default, Hugo expects to find Markdown files in your `content/` directory and template files in your `themes/` directory. It will create HTML files in your `public/` directory. You can change this by specifying alternate locations in the configuration file.

### Content

Content is stored in text files that contain two sections. The first section is the “front matter,” which is the meta-information on the content. The second section contains Markdown that will be converted to HTML.

#### Front Matter

The front matter is information about the content. Like the configuration file, it can be written in TOML, YAML, or JSON. Unlike the configuration file, Hugo doesn’t use the file’s extension to know the format. It looks for markers to signal the type. TOML is surrounded by “`+++`”, YAML by “`---`”, and JSON is enclosed in curly braces. I prefer to use TOML, so you’ll need to translate my examples if you prefer YAML or JSON.

The information in the front matter is passed into the template before the content is rendered into HTML.

#### Markdown

Content is written in Markdown which makes it easier to create the content. Hugo runs the content through a Markdown engine to create the HTML which will be written to the output file.

### Template Files

Hugo uses template files to render content into HTML. Template files are a bridge between the content and presentation. Rules in the template define what content is published, where it's published to, and how it will rendered to the HTML file. The template guides the presentation by specifying the style to use.

There are three types of templates: single, list, and partial. Each type takes a bit of content as input and transforms it based on the commands in the template.

Hugo uses its knowledge of the content to find the template file used to render the content. If it can’t find a template that is an exact match for the content, it will shift up a level and search from there. It will continue to do so until it finds a matching template or runs out of templates to try. If it can’t find a template, it will use the default template for the site.

Please note that you can use the front matter to influence Hugo’s choice of templates.

#### Single Template

A single template is used to render a single piece of content. For example, an article or post would be a single piece of content and use a single template.

#### List Template

A list template renders a group of related content. That could be a summary of recent postings or all articles in a category. List templates can contain multiple groups.

The homepage template is a special type of list template. Hugo assumes that the home page of your site will act as the portal for the rest of the content in the site.

#### Partial Template

A partial template is a template that can be included in other templates. Partial templates must be called using the “partial” template command. They are very handy for rolling up common behavior. For example, your site may have a banner that all pages use. Instead of copying the text of the banner into every single and list template, you could create a partial with the banner in it. That way if you decide to change the banner, you only have to change the partial template.

## Create a New Site

Let's use Hugo to create a new web site. I'm a Mac user, so I'll create mine in my home directory, in the Sites folder. If you're using Linux, you might have to create the folder first.

The "new site" command will create a skeleton of a site. It will give you the basic directory structure and a useable configuration file.

```
$ hugo new site ~/Sites/zafta
$ cd ~/Sites/zafta
$ ls -l
total 8
drwxr-xr-x  7 quoha  staff  238 Sep 29 16:49 .
drwxr-xr-x  3 quoha  staff  102 Sep 29 16:49 ..
drwxr-xr-x  2 quoha  staff   68 Sep 29 16:49 archetypes
-rw-r--r--  1 quoha  staff   82 Sep 29 16:49 config.toml
drwxr-xr-x  2 quoha  staff   68 Sep 29 16:49 content
drwxr-xr-x  2 quoha  staff   68 Sep 29 16:49 layouts
drwxr-xr-x  2 quoha  staff   68 Sep 29 16:49 static
$
```

Take a look in the content/ directory to confirm that it is empty.

The other directories (archetypes/, layouts/, and static/) are used when customizing a theme. That's a topic for a different tutorial, so please ignore them for now.

### Generate the HTML For the New Site

Running the `hugo` command with no options will read all the available content and generate the HTML files. It will also copy all static files (that's everything that's not content). Since we have an empty site, it won't do much, but it will do it very quickly.

```
$ hugo --verbose
INFO: 2014/09/29 Using config file: config.toml
INFO: 2014/09/29 syncing from /Users/quoha/Sites/zafta/static/ to /Users/quoha/Sites/zafta/public/
WARN: 2014/09/29 Unable to locate layout: [index.html _default/list.html _default/single.html]
WARN: 2014/09/29 Unable to locate layout: [404.html]
0 draft content
0 future content
0 pages created
0 tags created
0 categories created
in 2 ms
$
```

The "`--verbose`" flag gives extra information that will be helpful when we build the template. Every line of the output that starts with "INFO:" or "WARN:" is present because we used that flag. The lines that start with "WARN:" are warning messages. We'll go over them later.

We can verify that the command worked by looking at the directory again.

```
$ ls -l
total 8
drwxr-xr-x  2 quoha  staff   68 Sep 29 16:49 archetypes
-rw-r--r--  1 quoha  staff   82 Sep 29 16:49 config.toml
drwxr-xr-x  2 quoha  staff   68 Sep 29 16:49 content
drwxr-xr-x  2 quoha  staff   68 Sep 29 16:49 layouts
drwxr-xr-x  4 quoha  staff  136 Sep 29 17:02 public
drwxr-xr-x  2 quoha  staff   68 Sep 29 16:49 static
$
```

See that new public/ directory? Hugo placed all generated content there. When you're ready to publish your web site, that's the place to start. For now, though, let's just confirm that we have what we'd expect from a site with no content.

```
$ ls -l public
total 16
-rw-r--r--  1 quoha  staff  416 Sep 29 17:02 index.xml
-rw-r--r--  1 quoha  staff  262 Sep 29 17:02 sitemap.xml
$
```

Hugo created two XML files, which is standard, but there are no HTML files.

### Test the New Site

Verify that you can run the built-in web server. It will dramatically shorten your development cycle if you do. Start it by running the "server" command. If it is successful, you will see output similar to the following:

```
$ hugo server --verbose
INFO: 2014/09/29 Using config file: /Users/quoha/Sites/zafta/config.toml
INFO: 2014/09/29 syncing from /Users/quoha/Sites/zafta/static/ to /Users/quoha/Sites/zafta/public/
WARN: 2014/09/29 Unable to locate layout: [index.html _default/list.html _default/single.html]
WARN: 2014/09/29 Unable to locate layout: [404.html]
0 draft content
0 future content
0 pages created
0 tags created
0 categories created
in 2 ms
Serving pages from /Users/quoha/Sites/zafta/public
Web Server is available at http://localhost:1313
Press Ctrl+C to stop
```

Connect to the listed URL (it's on the line that starts with "Web Server"). If everything is working correctly, you should get a page that shows the following:

```
index.xml
sitemap.xml
```

That's a listing of your public/ directory. Hugo didn't create a home page because our site has no content. When there's no index.html file in a directory, the server lists the files in the directory, which is what you should see in your browser.

Let’s go back and look at those warnings again.

```
WARN: 2014/09/29 Unable to locate layout: [index.html _default/list.html _default/single.html]
WARN: 2014/09/29 Unable to locate layout: [404.html]
```

That second warning is easier to explain. We haven’t created a template to be used to generate “page not found errors.” The 404 message is a topic for a separate tutorial.

Now for the first warning. It is for the home page. You can tell because the first layout that it looked for was “index.html.” That’s only used by the home page.

I like that the verbose flag causes Hugo to list the files that it's searching for. For the home page, they are index.html, \_default/list.html, and \_default/single.html. There are some rules that we'll cover later that explain the names and paths. For now, just remember that Hugo couldn't find a template for the home page and it told you so.

At this point, you've got a working installation and site that we can build upon. All that’s left is to add some content and a theme to display it.

## Create a New Theme

Hugo doesn't ship with a default theme. There are a few available (I counted a dozen when I first installed Hugo) and Hugo comes with a command to create new themes.

We're going to create a new theme called "zafta." Since the goal of this tutorial is to show you how to fill out the files to pull in your content, the theme will not contain any CSS. In other words, ugly but functional.

All themes have opinions on content and layout. For example, Zafta uses "post" over "blog". Strong opinions make for simpler templates but differing opinions make it tougher to use themes. When you build a theme, consider using the terms that other themes do.

### Create a Skeleton

Use the hugo "new" command to create the skeleton of a theme. This creates the directory structure and places empty files for you to fill out.

```
$ hugo new theme zafta

$ ls -l
total 8
drwxr-xr-x  2 quoha  staff   68 Sep 29 16:49 archetypes
-rw-r--r--  1 quoha  staff   82 Sep 29 16:49 config.toml
drwxr-xr-x  2 quoha  staff   68 Sep 29 16:49 content
drwxr-xr-x  2 quoha  staff   68 Sep 29 16:49 layouts
drwxr-xr-x  4 quoha  staff  136 Sep 29 17:02 public
drwxr-xr-x  2 quoha  staff   68 Sep 29 16:49 static
drwxr-xr-x  3 quoha  staff  102 Sep 29 17:31 themes

$ find themes -type f | xargs ls -l
-rw-r--r--  1 quoha  staff  1081 Sep 29 17:31 themes/zafta/LICENSE.md
-rw-r--r--  1 quoha  staff     0 Sep 29 17:31 themes/zafta/archetypes/default.md
-rw-r--r--  1 quoha  staff     0 Sep 29 17:31 themes/zafta/layouts/_default/list.html
-rw-r--r--  1 quoha  staff     0 Sep 29 17:31 themes/zafta/layouts/_default/single.html
-rw-r--r--  1 quoha  staff     0 Sep 29 17:31 themes/zafta/layouts/index.html
-rw-r--r--  1 quoha  staff     0 Sep 29 17:31 themes/zafta/layouts/partials/footer.html
-rw-r--r--  1 quoha  staff     0 Sep 29 17:31 themes/zafta/layouts/partials/header.html
-rw-r--r--  1 quoha  staff    93 Sep 29 17:31 themes/zafta/theme.toml
$
```

The skeleton includes templates (the files ending in .html), license file, a description of your theme (the theme.toml file), and an empty archetype.

Please take a minute to fill out the theme.toml and LICENSE.md files. They're optional, but if you're going to be distributing your theme, it tells the world who to praise (or blame). It's also nice to declare the license so that people will know how they can use the theme.

```
$ vi themes/zafta/theme.toml
author = "michael d henderson"
description = "a minimal working template"
license = "MIT"
name = "zafta"
source_repo = ""
tags = ["tags", "categories"]
:wq

## also edit themes/zafta/LICENSE.md and change
## the bit that says "YOUR_NAME_HERE"
```

Note that the the skeleton's template files are empty. Don't worry, we'll be changing that shortly.

```
$ find themes/zafta -name '*.html' | xargs ls -l
-rw-r--r--  1 quoha  staff  0 Sep 29 17:31 themes/zafta/layouts/_default/list.html
-rw-r--r--  1 quoha  staff  0 Sep 29 17:31 themes/zafta/layouts/_default/single.html
-rw-r--r--  1 quoha  staff  0 Sep 29 17:31 themes/zafta/layouts/index.html
-rw-r--r--  1 quoha  staff  0 Sep 29 17:31 themes/zafta/layouts/partials/footer.html
-rw-r--r--  1 quoha  staff  0 Sep 29 17:31 themes/zafta/layouts/partials/header.html
$
```

### Update the Configuration File to Use the Theme

Now that we've got a theme to work with, it's a good idea to add the theme name to the configuration file. This is optional, because you can always add "-t zafta" on all your commands. I like to put it the configuration file because I like shorter command lines. If you don't put it in the configuration file or specify it on the command line, you won't use the template that you're expecting to.

Edit the file to add the theme, add a title for the site, and specify that all of our content will use the TOML format.

```
$ vi config.toml
theme = "zafta"
baseurl = ""
languageCode = "en-us"
title = "zafta - totally refreshing"
MetaDataFormat = "toml"
:wq

$
```

### Generate the Site

Now that we have an empty theme, let's generate the site again.

```
$ hugo --verbose
INFO: 2014/09/29 Using config file: /Users/quoha/Sites/zafta/config.toml
INFO: 2014/09/29 syncing from /Users/quoha/Sites/zafta/themes/zafta/static/ to /Users/quoha/Sites/zafta/public/
INFO: 2014/09/29 syncing from /Users/quoha/Sites/zafta/static/ to /Users/quoha/Sites/zafta/public/
WARN: 2014/09/29 Unable to locate layout: [404.html theme/404.html]
0 draft content
0 future content
0 pages created
0 tags created
0 categories created
in 2 ms
$
```

Did you notice that the output is different? The warning message for the home page has disappeared and we have an additional information line saying that Hugo is syncing from the theme's directory.

Let's check the public/ directory to see what Hugo's created.

```
$ ls -l public
total 16
drwxr-xr-x  2 quoha  staff   68 Sep 29 17:56 css
-rw-r--r--  1 quoha  staff    0 Sep 29 17:56 index.html
-rw-r--r--  1 quoha  staff  407 Sep 29 17:56 index.xml
drwxr-xr-x  2 quoha  staff   68 Sep 29 17:56 js
-rw-r--r--  1 quoha  staff  243 Sep 29 17:56 sitemap.xml
$
```

Notice four things:

1. Hugo created a home page. This is the file public/index.html.
2. Hugo created a css/ directory.
3. Hugo created a js/ directory.
4. Hugo claimed that it created 0 pages. It created a file and copied over static files, but didn't create any pages. That's because it considers a "page" to be a file created directly from a content file. It doesn't count things like the index.html files that it creates automatically.

#### The Home Page

Hugo supports many different types of templates. The home page is special because it gets its own type of template and its own template file. The file, layouts/index.html, is used to generate the HTML for the home page. The Hugo documentation says that this is the only required template, but that depends. Hugo's warning message shows that it looks for three different templates:

```
WARN: 2014/09/29 Unable to locate layout: [index.html _default/list.html _default/single.html]
```

If it can't find any of these, it completely skips creating the home page. We noticed that when we built the site without having a theme installed.

When Hugo created our theme, it created an empty home page template. Now, when we build the site, Hugo finds the template and uses it to generate the HTML for the home page. Since the template file is empty, the HTML file is empty, too. If the template had any rules in it, then Hugo would have used them to generate the home page.

```
$ find . -name index.html | xargs ls -l
-rw-r--r--  1 quoha  staff  0 Sep 29 20:21 ./public/index.html
-rw-r--r--  1 quoha  staff  0 Sep 29 17:31 ./themes/zafta/layouts/index.html
$
```

#### The Magic of Static

Hugo does two things when generating the site. It uses templates to transform content into HTML and it copies static files into the site. Unlike content, static files are not transformed. They are copied exactly as they are.

Hugo assumes that your site will use both CSS and JavaScript, so it creates directories in your theme to hold them. Remember opinions? Well, Hugo's opinion is that you'll store your CSS in a directory named css/ and your JavaScript in a directory named js/. If you don't like that, you can change the directory names in your theme directory or even delete them completely. Hugo's nice enough to offer its opinion, then behave nicely if you disagree.

```
$ find themes/zafta -type d | xargs ls -ld
drwxr-xr-x  7 quoha  staff  238 Sep 29 17:38 themes/zafta
drwxr-xr-x  3 quoha  staff  102 Sep 29 17:31 themes/zafta/archetypes
drwxr-xr-x  5 quoha  staff  170 Sep 29 17:31 themes/zafta/layouts
drwxr-xr-x  4 quoha  staff  136 Sep 29 17:31 themes/zafta/layouts/_default
drwxr-xr-x  4 quoha  staff  136 Sep 29 17:31 themes/zafta/layouts/partials
drwxr-xr-x  4 quoha  staff  136 Sep 29 17:31 themes/zafta/static
drwxr-xr-x  2 quoha  staff   68 Sep 29 17:31 themes/zafta/static/css
drwxr-xr-x  2 quoha  staff   68 Sep 29 17:31 themes/zafta/static/js
$
```

## The Theme Development Cycle

When you're working on a theme, you will make changes in the theme's directory, rebuild the site, and check your changes in the browser. Hugo makes this very easy:

1. Purge the public/ directory.
2. Run the built in web server in watch mode.
3. Open your site in a browser.
4. Update the theme.
5. Glance at your browser window to see changes.
6. Return to step 4.

I’ll throw in one more opinion: never work on a theme on a live site. Always work on a copy of your site. Make changes to your theme, test them, then copy them up to your site. For added safety, use a tool like Git to keep a revision history of your content and your theme. Believe me when I say that it is too easy to lose both your mind and your changes.

Check the main Hugo site for information on using Git with Hugo.

### Purge the public/ Directory

When generating the site, Hugo will create new files and update existing ones in the `public/` directory. It will not delete files that are no longer used. For example, files that were created in the wrong directory or with the wrong title will remain. If you leave them, you might get confused by them later. I recommend cleaning out your site prior to generating it.

Note: If you're building on an SSD, you should ignore this. Churning on a SSD can be costly.

### Hugo's Watch Option

Hugo's "`--watch`" option will monitor the content/ and your theme directories for changes and rebuild the site automatically.

### Live Reload

Hugo's built in web server supports live reload. As pages are saved on the server, the browser is told to refresh the page. Usually, this happens faster than you can say, "Wow, that's totally amazing."

### Development Commands

Use the following commands as the basis for your workflow.

```
## purge old files. hugo will recreate the public directory.
##
$ rm -rf public
##
## run hugo in watch mode
##
$ hugo server --watch --verbose
```

Here's sample output showing Hugo detecting a change to the template for the home page. Once generated, the web browser automatically reloaded the page. I've said this before, it's amazing.

```
$ rm -rf public
$ hugo server --watch --verbose
INFO: 2014/09/29 Using config file: /Users/quoha/Sites/zafta/config.toml
INFO: 2014/09/29 syncing from /Users/quoha/Sites/zafta/themes/zafta/static/ to /Users/quoha/Sites/zafta/public/
INFO: 2014/09/29 syncing from /Users/quoha/Sites/zafta/static/ to /Users/quoha/Sites/zafta/public/
WARN: 2014/09/29 Unable to locate layout: [404.html theme/404.html]
0 draft content
0 future content
0 pages created
0 tags created
0 categories created
in 2 ms
Watching for changes in /Users/quoha/Sites/zafta/content
Serving pages from /Users/quoha/Sites/zafta/public
Web Server is available at http://localhost:1313
Press Ctrl+C to stop
INFO: 2014/09/29 File System Event: ["/Users/quoha/Sites/zafta/themes/zafta/layouts/index.html": MODIFY|ATTRIB]
Change detected, rebuilding site

WARN: 2014/09/29 Unable to locate layout: [404.html theme/404.html]
0 draft content
0 future content
0 pages created
0 tags created
0 categories created
in 1 ms
```

## Update the Home Page Template

The home page is one of a few special pages that Hugo creates automatically. As mentioned earlier, it looks for one of three files in the theme's layout/ directory:

1. index.html
2. \_default/list.html
3. \_default/single.html

We could update one of the default templates, but a good design decision is to update the most specific template available. That's not a hard and fast rule (in fact, we'll break it a few times in this tutorial), but it is a good generalization.

### Make a Static Home Page

Right now, that page is empty because we don't have any content and we don't have any logic in the template. Let's change that by adding some text to the template.

```
$ vi themes/zafta/layouts/index.html
<!DOCTYPE html>
<html>
<body>
  <p>hugo says hello!</p>
</body>
</html>
:wq

$
```

Build the web site and then verify the results.

```
$ hugo --verbose
INFO: 2014/09/29 Using config file: /Users/quoha/Sites/zafta/config.toml
INFO: 2014/09/29 syncing from /Users/quoha/Sites/zafta/themes/zafta/static/ to /Users/quoha/Sites/zafta/public/
INFO: 2014/09/29 syncing from /Users/quoha/Sites/zafta/static/ to /Users/quoha/Sites/zafta/public/
WARN: 2014/09/29 Unable to locate layout: [404.html theme/404.html]
0 draft content
0 future content
0 pages created
0 tags created
0 categories created
in 2 ms

$ find public -type f -name '*.html' | xargs ls -l
-rw-r--r--  1 quoha  staff  78 Sep 29 21:26 public/index.html

$ cat public/index.html
<!DOCTYPE html>
<html>
<body>
  <p>hugo says hello!</p>
</html>
```

#### Live Reload

Note: If you're running the server with the `--watch` option, you'll see different content in the file:

```
$ cat public/index.html
<!DOCTYPE html>
<html>
<body>
  <p>hugo says hello!</p>
<script>document.write('<script src="http://'
        + (location.host || 'localhost').split(':')[0]
    + ':1313/livereload.js?mindelay=10"></'
        + 'script>')</script></body>
</html>
```

When you use `--watch`, the Live Reload script is added by Hugo. Look for live reload in the documentation to see what it does and how to disable it.

### Build a "Dynamic" Home Page

"Dynamic home page?" Hugo's a static web site generator, so this seems an odd thing to say. I mean let's have the home page automatically reflect the content in the site every time Hugo builds it. We'll use iteration in the template to do that.

#### Create New Posts

Now that we have the home page generating static content, let's add some content to the site. We'll display these posts as a list on the home page and on their own page, too.

Hugo has a command to generate a skeleton post, just like it does for sites and themes.

```
$ hugo --verbose new post/first.md
INFO: 2014/09/29 Using config file: /Users/quoha/Sites/zafta/config.toml
INFO: 2014/09/29 attempting to create  post/first.md of post
INFO: 2014/09/29 curpath: /Users/quoha/Sites/zafta/themes/zafta/archetypes/default.md
ERROR: 2014/09/29 Unable to Cast <nil> to map[string]interface{}

$
```

That wasn't very nice, was it?

The "new" command uses an archetype to create the post file. Hugo created an empty default archetype file, but that causes an error when there's a theme. For me, the workaround was to create an archetypes file specifically for the post type.

```
$ vi themes/zafta/archetypes/post.md
+++
Description = ""
Tags = []
Categories = []
+++
:wq

$ find themes/zafta/archetypes -type f | xargs ls -l
-rw-r--r--  1 quoha  staff   0 Sep 29 21:53 themes/zafta/archetypes/default.md
-rw-r--r--  1 quoha  staff  51 Sep 29 21:54 themes/zafta/archetypes/post.md

$ hugo --verbose new post/first.md
INFO: 2014/09/29 Using config file: /Users/quoha/Sites/zafta/config.toml
INFO: 2014/09/29 attempting to create  post/first.md of post
INFO: 2014/09/29 curpath: /Users/quoha/Sites/zafta/themes/zafta/archetypes/post.md
INFO: 2014/09/29 creating /Users/quoha/Sites/zafta/content/post/first.md
/Users/quoha/Sites/zafta/content/post/first.md created

$ hugo --verbose new post/second.md
INFO: 2014/09/29 Using config file: /Users/quoha/Sites/zafta/config.toml
INFO: 2014/09/29 attempting to create  post/second.md of post
INFO: 2014/09/29 curpath: /Users/quoha/Sites/zafta/themes/zafta/archetypes/post.md
INFO: 2014/09/29 creating /Users/quoha/Sites/zafta/content/post/second.md
/Users/quoha/Sites/zafta/content/post/second.md created

$ ls -l content/post
total 16
-rw-r--r--  1 quoha  staff  104 Sep 29 21:54 first.md
-rw-r--r--  1 quoha  staff  105 Sep 29 21:57 second.md

$ cat content/post/first.md
+++
Categories = []
Description = ""
Tags = []
date = "2014-09-29T21:54:53-05:00"
title = "first"

+++
my first post

$ cat content/post/second.md
+++
Categories = []
Description = ""
Tags = []
date = "2014-09-29T21:57:09-05:00"
title = "second"

+++
my second post

$
```

Build the web site and then verify the results.

```
$ rm -rf public
$ hugo --verbose
INFO: 2014/09/29 Using config file: /Users/quoha/Sites/zafta/config.toml
INFO: 2014/09/29 syncing from /Users/quoha/Sites/zafta/themes/zafta/static/ to /Users/quoha/Sites/zafta/public/
INFO: 2014/09/29 syncing from /Users/quoha/Sites/zafta/static/ to /Users/quoha/Sites/zafta/public/
INFO: 2014/09/29 found taxonomies: map[string]string{"category":"categories", "tag":"tags"}
WARN: 2014/09/29 Unable to locate layout: [404.html theme/404.html]
0 draft content
0 future content
2 pages created
0 tags created
0 categories created
in 4 ms
$
```

The output says that it created 2 pages. Those are our new posts:

```
$ find public -type f -name '*.html' | xargs ls -l
-rw-r--r--  1 quoha  staff  78 Sep 29 22:13 public/index.html
-rw-r--r--  1 quoha  staff   0 Sep 29 22:13 public/post/first/index.html
-rw-r--r--  1 quoha  staff   0 Sep 29 22:13 public/post/index.html
-rw-r--r--  1 quoha  staff   0 Sep 29 22:13 public/post/second/index.html
$
```

The new files are empty because because the templates used to generate the content are empty. The homepage doesn't show the new content, either. We have to update the templates to add the posts.

### List and Single Templates

In Hugo, we have three major kinds of templates. There's the home page template that we updated previously. It is used only by the home page. We also have "single" templates which are used to generate output for a single content file. We also have "list" templates that are used to group multiple pieces of content before generating output.

Generally speaking, list templates are named "list.html" and single templates are named "single.html."

There are three other types of templates: partials, content views, and terms. We will not go into much detail on these.

### Add Content to the Homepage

The home page will contain a list of posts. Let's update its template to add the posts that we just created. The logic in the template will run every time we build the site.

```
$ vi themes/zafta/layouts/index.html
<!DOCTYPE html>
<html>
<body>
  {{ range first 10 .Data.Pages }}
    <h1>{{ .Title }}</h1>
  {{ end }}
</body>
</html>
:wq

$
```

Hugo uses the Go template engine. That engine scans the template files for commands which are enclosed between "{{" and "}}". In our template, the commands are:

1. range
2. .Title
3. end

The "range" command is an iterator. We're going to use it to go through the first ten pages. Every HTML file that Hugo creates is treated as a page, so looping through the list of pages will look at every file that will be created.

The ".Title" command prints the value of the "title" variable. Hugo pulls it from the front matter in the Markdown file.

The "end" command signals the end of the range iterator. The engine loops back to the top of the iteration when it finds "end." Everything between the "range" and "end" is evaluated every time the engine goes through the iteration. In this file, that would cause the title from the first ten pages to be output as heading level one.

It's helpful to remember that some variables, like .Data, are created before any output files. Hugo loads every content file into the variable and then gives the template a chance to process before creating the HTML files.

Build the web site and then verify the results.

```
$ rm -rf public
$ hugo --verbose
INFO: 2014/09/29 Using config file: /Users/quoha/Sites/zafta/config.toml
INFO: 2014/09/29 syncing from /Users/quoha/Sites/zafta/themes/zafta/static/ to /Users/quoha/Sites/zafta/public/
INFO: 2014/09/29 syncing from /Users/quoha/Sites/zafta/static/ to /Users/quoha/Sites/zafta/public/
INFO: 2014/09/29 found taxonomies: map[string]string{"tag":"tags", "category":"categories"}
WARN: 2014/09/29 Unable to locate layout: [404.html theme/404.html]
0 draft content
0 future content
2 pages created
0 tags created
0 categories created
in 4 ms
$ find public -type f -name '*.html' | xargs ls -l
-rw-r--r--  1 quoha  staff  94 Sep 29 22:23 public/index.html
-rw-r--r--  1 quoha  staff   0 Sep 29 22:23 public/post/first/index.html
-rw-r--r--  1 quoha  staff   0 Sep 29 22:23 public/post/index.html
-rw-r--r--  1 quoha  staff   0 Sep 29 22:23 public/post/second/index.html
$ cat public/index.html
<!DOCTYPE html>
<html>
<body>

    <h1>second</h1>

    <h1>first</h1>

</body>
</html>
$
```

Congratulations, the home page shows the title of the two posts. The posts themselves are still empty, but let's take a moment to appreciate what we've done. Your template now generates output dynamically. Believe it or not, by inserting the range command inside of those curly braces, you've learned everything you need to know to build a theme. All that's really left is understanding which template will be used to generate each content file and becoming familiar with the commands for the template engine.

And, if that were entirely true, this tutorial would be much shorter. There are a few things to know that will make creating a new template much easier. Don't worry, though, that's all to come.

### Add Content to the Posts

We're working with posts, which are in the content/post/ directory. That means that their section is "post" (and if we don't do something weird, their type is also "post").

Hugo uses the section and type to find the template file for every piece of content. Hugo will first look for a template file that matches the section or type name. If it can't find one, then it will look in the \_default/ directory. There are some twists that we'll cover when we get to categories and tags, but for now we can assume that Hugo will try post/single.html, then \_default/single.html.

Now that we know the search rule, let's see what we actually have available:

```
$ find themes/zafta -name single.html | xargs ls -l
-rw-r--r--  1 quoha  staff  132 Sep 29 17:31 themes/zafta/layouts/_default/single.html
```

We could create a new template, post/single.html, or change the default. Since we don't know of any other content types, let's start with updating the default.

Remember, any content that we haven't created a template for will end up using this template. That can be good or bad. Bad because I know that we're going to be adding different types of content and we're going to end up undoing some of the changes we've made. It's good because we'll be able to see immediate results. It's also good to start here because we can start to build the basic layout for the site. As we add more content types, we'll refactor this file and move logic around. Hugo makes that fairly painless, so we'll accept the cost and proceed.

Please see the Hugo documentation on template rendering for all the details on determining which template to use. And, as the docs mention, if you're building a single page application (SPA) web site, you can delete all of the other templates and work with just the default single page. That's a refreshing amount of joy right there.

#### Update the Template File

```
$ vi themes/zafta/layouts/_default/single.html
<!DOCTYPE html>
<html>
<head>
  <title>{{ .Title }}</title>
</head>
<body>
  <h1>{{ .Title }}</h1>
  {{ .Content }}
</body>
</html>
:wq

$
```

Build the web site and verify the results.

```
$ rm -rf public
$ hugo --verbose
INFO: 2014/09/29 Using config file: /Users/quoha/Sites/zafta/config.toml
INFO: 2014/09/29 syncing from /Users/quoha/Sites/zafta/themes/zafta/static/ to /Users/quoha/Sites/zafta/public/
INFO: 2014/09/29 syncing from /Users/quoha/Sites/zafta/static/ to /Users/quoha/Sites/zafta/public/
INFO: 2014/09/29 found taxonomies: map[string]string{"tag":"tags", "category":"categories"}
WARN: 2014/09/29 Unable to locate layout: [404.html theme/404.html]
0 draft content
0 future content
2 pages created
0 tags created
0 categories created
in 4 ms

$ find public -type f -name '*.html' | xargs ls -l
-rw-r--r--  1 quoha  staff   94 Sep 29 22:40 public/index.html
-rw-r--r--  1 quoha  staff  125 Sep 29 22:40 public/post/first/index.html
-rw-r--r--  1 quoha  staff    0 Sep 29 22:40 public/post/index.html
-rw-r--r--  1 quoha  staff  128 Sep 29 22:40 public/post/second/index.html

$ cat public/post/first/index.html
<!DOCTYPE html>
<html>
<head>
  <title>first</title>
</head>
<body>
  <h1>first</h1>
  <p>my first post</p>

</body>
</html>

$ cat public/post/second/index.html
<!DOCTYPE html>
<html>
<head>
  <title>second</title>
</head>
<body>
  <h1>second</h1>
  <p>my second post</p>

</body>
</html>
$
```

Notice that the posts now have content. You can go to localhost:1313/post/first to verify.

### Linking to Content

The posts are on the home page. Let's add a link from there to the post. Since this is the home page, we'll update its template.

```
$ vi themes/zafta/layouts/index.html
<!DOCTYPE html>
<html>
<body>
  {{ range first 10 .Data.Pages }}
    <h1><a href="{{ .Permalink }}">{{ .Title }}</a></h1>
  {{ end }}
</body>
</html>
```

Build the web site and verify the results.

```
$ rm -rf public
$ hugo --verbose
INFO: 2014/09/29 Using config file: /Users/quoha/Sites/zafta/config.toml
INFO: 2014/09/29 syncing from /Users/quoha/Sites/zafta/themes/zafta/static/ to /Users/quoha/Sites/zafta/public/
INFO: 2014/09/29 syncing from /Users/quoha/Sites/zafta/static/ to /Users/quoha/Sites/zafta/public/
INFO: 2014/09/29 found taxonomies: map[string]string{"tag":"tags", "category":"categories"}
WARN: 2014/09/29 Unable to locate layout: [404.html theme/404.html]
0 draft content
0 future content
2 pages created
0 tags created
0 categories created
in 4 ms

$ find public -type f -name '*.html' | xargs ls -l
-rw-r--r--  1 quoha  staff  149 Sep 29 22:44 public/index.html
-rw-r--r--  1 quoha  staff  125 Sep 29 22:44 public/post/first/index.html
-rw-r--r--  1 quoha  staff    0 Sep 29 22:44 public/post/index.html
-rw-r--r--  1 quoha  staff  128 Sep 29 22:44 public/post/second/index.html

$ cat public/index.html
<!DOCTYPE html>
<html>
<body>

    <h1><a href="/post/second/">second</a></h1>

    <h1><a href="/post/first/">first</a></h1>

</body>
</html>

$
```

### Create a Post Listing

We have the posts displaying on the home page and on their own page. We also have a file public/post/index.html that is empty. Let's make it show a list of all posts (not just the first ten).

We need to decide which template to update. This will be a listing, so it should be a list template. Let's take a quick look and see which list templates are available.

```
$ find themes/zafta -name list.html | xargs ls -l
-rw-r--r--  1 quoha  staff  0 Sep 29 17:31 themes/zafta/layouts/_default/list.html
```

As with the single post, we have to decide to update \_default/list.html or create post/list.html. We still don't have multiple content types, so let's stay consistent and update the default list template.

## Creating Top Level Pages

Let's add an "about" page and display it at the top level (as opposed to a sub-level like we did with posts).

The default in Hugo is to use the directory structure of the content/ directory to guide the location of the generated html in the public/ directory. Let's verify that by creating an "about" page at the top level:

```
$ vi content/about.md
+++
title = "about"
description = "about this site"
date = "2014-09-27"
slug = "about time"
+++

## about us

i'm speechless
:wq
```

Generate the web site and verify the results.

```
$ find public -name '*.html' | xargs ls -l
-rw-rw-r--  1 mdhender  staff   334 Sep 27 15:08 public/about-time/index.html
-rw-rw-r--  1 mdhender  staff   527 Sep 27 15:08 public/index.html
-rw-rw-r--  1 mdhender  staff   358 Sep 27 15:08 public/post/first-post/index.html
-rw-rw-r--  1 mdhender  staff     0 Sep 27 15:08 public/post/index.html
-rw-rw-r--  1 mdhender  staff   342 Sep 27 15:08 public/post/second-post/index.html
```

Notice that the page wasn't created at the top level. It was created in a sub-directory named 'about-time/'. That name came from our slug. Hugo will use the slug to name the generated content. It's a reasonable default, by the way, but we can learn a few things by fighting it for this file.

One other thing. Take a look at the home page.

```
$ cat public/index.html
<!DOCTYPE html>
<html>
<body>
    <h1><a href="http://localhost:1313/post/theme/">creating a new theme</a></h1>
    <h1><a href="http://localhost:1313/about-time/">about</a></h1>
    <h1><a href="http://localhost:1313/post/second-post/">second</a></h1>
    <h1><a href="http://localhost:1313/post/first-post/">first</a></h1>
<script>document.write('<script src="http://'
        + (location.host || 'localhost').split(':')[0]
		+ ':1313/livereload.js?mindelay=10"></'
        + 'script>')</script></body>
</html>
```

Notice that the "about" link is listed with the posts? That's not desirable, so let's change that first.

```
$ vi themes/zafta/layouts/index.html
<!DOCTYPE html>
<html>
<body>
  <h1>posts</h1>
  {{ range first 10 .Data.Pages }}
    {{ if eq .Type "post"}}
      <h2><a href="{{ .Permalink }}">{{ .Title }}</a></h2>
    {{ end }}
  {{ end }}

  <h1>pages</h1>
  {{ range .Data.Pages }}
    {{ if eq .Type "page" }}
      <h2><a href="{{ .Permalink }}">{{ .Title }}</a></h2>
    {{ end }}
  {{ end }}
</body>
</html>
:wq
```

Generate the web site and verify the results. The home page has two sections, posts and pages, and each section has the right set of headings and links in it.

But, that about page still renders to about-time/index.html.

```
$ find public -name '*.html' | xargs ls -l
-rw-rw-r--  1 mdhender  staff    334 Sep 27 15:33 public/about-time/index.html
-rw-rw-r--  1 mdhender  staff    645 Sep 27 15:33 public/index.html
-rw-rw-r--  1 mdhender  staff    358 Sep 27 15:33 public/post/first-post/index.html
-rw-rw-r--  1 mdhender  staff      0 Sep 27 15:33 public/post/index.html
-rw-rw-r--  1 mdhender  staff    342 Sep 27 15:33 public/post/second-post/index.html
```

Knowing that hugo is using the slug to generate the file name, the simplest solution is to change the slug. Let's do it the hard way and change the permalink in the configuration file.

```
$ vi config.toml
[permalinks]
	page = "/:title/"
	about = "/:filename/"
```

Generate the web site and verify that this didn't work. Hugo lets "slug" or "URL" override the permalinks setting in the configuration file. Go ahead and comment out the slug in content/about.md, then generate the web site to get it to be created in the right place.

## Sharing Templates

If you've been following along, you probably noticed that posts have titles in the browser and the home page doesn't. That's because we didn't put the title in the home page's template (layouts/index.html). That's an easy thing to do, but let's look at a different option.

We can put the common bits into a shared template that's stored in the themes/zafta/layouts/partials/ directory.

### Create the Header and Footer Partials

In Hugo, a partial is a sugar-coated template. Normally a template reference has a path specified. Partials are different. Hugo searches for them along a TODO defined search path. This makes it easier for end-users to override the theme's presentation.

```
$ vi themes/zafta/layouts/partials/header.html
<!DOCTYPE html>
<html>
<head>
	<title>{{ .Title }}</title>
</head>
<body>
:wq

$ vi themes/zafta/layouts/partials/footer.html
</body>
</html>
:wq
```

### Update the Home Page Template to Use the Partials

The most noticeable difference between a template call and a partials call is the lack of path:

```
{{ template "theme/partials/header.html" . }}
```

versus

```
{{ partial "header.html" . }}
```

Both pass in the context.

Let's change the home page template to use these new partials.

```
$ vi themes/zafta/layouts/index.html
{{ partial "header.html" . }}

  <h1>posts</h1>
  {{ range first 10 .Data.Pages }}
    {{ if eq .Type "post"}}
      <h2><a href="{{ .Permalink }}">{{ .Title }}</a></h2>
    {{ end }}
  {{ end }}

  <h1>pages</h1>
  {{ range .Data.Pages }}
    {{ if or (eq .Type "page") (eq .Type "about") }}
      <h2><a href="{{ .Permalink }}">{{ .Type }} - {{ .Title }} - {{ .RelPermalink }}</a></h2>
    {{ end }}
  {{ end }}

{{ partial "footer.html" . }}
:wq
```

Generate the web site and verify the results. The title on the home page is now "your title here", which comes from the "title" variable in the config.toml file.

### Update the Default Single Template to Use the Partials

```
$ vi themes/zafta/layouts/_default/single.html
{{ partial "header.html" . }}

  <h1>{{ .Title }}</h1>
  {{ .Content }}

{{ partial "footer.html" . }}
:wq
```

Generate the web site and verify the results. The title on the posts and the about page should both reflect the value in the markdown file.

## Add “Date Published” to Posts

It's common to have posts display the date that they were written or published, so let's add that. The front matter of our posts has a variable named "date." It's usually the date the content was created, but let's pretend that's the value we want to display.

### Add “Date Published” to the Template

We'll start by updating the template used to render the posts. The template code will look like:

```
{{ .Date.Format "Mon, Jan 2, 2006" }}
```

Posts use the default single template, so we'll change that file.

```
$ vi themes/zafta/layouts/_default/single.html
{{ partial "header.html" . }}

  <h1>{{ .Title }}</h1>
  <h2>{{ .Date.Format "Mon, Jan 2, 2006" }}</h2>
  {{ .Content }}

{{ partial "footer.html" . }}
:wq
```

Generate the web site and verify the results. The posts now have the date displayed in them. There's a problem, though. The "about" page also has the date displayed.

As usual, there are a couple of ways to make the date display only on posts. We could do an "if" statement like we did on the home page. Another way would be to create a separate template for posts.

The "if" solution works for sites that have just a couple of content types. It aligns with the principle of "code for today," too.

Let's assume, though, that we've made our site so complex that we feel we have to create a new template type. In Hugo-speak, we're going to create a section template.

Let's restore the default single template before we forget.

```
$ mkdir themes/zafta/layouts/post
$ vi themes/zafta/layouts/_default/single.html
{{ partial "header.html" . }}

  <h1>{{ .Title }}</h1>
  {{ .Content }}

{{ partial "footer.html" . }}
:wq
```

Now we'll update the post's version of the single template. If you remember Hugo's rules, the template engine will use this version over the default.

```
$ vi themes/zafta/layouts/post/single.html
{{ partial "header.html" . }}

  <h1>{{ .Title }}</h1>
  <h2>{{ .Date.Format "Mon, Jan 2, 2006" }}</h2>
  {{ .Content }}

{{ partial "footer.html" . }}
:wq

```

Note that we removed the date logic from the default template and put it in the post template. Generate the web site and verify the results. Posts have dates and the about page doesn't.

### Don't Repeat Yourself

DRY is a good design goal and Hugo does a great job supporting it. Part of the art of a good template is knowing when to add a new template and when to update an existing one. While you're figuring that out, accept that you'll be doing some refactoring. Hugo makes that easy and fast, so it's okay to delay splitting up a template.










당신은 열린 마음을 가진 진보주의자입니까? 아닐 수도 있지만, 아마 친구들도 있을 거예요. 이것은 그들을 위한 것이다. 아마도 이 낯선 블로그, '무자격 예약'을 소개하는 일종의 역할을 할 수 있을 것 같습니다.

개방적인 진보주의자라면 아마 카톨릭 신자는 아닐 것이다. (만약 그렇다면 교황을 너무 심각하게 받아들이지 않을 것이다.) 가톨릭 신자들에게 공개 편지를 써서, 로마가 가진 음흉한 지배로부터 마음을 해방시킬 수 있는 방법을 제안하는 것을 상상해 보라. 그런 종류의 일은 요즘 유행이 좀 지나치네요. 어떤 경우든, 어떻게 시작하겠어요? 하지만 이곳 UR에서는 유행을 타지 않는 것을 결코 두려워하지 않는다. 그리고 시작에 관해서는, 우리는 이미 알고 있다.

진보주의자가 되는 것은 가톨릭 신자가 되는 것과 같은 것인가? 왜 그러면 안 되는 거죠? 각각은 일련의 믿음을 통해 세상을 이해하는 방법이다. 이 믿음들은 사실일 수도 있고, 거짓일 수도 있고, 거짓일 만큼 충분히 이치에 맞지 않는 헛소리일 수도 있다. 개방적인 진보주의자(혹은 개방적인 가톨릭 신자)로서, 당신은 당신이 가지고 있는 모든 신념이 진실이라고 생각하고 싶지만, 아마도 약간의 온화한 도움과 함께 그것들을 재평가할 용의가 있다.

가톨릭과 진보주의 사이에는 다음과 같은 큰 차이가 있다. 천주교는 이른바 '종교'다. 그것의 핵심 신념은 정신 세계에 대한 주장인데, 이 주장은 가톨릭 신자(물론 교황을 제외하고)가 직접 경험한 적이 없다. 반면에 진보적인 믿음은 현실 세계, 즉 정부, 역사, 경제, 사회에 대한 주장인 경향이 있다. 이것들은, 성 삼위일체와는 달리, 우리 모두가 직접 경험하는 현상들입니다.

아니면 우리가? 우리 대부분은 정부를 위해 일한 적이 없고, 정부의 아주 작은 구석만 본 사람들이다. 역사는 책에서 나온 것이다. 그것은 성경은 아니지만, 차라리 그럴 수도 있다. 우리의 경제학에 대한 개인적인 경험은 무엇인가? 휘발유 가격? 등등. 당신의 삶이 길고 상당히 특이한 것이 아니라면, 나는 당신의 기억이 정부, 역사 등의 위대한 의문들을 거의 밝혀내지 못했다고 생각한다. 제 것은 확실히 그렇지 않아요.

물론 진보적 사상의 상당 부분은 순수한 이성의 산물이라고 주장하고 있다. 그런가? 토마스 아퀴나스는 순수한 이성으로부터 카톨릭을 파생시켰다. 존 롤스는 순수 이성으로부터 진보주의를 이끌어냈다. 적어도 그들 중 한 명은 실수를 했을 것이다. 아마 둘 다 그랬을 거야. 그들의 작업을 확인해 보셨나요? 한 가지 나쁜 변수가 당신의 모든 증거를 무너뜨릴 것이다.

그리고 정말 이렇게 된 것일까. 당신은 아무것도 믿지 않는 데서 출발했기 때문에 진보주의자인가("우리는 허무주의자다! 우리는 아무것도 믿지 않는다!")고 생각하고, 진보주의자를 감쌌다. 물론 나는 당신 자신의 경험을 대변할 수는 없지만, 당신 부모가 진보주의자였기 때문에 진보주의자이거나, 아니면 어떤 책이나 교사, 또는 다른 지적 경험에 의해 전향된 사람일 것이라고 의심한다. 주목하라 이것이 바로 카톨릭 신자가 되는 방법이다.

하지만 한가지 차이점이 있다. 카톨릭 신자가 되려면 믿음을 가져야 해, 아무도 성령을 본 적이 없으니까. 진보주의자가 되기 위해서는 신뢰가 있어야 합니다. 왜냐하면 여러분은 자신의 세계관이 실제 세계를 정확하게 반영한다고 믿기 때문입니다. 자신의 작은 눈이 아니라, 인류 전체에 의해 경험됩니다.

하지만 당신은 인류의 경험을 공유하지 않았습니다. 당신은 단지 그것으로부터 편집된 텍스트, 오디오, 비디오의 말뭉치를 읽고 듣고 보았을 뿐이다. 누구에 의해 편찬되었나요? 거기서 신뢰가 생기는 거야 이것에 대해 조금 더 자세히 말해 주세요.

나는 진보주의자는 아니지만, 나는 진보주의자로 자랐다. 저는 샌프란시스코에 살고 있고, 외무부에서 일하며 자랐고, 브라운에 갔고, 80년대 중반부터 메인 주의 톰과 양치질을 하고 있습니다. 나에게 일어난 일은 신뢰를 잃었다는 것이다.

데이비드 마멧도 신뢰를 잃었다. 그의 빌리지 보이스 에세이는 세계에서 가장 유명한 극작가가 더 이상 '뇌사적 진보주의자'가 아니라고 선언하는 충격적 가치만 보더라도 읽을 만하다. 그 기사에 달린 댓글은 500여 개다. 아마 하나를 놓친 모양인데, 마메트가 눈을 떴다는 평론가의 주장은 전혀 눈치채지 못했다.

물론 마메트는 마메다. 그는 충격에 빠진 것이지 개종한 것이 아니다. 적어도 오늘날 정치적 설득을 지칭하는 것처럼 자유주의라는 단어조차 혐오발언에 접해 있다. 그것은 마치 카톨릭 출신자가 "왜 나는 더 이상 뇌사적인 파피스트가 아니다"라고 설명하는 것과 같다. 존 스튜어트 밀은 자유주의자였다. 버락 오바마는 진보주의자이고, 너도 진보주의자이다. 공손찬의 기본 원칙: 사람들이 스스로 부르지 않는 이름을 부르지 말라.

더욱이 마메트는 진보주의만 배척하는 것이 아니다. 그는 보수주의를 지지한다. 하느님 맙소사! 문제를 더 어렵게 만드는 것에 대해 이야기하세요. 여러분이 모든 사람이 가톨릭 신자와 힌두교 신자의 두 가지 중 하나인 나라에 산다고 상상해보세요. 사람의 마음을 로마의 음흉한 손아귀에서 해방시킬 만큼 힘들지 않은가. 칼리, 크리슈나, 가네샤를 동시에 받아들여야 하는 것일까.

예를 들어, 마메트는 보수적인 작가 토마스 소웰을 지지하는데, 그는 "우리 현대 최고의 철학자"라고 주장한다. 글쎄. 나는 토마스 소웰을 좋아해, 그의 작품은 확실히 가치가 없는 것은 아니지만, 정말이야. 구글을 검색하면 그의 칼럼이 townhall.com이라는 보수적인 웹사이트에 자주 뜨는 것을 볼 수 있을 것이다.

해당 링크를 클릭합니다. 잔혹한 그래픽 디자인을 관찰하십시오. (오바마의 나머지 그래픽 디자인이 얼마나 위에 있는지 알아챘습니까? 일부 글꼴 설계자는 다음을 수행합니다.) 폭스뉴스를 연상시킬 정도로 일반적인 참혹함을 관찰하라. 그리고 "뒤로"를 쳤다. 아니면, 나는, 앤 콜터 칼럼을 읽거나, 뭐 그런 것을 읽거나. 친애하는 주여.

저는 진보주의자는 아니지만 보수주의자도 아닙니다. (꼭 아셔야 한다면 저는 자코바이트입니다. 시간이 지나면서 저는 미국 보수주의 사상을 처리할 수 있는 능력을 갖게 되었습니다. 일반적으로 폭스뉴스나 townhall.com에서 다소 높은 시장성을 갖게 되었습니다. "맛"이 단어라도 된다면, 이것은 극도로 습득된 맛이다. 아마 버락 오바마가 목사를 다루는 방식과 매우 비슷할 것이다. 라이트의 더 다채로운 설교. 데이비드 마멧이 독자들에게 townhall.com의 일반적인 방향을 가리킬 때, 이것은 약간 공포스러운 삼촌에게 120일간의 소돔이라고 불리는 이 훌륭한 영화를 보고 동성애자 권리의 가치를 이해할 수 있다고 설명하는 것과 같습니다. 실제 소통이 아닙니다. 씨발 너야. 마메다.

그런데 많은 분들이 정확하게 이런 생각을 하실 텐데 진보성향을 그만두면 보수성향이 강해져야 된다. 저는 대부분의 진보진영의 1차적인 정서적 동기는 보수에 대해서 뭔가 조치가 필요하다고 생각하기 때문에 진보진영이라고 하는 것이 아닌가 하는 의심이 듭니다. 게임 오버. 거터볼. 음흉한 손아귀로 바로 돌아온다.

NPR이 틀렸다면 폭스뉴스가 옳아야 한다는 생각은 어디서 나온 것일까? 서로 모순되기 때문에 둘 다 옳을 수는 없다. 하지만 둘 다 틀릴 수는 없을까? 나는 약간 틀렸다는 뜻이 아니라, 각각 반은 옳고 반은 틀렸다는 뜻이 아니다. 진실은 그들 사이에 있다는 뜻이 아니다. 나는 그들 둘 다 현실과 일관된 관계를 가지고 있지 않다.

이 점에 대해서는 잠시 생각해 보자. 진보주의자로서, 여러분은 보수가 집단 망상이라고 믿어야 합니다. 정말 기상천외한 일이군요! 백만이 넘는 사람들, 많은 사람들은 꽤 둔하지만 눈에 띄게 지능이 뛰어난 사람들, 모두 일종의 집단 최면상태에서 행동한다. 우리는 이것을 당연하게 여긴다. 우리는 그것에 익숙하다. 하지만 우리는 그것이 정말, 정말 이상하다는 것을 인정해야만 합니다.

믿어야 할 것은 보수층이 조직적으로 잘못 알려졌다는 겁니다. 그들은 바보가 아니다. 적어도 그들 모두가 바보는 아니다. 악한 것도 아니다. 당신은 당신이 원하는 모든 시간을 townhall.com에서 보낼 수 있으며, 세상을 노예화하고 파괴하려는 그들의 사악한 계획에 골룸처럼 꼬꼬댁거리는 사람을 발견하지 못할 것이다. 그들은 모두 여러분과 마찬가지로 보수주의자가 됨으로써 달콤하고 선한 것과 진실한 것을 옹호한다고 생각합니다.

보수주의(保守主義, )는 정부의 개인적 경험이 없는 다수의 사람들이 갖고 있는 정부론이다. 그들은 폭스 뉴스, townhall.com, 그리고 그들의 지역 메가처치와 같이 그들이 선택한 정보원이 이론을 지지하고, 강화시키고, 확인하는 경향이 있는 (그리고 아마도 몇몇 비사실적) 사실의 꾸준한 식단을 먹여주기 때문에 이 이론을 보유하고 있다.

그리고 왜 이런 이상한 패턴이 존재하는 것일까? 왜냐하면 보수는 그냥 보통 의견이 아니거든요. 정부론 대신 보수가 농구론이었다고 가정해 보자. "보수주의"는 농구 선수와 코치들에게 중요한 선택과 롤, 외곽 경기, 삼각 수비 그리고 다른 이슈들에 대한 관점의 체계일 것이다.

분명한 차이점은, 여러분이 농구 코치가 아니라면, 농구 문제에 대한 여러분의 의견은 전혀 그렇지 않다는 것입니다. 왜냐하면 농구는 민주주의가 아니기 때문입니다. 선수들은 팬은커녕 투표조차 받지 못한다. 그러나 보수주의는 조직적인 망상의 패턴을 유지할 수 있는데, 왜냐하면 보수주의의 팬들은 단순한 팬이 아니기 때문이다: 그들은 정치 기계의 지지자들이다. 이 기계는 신자를 지키지 못하면 없어질 것이므로 신자를 지킬 동기가 생긴다. 그리고 그것은 그렇다. 그게 어떻게 작동하는지 웃기네

진보주의자로서 미국의 민주주의를 보는 방법은 이렇습니다. 진실과 이성이 선전, 무지, 오보 위에 세워진 준범죄적 정치 기계와 대결하는 경합입니다. 아마 세상을 냉소적으로 보는 시각일 텐데 진보주의가 옳다고 믿는다면 보수가 잘못됐다고 믿을 수밖에 없고, 다른 선택의 여지가 없습니다.

그러나 더욱 비관적인 견해가 있다. 미국의 민주주의가 진리와 이성의 경합이 아니라 두 개의 퀘이사의 정치 기계 사이의 경합이라고 가정해 보자. 진보주의가 보수와 똑같다고 가정해 보자. 만약 그랬다면, 누가 당신에게 말하겠어요?

보수주의를 일종의 정신질환으로 생각하라. 모기가 말라리아를 전염시키면서 폭스뉴스가 전염시킨 바이러스 X는 미국 인구의 절반의 뇌를 감염시켜 조지 W. 부시가 "정규적인 사람"이라고 믿게 만들었다. 지구 온난화는 일어나지 않고 있으며, 미군은 사드르 시에 민주주의를 가져다 줄 수 있다. 다행히도 미국의 나머지 절반은 진보적인 항체로 보호되고 있는데, 이 항체는 매일 타임즈와 NPR의 건강한 모유에 스며들어 진실의 달콤한 빛에 안전하게 노출될 수 있게 한다.

아니면 그런가요? 참고로 방금 두 종류의 실체를 가정해 보았습니다. 바이러스와 항체, 모기, 모유입니다. 오컴의 윌리엄은 행복하지 않을 것이다. 우리가 Y 바이러스를 다루고 있다고 상상하는 것이 더 간단하지 않은가? 한 세트의 사람들이 감염되고 다른 한 세트는 면역되는 것이 아니라, 모든 사람들이 감염됩니다. 단지 다른 변종들로 말이죠.

X바이러스를 바이러스로 만드는 것은 죠스의 상어처럼 이것의 인생의 유일한 목표는 먹고 헤엄치고 아기 바이러스를 만드는 것이다. 즉, 그 특징은 적응적으로 가장 잘 설명되어 있다. 현실을 정확하게 대변해 성공할 수 있다면 그렇게 할 것이다. 예를 들어 당신과 나 그리고 바이러스 X는 국제 유태인 음모의 주제에 대해 동의한다: 그런 것은 없다. 우리는 요즘 다행히도 희귀한 사악한 바이러스 N에 동의하지 않는다. 이것은 여러 가지로 설명될 수 있지만, 가장 간단한 것 중 하나는 폭스 뉴스가 스와스티카를 로고에 꽂고 빌 오라일리에게 시온 장로들에 대해 호통을 치기 시작하라고 한다면, 시청률은 아마 떨어질 것이다.

'현실과 일관된 관계가 없다'는 말은 이렇다. 어떤 이유에서든 간에, 진실이 아닌 보수적 정신 안에서 오류를 재현하는 것이 더 낫다면, 보수주의자들은 그 오류를 믿게 될 것이다. 진리가 좀 더 순응적이면 그들은 진리를 믿게 될 것이다. 어떻게 오류가 진실보다 더 나은 이야기를 만들 수 있는지 폭스뉴스에서 보는 것은 꽤 쉽다. 그래서 그 근원에서 진실을 얻는 것은 잘못된 것이다.

그래서 의심을 향한 우리의 첫걸음은 쉽습니다. 우리는 단지 진보주의자들이 신뢰하는 기관들이 같은 방식으로 잘못될 수 있다는 것을 스스로에게 의심하게 합니다. 만약 NPR이 폭스뉴스와 마찬가지로 오류를 재현할 수 있다면, 우리는 실제로 Y 바이러스를 보고 있는 것이다. 바이러스 X가 틀렸을 때 바이러스 Y가 옳을 수도 있고, 바이러스 X가 옳을 때 옳을 수도 있고, 바이러스 X가 틀렸을 때 그른 것일 수도 있다. 두 사람은 현실과 일관된 관계가 없기 때문에 서로 일관된 관계가 없다.

이 이론에는 유혹적인 대칭성이 있습니다. 그것은 (세계적이고 역사적인 기준에 의해) 다른 반쪽이 다른 반쪽과 그다지 다르지 않은 것처럼 보이는 사회의 반쪽이 어떻게 조직적으로 현혹될 수 있는가에 대한 문제를 해결합니다. 반면 나머지 반쪽은 꽤 제정신입니다. 답: 그렇지 않다.

게다가, 그것은 마멧의 작품에서 아름답게 나타나는 기괴한 모순을 설명한다. 어느 순간 그는 새로운 보수적인 인물로서 다음과 같이 쓰고 있다.

>정부의 역할은? 음, 추상적으로, 제 시대와 배경에서, 저는 그것이 다소 좋은 것이라고 생각했지만, 제게 영향을 미치는 것들과 제가 관찰하는 것들의 원장을 정리하면서, 저는 정부의 개입이 슬픔을 훨씬 넘어섰던 사례를 보기 힘들어요.

하지만 아까 그는 우리에게 다음과 같이 말했다.

>60년대의 어린 시절, 나는 정부가 부패하고, 사업이 착취적이며, 일반적으로 사람들은 마음씨가 좋다는 신앙의 기사로 받아들였다.

좋아요, 데이브. 60년대의 어린 시절, 당신은 정부가 나쁘다는 믿음의 기사로 받아들였지만, 지금은 정부가 나쁘다고 믿는군요? 다마스쿠스로 가는 길에 누가 도넛을 해?

오늘날 미국 정치의 매혹적인 사실 중 하나는 진보와 보수 모두 그들의 정부를 싫어한다는 것이다. 그들은 단지 그것의 다른 부분들을 싫어하고, 다른 부분들을 사랑하고 소중히 여깁니다. 예를 들어 외교정책에서는 진보주의자들이 국방부를 싫어하고, 국무부를 사랑하고 아끼고 있다. 보수주의자들은 국무부를 싫어하고, 국방부를 사랑하고 소중히 여긴다.

이것이 우리의 바이러스 X-Y 이론과 얼마나 잘 들어맞는지를 보라. 워싱턴에는 많은 저택이 있는데, 그 중 일부는 바이러스 X 기계의 일부이고, 다른 일부는 바이러스 Y에 의해 영구히 감염되어 있다. 벨트웨이 밖에는 침을 흘리고 바이러스에 감염된 좀비 유권자들의 무리가 있다. X 좀비들은 Y 기관을 싫어하고, Y 좀비들은 X 기관을 싫어한다.

그러나 그들 중 누구도 워싱턴 전체를 싫어하지는 않는다. 그래서 그들은 결코 연합하여 그것을 파괴할 수 없으며, 기계 전체가 안정되어 있다. 이게 얼마나 아름다운지 보여? 유권자들을 경쟁적이지만 협력적인 두 정당으로 분리시킴으로써, 둘 중 어느 쪽도 상대방을 파괴할 수 없는 양당 체제는 국민들이 얼마나 더 행복할지라도 무한정 살아남을 정부를 만들어낸다.

이것이 우리의 미스터리의 끝에 있는 상이다. 보수화하지 않고 진보화를 그만둘 수 있는 길을 찾을 수 있다면 실제로 정부를 반대하는 방법까지 찾을 수도 있을 것이다. 최소한, 여러분은 이 정치인들, 운동, 기관들 중 어느 것도 여러분의 지지를 받을 만한 가치가 없다고 결정할 수 있습니다. 날 믿어. 그건 아주 해방된 기분이야.

하지만 우리는 아직 그 근처 어디에도 없다. 우리는 실제로 진보주의를 의심할 만한 진정한 이유를 찾지 못했다. 사소한 실수들-타임스든 뭐든 간에 약간의 팩트체크 실수들은 계산에 넣지 마세요. 왜냐하면 진보주의는 기본적으로 옳고 보수주의는 기본적으로 틀리다는 당신의 확신에 대해 아무것도 하지 않기 때문입니다. 몇 가지 별난 점이 있더라도 보수의 치유책으로서의 진보주의는 지킬 가치가 있다. 항체가 아닐 수도 있지만, 아마도 Y 바이러스는 적어도 백신일 것이다.

게다가 진보와 보수 운동 사이의 몇 가지 주요한 비대칭성을 간과해 왔습니다. 그들은 서로의 사악한 쌍둥이가 아니다. 그것들은 매우 다른 것이다. 하나는 신용할 수 있고 다른 하나는 신용하지 않을 것이라는 것은 꽤 그럴듯하며, 그 장점들은 모두 진보적인 쪽에 있는 것처럼 보인다.

먼저 진보진영 인사들부터 살펴보겠습니다. 청색국가, 적색국가라는 표현이 말해주듯 오늘날 미국의 진보와 보수는 다른 종족이다. 그들은 무작위로 배포된 의견이 아니다. 그들은 명확한 패턴을 따른다.

제 아내와 저는 몇 주 전에 딸이 하나 있었는데, 그녀가 퇴원하기로 되어 있기 직전에 의사들은 UCSF의 소아 심장학 책임자의 간단한 방문을 필요로 하는 경미한 심장 질환을 발견했습니다. 아주 유쾌한 사람. 그리고 그가 처음으로 한 말 중 하나는, 그의 침상 태도의 일부, 즉 우리를 편안하게 하는 방법이었는데, 조지 W. 부시 대통령에 대한 발언이었다. 어쩐지 그가 우리를 스톡턴에서 온 힉스라고 진단했다면 이런 소음을 내지 않았을 거라는 의심이 든다.

오히려 그 선량한 의사는 우리를 스터프 화이트 피플 라이크 부족의 일원으로 지목했었다.[^1] 이 작은 풍자 사이트는 대략 10분의 1의 시간에 UR의 100배의 교통량을 끌어 모았습니다. 이것은 무언가에 연결되어 있다는 꽤 확실한 신호입니다. 작가 크리스 랜더는 딱 한 가지 농담이 있습니다. 그는 묘사되는 것을 좋아하지 않는 그룹을 묘사하고 있습니다. 그리고 그는 그들이 스스로 선택할 성을 그들에게 할당했습니다.

버닝맨을 다녀온 사람이라면 누구나 증언할 수 있듯이 랜더의 '백인들'은 실로 압도적으로 백인이다. 그러나 동양인, 심지어는 흑인이나 라틴계인 '백인'들도 얼마든지 있다. 사실, 랜더가 지적하듯이, "백인"은 인종 차별주의자와 정반대이다. 그들은 소수민족을 갖고 싶어 안달이다. 그리하여 그들을 "백인"이라고 부르는 유머가 생겨났다. 사실, 통합 고등학교에 다닌 사람이라면 누구나 증언할 수 있듯이, 랜더가 "흰색"이라는 단어를 사용한 것은 거의 미국의 흑인용어인 "너무 하얀색"과 거의 똑같다. "빵"이라는 단어를 추가하면 당신은 그것을 내려놓을 것이다.

이 이상한 사람들은 누구입니까? 간략하게, 그들은 미국의 지배계급이다. 여기 UR에서는 브라만이라고 부릅니다. 브라만 부족은 세습이라기보다는 양자로 되어 있다. 누구나 브라만이 될 수 있고, 사실 당신의 배경이 덜 "백인"수록 더 좋다. 왜냐하면 그것은 당신의 업적이 모두 당신 자신의 것이라는 것을 의미하기 때문이다. 힌두교 원문과 마찬가지로 브라만으로서의 당신의 지위는 돈의 기능이 아니라 학자, 과학자, 예술가, 혹은 공무원으로서의 당신의 성공에 대한 것입니다. 브라만은 정신으로 일하는 사람들이다.

브라만은 말 그대로 통치하는 민족이기 때문에 지배계급이다. 현대 민주주의 체제에서 공공 정책은 일반적으로 브라만들에 의해 공식화되는데, 일반적으로 이 "백인"들이 모이는 것을 좋아하는 NGO에서이다. 그리고 모든 진보주의자가 브라만이고 모든 브라만이 진보주의자는 아니지만, 일반적으로 그 방정식이 따른다.

가장 중요한 것은 브라만의 정체성이 미국 대학 시스템과 불가분의 관계에 있다는 점이다. 만약 당신이 브라만이라면 당신의 지위는 학문적 성공에 의해서, 혹은 어떤 준학파적 성취에 의해서, 책 한 권을 쓰고 지구를 구하는 일 등 어느 정도 부여된다. 그러므로 대부분의 브라만들이 상당히 지적이고 세련되었다는 것은 놀랄 일이 아니다. 그들은 그래야만 한다. 적어도 가짜만은 못 꾸며낸다면 브라만이 아니다.

브라만의 천적은 물론 붉은 주 미국인이다. 나는 이 부족에 다른 힌두 카스트 이름인 '바이지사스'를 사용하곤 했지만, 그들을 타운리스라고 부르는 것이 더 발현적인 것 같다.[^2]진보주의자로서 여러분은 아마도 브라만일 것입니다. 여러분은 이 사람들을 알고 있고, 그들을 좋아하지 않습니다. 뚱뚱하고, 배타적으로 백인이고, 교외에 살거나, 참나무와 크로셰와 미니밴에 빠져있으며, 물론 공화당원인 경향이 있다. 아예 대학에 진학했다면 신입생 다양성 요건을 뚫고 이를 악물었다. 그리고 그들의 작품은 화이트칼라일지는 몰라도 실제 지적 내용은 없다.

(이 부족의 렌즈를 통해 한번 보면 미국 정치가 얼마나 더 단순해지는지가 흥미롭다. 여러분은 제3세계 국가들에서 종종 이것을 볼 수 있습니다. 말하자면, 앙골라 인민 운동과 민주 앙골라 전선이 있을 것입니다. 각자 앙골라 전체 국민의 미래를 위해 일한다고 아래위로 맹세할 것이다. 하지만 APM의 모든 사람들은 오밤보이고, DAF의 모든 사람들은 바콩고라는 것을 알 수 있습니다.)

브라만과 타운시의 지위 관계는 명확하다: 브라만은 더 높고, 타운은 더 낮다. 브라만이 타운시를 싫어할 때 그 태도는 경멸이다. 타운에이들이 브라만을 미워할 때 그 태도는 원한이다. 그 둘은 혼동할 수 없다. 브라만족과 타운족이 층층화된 사투리를 공유한다면, 브라만족은 곡예를 말하고 타운족은 음모를 쓴다.

즉, 브라만이 타운리보다 더 유행하는 것이다. 기본적으로 더 좋은 취향인 브라만의 취향은 타운시를 향해 아래로 흐른다. 20년 전만 해도 '건강식품'은 틈새의 초브라만 기행이었다. 이제 사방에 널려 있다. 교외 거주민들은 에스프레소를 마시고, Whole Foods에서 쇼핑하고, 얼터너티브 록을 듣고, 이름을 대세요.

그래서 우리는 왜 진보주의가 보수주의보다 더 유행하는지 알게 된다. 예를 들어 진보적인 유명인사들은 어디에나 있다. 보수적인 것은 예외다. 비록 많은 진보적인 유명인사들이 그들의 신념에 성실한 것처럼 보이지만, 냉정한 계산은 충분할 것이다. 보노의 홍보 담당자들은 그가 에이즈에 대해 목소리를 높인 것에 대해 기뻐하고 있다. 멜 깁슨의 홍보 담당자들은 그가 유대인들에게 목소리를 높인 것에 대해 기뻐하지 않는다.

그래서 보수에 대해서 의문을 제기할 때 우리 부족 사람들에게 자연스럽고 분별력 있는 사고방식, 즉 적을 공격하고 있는 것입니다. 그리고 적은 실로 만만한 상대다. 사실 적은 의심스러울 정도로 쉽게 밀어 넘어간다.

보수의 전체 라이프사이클을 보세요. 그 모든 것이 악취를 풍긴다. 바이러스 X는 교육을 받지 못한, 일반적으로 지능이 떨어지는 사람들의 마음속에서 복제한다. 사실 타운티족은 히틀러와 무솔리니를 우리에게 준 바로 그 기본 부족이다. 그것들과 같은 그것의 지적 기관들은 별난 재벌들에 의해 지원된 보조된 프린지 신문, TV 채널, 그리고 이상한 싱크탱크들이다. 정부에서 보수의 보루는 사람을 죽이는 것이 목적인 군대, 예를 들어 기업의 로비스트들이 환경을 강간함으로써 책임을 질 수 있는 기관이다.

반면에, 바이러스 Y는 "바이러스"가 정말로 그것의 이름이라면, 미국에서 가장 저명한 서클들, 실제로 세계에서 가장 유명한 것들, 즉 일류 대학들, 위대한 신문들, 록펠러, 카네기, 포드 같은 오래된 재단들에서 복제된다. 침 흘리는 좀비들은 이 나라에서 가장 똑똑하고 가장 성공한 사람들입니다. 정부에서는 세계 평화를 구축하고, 환경을 보호하며, 가난한 사람들을 돌보고, 아이들을 교육한다.

문제의 진실은 진보주의가 미국의 주류 전통이라는 것이다. 이것은 그것이 지난 200년 동안, 심지어 지난 50년 동안 변하지 않았다는 것을 말하는 것이 아닙니다. 그러나 나라살이를 하는 동안 하버드에서 가르치고 공부한 사상과 이상을 살펴보면 지금까지는 순조로운 진행을 보았고, 폭력적인 역전이나 심지어는 변곡점까지도 보지 못했으며, 결국 낡은 근대적 진보주의로 끝나게 된다. 물론, "미국의 전통"은 뉴잉글랜드의 전통을 의미합니다. 남북전쟁이 다르게 전개되었다면, 상황은 다르게 흘러갔을 수도 있습니다. 그러나 150년 전에 나다니엘 호손(Nathaniel Hawthorne)이 히피 공동체에 관한 소설을 썼다는 것을 알게 되면, 태양 아래서는 새로운 것이 없다는 것을 깨닫게 된다.

마키아벨리가 말한 바와 같이, 왕을 치거든 죽이려고 쳐라.[^3]겨우 50년 된, 그리고 수많은 허름한 뿌리를 가진 보수주의는 조롱과 경시, 경멸을 받을 수 있다. 보수 비판과 진보 비판의 차이는 모르몬교 비판과 기독교 비판의 차이입니다. 진보주의를 조금만 의심해도 안 된다. 대승적 차원에서 의심해야 한다.

보수가 부패하고 망상적인 전통이라고 하는 것은, 어느 정도의 '바이러스 X'에 지나지 않는다고 하는 것은, 미국의 편에 선 진드기, 일탈, 낙태, 고쳐야 할 오류라고 하는 것입니다. 교육의 실패, 리더십의 실패, 진보의 실패. 정말 작은 일이지.

진보주의를 의심하는 것은 미국의 생각 자체를 의심하는 것이다. 왜냐하면 진보주의는 그 생각이 결국 끝난 것이기 때문이다. 진보주의가 '바이러스 Y'라면 미국 자체도 감염된다. 그것에 대한 치료법은 무엇인가? 그것은 이상하고 끔찍한 생각, 즉 종말론의 약속이다.

그럼에도 불구하고 그것은 끔찍한 종류의 이치에 맞습니다. 우선, 만약 당신이 정신 바이러스라면, 어떤 전통을 선택해서 감염시키겠습니까? 미국 사상의 중심 조류, 아니면 어떤 베니티드 백워터? 브라만족, 아니면 타운족? 유행하는 사람들, 아니면 유행하지 않는 사람들?

여러분의 DNA를 뉴욕 타임즈에 복사하면 2, 30년 후에 폭스 뉴스로 흘러가게 될 것입니다. 폭스 뉴스로 복사하면 다음 선거에 영향을 미칠지도 몰라 아니면 두 개. 하지만 그게 얼마나 오래가는 거야? 조지 W 부시에게 지적으로 감동을 받는 사람이 얼마나 될까?(강제는 계산에 넣지 않는다.)

브라만(나는 당신이 브라만이라고 가정할 것이다)으로서 당신은 바이러스 Y 안에 산다. 당신은 좀비 중의 한 명입니다. 여러분의 세계관은 하버드, 타임즈, 그리고 나머지 것들은 데이비드 마멧의 시대에 설립자라고 부르곤 했습니다. 정부, 역사, 과학, 사회에 대해 당신이 알고 있는 모든 것들은 이 기관들에 의해 걸러졌습니다. 분명히 이 서술은 모순되지 않는다. 하지만 과연 그것이 사실일까요?

음, 대부분 모순되지 않아요. 아주 잘 조립되어 있습니다. 하지만, 어떤 곳에서는, 여러분이 정말 자세히 본다면, 여러분은 실오라기 같은 것을 볼 수 있다고 생각해요. 트루먼 쇼의 짐 캐리처럼 세상의 가장자리로 항해할 필요는 없다. 우선, 의심스러운 근육을 간지럽히고 약간 경련을 일으키는 데 필요한 것은, 딱 들어맞지 않는 몇 가지 세부사항이다.

세 가지 질문으로 시작하겠습니다. 우리는 작은 게임을 할 것입니다. 여러분은 진보적인 답을 생각해내려고 하고, 저는 진보적이지 않은 답을 생각해내려고 합니다. 어느 쪽이 더 말이 되는지 두고 보자.

이런 질문들이 진보적인 답변이 없다는 뜻은 아닙니다, 왜냐하면 그렇거든요. 모든 것이 보수적인 답변이 있듯이 진보적인 답변이 있습니다. 답안을 작성할 진보진영이 부족함이 없다. 그런데 이런 질문들이 만족스러운 진보적인 답변들을 가지고 있다고 생각하지 않습니다. 물론 이것을 당신 자신의 좋은 취향을 가지고 스스로 판단해야 할 것이다.

첫째: 제3세계는 어떻게 되었는가?

예를 들어, 여기 말라리아와의 싸움에 관한 타임즈 기사가 있다. 종종 정치인과 마찬가지로, 기자들은 그들의 진짜 관심사가 다른 것일 때, 멍한 상태에서 진실을 말한다. 만약 당신이 그 이야기를 읽는다면, 당신은 내가 한 것과 같은 놀라운 그래프를 알아차릴 수 있을 것이다.

>그리고 세상은 바뀌었다. 1960년대 이전에, 식민지 정부와 기업들은 말라리아와 싸웠다. 왜냐하면 그들의 관리들이 나이지리아의 언덕 정거장이나 베트남의 마블 산맥과 같은 외진 전초기지에서 종종 살았기 때문이다. 독립 운동은 자유로 이어졌지만 내전, 빈곤, 부패한 정부, 의료 붕괴로 이어지는 경우도 많았다.

그 마지막 문장에 초점을 맞추자. 독립 운동은 자유로 이어졌지만 내전, 빈곤, 부패한 정부, 의료 붕괴로 이어지는 경우도 많았다.

나는 종종 내가 목성 행성에서 온 외계인이라고 상상하는 것이 유용하다고 생각한다. 만약 내가 이 문장을 읽는다면, 나는 이렇게 묻고 싶다: 이 자유란 단어는 무엇인가? 이 작가가 말하는 자유란, 정확히 어떤 것인가? 특히 내전, 빈곤, 부패한 정부라는 맥락에서?

여기서 우리가 보는 것은, 작가가 분명히 좋은 것이라고 믿고 있는 독립 운동이, 이 기이한 추상화 외에도, 매우 구체적이고 매우 끔찍한 결과를 초래했다는 것입니다. 분명히 이 특정한 맥락에서 자유가 무엇을 의미하든지 간에 그것은 너무나 큰 긍정적이어서 그것을 내란, 가난, 부패한 정부, 의료의 붕괴에 보태도 그 결과는 여전히 제로를 넘어선다.

이상하지 않나요? 이 특별한 산수를 다시 찾고 싶지 않을까요? 하지만 우리는 그럴 수 없습니다. 만약 우리가 식민지 정부와 기업(이것이 무엇이든 간에)이 자유가 없는 독립 운동보다 더 바람직했다고 가정한다면, 우리는 진보적인 입장에서 이 같은 자유를 창조했기 때문입니다.

사실상 진보적 성향에서 벗어나 있을 뿐만 아니라 보수적 성향에서도 벗어났습니다. 아무도 이것을 믿지 않는다. 여러분은 폭스 뉴스나 townhall.com이나, 식민주의라고 주장하는 가장 형편없는 출판물들 말고는 아무도 찾을 수 없을 것입니다. 그 본질적인 자유 부재와 이상하게 효과적인 말라리아 통제를 가지고 있습니다(필자가 실제로 말할 것도 없이, 이것은 단지 악한 식민지 중첩의 이기적인 목적을 위해서만 전달되었다는 것을 암시합니다).), 자유, 말라리아, 내전 등과 함께 어떤 면에서든 후기 식민주의보다 우위에 있었다.

그리고 이 단어가 정확히 무엇인가요? 독립이란 단어는? 자유와 같은 의미인 것 같은데, 그런데도 이상하다. 예를 들어, CFR의 미셸 가빈에 의한 이 포스트 오피드를 생각해 보자. 이는 다음과 같은 흥미로운 선으로 시작한다.

>1980년 짐바브웨가 독립국이 되었을 때 아프리카의 미래에 대한 국제적 낙관론의 초점이었다. 오늘날 짐바브웨는 한 나라의 바구니 사건이다.

목성에서 떨어진 외계인 모자를 다시 쓰고, 다음과 같은 구절을 생각해 보자. 짐바브웨가 1980년에 독립국이 되었을 때…

그녀가 보통 말하는 영어에서, 독립적인 단어는 "아니오"를 의미하는 접두사와 "의존적"을 의미하는 접미사로 구성되어 있다. 그러니까 예를 들어서 미국이 독립했을 때 어떤 외부 정당도 자기 정부에 자금을 지원하거나 통제하고 있지 않다는 것을 의미했습니다. 내 딸이 독립한다면, 그것은 그녀가 세상에서 스스로 결정을 내리고 있다는 것을 의미할 것이고, 나는 세 시간마다 그녀에게 한 병씩 줄 필요가 없었다.

그러나 짐바브웨의 경우 이 말이 이상하게 바뀌어 거의 정반대의 의미를 띠게 된 것 같다. La 위키에서:

>영국으로부터 로디지아의 일방적인 독립 선언(UDI)은 1965년 11월 11일 당시 영국 식민지에서 흑인의 다수결 원칙에 반대한 이언 스미스의 행정부에 의해 서명되었다. 영국으로부터의 독립을 선언했지만 엘리자베스 2세 여왕에 대한 충성을 유지했다. 영국 정부와 영연방, 유엔은 이번 조치가 불법이라고 규탄했습니다. 로디지아는 1979년부터 1980년까지 짧은 기간 동안 영국의 사실상의 지배와 드 쥬레 영국의 지배로 되돌아가다가 1980년 짐바브웨로서 독립을 되찾았다.

그래서 이상하게도, 현재 짐바브웨로 알려진 이 나라는 미국이 1776년에 독립을 선언했던 만큼 1965년에 독립을 선언했다. 그러나 전자는 진정한 독립이 아니라 오히려 불법적인 독립이었다. 진정한 합법적 독립을 얻기 위해 짐바브웨로 알려진 이 나라는 우선 영국의 지배로 되돌아가야 했고, 즉 불법적인 독립을 포기해야 했다. 아직 혼란스러우세요? 더 나아지고 있습니다.

>1980년 짐바브웨가 독립국이 되었을 때 아프리카의 미래에 대한 국제적 낙관론의 초점이었다. 오늘날 짐바브웨는 한 나라의 바구니 사건이다. 지난 10년 동안 로버트 무가베 대통령과 집권당이 자신들의 권력에 대한 도전을 용납하지 않는 바람에 짐바브웨 경제 및 정치체제의 가장 효과적인 작업을 조직적으로 해체하고 이를 부패, 노골적인 후원, 억압의 구조로 대체하게 되었다.

그래서: 자유 짐바브웨의 독립된 통치자들은 그들의 권력에 대한 도전을 용납하지 않았다. 따라서 가빈(Gavin, 1980년에 아마도 한 두 병이 필요했을 것이다) 여사와 그녀의 우유가 가지고 있는 국제적 낙관주의는 비관론에 자리를 내주었고, 그 장소는 이제 바스켓 케이스가 되었다. 그리고 누가 좋은 무가베 대통령의 권력에 도전했을까? 아마도 짐바브웨의 경제 및 정치 시스템의 가장 효과적인 작업을 해체할 의도가 없었던 것으로 추정되며, 이는 가빈 씨와 그녀의 불유불능의 우애를 얻는 것이다. 이 독립성은 보시다시피 매우 호기심이 많은 것입니다.

스스로 일을 하고 절대 병도 필요치 않다는 의미에서, 사실 세계에는 눈에 띄게 독립된 나라가 하나 있다. 소말릴란드라고 하는데, 국제사회에서 누구에게도 인정받지 못하고 있습니다. 소말릴란드의 수도인 하르게이사를 위한 위키백과 페이지는 의도하지 않은 높은 코미디의 영광스러운 수준을 달성합니다.

>외국 정부의 원조는 존재하지 않아 아프리카에서는 대외원조에 대한 의존도가 낮아 이례적이었다. 소말릴란드는 독립국가로서 탈사실화 되어 있지만 국제적으로 (법적으로) 인정받지 못하고 있다. 따라서 소말릴란드 정부는 IMF와 세계은행의 원조에 접근할 수 없다.

이 모든 게 상당히 궁금하지 않습니까? 짐 캐리가 요트를 세상 가장자리의 매트 그림 속으로 쾅쾅 들이치는 장면이 조금이라도 생각나지 않는가?

둘째: 민족주의란 무엇인가? 그리고 그것이 좋은가, 나쁜가?

이 질문은 질문1과 다소 유사하다. 제가 존경하는 진보 블로거가 '호찌민은 민족주의자'라는 오프핸드 코멘트를 했을 때 떠올렸다. "그럼," 나는 생각하고 있는 자신을 발견했다. "그리고 팻 부캐넌도 마찬가지야." 그때가 아니었지만, 이 작은 모티브인 데스칼리어를 구했고 지금 다시 꺼내지 않고는 못 배깁니다, 나쁜 물고기처럼.

독립과 달리, 나는 모든 사람들이 민족주의의 정의에 거의 동의한다고 생각한다. 민족주의(國民主義, )는 공통의 언어적, 민족적, 인종적 유산의 사람들이 하나의 정치적 실체로서 집단적으로 행동할 필요성을 느끼는 때를 말한다. 독일 민족주의는 독일인이 할 때, 베트남 민족주의는 베트남인이 할 때, 흑인 민족주의는 아프리카계 미국인이 할 때, 미국의 민족주의는 팻 부캐넌이 할 때이다.

그리고 여기서 협정이 끝난다. La 위키의 첫 단락은 난독화의 걸작이다.

>민족주의(國民主義, Nationalism)는 보통 민족이나 문화면에서 정의되는 민족이 역사나 공동운명에 기초하여 독립적 또는 자율적 정치공동체를 구성할 권리를 갖는다고 주장하는 교리나 정치운동을 가리키는 용어이다. 대부분의 민족주의자들은 국가의 국경이 국가의 국경과 일치해야 한다고 믿는다. 그러나 최근 민족주의자들은 그 호혜적 가치를 위해 '공조'라는 개념을 거부해 왔다. 현대 민족주의자들은 국가가 단일 국가에 의해 통치되어야지, 국가가 단일 국가에 의해 통치되어야 한다고 주장할 것이다. 때때로 민족주의적인 노력은 우월주의나 제국주의 때문에 골치를 앓을 수 있다. 20세기 파시스트 운동에 의해 전파된 것과 같은 이러한 전 민족주의적인 노력은 여전히 국적이 정체성의 가장 중요한 측면이라는 민족주의 개념을 가지고 있는 반면, 그들 중 일부는 인종이나 유전학적으로 부정확하게 국가를 정의하려고 시도했다. 다행히도, 현대 민족주의자들은 이들 집단의 인종차별주의적 우월주의를 거부하고, 민족 정체성이 민족에 대한 생물학적 애착을 대체한다고 확신하고 있다.

그들 사이의 모든 것은 내가 알 수 있는 한 순전히 허튼소리이지만, 첫 문장과 마지막 문장의 직접적인 모순에 주목하라. 민족적 정체성이 민족에 대한 생물학적 애착을 대체한다고 믿는다면 어떻게 민족주의자가 될 수 있겠는가? 민족주의가 인종주의적 우월주의에 시달리지 않는다면 어떤 의미에서 민족주의란 말인가.

제가 체코인이며 오스트리아-헝가리에 살고 있다면, 제 조국에 대한 권리가 있나요? 받을 때까지 폭력과 테러와 폭탄을 만들어야 할까요? 만약 내가 독일인이고 체코슬로바키아에 산다면? 폭력과 테러와 폭탄을 만들어야 할까요?

많은 독일인들이 20년대와 30년대에 이 이상한 점을 알아차렸다. 그들은 미국과 그녀의 친구들이 국가적 자결, 즉 당신이 우연히 독일인이 되지 않는 한 대단히 헌신적이라는 것을 알아차렸다. 체코 민족주의는 좋았어. 아주 좋았어. 독일의 민족주의는 나빴어. 아주 나빴어.

일단 캔버스에서 이 작은 꿰미를 찾기 시작하면, 어디에서나 발견하게 됩니다. 흑인 민족주의자가 되는 것은 좋은, 아주 좋은 일이다. 래퍼 라이트(Light)에서 우리는 진보주의와 흑인 민족주의 사이의 친밀함을 보아왔다. 톰 울프가 잘 보여 주었다. 실제로, 미국의 모든 유명한 대학에는 학생들이 본질적으로 흑인 민족주의를 전공할 수 있는 학과가 있다.

반면에 남부 민족주의자가 되는 것은 나쁘다, 매우 나쁘다. 남방 민족주의와의 어떤 연관성이라도 즉각적으로 하나의 파리아를 낳는다. 물론 남부 민족주의자들은 죄를 지었다. 하지만 역시 흑인 민족주의자들 역시 마찬가지입니다. 흑인이든 백인이든 미국인이든 흑인의 팬터, 이슬람의 국가, 혹은 심지어 선량한 신부의 활동에 정말로 더 잘 살고 있는가? 라이트?

마찬가지로 베트남 국수주의자가 되는 것도 좋다. 독일 민족주의자, 혹은 영국 민족주의자, 혹은 심지어 프랑스 민족주의자가 되는 것은 여전히 나쁘다. 독일인, 영국인, 프랑스인은 모든 인류의 공통된 운명을 믿게 되어 있다. 베트남인이나 멕시코인이나 체코인은 자유롭게 베트남인이나 멕시코인, 체코인의 공동운명체를 믿고 있다.(사실 체코인에 대해서는 잘 모르겠다. 이게 바뀌었을지도 몰라요.)

이게 말이 됩니까? 말도 안 되는 소리야?

이 과목은 너무 까다롭기 때문에, 나는 그것에 대한 내 감정을 흘려보낼 것이다. 나는 어떤 종류의 민족주의도 믿지 않는다. 물론, 자코바이트인이고 뭐고, 나 또한 스트라포드의 자치구를 믿으니까, 당신은 나에게서 당신의 헌법적 조언을 얻고 싶지 않을지도 모른다.

셋째: 나치가 뭐가 그리 나쁜가?

천만 명을 죽였어 안됐군요. 수백만 명의 민간인 학살을 옹호하는 것은 정말 있을 수 없는 일입니다.

한편, 니콜슨 베이커의 신간 '인간 연기'를 정말 추천합니다. 베이커는 진보적이고 평화주의자이며 (그의 이전 업적은 부시 대통령을 암살하는 환상을 가진 소설이었다) 그리고 인간 연기 드럼이 당신에게 주는 것은 특정한 메시지가 아니라, 내가 계속 말하는 것과 같은 것이다: 그림의 조각들이 서로 맞지 않는다. 거의 맞긴 맞는데 잘 안 맞네요. 베이커의 책의 천재성은 단순히 그림이 어울리지 않는 것을 보여주고, 분석을 당신에게 맡긴다는 것이다.

예를 들어, 우리는 나치들이 대량학살을 저질렀기 때문에 나쁘다고 배웁니다. 재치있게 말하자면, 홀로코스트를 저질렀기 때문입니다. 반면에 (a): 우리를 포함한 나치에 대항하는 어떤 정당도 유태인이나 홀로코스트에 대해 많은 것을 주지 않은 것 같다; (b) 우리 쪽의 정당 중 하나는 당시 대량 살인에 대한 기록이 알려졌고 적어도 나치만큼 끔찍했던 소비에트 연방이었다.

그리고 물론 (c) : 연합군은 독일과 일본 민간인의 공중 소각에서 긍정적으로 폭로를 했다. 6백만 명을 죽이지 않았지만 한두 명을 죽였습니다. 이런 부분에 대해서 군사적으로 변명하는 부분이 있었지만 상당히 경색된 측면이 있었습니다. 그것은 (물론 적 민간인으로 본) 유대인들을 살해한 나치의 변명보다 나았다. 사실, 훨씬 더 좋았어요. 그런데 많이 좋아졌나요? 잘 모르겠어요.

그리고 베이커가 언급하지 않듯이, 우리의 영웅인 연합군도 전쟁 후 러시아 난민 100만 명을 수용소로 추방하거나, 수십만 명의 독일군 포로를 소련에 노예 노동자로 빌려주는 것에 대해 거리낌이 없었다. 제2차 세계대전을 인권전쟁으로 보는 발상은 그야말로 역사학적이다. 안 맞아요. 나치 인권침해가 지금 우리가 살고 있는 세상을 창조한 전쟁의 동기가 아니었다면 - 무엇이었을까?

더군다나 오늘날 미국의 외교정책을 당연히 비판하고 있는 베이커는 이라크와 독일에도 똑같은 기준을 적용하려 할 때 혼란밖에 보지 못한다. 아부그라이브가 이라크에 무력으로 민주주의를 강요하는 데 가교할 수 없는 장애물이라면 드레스덴이나 함부르크, 독일은 어떨까. 확실히 수만 명을 산 채로 태우는 것이, 가짜 전선을 쓰고 우스꽝스러운 모자를 쓰고 상자 위에 서게 하는 것보다 더 나쁘단 말인가? 아니면 이라크가 독일과 다를 뿐인가? 하지만 그건 인종차별이겠죠?

이를 넘어서면 파시스트 대량살해 대 마르크스주의 대량살해라는 처사에 있어서 독특한 비대칭성이 나타난다. 두 이데올로기 모두 분명히 대량 살인의 역사를 가지고 있다. 만약 숫자가 중요하다면, 왜 그렇게 하지 않을까요?— 마르크시즘은 규모 순으로 앞서 있다. 그러나 어찌된 일인지 오늘날 파시즘이나 그것을 연상시키는 것은 순수한 독이고 손댈 수 없는 것이지만 반면에 마르크스주의는 기껏해야 페카딜로의 일종인 것이다. 존 즈미락은 여기에 대한 멋진 패러디를 보여주는데, 내가 아직 로베르토 볼라뇨를 읽지 않은 동안, 그 리뷰들은 꽤 빛을 발하고 있다.

소련도, 제3제국도 오늘날 우리와 함께 있지 않지만 가장 최근의 역사적 예는 북한과 남아프리카이다. 아파르트헤이트인 남아프리카는 느슨했지만 여전히 나치즘과의 연관성을 알아볼 수 있는 반면, 북한은 분명히 어느 정도 스탈린주의적이다. 나는 국경 울타리가 이민자들을 막기 위해 만들어진 남아프리카가 북한보다 더 나쁜 인권침해국이었다는 것을 주장하고 싶은 모든 사람들을 환영한다. 북한 전체가 감옥으로 변했다. 그럼에도 불구하고 우리는 북한과 같은 비대칭성을 봅니다. 즉, 남아프리카에 대한 순수한 적대감입니다. 만약 뉴욕 필하모닉이 양국의 신뢰를 구축하기 위해 프리토리아를 방문하는 것을 상상할 수 있다면, 당신은 볼라뇨울드에 확고히 있다.

다시 말하지만, 이건 정말 이상해. 민족주의와 마찬가지로 각각의 개별적인 사례도 그 나름의 용어로 설명될 수 있다. 모든 사건을 종합해 보면 이중잣대가 도처에 널려 있다. 그럼에도 불구하고 모순은 무작위로 보이지 않는다. 나치가 가지고 있고 소련이 가지고 있지 않거나, 남아공 사람들이 가지고 있고, 북한 사람들이 가지고 있지 않은 신비스러운 X 요인이 있는 것 같다. 그 치료는 X에 의해서만 이루어지는 것이 아니라 X+인권일 수도 있지만 분명히 단순한 인권이 아니다. 그런데도 설명에는 X가 나타나지 않는다.

X는 나치가 '우파'이고 소련이 '좌파'라는 것과 관련이 있는 듯하다. 프랑스 사람들이 말하길: pas d'ennemis à gookhe, pas d'amis à 드로우이트.[^4]  그런데 왜? '우파'와 '좌파'는 무슨 의미까지 있는 것일까. 소련과 나치 체제가 모두 전체주의 독재체제가 아니었던가. 공산주의가 '너무 뜨겁다', 파시즘이 '너무 춥다', 자유민주주의가 '그저 옳다'면 공산주의와 파시즘을 동등하게 반대하면 어떨까. 사실, 전자는 적어도 1945년 이후 훨씬 더 성공적이기 때문에, 여러분은 사람들이 그것에 대해 더 걱정하게 될 것이라고 생각할 것입니다.

다시 말하지만 우리는 순수한 혼란에 빠져 있다. 수평선이 캔버스로 이루어진다는 것은 도저히 있을 수 없는 일이다. 그럼에도 불구하고 우리 보트는 충돌했고, 큰 바가지를 남겼습니다.

[^1]:스터프 화이트 피플 라이크 부족을 지칭하기 위해 이니셜리즘 "SWPL"(스위플로 발음)을 사용하는 것이 일반화되었다.
[^2]:Moldbug는 나중에 이 그룹에 대해 더 나은 용어를 만들었다. 남아프리카의 아프리케너족과 유추하여 아메리케너족. 그가 <외국의 영토를 점령하고 통치하는 방법>에 다음과 같이 적고 있다. 


    >그들의 어휘적 비유처럼, 아메리카네르족은 유럽 주식의 문화 집단이지만, 그들의 현대 유럽의 어떤 집단과도 쉽게 연결될 수 없다.

[^3]:   이 제형은 랠프 왈도 에머슨에 의한 것으로, 올리버 웬델 홈즈에 의해 대중화되었다. 마키아벨리의 왕자에 영감을 준 인용문은 다음과 같다. 

    > 이 말을 듣고, 사람들은 더 가벼운 상처와 더 심각한 상처로 복수할 수 있기 때문에, 사람들은 잘 치료받거나 으스러져야 한다고 말해야 한다. 그러므로 사람에게 해야 할 상처는 복수를 두려워하지 않는 그런 종류의 것이어야 한다.

[^4]: 보통 영어로 "왼쪽에는 적이 없고, 오른쪽에는 친구가 없다"라고 번역되어 있다.














