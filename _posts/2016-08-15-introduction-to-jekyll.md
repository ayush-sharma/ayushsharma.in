---
layout: post
title:  "Introduction to Jekyll"
number: 12
date:   2016-08-15 2:00
categories: development
---
<em>Note: Because this post is about Jekyll itself, putting Jekyll code here as examples would cause my Jekyll installation to build it as code when I deploy, and that would suck. To get around that hurdle, I've put extra spaces within some of the example content, specifically like `{ {` and `{ %`.</em>  


There are already a lot of blogging platforms out there, and if you're looking for a static site generators, the most popular one right now is Jekyll. A static site generator will take your content, some configuration and markup, and turn it into HTML which you can then host anywhere. There are advantages to a static site, the main ones being that your site is free from the pitfalls of a dynamic website built using, say, PHP, and that it's going to be really fast since you're only serving standard HTML. In order to write your content, you can use Markdown and Liquid, which only adds to the fun.

## Installation
You will need Ruby and RubyGems installed for this to work. Jekyll can be installed using the following commands:

```
gem install jekyll
jekyll new my_blog
cd my_blog
jekyll serve
```
Now visit `http://localhost:4000` in your browser. You should see your default "You awesome title" blog.

Note: To initialize Jekyll in your current directory, use `jekyll new .`, and `jekyll new . --force` to initialize it in a non-empty directory.

Further installation notes can be [found here](https://jekyllrb.com/docs/installation/).

## Directory Structure

```
- `_posts`: This directory will contain your main content.
- `index.html`: Home page.
- `_drafts`: Drafts go here.
- `_layouts`: Layouts for posts and pages.
- `_includes`: Included files.
- `_data`: Data directory.
- `_site`: Your final HTML content will go here.
- `_config.yml`: Site-wide configuration settings file.
```

## Posts
Creating posts is simple. All you need to do is create a new file under `_posts` with the proper format and extension, and you're all set.
A valid file name is `2016-08-15-my-first-post.md`. A post file must contain what Jekyll calls YAML Front Matter. This is a special section at the beginning of the file, contained within triple-dashes, which contain some metadata to be used while generating that file. This section can also contain special variables whose values are available for use within that post or other files that post includes. Useful for setting post titles, categories, and tags. It looks something like this:

```yaml
---
layout: post
title: My Post Title
tags: tag1 tag2
categories: category1 category2
---
```
The values you define here can be used within your post. A full list of variables can be found [here](https://jekyllrb.com/docs/variables/).

Let's create a new post. Create `2016-08-15-my-first-post.md` under `_posts` and add the following content to it:

```yaml
---
layout: post
title:  "My Post Title"
date:   2016-08-15 0:00
categories: post general
---
This is my first post.

# This is a heading.

## This is another heading.

This is a [link](http://notes.ayushsharma.in)
```

If the `jekyll serve` command you executed previously is still running, just refreshing the blog should show you your new entry. You've just created a new blog post, and it's really that simple. Check out the source code of the post and you should see that it only contains plain HTML. That's what Jekyll is all about, converting your convenient markdown content to HTML.

In addition to markdown, Jekyll also processes variables and Liquid tags. For example, an index of posts can be displayed by using the `site.posts` variable and iterating over it. Check out the source code for `index.html` and you can see this in action. Syntax highlighting is also supported, and can be shown using a block starting with `{ % highlight ruby % }` and ending with `{ % end highlight % }`. Syntax highlighting is supported using Pygments or Rouge.

## Drafts
Jekyll also supports drafts. They are posts you're not ready to publish yet. They live in the `_drafts` folder, and you can see them using `jekyll build --drafts`.

## Layouts And Includes
In the post we created above, we specified `layout: post` in the Front Matter. Check out the `_layout` folder and you should see a `post.html` file inside. Open it up. This file contains the HTML that is contained within each post. The `{ { content }}` on line 12 is where all your post data actually goes, and the rest of the content defined in this file goes around it. That's how Jekyll layouts work. As you can see, this `post.html` file itself uses a layout, and if you open up that file, you'll find another `{ { content }}` variable. You'll also find some `include` variables, along with file names. These include files can be found under `_includes`. Starting to figure out the process? Jekyll will take your post, process all the layouts and includes, placing content where it finds `{ { content }}`, and generate a final HTML file that you can use. If you've created HTML content dynamically, all of this should be familiar. We create dynamic header and footer files and place content between them. Jekyll does it the same way. You are, of course, free to change the content of all of the files that you see. You have full control over the structure and design of your blog, with Jekyll providing the framework to join things together.

## Pages
Not all content you may want to create will fit a blog post. You can create static HTML pages for anything you want. Depending on the page URLs you want, there are two ways of doing this. First, you can create a `my_page.html` or `my_page.md` in the root directory of your Jekyll installation, which will create a corresponding `http://myblog/my_page.html` URL. The second way is to create a `my_page` folder in root, and place an `index.html` or `index.md` file inside. This will create a URL like `http://myblog/my_page/`. You can use Jekyll layouts within Pages. You default installation should already have an `about.md` page. Be sure to check out the Front Matter in this file. It uses a `page` layout rather than a `post` layout. These layouts can be found in the `_layouts` directory.

## Data Files
Data files live in the `_data` directory, and can be `.yml`, `.json`, or `.csv`. These will be read in by the templating engine. For example, a `_data/members.yml` file may contain:

```yaml
- name: A
  github: a@a

- name: B
  github: b@b

- name: C
  github: c@c
```

This can then be accessed using `site.data.members`.

```html
<ul>
{ % for member in site.data.members %}
  <li>
    <a href="https://github.com/{{ member.github }}">
      { { member.name }}
    </a>
  </li>
{ % endfor %}
</ul>
```

## Permalinks
Permalink can be defined in your configuration file as, for example, `permalink: /:year/:month/:title`. Add this to your configuration file, rerun `jekyll serve`, and the URL of your post should have changed.

## Building
The command `jekyll serve` is great for local testing, but once you're with the content and customisation, you can build the final static site using `jekyll build` in the current directory, or `jekyll build --source source_dir --destination destination_dir`. This command will process everything, and place the final HTML content inside the `_site` folder. Note that this folder is clean up before every build, so don't place important things in there. Once the content is generated, you can host it on a service of your choosing.